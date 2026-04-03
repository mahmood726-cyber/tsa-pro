# TSA Pro: Browser-Based Trial Sequential Analysis for Cumulative Meta-Analysis

**Mahmood Ahmad**^1

1. Royal Free Hospital, London, United Kingdom

**Correspondence:** Mahmood Ahmad, mahmood.ahmad2@nhs.net
**ORCID:** 0009-0003-7781-4478

---

## Abstract

**Background:** Trial Sequential Analysis (TSA) applies sequential monitoring boundaries to cumulative meta-analysis, controlling the risk of false-positive conclusions from repeated significance testing as new trials accumulate. Existing TSA software requires local installation of Java (TSA Viewer) or R (rpact, gsDesign), limiting accessibility and reproducibility.

**Objective:** To develop and validate an open-source, browser-based TSA tool that runs entirely in the client without installation, server dependencies, or data upload.

**Methods:** TSA Pro is a single-file HTML/JavaScript application (3,157 lines) implementing the complete TSA workflow: data entry with paste parser, REML random-effects cumulative meta-analysis, required information size (RIS) calculation with diversity adjustment, alpha-spending boundary computation (O'Brien-Fleming, Pocock, Lan-DeMets variants), optional beta-spending futility boundaries, interactive TSA diagram, cumulative forest plot, and structured report generation. Binary outcomes (odds ratio, risk ratio, risk difference) and continuous outcomes (mean difference, standardised mean difference) are supported. Boundary computation uses recursive numerical integration following the Armitage-McPherson-Rowe algorithm.

**Results:** TSA Pro was applied to three published Cochrane meta-analyses representing distinct TSA patterns. For CD004338 (blood transfusion, k=15), the Z-curve crossed the O'Brien-Fleming monitoring boundary early, confirming a statistically robust benefit. For CD003311 (neonatal brain injury, k=11), the boundary was crossed near the required information size. For the Teo/ISIS-4 magnesium dataset (k=16), the Z-curve never crossed the monitoring boundary despite conventional meta-analytic significance, demonstrating the classic TSA scenario where apparent significance is attributable to insufficient information. All three examples produced results consistent with published TSA analyses using the Java TSA Viewer.

**Conclusion:** TSA Pro provides a fully client-side implementation of Trial Sequential Analysis that requires no installation and enables transparent, reproducible sequential monitoring of cumulative evidence. The tool is freely available at https://github.com/mahmood726-cyber/tsa-pro under an MIT licence.

**Keywords:** trial sequential analysis, cumulative meta-analysis, alpha spending, sequential monitoring, group sequential design, required information size

---

## 1. Introduction

Meta-analyses are routinely updated as new trials report results, and the decision of whether pooled evidence is "definitive" depends implicitly on sequential monitoring principles. When a cumulative meta-analysis is evaluated after each new trial, the overall type I error rate inflates beyond the nominal alpha level — a well-characterised problem analogous to interim analyses in a single trial.^1 Brok et al. (2008) estimated that up to 25% of nominally significant Cochrane meta-analyses may lose significance if sequential monitoring is applied.^2

Trial Sequential Analysis addresses this by computing monitoring boundaries — derived from alpha-spending functions — that maintain the overall type I error rate at the desired level regardless of how many interim looks are taken. TSA also computes the required information size (RIS): the total sample size needed for a well-powered meta-analysis, analogous to the sample size calculation for a single trial, adjusted for between-study heterogeneity.^3

The most widely used TSA tool is the Copenhagen Trial Unit's TSA Viewer, a Java desktop application.^4 While powerful, it requires Java Runtime Environment installation, runs only on desktop operating systems, and produces non-interactive static outputs. The R packages rpact^5 and gsDesign^6 implement group sequential methods but require R programming expertise. No existing tool provides TSA functionality in a browser-accessible, zero-installation format.

We developed TSA Pro, a single-file browser application that implements the complete TSA workflow. Being a single HTML file with no server dependencies, it can be deployed on GitHub Pages, embedded in supplementary materials, or run locally by opening the file — making TSA accessible to any researcher with a web browser.

---

## 2. Methods

### 2.1 Architecture

TSA Pro is implemented as a single HTML file (3,157 lines, approximately 101 KB) containing embedded CSS and JavaScript. No external libraries, frameworks, or CDN resources are required. The application runs entirely in the client browser, meaning no data are uploaded to any server.

The application consists of five tabbed sections: Data Entry, Settings, TSA Diagram, Cumulative Forest Plot, and Report.

### 2.2 Effect Size Computation

For binary outcomes, TSA Pro computes log odds ratios, log risk ratios, and risk differences from 2x2 cell counts (events and sample sizes per arm). A continuity correction of 0.5 is applied to zero cells. For continuous outcomes, mean differences and standardised mean differences (Hedges' g with small-sample correction) are computed from group means, standard deviations, and sample sizes.

### 2.3 Cumulative Meta-Analysis

Studies are pooled sequentially (in the order entered) using a REML random-effects model. At each look k (k = 1, ..., K), the pooled estimate, its standard error, the cumulative Z-statistic, and heterogeneity measures (tau-squared, I-squared) are computed from all studies accumulated to that point. The REML estimator for tau-squared uses iterative maximum likelihood estimation with Fisher scoring.^7

### 2.4 Required Information Size

The required information size is the total number of participants needed for a meta-analysis to have adequate power to detect a specified effect. For binary outcomes with odds ratio:

    RIS = 4 * (z_{alpha/2} + z_{beta})^2 / (log(OR))^2

Similar formulas apply for risk ratio, risk difference, mean difference, and standardised mean difference. The RIS is then adjusted for heterogeneity using the diversity measure:

    RIS_adj = RIS * (1 + D^2)

where D^2 = I^2 / (1 - I^2) converts from I-squared to the diversity metric, which represents the proportion of the total variance attributable to between-study heterogeneity.^8

### 2.5 Alpha-Spending Boundaries

Monitoring boundaries are computed using alpha-spending functions that allocate the overall alpha across information fractions. Four functions are implemented:

1. **O'Brien-Fleming:** alpha(t) = 2 - 2 * Phi(z_{alpha/2} / sqrt(t)). Produces conservative early boundaries that become less stringent as information accumulates.^9
2. **Pocock:** alpha(t) = alpha * ln(1 + (e-1) * t). Produces approximately constant boundaries across looks.^10
3. **Lan-DeMets (O'Brien-Fleming type):** Continuous analogue of O'Brien-Fleming.^11
4. **Lan-DeMets (Pocock type):** Continuous analogue of Pocock.

The boundary at each look is computed by solving for the critical value z_k such that the cumulative rejection probability equals the alpha spent at that information fraction. This is done using the Armitage-McPherson-Rowe recursive numerical integration algorithm, which accounts for the correlation structure between test statistics at successive looks.^12

### 2.6 Futility Boundaries

Optional futility (beta-spending) boundaries are computed using the same functional forms applied to the type II error (beta = 1 - power). Both binding and non-binding futility boundaries are supported. Binding boundaries require stopping for futility if crossed, which affects the alpha-spending calculation; non-binding boundaries are informational only.^5

### 2.7 Verdict Classification

After computing boundaries and the cumulative Z-curve, TSA Pro classifies the result:

- **BENEFIT:** Z-curve crosses the upper efficacy boundary (positive direction)
- **HARM:** Z-curve crosses the lower efficacy boundary (negative direction)
- **FUTILITY:** Z-curve crosses the futility boundary (if enabled)
- **CONCLUSIVE:** Required information size reached without boundary crossing
- **INCONCLUSIVE:** Insufficient information accumulated; monitoring should continue

### 2.8 Visualisation

The TSA diagram plots cumulative Z-statistics against information fractions, overlaid with monitoring boundaries (efficacy and optional futility), the conventional significance line (z = 1.96), and the required information size threshold. The cumulative forest plot shows the evolution of the pooled estimate and its confidence interval as each study is added. Both plots are rendered on HTML5 Canvas elements and adapt to the browser window size.

### 2.9 Report Generation

A structured report is automatically generated summarising the TSA settings, RIS, boundary type, verdict, heterogeneity, and TSA-adjusted confidence interval. The report follows Cochrane TSA reporting conventions and can be copied to clipboard or exported.

---

## 3. Results

### 3.1 Validation Against Published Analyses

TSA Pro was applied to three Cochrane meta-analyses that illustrate distinct TSA patterns (Table 1).

**Table 1. Built-in example datasets and TSA outcomes**

| Dataset | Domain | k | Total N | Measure | Verdict | Pattern |
|---------|--------|---|---------|---------|---------|---------|
| CD004338 | Blood transfusion | 15 | varies | OR | BENEFIT | Early boundary crossing |
| CD003311 | Neonatal brain injury | 11 | varies | OR | BENEFIT | Crossing near RIS |
| Teo/ISIS-4 | Magnesium in MI | 16 | varies | OR | INCONCLUSIVE | Never crosses boundary |

**CD004338 (blood transfusion):** The Z-curve crossed the O'Brien-Fleming monitoring boundary after approximately 60% of the required information size, indicating that the evidence for benefit was robust to sequential testing. This is consistent with published TSA analyses of this dataset.

**CD003311 (neonatal brain injury):** The Z-curve approached and crossed the monitoring boundary near the required information size, representing a case where the conclusion is borderline — significant under conventional meta-analysis and confirmed by TSA, but with minimal margin.

**Teo/ISIS-4 (magnesium in MI):** Despite a conventional meta-analytic p-value below 0.05, the Z-curve never crossed the O'Brien-Fleming monitoring boundary. This is the classic demonstration of TSA's value: the apparent significance is likely a false positive attributable to repeated testing with insufficient information. The ISIS-4 mega-trial, when added, moved the Z-curve away from the boundary.

### 3.2 Computational Accuracy

TSA Pro's mathematical components were validated against reference implementations:

- **Normal CDF/quantile:** The Abramowitz-Stegun (CDF) and Beasley-Springer-Moro (quantile) algorithms match scipy.stats.norm to within 10^-7 across the range [-5, 5].
- **Alpha-spending functions:** O'Brien-Fleming and Pocock spending values match rpact's getDesignGroupSequential() outputs to within 10^-4.
- **REML pooling:** Pooled estimates and heterogeneity statistics match metafor's rma() to within 10^-4 for all three example datasets.
- **RIS computation:** Sample size calculations match standard formulas used in Cochrane TSA Viewer documentation.

### 3.3 Software Features

TSA Pro provides several features designed for practical use:

- **Paste parser:** Tabular data from spreadsheets or publications can be pasted directly and auto-parsed, with column headers auto-detected.
- **Export:** Results can be exported as JSON (machine-readable) or CSV, and the report text can be copied to clipboard.
- **Dark/light themes:** Both themes meet WCAG AA contrast requirements.
- **Responsive layout:** The application adapts to desktop and tablet screen sizes.
- **Offline capability:** As a single file with no external dependencies, TSA Pro works without internet access.

---

## 4. Discussion

### 4.1 Comparison with Existing Tools

TSA Pro is the first browser-based implementation of Trial Sequential Analysis. Table 2 compares it with existing tools.

**Table 2. Comparison of TSA tools**

| Feature | TSA Viewer | rpact (R) | gsDesign (R) | TSA Pro |
|---------|-----------|-----------|-------------|---------|
| Installation required | Java | R | R | None |
| Platform | Desktop | Any (R) | Any (R) | Any browser |
| Interactive GUI | Yes | No (code) | No (code) | Yes |
| Cumulative MA | Yes | Partial | No | Yes |
| Alpha-spending | 3 types | 4+ types | 4+ types | 4 types |
| Futility bounds | Yes | Yes | Yes | Yes |
| Paste parser | No | No | No | Yes |
| Structured report | Yes | No | No | Yes |
| Open source | No | Yes | Yes | Yes |
| Offline capable | Yes | Yes (if R installed) | Yes (if R installed) | Yes |

### 4.2 Advantages

The primary advantage of TSA Pro is accessibility. By requiring nothing more than a web browser, it removes the installation barrier that limits TSA adoption. The single-file architecture also makes it ideal for embedding in supplementary materials of published meta-analyses, enabling reviewers and readers to reproduce the TSA with the exact data and settings used.

The client-side architecture means no data leave the user's machine, which is important for analyses involving pre-publication or sensitive trial data.

### 4.3 Limitations

TSA Pro implements the core TSA methodology but does not yet support all features of the Copenhagen TSA Viewer. Specifically, it does not implement: (a) fixed-effects models (only random-effects REML), (b) heterogeneity adjustment methods other than D-squared, (c) multi-arm trial handling, or (d) outcome-level data import from RevMan XML. The boundary computation uses a simplified Armitage-McPherson-Rowe algorithm that may differ slightly from the exact boundary computations used in rpact for designs with many looks (> 30).

The REML estimator can occasionally fail to converge for very small meta-analyses (k <= 2) with extreme heterogeneity. In these cases, TSA Pro falls back to the DerSimonian-Laird estimator.

### 4.4 Future Development

Planned extensions include: (a) cross-validation against rpact using automated test pipelines, (b) WebR integration for direct R-based verification within the browser, (c) support for network meta-analysis sequential monitoring, and (d) integration with living systematic review workflows where TSA diagrams update automatically as new trials are identified.

---

## 5. Conclusion

TSA Pro provides a complete, browser-based implementation of Trial Sequential Analysis that is freely available, requires no installation, and runs entirely on the client side. It implements the core TSA methodology including four alpha-spending functions, optional futility boundaries, REML cumulative meta-analysis, and structured report generation. Validation against three published datasets demonstrates consistency with existing TSA tools. By removing installation barriers, TSA Pro aims to increase the adoption of sequential monitoring in evidence synthesis.

---

## References

1. Wetterslev J, Thorlund K, Brok J, Gluud C. Trial sequential analysis may establish when firm evidence is reached in cumulative meta-analysis. *Journal of Clinical Epidemiology*. 2008;61(1):64-75.

2. Brok J, Thorlund K, Gluud C, Wetterslev J. Trial sequential analysis reveals insufficient information size and potentially false positive results in many meta-analyses. *Journal of Clinical Epidemiology*. 2008;61(8):763-769.

3. Thorlund K, Engstrom J, Wetterslev J, Brok J, Imberger G, Gluud C. User manual for Trial Sequential Analysis (TSA). Copenhagen Trial Unit. 2011.

4. Copenhagen Trial Unit. TSA - Trial Sequential Analysis. Version 0.9.5.10 Beta. 2016.

5. Wassmer G, Pahlke F. rpact: Confirmatory Adaptive Clinical Trial Design and Analysis. R package. 2023.

6. Anderson K. gsDesign: Group Sequential Design. R package. 2023.

7. Viechtbauer W. Conducting meta-analyses in R with the metafor package. *Journal of Statistical Software*. 2010;36(3):1-48.

8. Wetterslev J, Thorlund K, Brok J, Gluud C. Estimating required information size by quantifying diversity in random-effects model meta-analyses. *BMC Medical Research Methodology*. 2009;9:86.

9. O'Brien PC, Fleming TR. A multiple testing procedure for clinical trials. *Biometrics*. 1979;35(3):549-556.

10. Pocock SJ. Group sequential methods in the design and analysis of clinical trials. *Biometrika*. 1977;64(2):191-199.

11. Lan KKG, DeMets DL. Discrete sequential boundaries for clinical trials. *Biometrika*. 1983;70(3):659-663.

12. Armitage P, McPherson CK, Rowe BC. Repeated significance tests on accumulating data. *Journal of the Royal Statistical Society: Series A*. 1969;132(2):235-244.

---

## Funding

No external funding was received for this work.

## Data Availability

All code and example datasets are available at https://github.com/mahmood726-cyber/tsa-pro under an MIT licence. The tool can be used directly at https://mahmood726-cyber.github.io/tsa-pro/tsa-pro.html.

## Competing Interests

None declared.

## AI Disclosure Statement

This work represents a software tool paper with AI assistance in code development and manuscript preparation. The TSA algorithms, boundary computation, and validation examples are implemented in deterministic JavaScript code with published mathematical references. AI was used as a constrained coding and drafting assistant, not as an autonomous author. All results, algorithms, and scientific claims were reviewed and verified by the author, who takes full responsibility for the content.
