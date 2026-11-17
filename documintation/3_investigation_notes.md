# Investigation: Suspect Employee Discrepancies
**Project:** Maji Ndogo — Water Access Monitoring System

---

## Business Question
Which field surveyors show a statistically unusual pattern of disagreement
between their subjective quality score and the independent auditor's
`true_water_source_score` — potentially indicating negligence or a data
integrity issue (e.g. bribery)?

---

## Methodology

1. Joined `auditor_report` with `visits`, `water_quality`, and `employee`
   on `location_id` / `record_id` / `assigned_employee_id`.
2. Restricted comparison to `visit_count = 1` (first visit) — assumes this
   best reflects the source's condition at the time the auditor visited.
3. Computed **mismatch count** per employee (`n_of_mis`) — number of
   visits where `auditor_score <> subjective_score`.
4. Flagged a `suspect_list` where `n_of_mis >= AVG(n_of_mis)` across all
   employees.
5. Cross-referenced `suspect_list` against auditor `statements` containing
   direct textual evidence: `cash`, `bribe`, `money`, `paid`, `gift`.

---

## Findings

- **19 records** flagged with direct textual evidence of a cash-related
  transaction combined with descriptions of an official's arrogance /
  detachment.
- Language is highly consistent across records ("cash changing hands",
  "cash transactions", "cash passing discreetly"), suggesting a real
  and recurring pattern rather than random noise.
- [ ] **Pending:** verify whether any of the 19 records are exact
  duplicates (same `location_id` + `employee_name` + identical
  statement text) before reporting a final headcount.

---

## ⚠️ Important Disclaimer

**This is a screening tool for further human investigation — it is NOT
proof of misconduct.** Statements may reference these words in unrelated,
innocent contexts. All flagged cases require manual review of full
context before any administrative or disciplinary action is taken.

