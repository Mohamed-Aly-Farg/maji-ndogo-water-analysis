# 1_Exploratory Data Analysis Log
**Project:** Maji Ndogo — Water Access Monitoring System

---

## Purpose
Initial exploration phase, conducted before the auditor investigation and
the infrastructure action plan. Goal: understand table shapes, validate
data reliability, and surface early business insights before committing
to deeper analysis.

---

## 1. Employee & Location Overview

```sql
SELECT town_name, COUNT(employee_name)
FROM employee
GROUP BY town_name;
```
- 56 employees total, distributed across towns (Rural = 29 is the
  largest group by residence).

```sql
SELECT province_name, COUNT(*) as Recorded_Locations
FROM location
GROUP BY province_name
ORDER BY COUNT(*) DESC;
```
- Kilimani has the most recorded locations (9,510), Hawassa the fewest
  (6,030).

```sql
WITH locations_per_province AS (
    SELECT
        province_name,
        COUNT(DISTINCT location_id) AS total_locations
    FROM location
    GROUP BY province_name
),
employees_per_province AS (
    SELECT
        province_name,
        COUNT(DISTINCT assigned_employee_id) AS total_employees
    FROM employee
    GROUP BY province_name
)

SELECT
    l.province_name AS province,
    l.total_locations AS locations,
    e.total_employees AS employees,
    ROUND(l.total_locations / NULLIF(e.total_employees, 0),0) AS locations_per_employee
FROM locations_per_province l
JOIN employees_per_province e
    ON l.province_name = e.province_name
ORDER BY locations_per_employee DESC;
```
- Amanzi has the highest ratio of documented locations per employee (approximately 993), followed by Sokoto (913).

### 💡 Insight: Data Reliability
Every province and town has a substantial number of documented water
sources (see province/town breakdown). This broad, consistent coverage
supports confidence that the dataset is reliable enough to base
decisions on — this is a communicable data-integrity finding in its
own right, not just a sanity check.

---

## 2. Top 3 Field Surveyors (by visit count)

```sql
WITH ranked_visits AS (
    SELECT
        assigned_employee_id,
        COUNT(*) AS total_visits,
        RANK() OVER (ORDER BY COUNT(*) DESC) AS rank_visit
    FROM visits
    GROUP BY assigned_employee_id
)
SELECT e.employee_name, e.email, r.total_visits, r.rank_visit
FROM employee e
JOIN ranked_visits r ON e.assigned_employee_id = r.assigned_employee_id
WHERE r.rank_visit <= 3;
```

**Design decision:** Used `RANK()` instead of `LIMIT 3` to fairly handle
potential ties — an employee tied for 3rd place should not be excluded
just because of arbitrary row ordering. `COUNT(*)` used instead of
`COUNT(visit_count)` to avoid excluding rows with a NULL `visit_count`
value.

**Result:**

| name         | rank  |
|--------------|-------|
| Bello Azibo  | 1     |
| Pili Zola    | 2     |
| Rudo Imani   | 3     |

---

## 3. Water Source Overview

```sql
SELECT SUM(number_of_people_served) FROM water_source;
```
- **27,628,140** people surveyed in total.

```sql
SELECT type_of_water_source, COUNT(*)
FROM water_source
GROUP BY type_of_water_source;
```
| Source type | Count |
|---|---|
| well | 17,383 |
| tap_in_home | 7,265 |
| tap_in_home_broken | 5,856 |
| shared_tap | 5,767 |
| river | 3,379 |

```sql
SELECT type_of_water_source, ROUND(AVG(number_of_people_served), 0)
FROM water_source
GROUP BY type_of_water_source;
```
- `shared_tap` serves by far the most people per source on average
  (2,071), consistent with it being a shared community resource.

### % of population served by source type
```sql
SELECT 
    type_of_water_source,
    SUM(number_of_people_served) AS total_people_served,
    SUM(SUM(number_of_people_served)) OVER () AS grand_total,
    ROUND(SUM(number_of_people_served) * 100.0 
        / SUM(SUM(number_of_people_served)) OVER (), 2) AS pct_of_total
FROM water_source
GROUP BY type_of_water_source;
```
| Source type | People served | % of total |
|---|---|---|
| shared_tap | 11,945,272 | 43.24% |
| well | 4,841,724 | 17.52% |
| tap_in_home | 4,678,880 | 16.94% |
| tap_in_home_broken | 3,799,720 | 13.75% |
| river | 2,362,544 | 8.55% |

**Note:**

1 - `shared_tap` alone accounts for the largest share of the
population — any infrastructure improvement to shared taps has
outsized reach compared to other source types.

2 - Improving broken home taps may reduce dependence on shared taps and potentially ease pressure on shared water sources. However, this relationship requires further validation.

---

## 4. Infrastructure Prioritization (by people affected)

```sql
WITH total_people_per_source AS (
    SELECT 
        type_of_water_source,
        SUM(number_of_people_served) AS people_per_source,
        SUM(SUM(number_of_people_served)) OVER () AS total_people,
        (SUM(number_of_people_served) / SUM(SUM(number_of_people_served)) OVER ()) * 100 AS percentage_share
    FROM water_source
    WHERE type_of_water_source != 'tap_in_home'
    GROUP BY type_of_water_source
)
SELECT
    type_of_water_source, people_per_source, total_people, percentage_share,
    RANK() OVER (ORDER BY percentage_share DESC) AS source_rank
FROM total_people_per_source;
```

**Design decision:** 

Excluded `tap_in_home` (already working, no intervention needed) while deliberately keeping `tap_in_home_broken` in
the ranking, since broken home taps are a prime candidate for repair.

**Result:** 

`shared_tap` ranks #1 by people affected (52.1%), followed
by `well` (21.1%), `tap_in_home_broken` (16.6%), and `river` (10.3%) —
a data-driven starting point for prioritizing infrastructure investment.

### Within-type prioritization
```sql
SELECT
    source_id, type_of_water_source, number_of_people_served,
    ROW_NUMBER() OVER (PARTITION BY type_of_water_source 
        ORDER BY number_of_people_served DESC) AS priority_rank
FROM water_source
WHERE type_of_water_source IN ('river','shared_tap','well');
```
Ranks individual sources within each type by population served — used
to identify which *specific* sources should be visited/fixed first
within a given category.

---

## 5. Queue Time Analysis

```sql
SELECT
    DAYNAME(time_of_record) AS day_of_week,
    COUNT(*) AS total_visits,
    ROUND(AVG(time_in_queue), 0) AS avg_queue_time
FROM visits
GROUP BY DAYNAME(time_of_record);
```
- Saturday has a substantially higher average queue time than the other days.


```sql
SELECT MIN(time_of_record), MAX(time_of_record), 
       DATEDIFF(MAX(time_of_record), MIN(time_of_record))
FROM visits;
```
- Survey spans approximately **924 days** (~2.5 years).

### ⚠️ Critical data decision: how to treat `time_in_queue = 0`

```sql
SELECT 
    HOUR(time_of_record) AS hour_of_day,
    COUNT(*) AS total_visits,
    SUM(CASE WHEN time_in_queue = 0 THEN 1 ELSE 0 END) AS zero_queue_count,
    ROUND(SUM(CASE WHEN time_in_queue = 0 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 1) AS pct_zero
FROM visits
GROUP BY HOUR(time_of_record)
ORDER BY hour_of_day;
```
- Hours 6–8 and 17–19 (peak commute hours): **0% zero-queue visits**
- Hours 9–16 (midday): **~58% zero-queue visits**

This is not random noise — it reflects a real daily rhythm (people
fetch water before/after work, so midday is genuinely less crowded).
**Decision: `NULLIF` was removed from all queue-time averages.** Zeros
are legitimate observations and must be included, not filtered out.

### Final queries (post-correction, no NULLIF)

```sql
-- Overall average
SELECT ROUND(AVG(time_in_queue), 0) AS avg_time_queue FROM visits;

-- By day of week
SELECT DAYNAME(time_of_record) AS day_of_week,
       ROUND(AVG(time_in_queue), 0) AS avg_time_queue
FROM visits
GROUP BY DAYNAME(time_of_record);

-- By hour of day
SELECT HOUR(time_of_record) AS hour_of_day,
       ROUND(AVG(time_in_queue), 0) AS avg_time_queue
FROM visits
GROUP BY HOUR(time_of_record)
ORDER BY hour_of_day;

-- Pivot: hour x day of week
SELECT 
    HOUR(time_of_record) AS hour_of_day,
    ROUND(AVG(CASE WHEN DAYNAME(time_of_record)='Sunday'    THEN time_in_queue END),0) AS Sunday,
    ROUND(AVG(CASE WHEN DAYNAME(time_of_record)='Monday'    THEN time_in_queue END),0) AS Monday,
    ROUND(AVG(CASE WHEN DAYNAME(time_of_record)='Tuesday'   THEN time_in_queue END),0) AS Tuesday,
    ROUND(AVG(CASE WHEN DAYNAME(time_of_record)='Wednesday' THEN time_in_queue END),0) AS Wednesday,
    ROUND(AVG(CASE WHEN DAYNAME(time_of_record)='Thursday'  THEN time_in_queue END),0) AS Thursday,
    ROUND(AVG(CASE WHEN DAYNAME(time_of_record)='Friday'    THEN time_in_queue END),0) AS Friday,
    ROUND(AVG(CASE WHEN DAYNAME(time_of_record)='Saturday'  THEN time_in_queue END),0) AS Saturday
FROM visits
GROUP BY HOUR(time_of_record)
ORDER BY hour_of_day;
```

### 🎯 Key Insight: Weekday vs. Weekend congestion pattern differs structurally

- **Weekdays (Mon–Fri):** Two sharp peaks — early morning (6–8) and
  evening (17–19), roughly 100–250 min queue time, with a clear midday
  lull (9–16, roughly 30–90 min). Reflects people fetching water
  around work schedules.
- **Weekend (Sat–Sun):** Queue times stay elevated and roughly
  consistent throughout the day, with no comparable midday dip.
  Reflects the absence of a fixed work schedule structuring demand.

### Business implication
Staffing/resource allocation should differ by day type:
- **Weekdays:** concentrate additional resources during the two peak
  windows only; midday has slack capacity.
- **Weekends:** resources need to be spread evenly across the day, since
  there is no low-demand window to reallocate from.