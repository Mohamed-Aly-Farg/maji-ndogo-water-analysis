# Water Source Distribution & Infrastructure Action Plan
**Project:** Maji Ndogo — Water Access Monitoring System
**Date:** 2026-08-16
**Author:** [ Mohamed Aly Farg ]

---

## Business Questions Answered

1. What is the distribution of water source types (river, well, shared_tap,
   tap_in_home, tap_in_home_broken) by province and by town, weighted by
   population served?
2. What percentage of home taps are broken, per town?
3. What specific infrastructure intervention is needed at each flagged
   water source (contamination, broken taps, overcrowded shared taps)?

---

## Data Pipeline (in execution order)

| Step | Object | Purpose |
|---|---|---|
| 1 | `combined_analysis_table` (VIEW) | Deduplicated base table: one row per water source, joined with location |
| 2 | Province-level pivot query | % of population served by each source type, per province |
| 3 | `town_aggregated_water_access` (TEMP TABLE) | Same pivot, at town granularity |
| 4 | `Pct_broken_taps` query | % of home taps broken, per town |
| 5 | Final Action Plan query | Row-level improvement recommendation per water source |

---

## Key Design Decisions

### 1. Deduplication strategy: `WHERE visit_count = 1`
`visits.visit_count` is a **sequential visit number per source**, not a
total count. Filtering on `= 1` selects exactly one row per `source_id`,
which is required before any `SUM(number_of_people_served)` — otherwise
population figures would be duplicated for sources visited more than once.

### 2. `LEFT JOIN well_pollution` — verified safe, not assumed safe
Initially flagged as a duplication risk (a source could theoretically have
multiple pollution tests over time via the `date` column). Verified with:
```sql
SELECT source_id, COUNT(*) FROM well_pollution 
GROUP BY source_id HAVING COUNT(*) > 1;
```
Result: 0 rows. Confirmed each source has exactly one pollution test
record in this dataset, so the LEFT JOIN does not cause row explosion.
**This must be re-verified if the dataset is refreshed/expanded.**

### 3. Congestion metric: `time_in_queue`, not `visit_count`
Originally attempted to flag overcrowded `shared_tap` sources using
`visit_count >= 30`. This was logically impossible — the query already
filters `visit_count = 1`, so a value of 30 in the same row could never
occur. Corrected to use `time_in_queue >= 30` (minutes), which is a
direct, real-time measure of congestion at that source.

### 4. Population-weighted % vs. simple count %
The province/town pivot tables weight source-type prevalence by
`number_of_people_served`, not by raw source count. This answers "what
% of *people* rely on each source type" rather than "what % of
*sources* are of each type" — a more meaningful metric for prioritizing
infrastructure investment by human impact.

### 5. Priority order in the Action Plan `CASE` statement
Recommendations are evaluated in this order: Chemical contamination →
Biological contamination → River source → Overcrowded shared tap →
Broken home tap. This reflects an assumed severity ranking (chemical
contamination treated as most urgent). **This ordering is a business
judgment call and should be validated with stakeholders**, not treated
as objectively "correct."



