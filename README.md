# Maji Ndogo: Water Access Analysis

SQL analysis and a 5-page Power BI dashboard on water access across 5 provinces,
built to answer one question: **where are people most exposed to unsafe or unreliable
water, and where should repairs start?**

> Dashboard preview: add screenshots of the 5 pages here (`/images/cover.png`, ...)

---

## The problem

Maji Ndogo is surveying its water sources. The raw data covers **27.6 million people**
served by **39,650 water sources** (wells, rivers, shared taps, home taps) across
**5 provinces**. Decision-makers need to know which problems are biggest, where they
are, and what to fix first.

I framed it as three questions:

1. **Congestion:** where do people wait the longest?
2. **Pollution:** which water is not safe to drink?
3. **Broken taps:** where is infrastructure failing?

---

## Key findings

| Area | Finding |
|---|---|
| Access | Shared taps serve **43%** of the population, the largest share of any source type |
| Congestion | Shared taps average about **96 minutes** of queue, versus about **17** for rivers. All five provinces sit between 92 and 103 minutes on shared taps |
| Pollution | **72%** of wells (12,467 of 17,383) are contaminated, mostly chemical. Hawassa is highest at 85%, and even the lowest province (Akatsi) is at 56% |
| Broken taps | About **45%** of home taps are broken. Hawassa has the highest share (50%), but Amanzi has the most people affected (**1.3 million**) |
| Action plan | **25,398 sources (64%)** need intervention. Largest group: chemically contaminated wells (7,093) |

**Share versus scale:** ranking provinces by percentage and by people affected gives
different priorities (Hawassa versus Amanzi for broken taps). Both views are shown on
purpose.

---

## Dashboard pages

| # | Page | Question it answers |
|---|---|---|
| 1 | Cover | How big is the project? |
| 2 | Congestion | Who waits longest, and where? |
| 3 | Pollution | How many wells are contaminated, and in which provinces? |
| 4 | Broken taps | Where are home taps broken, and how many people are affected? |
| 5 | Action plan | What intervention does each source need? Filter by province |

---

## Tools

- **MySQL:** joins, CTEs, window functions, views, data cleaning
- **Power BI:** data model, visuals, slicers
- **Markdown:** analysis logs documenting every decision

---

## Approach

**Views, not raw joins.** Water sources are visited more than once, so joining raw tables
duplicates people and inflates totals. I deduplicated once in SQL (first visit per source,
`visit_count = 1`) and built three views that Power BI reads directly:

| View | Used for |
|---|---|
| `combined_analysis_table` | Source types, queue times, broken taps |
| `pollution_analysis` | Well test results by province |
| `action_plan` | One recommended intervention per source |

**Relationships reviewed manually.** The unsafe relationship between visits and water
sources was set to inactive in the data model to prevent filters from silently returning
wrong population totals.

**Every dashboard number was checked against a SQL query** before being published.

---

## Data quality notes

- **Mislabelled contamination:** some wells were marked "Clean" although their
  biological reading exceeded the safe threshold. I corrected both the description and
  the result. A later check on the dashboard showed 64 records still carried an
  inconsistent label, which I fixed so wells fall into exactly three categories.
- **Queue time is structural:** zero queue time belongs entirely to wells and home taps
  (no queue by nature). Queue analysis therefore covers rivers and shared taps only.
  An earlier version of this analysis treated those zeros as a daily rhythm. Checking
  by source type showed that was wrong, and it was corrected.

---

## Limitations

- Figures use the **first recorded visit per source**, not every visit.
- Action-plan priority (chemical, biological, river, long queue, broken tap) is my
  assumption about severity and should be validated with stakeholders.
- Time-of-day and weekday queue patterns were explored in SQL but **not** presented on
  the dashboard, because they may reflect which source types were visited at which hour
  rather than real behaviour.
- A separate analysis compared field-surveyor scores with independent auditor scores.
  It is a **screening step for human review, not evidence of misconduct**, and it is
  documented separately rather than on the dashboard.

---

## Repository structure

```
maji-ndogo-project/
├── README.md
├── maji_ndogo_dashboard.pbix        Power BI file
├── maji_ndogo_dashboard.pdf         exported dashboard
├── exploratory_analysis_log.md      initial exploration
├── water_source_analysis_log.md     source distribution and action plan
├── investigation_notes.md           auditor comparison (screening only)
└── images/                          dashboard screenshots
```

## How to reproduce

2. Run `queries.sql` to create the three views.
3. Open the `.pbix` file and point it at your MySQL server (Import mode).

---

**Author:** [Your name] · [LinkedIn] · [Email]
