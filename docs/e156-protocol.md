# TSA Pro: Trial Sequential Analysis in the Browser — Protocol and Pre-Registration

**Authors:** Mahmood Ahmad
**Affiliation:** Royal Free Hospital, London, UK
**Email:** mahmood.ahmad2@nhs.net
**ORCID:** 0009-0003-7781-4478
**Date:** 2026-03-26

---

## Abstract (156 words)

**Background:** Sequential testing across interim analyses requires type I error control. Trial Sequential Analysis (TSA) adjusts significance thresholds by accumulated information fraction, preventing premature conclusions from underpowered cumulative evidence.

**Objective:** To describe TSA Pro, a browser-based tool implementing a complete alpha-spending framework without specialist software installation.

**Methods:** TSA Pro computes required information size, accumulates evidence using fixed-effect or random-effects meta-analysis, and applies sequential boundaries via four spending functions: O'Brien-Fleming, Pocock, Lan-DeMets (O'Brien-Fleming type), and Lan-DeMets (Pocock type). Futility boundaries use beta-spending. Boundary computation follows Armitage-McPherson-Rowe recursive numerical integration. Binary (risk ratio, odds ratio, risk difference) and continuous (mean difference, standardised mean difference) outcomes are supported.

**Validation Plan:** Cross-validation against rpact (R, v3.x) using three built-in datasets: Cochrane CD004338 (15 RCTs), Cochrane CD003311 (11 RCTs), Teo/ISIS-4 (16 RCTs). Tolerance: absolute Z-score difference below 0.05.

**Expected Output:** Open-source, browser-deployable TSA engine with Cochrane-compatible report text, reproducible examples, and a public validation table comparing boundaries against rpact reference values.

---

## References

- Protocol registration: https://github.com/mahmood726-cyber/tsa-pro/releases/tag/v0.1.0-preregistration
- Code repository: https://github.com/mahmood726-cyber/tsa-pro
- Live demo: https://mahmood726-cyber.github.io/tsa-pro/tsa-pro.html

---

## AI Disclosure Statement

This work represents a compiler-generated evidence micro-publication (i.e., a structured, pipeline-based synthesis output). AI is used as a constrained synthesis engine operating on structured inputs and predefined rules, rather than as an autonomous author. Deterministic components of the pipeline, together with versioned, reproducible evidence capsules (TruthCert), are designed to support transparent and auditable outputs. All results and text were reviewed and verified by the author, who takes full responsibility for the content. The workflow operationalises key transparency and reporting principles consistent with CONSORT-AI/SPIRIT-AI, including explicit input specification, predefined schemas, logged human-AI interaction, and reproducible outputs.
