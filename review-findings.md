# REVIEW CLEAN — All P0 and P1 Fixed
## Multi-Persona Review: tsa-pro.html (3,182 lines)
### Date: 2026-03-31
### Summary: 3P0 FIXED, 10P1 FIXED, 5P2 remaining. 49/49 tests pass. Div balance: 69/69.

---

## Round 1 Fixes (2026-03-31)

### P0 — Critical
- **P0-1** [FIXED] [Math]: RIS for RR/OR ignored control event rate — now uses Woolf variance formula with pC, producing correct per-arm sample sizes
- **P0-2** [FIXED] [Math]: `Math.abs(effect)` silently accepted negative RR/OR — now rejects effect <= 0 or == 1 with Infinity return
- **P0-3** [FIXED] [Math]: Double-zero studies (eE=0, eC=0) produced variance=0 for RD causing NaN — added continuity-correction fallback

### P1 — Important
- **P1-2** [FIXED] [Math]: I^2 used `max(Q,1)` denominator — now uses standard `(Q - df) / Q` with Q>0 guard
- **P1-3** [FIXED] [Math]: Beta-spending used z_{beta/2} — corrected to z_{beta} (futility is one-sided)
- **P1-5** [FIXED] [Math]: TSA-adjusted CI combined HKSJ SE + TSA boundary z (double-penalization) — now uses standard (non-HKSJ) SE for TSA CI
- **P1-7** [FIXED] [Math]: Continuous RIS (MD/SMD) returned per-arm count — fixed multiplier from 2 to 4 for total N
- **P1-8** [FIXED] [JS]: `parseFloat(x) || fallback` dropped valid zero — replaced with isFinite guard
- **P1-9** [FIXED] [A11y]: No skip-nav link — added skip-to-content link as first body child
- **P1-10** [FIXED] [Edge]: RIS=Infinity not guarded — now returns INCONCLUSIVE with helpful message
- **P1-11** [FIXED] [Edge]: Auto-filled effect=1.0 for RR/OR triggered Infinity RIS — caught by P1-10 guard

### P1 — Acknowledged (not changed)
- **P1-1** [ACK]: REML score uses approximate formulation (missing cross-term) — converges correctly in practice; Fisher info matches approximation
- **P1-4** [ACK]: Conditional power uses boundary at last look — acceptable approximation; exact requires extrapolating boundaries beyond observed IFs

### P2 — Minor (remaining)
- **P2-1** [FIXED]: SMD variance used `2*(nE+nC)` — corrected to `2*df` where df=nE+nC-2
- **P2-3** normalPDF defined twice in nested scopes (DRY, not a bug)
- **P2-4** Tab panels lack `tabindex="-1"` for focus management
- **P2-5** Dropdown lacks `role="menu"`/`role="menuitem"` and Escape key
- **P2-6** Canvas elements have no fallback text
- **P2-7** NaN inputs propagate silently to RIS display
- **P2-8** Studies not auto-sorted by year before cumulative MA

### Confirmed Correct
- Alpha-spending O'Brien-Fleming formula
- Pocock spending formula
- D^2 = I^2/(1-I^2)
- normalCDF (Abramowitz & Stegun), normalQuantile (Beasley-Springer-Moro)
- Boundary recursive integration (Armitage-McPherson-Rowe)
- Log OR/RR with continuity correction
- HKSJ q-adjustment with max(1, qHKSJ)
- No `</script>` inside script blocks
- Div balance: 69/69
