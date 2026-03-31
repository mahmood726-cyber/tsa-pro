# TSA Pro: Trial Sequential Analysis in the Browser — Results

**Authors:** Mahmood Ahmad
**Affiliation:** Royal Free Hospital, London, UK
**Email:** mahmood.ahmad2@nhs.net
**ORCID:** 0009-0003-7781-4478
**Date:** 2026-03-26

---

## Abstract (156 words)

**What Was Built:** TSA Pro is a single-file browser application (3,157 lines, ~101 KB) implementing Trial Sequential Analysis for cumulative meta-analysis. It is the first browser-deployable TSA tool requiring no local installation or R environment.

**Features:** TSA Pro supports binary outcomes (risk ratio, odds ratio, risk difference) and continuous outcomes (mean difference, standardised mean difference). Four alpha-spending functions are implemented: O'Brien-Fleming, Pocock, Lan-DeMets (O'Brien-Fleming type), and Lan-DeMets (Pocock type). Futility bounds use beta-spending. The tool computes required information size, displays cumulative Z-statistics against sequential boundaries, and generates Cochrane-compatible reports.

**Built-in Examples:** Three datasets demonstrate distinct patterns: Cochrane CD004338 (15 RCTs, blood transfusion) illustrates early crossing; Cochrane CD003311 (11 RCTs, neonatal brain injury) shows crossing near required information size; Teo/ISIS-4 (16 RCTs, magnesium-in-MI) demonstrates a boundary never crossed.

**Internal Consistency:** TSA Pro implements published boundary algorithms. Internal checks confirm monotone information accumulation, correct alpha-spending allocation, and stable boundary computation across look counts. Cross-validation against rpact is planned.

---

## References

- Protocol registration: https://github.com/mahmood726-cyber/tsa-pro/releases/tag/v0.1.0-preregistration
- Code repository: https://github.com/mahmood726-cyber/tsa-pro
- Live demo: https://mahmood726-cyber.github.io/tsa-pro/tsa-pro.html

---

## AI Disclosure Statement

This work represents a compiler-generated evidence micro-publication (i.e., a structured, pipeline-based synthesis output). AI is used as a constrained synthesis engine operating on structured inputs and predefined rules, rather than as an autonomous author. Deterministic components of the pipeline, together with versioned, reproducible evidence capsules (TruthCert), are designed to support transparent and auditable outputs. All results and text were reviewed and verified by the author, who takes full responsibility for the content. The workflow operationalises key transparency and reporting principles consistent with CONSORT-AI/SPIRIT-AI, including explicit input specification, predefined schemas, logged human-AI interaction, and reproducible outputs.
