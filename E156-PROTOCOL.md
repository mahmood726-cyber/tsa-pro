# E156 Protocol — `tsa-pro`

This repository is the source code and dashboard backing an E156 micro-paper on the [E156 Student Board](https://mahmood726-cyber.github.io/e156/students.html).

---

## `[289]` TSA Pro: World-First Browser-Based Trial Sequential Analysis with Alpha-Spending and Futility Boundaries

**Type:** methods  |  ESTIMAND: Cumulative Z-statistic relative to monitoring boundaries  
**Data:** Chronologically ordered meta-analysis data for sequential monitoring

### 156-word body

Can trial sequential analysis with alpha-spending boundaries and futility rules be performed entirely in a browser to monitor accumulating meta-analytic evidence without requiring specialised desktop software? We developed TSA Pro as a 3,181-line single-file application implementing O Brien-Fleming, Pocock, and Haybittle-Peto alpha-spending functions with both binding and non-binding futility boundaries for cumulative meta-analysis monitoring. The tool computes required information size, constructs monitoring boundaries, plots cumulative Z-statistics against boundaries, and classifies the current evidence state as crossed, trending, in monitoring zone, or futile. Boundary calculations matched the TSA software and R rpact package within three decimal places across all validated scenarios including DAPA-HF, EMPEROR-Reduced, and corticosteroid exemplars. Futility boundary implementation correctly identified three simulated scenarios where early evidence suggested the treatment effect was unlikely to reach significance even with maximum planned information. Browser-based trial sequential analysis could democratise access to sequential monitoring methods for living meta-analysis teams without requiring software installation. The tool implements group-sequential boundaries and does not yet support adaptive information fraction designs or Bayesian monitoring approaches.

### Submission metadata

```
Corresponding author: Mahmood Ahmad <mahmood.ahmad2@nhs.net>
ORCID: 0000-0001-9107-3704
Affiliation: Tahir Heart Institute, Rabwah, Pakistan

Links:
  Code:      https://github.com/mahmood726-cyber/tsa-pro
  Protocol:  https://github.com/mahmood726-cyber/tsa-pro/blob/main/E156-PROTOCOL.md
  Dashboard: https://mahmood726-cyber.github.io/tsa/

References (topic pack: trial sequential analysis (TSA)):
  1. Wetterslev J, Thorlund K, Brok J, Gluud C. 2008. Trial sequential analysis may establish when firm evidence is reached in cumulative meta-analysis. J Clin Epidemiol. 61(1):64-75. doi:10.1016/j.jclinepi.2007.03.013
  2. Pogue JM, Yusuf S. 1997. Cumulating evidence from randomized trials: utilizing sequential monitoring boundaries for cumulative meta-analysis. Control Clin Trials. 18(6):580-593. doi:10.1016/S0197-2456(97)00051-2

Data availability: No patient-level data used. Analysis derived exclusively
  from publicly available aggregate records. All source identifiers are in
  the protocol document linked above.

Ethics: Not required. Study uses only publicly available aggregate data; no
  human participants; no patient-identifiable information; no individual-
  participant data. No institutional review board approval sought or required
  under standard research-ethics guidelines for secondary methodological
  research on published literature.

Funding: None.

Competing interests: MA serves on the editorial board of Synthēsis (the
  target journal); MA had no role in editorial decisions on this
  manuscript, which was handled by an independent editor of the journal.

Author contributions (CRediT):
  [STUDENT REWRITER, first author] — Writing – original draft, Writing –
    review & editing, Validation.
  [SUPERVISING FACULTY, last/senior author] — Supervision, Validation,
    Writing – review & editing.
  Mahmood Ahmad (middle author, NOT first or last) — Conceptualization,
    Methodology, Software, Data curation, Formal analysis, Resources.

AI disclosure: Computational tooling (including AI-assisted coding via
  Claude Code [Anthropic]) was used to develop analysis scripts and assist
  with data extraction. The final manuscript was human-written, reviewed,
  and approved by the author; the submitted text is not AI-generated. All
  quantitative claims were verified against source data; cross-validation
  was performed where applicable. The author retains full responsibility for
  the final content.

Preprint: Not preprinted.

Reporting checklist: PRISMA 2020 (methods-paper variant — reports on review corpus).

Target journal: ◆ Synthēsis (https://www.synthesis-medicine.org/index.php/journal)
  Section: Methods Note — submit the 156-word E156 body verbatim as the main text.
  The journal caps main text at ≤400 words; E156's 156-word, 7-sentence
  contract sits well inside that ceiling. Do NOT pad to 400 — the
  micro-paper length is the point of the format.

Manuscript license: CC-BY-4.0.
Code license: MIT.

SUBMITTED: [ ]
```


---

_Auto-generated from the workbook by `C:/E156/scripts/create_missing_protocols.py`. If something is wrong, edit `rewrite-workbook.txt` and re-run the script — it will overwrite this file via the GitHub API._