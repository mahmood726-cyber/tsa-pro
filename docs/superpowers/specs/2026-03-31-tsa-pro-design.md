# TSA Pro — Design Specification

**World-First Browser-Based Trial Sequential Analysis**

*"No installation, no Java, no desktop software — open a file and run TSA"*

**Date:** 2026-03-31
**Author:** Mahmood Ahmad (Royal Free Hospital, London)
**Project directory:** `C:\Models\TSA\`
**Status:** Design approved, pending implementation

---

## 1. Problem Statement

Trial Sequential Analysis (TSA) determines whether accumulated evidence in a meta-analysis is sufficient to draw firm conclusions, or whether more trials are needed. The only existing tool is the Copenhagen Trial Unit's Java desktop application — closed-source, Windows-only, requires installation. No browser-based implementation exists.

**TSA Pro** is the first open-access, browser-based TSA tool. It runs entirely in the browser from a single HTML file with no dependencies.

## 2. What Makes This Novel

1. **World-first browser implementation** — no installation, works offline
2. **Full alpha-spending framework** — O'Brien-Fleming, Pocock, Lan-DeMets, custom
3. **Futility boundaries** — binding and non-binding beta-spending
4. **Binary + continuous** outcomes (RR, OR, RD, MD, SMD)
5. **Auto-generated Cochrane-ready report text**
6. **3 built-in landmark examples** demonstrating different TSA conclusions

## 3. Statistical Framework

### 3.1 Required Information Size (RIS)

**Binary outcomes:**
```
RIS = 4 * (z_{alpha/2} + z_beta)^2 * p_bar * (1 - p_bar) / delta^2
```
Where `p_bar` = average control event rate, `delta` = anticipated absolute risk difference.

For RR/OR on log scale:
```
RIS = 4 * (z_{alpha/2} + z_beta)^2 / (ln(RR_anticipated))^2
```

**Continuous outcomes:**
```
RIS = 2 * (z_{alpha/2} + z_beta)^2 * sigma^2 / MCID^2
```

**Heterogeneity adjustment:**
```
D^2 = (I^2) / (1 - I^2)    [diversity]
RIS_adj = RIS * (1 + D^2)
```
When I^2 = 0, D^2 = 0 and RIS_adj = RIS.

### 3.2 Information Fraction

At each cumulative look k (after adding study k):
```
IF_k = N_cumulative_k / RIS_adj
```
Where N is total participants across all studies included up to look k.

### 3.3 Alpha-Spending Functions

All spending functions map information fraction t in [0,1] to cumulative alpha spent alpha*(t):

- **O'Brien-Fleming:** `alpha*(t) = 2 - 2 * Phi(z_{alpha/2} / sqrt(t))`
- **Pocock:** `alpha*(t) = alpha * ln(1 + (e - 1) * t)`
- **Lan-DeMets (OF-type):** Same formula as O'Brien-Fleming
- **Lan-DeMets (Pocock-type):** Same formula as Pocock
- **Custom:** User provides alpha*(t) at each look

Properties: alpha*(0) = 0, alpha*(1) = alpha, monotonically increasing.

### 3.4 Monitoring Boundary Computation

At each look k with information fraction t_k:

1. Compute cumulative alpha spent: `alpha*(t_k)`
2. Compute incremental alpha: `delta_k = alpha*(t_k) - alpha*(t_{k-1})`
3. Use recursive numerical integration (Armitage-McPherson-Rowe algorithm) to find critical value z_k

The recursive integration:
- Discretise the standard normal distribution into M=500 grid points
- At look 1: find z_1 such that `P(|Z_1| > z_1) = delta_1`
- At look k: propagate the density forward using the correlation structure `rho_{j,k} = sqrt(t_j/t_k)`, then find z_k such that the incremental crossing probability = delta_k

This produces the monitoring boundary: `{(t_1, z_1), (t_2, z_2), ..., (t_K, z_K)}`

### 3.5 Futility Boundaries (Beta-Spending)

Same framework applied to type II error:
- Beta-spending function: `beta*(t)` mapping IF to cumulative beta spent
- OF-type and Pocock-type beta-spending functions available
- **Binding:** Probability calculation assumes trial stops at futility (more conservative)
- **Non-binding:** Futility is advisory; alpha boundary unchanged (Copenhagen default)

### 3.6 Cumulative Z-Statistic

After adding study k to the cumulative meta-analysis:
1. Pool all studies 1..k using REML random-effects with HKSJ
2. Compute `Z_k = estimate_k / SE_k`
3. Plot (IF_k, Z_k)

**Verdict logic:**
- Z_k crosses upper efficacy boundary → BENEFIT CONFIRMED
- Z_k crosses lower efficacy boundary → HARM CONFIRMED
- Z_k crosses futility boundary → EFFECT UNLIKELY, stop for futility
- Z_k between all boundaries → INCONCLUSIVE, more trials needed
- IF_k > 1.0 and Z_k not crossed → EVIDENCE EXCEEDS RIS but remains inconclusive

### 3.7 TSA-Adjusted Confidence Intervals

Account for the sequential stopping rule using the stage-wise ordering:
```
CI_adjusted = estimate_K ± z_K_boundary * SE_K
```
Where z_K_boundary is the boundary value at the final look. These are wider than naive CIs, reflecting the penalty for sequential testing.

## 4. Architecture

Single HTML file. No build step, no server, no dependencies.

```
C:\Models\TSA\
├── tsa-pro.html              # Main app (~8,000-12,000 lines)
├── docs/
│   ├── e156-protocol.md      # 156-word protocol
│   ├── e156-results.md       # 156-word results paper
│   └── superpowers/
│       ├── specs/             # This file
│       └── plans/             # Implementation plan
└── validation/
    ├── rpact_validate.R       # R validation script
    └── validation_results.md  # Cross-validation output
```

### Module structure within the HTML

The `<script>` block is organized as:

```
1. DATA (built-in examples)
2. MATH ENGINE
   - normalCDF, normalQuantile (Phi, Phi-inverse)
   - sampleSizeRIS(params) → RIS
   - diversityAdjust(I2) → D2
   - alphaSpending(t, type, alpha) → cumulative alpha
   - betaSpending(t, type, beta) → cumulative beta
   - computeBoundary(looks, spending, alpha) → [{t, z}]
   - computeFutility(looks, spending, beta, binding) → [{t, z}]
3. META-ANALYSIS ENGINE
   - computeLogOR / computeMD / computeSMD
   - poolREML(yi, vi) → {estimate, se, tau2, I2}
   - cumulativeMA(studies) → [{k, estimate, se, ci, z, N_cum, IF}]
4. TSA ENGINE
   - computeTSA(studies, settings) → {boundaries, futility, zCurve, RIS, verdict}
5. CHART ENGINE
   - drawTSADiagram(canvas, tsaResult)
   - drawCumulativeForest(canvas, cumulativeResults)
6. UI ENGINE
   - Data entry (manual + paste + examples)
   - Settings panel
   - Report generator
   - Export (PNG + CSV + text)
7. INIT
```

## 5. User Interface

### Tab 1: Data Entry

**Outcome type selector:** Binary / Continuous (radio buttons at top)

**Manual entry table:**
- Binary: Study | Year | Events Exp | N Exp | Events Ctrl | N Ctrl
- Continuous: Study | Year | Mean Exp | SD Exp | N Exp | Mean Ctrl | SD Ctrl | N Ctrl
- Add/remove row buttons
- Auto-calculate totals row

**Paste area:**
- Textarea with placeholder showing expected format
- "Parse" button that auto-detects columns from headers
- Preview table showing parsed data before confirming

**Examples dropdown:**
- "Load Example" button with 3 options
- Loading replaces current data (with confirmation if data exists)

### Tab 2: TSA Settings

**Analysis parameters:**
- Alpha: 0.05 (default), input with ±0.01 step
- Power (1-beta): 0.80 (default), dropdown: 0.80 / 0.90 / 0.95
- Anticipated effect: Auto-filled from pooled estimate, editable
- Control event rate: Auto-filled from data (binary only), editable
- Effect measure: RR / OR / RD (binary) or MD / SMD (continuous)

**Boundary settings:**
- Alpha-spending function: O'Brien-Fleming / Pocock / Lan-DeMets (OF) / Lan-DeMets (Pocock) / Custom
- Futility boundary: Off / Non-binding / Binding
- Beta-spending function: (same options, shown when futility enabled)

**Heterogeneity:**
- D² (diversity): Auto from data I², editable
- Show I² and tau² from cumulative MA

**"Run TSA" button** — computes everything and switches to Tab 3

### Tab 3: TSA Diagram

**Main TSA plot** (Canvas, ~600x400):
- X-axis: Information fraction (%) or cumulative N (toggle)
- Y-axis: Cumulative Z-statistic
- Upper efficacy boundary: solid red line with dots at each look
- Lower efficacy boundary: solid red line (mirror)
- Futility boundaries: dashed blue lines (if enabled)
- Z-curve: black stepped line connecting cumulative Z values
- Vertical dashed grey line at IF=100% (RIS reached)
- Shaded regions: green (inconclusive), red (beyond boundary)
- Final point: large green circle (crossed) or orange circle (not crossed)

**Verdict box** (below chart):
- Large text: "BENEFIT CONFIRMED" / "INCONCLUSIVE" / "FUTILITY — STOP"
- Explanation paragraph
- TSA-adjusted CI

**RIS panel** (right sidebar):
- Required Information Size: N patients
- Current information: M patients (X%)
- Remaining: Y patients needed
- D² (diversity): value
- Conditional power: Z%
- Number of looks: K

**Cumulative forest plot** (below TSA diagram):
- One row per cumulative step
- Study name | Cumulative estimate | 95% CI | Weight bar
- Diamond grows as studies accumulate
- Background colour: green/amber/red matching TSA verdict at that point

### Tab 4: Report & Export

**Auto-generated report text:**
```
Trial sequential analysis was performed using TSA Pro (Ahmad, 2026)
with a two-sided alpha of [X]%, power of [Y]%, and an anticipated
[effect measure] of [Z]. The [alpha-spending function] monitoring
boundary was applied. The required information size was [N] participants
(adjusted for diversity D²=[D]). After [K] studies including [M]
participants ([P]% of RIS), the cumulative Z-curve [DID/DID NOT]
cross the monitoring boundary for [benefit/harm]. The TSA-adjusted
95% confidence interval was [A] to [B].
```

**Export buttons:**
- Copy report text to clipboard
- Download TSA diagram as PNG
- Download boundary values as CSV (look, IF, Z_boundary, Z_futility, Z_observed)
- Download full results as JSON

### Design constraints
- Single HTML file, no build step, no server
- Canvas-based charts (no D3/SVG dependencies)
- Dark mode matching MetaAudit aesthetic (same CSS variables)
- Light mode toggle (localStorage `tsa-theme`)
- WCAG AA contrast
- Print-friendly (`@media print`)
- `aria-label` on all canvases
- `.sr-only` severity text for screen readers

## 6. Built-in Example Datasets

### Example 1: Aprotinin for Bleeding in Cardiac Surgery
- **Source:** Cochrane CD004338 (Henry et al.)
- **Outcome:** Blood transfusion (binary, RR)
- **Studies:** ~20 RCTs
- **TSA verdict:** Boundary crossed early (~10 studies) — evidence was conclusive well before all trials completed
- **Teaching:** "We could have stopped 10 trials ago"

Approximate data (events/N per arm for ~15 key studies):
```
[{name:"Cosgrove 1992", eE:18, nE:56, eC:28, nC:57},
 {name:"Lemmer 1994", eE:12, nE:29, eC:22, nC:26},
 {name:"Levy 1995", eE:8, nE:103, eC:22, nC:112},
 {name:"Alderman 1998", eE:44, nE:436, eC:57, nC:434},
 {name:"Diprose 2005", eE:16, nE:60, eC:30, nC:60},
 ...]  // Will use published summary data from Cochrane review
```

### Example 2: Therapeutic Hypothermia for Neonatal Encephalopathy
- **Source:** Cochrane CD003311 (Jacobs et al.)
- **Outcome:** Death or major disability (binary, RR)
- **Studies:** ~12 RCTs
- **TSA verdict:** Boundary crossed near RIS — evidence sufficient but accumulated gradually
- **Teaching:** "Enough evidence, but barely"

### Example 3: IV Magnesium for Acute MI
- **Source:** Teo et al. 1991 + ISIS-4 1995
- **Outcome:** Mortality (binary, OR)
- **Studies:** ~16 RCTs including ISIS-4
- **TSA verdict:** Z-curve NEVER crosses the monitoring boundary despite appearing "significant" in traditional MA after early trials
- **Teaching:** "Traditional meta-analysis was wrong — TSA would have prevented premature conclusions"

## 7. Testing & Validation

### Against R rpact package
- Run all 3 examples through `rpact::getDesignGroupSequential()`
- Compare: Z-boundaries at each look (tolerance ±0.01), RIS (tolerance ±5%)
- R is at `C:\Program Files\R\R-4.5.2\bin\Rscript.exe`

### Against Copenhagen TSA published values
- Copenhagen papers report boundary values for standard examples
- Compare our computed boundaries against their published tables

### Internal consistency
- `alpha*(1.0)` must equal alpha for all spending functions
- `beta*(1.0)` must equal beta for all spending functions
- Boundaries monotonically decreasing for OF-type
- Boundaries approximately constant for Pocock-type
- Z-curve at each look matches independent re-pooling
- RIS with D²=0 matches standard sample size formula

### Edge cases
- k=1 (single study, D²=0)
- All identical studies (tau²=0)
- Zero events (continuity correction)
- IF > 200% (far past RIS)
- Boundary crossing at first look
- Custom spending function validation (must be monotone, start at 0, end at alpha)

## 8. Deliverables & Publication

| # | Deliverable | Target |
|---|-------------|--------|
| 1 | TSA Pro HTML app | `C:\Models\TSA\tsa-pro.html` |
| 2 | E156 protocol (156 words) | Register before validation |
| 3 | E156 results paper (156 words) | After validation complete |
| 4 | GitHub repo + Pages | Live demo |
| 5 | Protocol registration | GitHub release `v0.1.0-preregistration` |

**Paper target:** Clinical Trials (primary) / Research Synthesis Methods (secondary)

### AI Disclosure Statement (required in all publications)

> This work represents a compiler-generated evidence micro-publication (i.e., a structured, pipeline-based synthesis output). AI is used as a constrained synthesis engine operating on structured inputs and predefined rules, rather than as an autonomous author. Deterministic components of the pipeline, together with versioned, reproducible evidence capsules (TruthCert), are designed to support transparent and auditable outputs. All results and text were reviewed and verified by the author, who takes full responsibility for the content. The workflow operationalises key transparency and reporting principles consistent with CONSORT-AI/SPIRIT-AI, including explicit input specification, predefined schemas, logged human-AI interaction, and reproducible outputs.

## 9. Implementation Phases

| Phase | What | Depends on |
|-------|------|-----------|
| **1** | Math engine: RIS, spending functions, boundary computation, cumulative Z | — |
| **2** | UI: data entry + paste + examples + settings panel | — |
| **3** | TSA diagram (Canvas) + cumulative forest plot + RIS panel | Phase 1 |
| **4** | Report generation + export (PNG/CSV/text) | Phase 3 |
| **5** | Validation against rpact | Phase 1 |
| **6** | E156 papers + GitHub Pages + protocol registration | Phase 4 |

Phases 1 and 2 can run in parallel.

## 10. Dependencies

**None.** Single HTML file with zero external dependencies.

All maths implemented in vanilla JavaScript:
- Normal CDF: rational approximation (Abramowitz & Stegun 26.2.17)
- Normal quantile: Beasley-Springer-Moro algorithm
- REML tau²: Fisher scoring (same algorithm as MetaAudit, ported to JS)
- Recursive boundary integration: Gaussian quadrature with M=500 grid points
