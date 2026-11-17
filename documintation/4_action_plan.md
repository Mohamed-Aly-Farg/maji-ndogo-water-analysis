# Intervention Rules & Final Action Plan

## 1. Purpose

The purpose of this stage is to translate water access analysis findings
into actionable interventions.

The goal is to identify water sources that require attention, determine
the appropriate intervention, and support prioritization based on the
problem and the number of people affected.

## 2. Grain

The final action plan will contain one row per water source.

**Grain:** `source_id`

Each source should have its own location, condition, affected
population, and recommended intervention.

## 3. Intervention Rules

  Condition                                   Recommended Intervention
  ------------------------------------------- -------------------------------
  Contaminated: Chemical                      Install RO Filter
  Contaminated: Biological                    Install UV and RO Filter
  River                                       Drill Well
  Shared Tap with Queue Time \>= 30 minutes   Install Additional Taps
  Broken Home Tap                             Diagnose Local Infrastructure

## 4. Priority Rules

The initial priority order is:

1.  Chemical Contamination
2.  Biological Contamination
3.  Broken Home Tap
4.  Shared Tap with Long Queue
5.  River Source

The priority order is a preliminary business assumption and requires
validation with relevant technical stakeholders.

## 5. sql

```sql
SELECT
location.address,
location.town_name,
location.province_name,
water_source.source_id,
water_source.type_of_water_source,
well_pollution.results,
CASE
	WHEN well_pollution.results = 'Contaminated: Chemical' then 'INSTALL RO FILTER'
    WHEN well_pollution.results = 'Contaminated: Biological' then 'INSTALL UV AND RO FILTER'
    WHEN water_source.type_of_water_source = 'river' then 'DRILL WELL'
    WHEN water_source.type_of_water_source  = 'shared_tap' AND visits.visit_count>=30 then concat('INSTALL',FLOOR(visits.visit_count/30),'TAPS NEARDY')
    WHEN water_source.type_of_water_source  = 'tap_in_home_broken' then 'DIAGNOSE LOCAL INFRASTUCTIURS'
ELSE NULL
END AS IMPROVEMENTS
FROM 
water_source
LEFT JOIN
well_pollution ON water_source.source_id = well_pollution.source_id
INNER JOIN
visits ON water_source.source_id = visits.source_id
INNER JOIN
location ON location.location_id = visits.location_id
where visits.visit_count = 1
AND ( 	well_pollution.results != 'Clean'
OR 		water_source.type_of_water_source IN ('river','tap_in_home_broken')
OR (	water_source.type_of_water_source = 'shared_tap' AND visits.visit_count>=30));
```


