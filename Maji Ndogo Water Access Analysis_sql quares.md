# Maji Ndogo Water Access Analysis

## **📌 Introduction**

- **Project Overview**
    
    > This database models the water access monitoring system in *Maji Ndogo*.
    > 
    
    > The system tracks <u>**employees**</u>, their <u>**visits**</u> to <u>**water sources**</u>, <u>**Location**</u> the subjective <u>**water quality**</u> they record, and <u>**laboratory pollution tests**</u> performed on wells. The schema also includes external <u>**global water data**</u> used for benchmarking.
    > 
- **Project Goal**
    
    > The main goal of this project is to analyze water access in *Maji Ndogo* by exploring visit logs, water source information, pollution tests, and quality scores. The analysis aims to identify unsafe water sources, detect contamination risks, evaluate usage patterns, and highlight communities that are underserved. The insights generated from this project will support decision-makers in improving access to clean and safe water across the region.
    > 

## **📊 ERD Schema**

- ERD
    
    ![image.png](image.png)
    
    ![image.png](image%201.png)
    

- Description
    
    > The ERD for the Maji Ndogo Water Access System demonstrates how employee visits connect locations and water sources, creating a centralized structure for analyzing water quality, crowding, and contamination. Subjective and scientific assessments are stored in separate tables (water_quality and well_pollution), giving a comprehensive view of the water safety situation. External benchmarking data (global_water_access) supports broader comparisons. This structure enables efficient SQL querying for insights on pollution, usage patterns, and data quality
    > 
- Relationships
    - Employee → Visits (One to Many)
    - Location → Visits (One to Many)
    - Water Source → Visits (One to Many)
    - Water Source → Well Pollution (One to Many)

## 📁 Dataset Description

- Description
    
    > This database contains the records of a water survey for the Integrated Project.
    > 
    - **data dictionary**
        
        
        | <u>**Tables in (md_water_services_student)**</u> |
        | --- |
        | **employee** |
        | **global_water_access** |
        | **location** |
        | **visits** |
        | **water_quality** |
        | **water_source** |
        | **well_pollution** |
- Data model
The database md_water_services includes more than 60000 unique records and 43 columns. The
data model , including the column names ,data types and feature descriptions
    - **employee**
        
        
        | column_name | description | datatype |
        | --- | --- | --- |
        | assigned_employee_id | Unique ID assigned to each employee | INT |
        | employee_name | Name of the employee | VARCHAR(255) |
        | phone_number | Contact number of the employee | VARCHAR(15) |
        | email | Email address of the employee | VARCHAR(255) |
        | address | Residential address of the employee | VARCHAR(255) |
        | town_name | Name of the town where the employee resides | VARCHAR(255) |
        | province_name | Name of the province where the employee resides | VARCHAR(255) |
    
    ---
    
    - **global_water_access**
        
        
        | name | The country or area name | varchar(255) |
        | --- | --- | --- |
        | region | Geographical region | varchar(255) |
        | year | Year of the data record | int |
        | pop_n | The national population size estimate in thousands | float |
        | pop_u | The urban population share estimate in percentage points (%) | float |
        | wat_bas_n | The estimated national share of people with at least basic service (%) | float |
        | wat_lim_n | The estimated national share of people with limited service (%) | float |
        | wat_unimp_n | The estimated national share of people with unimproved service (%) | float |
        | wat_sur_n | The estimated national share of people with surface service (%) | float |
        | wat_bas_r | The estimated rural share of people with at least basic service (%) | float |
        | wat_lim_r | The estimated rural share of people with limited service (%) | float |
        | wat_unimp_r | The estimated rural share of people with unimproved service (%) | float |
        | wat_sur_r | The estimated rural share of people with surface service (%) | float |
        | wat_bas_u | The estimated urban share of people with at least basic service (%) | float |
        | wat_lim_u | The estimated urban share of people with limited service (%) | float |
        | wat_unimp_u | The estimated urban share of people with unimproved service (%) | float |
        | wat_sur_u | The estimated urban share of people with surface service (%) | float |
    
    ---
    
    - **location**
        
        
        | location_id | Unique ID assigned to each location | varchar(255) |
        | --- | --- | --- |
        | address | Address of the location | varchar(255) |
        | province_name | Name of the province where the location is situated | varchar(255) |
        | town_name | Name of the town where the location is situated | varchar(255) |
        | location_type | Type or category of the location | varchar(255) |
    
    ---
    
    - **visits**
        
        
        | record_id | Unique ID assigned to each visit | int |
        | --- | --- | --- |
        | location_id | ID of the location visited | varchar(255) |
        | source_id | ID of the water source visited | varchar(510) |
        | time_of_record | Date and time of the visit | datetime |
        | visit_count | Number of visits made to this location | int |
        | time_in_queue | Time spent by people waiting for water in a queue at the location | int |
        | assigned_employee_id | ID of the employee who visited the location | int |
    
    ---
    
    - **water_quality**
        
        
        | record_id | Unique ID assigned to each record | int |
        | --- | --- | --- |
        | subjective_quality_score | Score representing the subjective quality of the water source | int |
        | visit_count | Number of visits made for data collection | int |
        
        > They assigned a score to each source from 1, being terrible, to 10 for a good, clean water source in a home. Shared taps are not rated as high, and the score also depends on how long the queue times are.
        > 
    
    ---
    
    - **water_source**
        
        
        | source_id | Unique ID assigned to each water source | VARCHAR(510) |
        | --- | --- | --- |
        | type_of_water_source | Type or category of the water source. Can be : tap_in_home, tap_in_home_broken, well, shared_tap, river | VARCHAR(255) |
        | number_of_people_served | Number of people served by this water source | INT |
        
        1. River - This is an open water source that millions of people use in Maji Ndogo. Water from a river has a high risk of being contaminated with biological and other pollutants, so it is the worst source of water possible.
        2. Well - These sources draw water from underground sources, many of our wells are not clean.
        3. Shared tap - This is a tap in a public area shared by communities.
        4. Tap in home - These are taps that are inside the homes of our citizens. On average about 6 people live together in Maji Ndogo
        5. Broken tap in home - These are taps that have been installed in a citizen’s home, but the infrastructure connected to that tap is not functional. This can be due to burst pipes, broken pumps or water treatment plants that are not working.
        - important note
            
            > on the home taps: About 6-10 million people have running water installed in their homes in Maji Ndogo, including broken taps. If we were to document this, we would have a row of data for each home, so that one record is one tap. That means our database would contain about 1 million rows of data, which may slow our systems down.
            > 
            - For now, the surveyors combined the data of many households together into a single record.
                
                > For example, the first record, AkHa00000224 is for a tap_in_home that serves 956 people. What this means is that the records of about 160 homes nearby were combined into one record, with an average of 6 people living in each house 160 x 6 ≈ 956. So 1 tap_in_home or tap_in_home_broken record actually refers to multiple households, with the sum of the people living in these homes equal to number_of_people_served.
                > 
            
    
    ---
    
    - **well_pollution**
        
        
        | source_id | ID of the water source being tested for pollution | varchar(258) |
        | --- | --- | --- |
        | date | Date of the pollution test | datetime |
        | description | Description of the pollution test | varchar(255) |
        | pollutant_ppm | Result of the pollution test in parts per million | float |
        | biological | Biological contamination level | float |
        | results | Result of the pollution test. Can be 'dirty', 'biologically contaminated', or 'clean' | varchar(255) |
        
        > In the well pollution table, the descriptions are notes taken by our scientists as text, so it will be challenging to process it. The biological column is in units of CFU/mL, so it measures how much contamination is in the water. 0 is clean, and anything more than 0.01 is contaminated.
        > 

## 🔧 Data Cleaning Steps

- Assess the quality of water sources:
    
    Let's check if this is true. The surveyors only made multiple visits to shared taps and did not revisit other types of water sources. So there should be no records of second visits to locations where there are good water sources, like taps in homes.
    
    - **Shared Taps/Communal Pumps:** These are expected to be visited more than once to record changes (such as peak times or outages).
    - **Taps in Homes and Good Sources:** These should not be visited more than once. Each should have only one visit recorded.
    - المعلومة دية صح
    
    ```jsx
    SELECT * 
    FROM water_quality
    join visits on visits.record_id = water_quality.record_id
    join water_source on water_source.source_id = visits.source_id
    where water_source.type_of_water_source ="tap_in_home" and water_quality.visit_count>2;
    ```
    
- Investigate pollution issues:
    
    > It looks like our scientists diligently recorded the water quality of all the wells. Some are contaminated with biological contaminants, while others are polluted with an excess of heavy metals and other pollutants. Based on the results, each well was classified as Clean, Contaminated: Biological or Contaminated: Chemical. It is important to know this because wells that are polluted with bio- or other contaminants are not safe to drink. It looks like they recorded the source_id of each test, so we can link it to a source, at some place in Maji Ndogo.
    > 
    
    > In the well pollution table, the descriptions are notes taken by our scientists as text, so it will be challenging to process it. The biological column is in units of CFU/mL, so it measures how much contamination is in the water. 0 is clean, and anything more than 0.01 is contaminated.
    Let's check the integrity of the data. The worst case is if we have contamination, but we think we don't. People can get sick, so we need to make sure there are no errors here.
    11:37 So, write a query that checks if the results is Clean but the biological column is > 0.01.
    > 
    
    > it seems that some data input personnel might have mistaken the description field for determining the cleanliness of the water.
    > 
    
    > find and remove the “Clean” part from all the descriptions that do have a biological contamination so this mistake is not made again.
    > 
    
    > The second issue has arisen from this error, but it is much more problematic. Some of the field surveyors have marked wells as Clean in the results column because the description had the word “Clean” in it, even though they have a biological contamination. So we need to find all the results that have a value greater than 0.01 in the biological column and have been set to Clean in the results column.
    > 
    
    > The query should return 38 wrong descriptions. Now we need to fix these descriptions so that we don’t encounter this issue again in the future.
    > 
    
    > Looking at the results we can see two different descriptions that we need to fix:
    > 
    > 
    > > All records that mistakenly have Clean Bacteria: E. coli should updated to Bacteria: E. coli
    > > 
    > 
    > > All records that mistakenly have Clean Bacteria: Giardia Lamblia should updated to Bacteria: Giardia Lamblia
    > > 
    
    > The second issue we need to fix is in our results column. We need to update the results column from Clean to Contaminated: Biological where the biological column has a value greater than 0.01.
    > 
    
    | 3 | 32 | 04:19:10 | update well_pollution  SET description = 'Bacteria: E. coli' where description = 'Clean Bacteria: E. coli' | 26 row(s) affected Rows matched: 26  Changed: 26  Warnings: 0 | 0.031 sec |
    | --- | --- | --- | --- | --- | --- |
    | 3 | 33 | 04:19:14 | update well_pollution  SET description = 'Bacteria: Giardia Lamblia' where description = 'Clean Bacteria: Giardia Lamblia' | 12 row(s) affected Rows matched: 12  Changed: 12  Warnings: 0 | 0.031 sec |
    | 0 | 34 | 04:19:19 | update results  SET description =  'Contaminated' where description = 'Clean' and biological  > 0.01 | Error Code: 1146. Table 'md_water_services.results' doesn't exist | 0.000 sec |
    | 3 | 35 | 04:20:05 | update well_pollution  SET results =  'Contaminated' where results = 'Clean' and biological  > 0.01 | 64 row(s) affected Rows matched: 64  Changed: 64  Warnings: 0 | 0.031 sec |
- note that the email addresses have not been added.
    
    We will have to send them reports and figures, so let's update it. Luckily the emails for our department are easy: [first_name.last_name@ndogowater.gov](mailto:first_name.last_name@ndogowater.gov).
    
    ```sql
    SELECT 
    	employee_name , 
      concat(lower(replace(rtrim(ltrim(employee_name)),' ','.')),'@ndogowater.gov') AS new_email
    FROM md_water_services.employee;
    
    update 
    	employee
    set 
    	email = concat(lower(replace(rtrim(ltrim(employee_name)),' ','.')),'@ndogowater.gov')
    ```
    
- phone numbers should be 12 characters
    
    
    ```sql
    SELECT
    	phone_number,
        length(phone_number),
        length(trim(phone_number))
    FROM 
    	md_water_services.employee;
    	
    update employee
    set phone_number = trim(phone_number);
    ```
    
    - length():to ensure from the problem

## 🔍 SQL Analysis

```markdown
Initial exploration phase, conducted before the auditor investigation and
the infrastructure action plan. Goal: understand table shapes, validate
data reliability, and surface early business insights before committing
to deeper analysis.
```

### Explore data

- how many of our employees live in each town?
    
    
    ```sql
    select 
    		town_name,
    		count(employee_name)
    from 
    		md_water_services.employee
    group by
    		(town_name);
    ```
    
    | town_name | count(employee_name) |
    | --- | --- |
    | Ilanga | 3 |
    | Rural | 29 |
    | Lusaka | 4 |
    | Zanzibar | 4 |
    | Dahabu | 6 |
    | Kintampo | 1 |
    | Harare | 5 |
    | Yaounde | 1 |
    | Serowe | 3 |
- asked we send out an email or message congratulating the top 3 field surveyors.
    - تصحيح
        
        ```jsx
        select employee.employee_name,employee.email,count(visits.visit_count)
        from visits
        join employee
        on employee.assigned_employee_id = visits.assigned_employee_id
        group by employee.employee_name,employee.email
        order by count(visits.visit_count) desc
        limit 3;
        ```
        
        ```jsx
        مشكلة 1: COUNT(visits.visit_count)
        COUNT(*) AS total_visits
        ```
        
        ```jsx
        مشكلة 2: استخدمت LIMIT 3 مش RANK()
        
        ```
        
    - markdoun
        
        ## Query: Top 3 Field Surveyors
        
        - Used RANK() instead of LIMIT to handle potential ties fairly
        - Current data: no tie exists at rank 1-3 (verified via DENSE_RANK comparison)
        - Decision: if ties occur in future data, all tied employees will be included
        (business accepted this behavior over forcing exactly 3 names)
    
    ```sql
    WITH ranked_visits AS (
        SELECT
            assigned_employee_id,
            count(visit_count) AS total_visits,
            RANK() OVER (ORDER BY count(visit_count) DESC) AS rank_visit
        FROM visits
        GROUP BY assigned_employee_id
    )
    SELECT
        e.*,
        r.total_visits,
        r.rank_visit
    FROM employee e
    JOIN ranked_visits r
        ON e.assigned_employee_id = r.assigned_employee_id
    WHERE r.rank_visit <= 3;
    ```
    
    - rank(): to get the ranking
    - We created a subquery because the goal is to get the top 3 in traffic, not just 3. Someone else might be tied with 3 and still not get it.
    
    | 1 | Bello Azibo | +99643864786 | bello.azibo@ndogowater.gov | 129 Ziwa La Kioo Road | Kilimani | Rural | Field Surveyor | 3708 | 1 |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 30 | Pili Zola | +99822478933 | pili.zola@ndogowater.gov | 46 Stone Town Street | Hawassa | Zanzibar | Field Surveyor | 3676 | 2 |
    | 34 | Rudo Imani | +99046972648 | rudo.imani@ndogowater.gov | 15 Lake Malawi Avenue | Kilimani | Ilanga | Field Surveyor | 3539 | 3 |
- count the records per province.
    
    
    ```sql
    select 
    		province_name , 
    		count(**)
    from 
    		location
    group by
    		(province_name)
    order by 
    		count(**)desc;
    ```
    
    | Kilimani | 9510 |
    | --- | --- |
    | Akatsi | 8940 |
    | Sokoto | 8220 |
    | Amanzi | 6950 |
    | Hawassa | 6030 |
- Create a result set showing
    
    
    ```sql
    select 
    		province_name,
        town_name,
        count(*)
    from 
    		location
    group by 
    		province_name,
    		town_name
    order by 
    		province_name ,
    		count(*) desc;
    ```
    
    These results show us that our field surveyors did an excellent job of documenting the status of our country's water crisis. Every province and town has many documented sources. This makes me confident that the data we have is reliable enough to base our decisions on. This is an insight we can use to communicate data integrity, so let's make a note of that.
    
    | Akatsi | Rural | 6290 |
    | --- | --- | --- |
    | Akatsi | Lusaka | 1070 |
    | Akatsi | Harare | 800 |
    | Akatsi | Kintampo | 780 |
    | Amanzi | Rural | 3100 |
    | Amanzi | Asmara | 930 |
    | Amanzi | Dahabu | 930 |
    | Amanzi | Amina | 670 |
    | Amanzi | Pwani | 520 |
    | Amanzi | Abidjan | 400 |
- look at the number of records for each location type
    
    
    ```sql
    select 
    	location_type,
       count(*)
    from 
    	location
    group by 
    	location_type;
    ```
    
    | Urban | 15910 |
    | --- | --- |
    | Rural | 23740 |
- Diving into the sources
    - How many people did we survey in total?
        
        
        ```sql
        select sum(number_of_people_served)
        from water_source;
        ```
        
        - 27628140
    - How many wells, taps and rivers are there?
        
        
        ```sql
        select type_of_water_source ,count(*)
        from water_source
        group by type_of_water_source;
        ```
        
        | tap_in_home | 7265 |
        | --- | --- |
        | tap_in_home_broken | 5856 |
        | well | 17383 |
        | shared_tap | 5767 |
        | river | 3379 |
    - How many people share particular types of water sources on average?
        
        
        ```sql
        select type_of_water_source ,round(avg(number_of_people_served),0)
        from water_source
        group by type_of_water_source;
        ```
        
        | tap_in_home | 644 |
        | --- | --- |
        | tap_in_home_broken | 649 |
        | well | 279 |
        | shared_tap | 2071 |
        | river | 699 |
    - How many people are getting water from each type of source? percentage
        
        
        ```sql
        select type_of_water_source ,sum(number_of_people_served)
        from water_source
        group by type_of_water_source;
        ```
        
        | tap_in_home | 4678880 |
        | --- | --- |
        | tap_in_home_broken | 3799720 |
        | well | 4841724 |
        | shared_tap | 11945272 |
        | river | 2362544 |
        
        ```sql
        SELECT 
            type_of_water_source,
            SUM(number_of_people_served) AS total_people_served,
            SUM(SUM(number_of_people_served)) OVER () AS grand_total,
            ROUND(
                SUM(number_of_people_served) * 100.0 / SUM(SUM(number_of_people_served)) OVER (), 
                2
            ) AS pct_of_total
        FROM water_source
        GROUP BY type_of_water_source;
        ```
        
        | tap_in_home | 4678880 | 27628140 | 16.9352 |
        | --- | --- | --- | --- |
        | tap_in_home_broken | 3799720 | 27628140 | 13.7531 |
        | well | 4841724 | 27628140 | 17.5246 |
        | shared_tap | 11945272 | 27628140 | 43.2359 |
        | river | 2362544 | 27628140 | 8.5512 |

---

- Start of a solution
    
    
    ```sql
    WITH total_people_per_source AS (
        SELECT 
            type_of_water_source,
            SUM(number_of_people_served) AS people_per_source,
            SUM(SUM(number_of_people_served)) OVER () AS total_people,
            (SUM(number_of_people_served)/ SUM(SUM(number_of_people_served)) OVER ()) * 100 AS percentage_share
        FROM water_source
        WHERE type_of_water_source != 'tap_in_home'
        GROUP BY type_of_water_source
    )
    SELECT
        type_of_water_source,
        people_per_source,
        total_people,
        percentage_share,
        RANK() OVER (ORDER BY percentage_share DESC) AS source_rank
    FROM total_people_per_source;
    ```
    
    Start of a solution
    At some point, we will have to fix or improve all of the infrastructure, so we should start thinking about how we can make a data-driven decision
    how to do it. I think a simple approach is to fix the things that affect most people first. So let's write a query that ranks each type of source based
    on how many people in total use it. RANK() should tell you we are going to need a window function to do this, so let's think through the problem.
    12:26
    We will need the following columns:
    
    - Type of sources -- Easy
    - Total people served grouped by the types -- We did that earlier, so that's easy too.
    - A rank based on the total people served, grouped by the types -- A little harder.
    
    | shared_tap | 11945272 | 22949260 | 52.0508 | 1 |
    | --- | --- | --- | --- | --- |
    | well | 4841724 | 22949260 | 21.0975 | 2 |
    | tap_in_home_broken | 3799720 | 22949260 | 16.5570 | 3 |
    | river | 2362544 | 22949260 | 10.2946 | 4 |
- We prioritize visiting certain areas over those that are unclean.
    
    ```sql
    select
    	source_id,
    	type_of_water_source,
    	number_of_people_served,
    	row_number()over(partition by type_of_water_source order by number_of_people_served desc) as priority_rank
    from 
    	water_source
    where 
    	type_of_water_source in ('river','shared_tap','well');
    ```
    

---

- Analyzing queues
    - How long did the survey take?
        
        
        ```sql
        select 
        	min(time_of_record),
            max(time_of_record),
            datediff(max(time_of_record),min(time_of_record))
        from visits;
        ```
        
        To calculate how long the survey took, we need to get the first and last dates (which functions can find the largest/smallest value), and subtract them. Remember with DateTime data, we can't just subtract the values. We have to use a function to get the difference in days.
        
        - When I do it, I get 924 days which is about 2 and a half years!
    - What is the average total queue time for water?
        
        
        ```sql
        select round(avg(nullif(time_in_queue,0)),0) as avg_time_queue
        from visits
        ```
        
        > Let's see how long people have to queue on average in Maji Ndogo. Keep in mind that many sources like taps_in_home have no queues. These are just recorded as 0 in the time_in_queue column, so when we calculate averages, we need to exclude those rows.
        > 
        > - using NULLIF() do to this.
        
    - What is the average queue time on different days?
        
        
        ```sql
        select
        		dayname(time_of_record) as day_of_week,
        		round(avg(nullif(time_in_queue,0)),0)
        from 
        		visits
        group by 
        		dayname(time_of_record);
        ```
        
        - calculate the average queue time, grouped by day of the week. Remember to revise DateTime functions, and also think
        about how to present the results clearly.
    - How can we communicate this information efficiently? (average queue time on different hours)
        
        > We can also look at what time during the day people collect water. Try to order the results in a meaningful way
        > 
        
        ```sql
        select
        TIME_FORMAT(TIME(time_of_record), '%H:00')as time_w,
        round(avg(nullif(time_in_queue,0)),0) as avg_time_queue
        from
        visits
        group by TIME_FORMAT(TIME(time_of_record), '%H:00');
        ```
        
        متوسط وقت الانتظار لكل ساعة
        
        ---
        
        ```sql
        SELECT 
            TIME_FORMAT(time_of_record, '%H:00') AS hour_of_day,
        
            ROUND(AVG(CASE WHEN DAYNAME(time_of_record) = 'Sunday'    THEN time_in_queue END), 0) AS Sunday,
            ROUND(AVG(CASE WHEN DAYNAME(time_of_record) = 'Monday'    THEN time_in_queue END), 0) AS Monday,
            ROUND(AVG(CASE WHEN DAYNAME(time_of_record) = 'Tuesday'   THEN time_in_queue END), 0) AS Tuesday,
            ROUND(AVG(CASE WHEN DAYNAME(time_of_record) = 'Wednesday' THEN time_in_queue END), 0) AS Wednesday,
            ROUND(AVG(CASE WHEN DAYNAME(time_of_record) = 'Thursday'  THEN time_in_queue END), 0) AS Thursday,
            ROUND(AVG(CASE WHEN DAYNAME(time_of_record) = 'Friday'    THEN time_in_queue END), 0) AS Friday,
            ROUND(AVG(CASE WHEN DAYNAME(time_of_record) = 'Saturday'  THEN time_in_queue END), 0) AS Saturday
        
        FROM visits
        WHERE time_in_queue <> 0
        GROUP BY TIME_FORMAT(time_of_record, '%H:00')
        ORDER BY hour_of_day;
        ```
        

### create a table to track our progress

- **put it together**
    
    ```sql
    CREATE OR REPLACE VIEW combined_analysis_table AS
    SELECT
        ws.source_id,
        ws.type_of_water_source,
        ws.number_of_people_served,
        l.location_id,
        l.address,
        l.town_name,
        l.province_name,
        l.location_type,
        v.time_of_record,
        v.time_in_queue,
        wp.results AS pollution_results
    FROM water_source AS ws
    LEFT JOIN visits AS v
        ON ws.source_id = v.source_id
    LEFT JOIN location AS l
        ON v.location_id = l.location_id
    LEFT JOIN well_pollution AS wp
        ON ws.source_id = wp.source_id
    WHERE v.visit_count = 1;
    ```
    
- **calculates the population of each province**
    
    
    ```sql
    WITH province_totals AS (
        SELECT
            province_name,
            SUM(number_of_people_served) AS total_ppl_serv
        FROM combined_analysis_table
        GROUP BY province_name
    )
    select 
    	*
    from 
    	province_totals;
    ```
    
    | Sokoto | 5774434 |
    | --- | --- |
    | Kilimani | 6584764 |
    | Hawassa | 3843810 |
    | Akatsi | 5993306 |
    | Amanzi | 5431826 |
    
- **where some sources are more abundant? (report for every provinces )**
    
    > building a pivot table ,to break down our data into provinces or towns and source tpes.
    > 
    
    ```sql
    WITH province_totals AS (
        SELECT
            province_name,
            SUM(number_of_people_served) AS total_ppl_serv
        FROM combined_analysis_table
        GROUP BY province_name
    )
    
    SELECT
        ct.province_name,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'river' THEN ct.number_of_people_served ELSE 0 END) * 100.0 / pt.total_ppl_serv, 1) AS river,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'shared_tap' THEN ct.number_of_people_served ELSE 0 END) * 100.0 / pt.total_ppl_serv, 1) AS shared_tap,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'tap_in_home' THEN ct.number_of_people_served ELSE 0 END) * 100.0 / pt.total_ppl_serv, 1) AS tap_in_home,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'tap_in_home_broken' THEN ct.number_of_people_served ELSE 0 END) * 100.0 / pt.total_ppl_serv, 1) AS tap_in_home_broken,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'well' THEN ct.number_of_people_served ELSE 0 END) * 100.0 / pt.total_ppl_serv, 1) AS well
    FROM combined_analysis_table ct
    JOIN province_totals pt
        ON ct.province_name = pt.province_name
    GROUP BY
        ct.province_name,
        pt.total_ppl_serv
    ORDER BY ct.province_name;
    
    ```
    
    | province_name | river | shared_tap | tap_in_home | tap_in_home_broken | well |
    | --- | --- | --- | --- | --- | --- |
    | Akatsi | 4.9 | 48.7 | 13.7 | 9.8 | 22.9 |
    | Amanzi | 3.4 | 37.7 | 27.8 | 24.4 | 6.8 |
    | Hawassa | 3.8 | 43.1 | 14.7 | 14.9 | 23.6 |
    | Kilimani | 8.3 | 47.2 | 12.6 | 11.7 | 20.2 |
    | Sokoto | 20.7 | 38.4 | 16.5 | 9.5 | 14.9 |
    
    > **Note**
    > 
    > - Sokoto has the largest population of people drinking river water. We should send our drilling equipment to Sokoto first, so people can drink safe filtered water from a well.
    > - The majority of water from Amanzi comes from taps, but half of these home taps don't work because the infrastructure is broken. We need to send out engineering teams to look at the infrastructure in Amanzi first.
- **where some sources are more abundant? (report for every provinces , towns )**
    
    ```sql
    DROP TEMPORARY TABLE IF EXISTS town_aggregated_water_access;
    WITH town_totals AS (
        SELECT
            province_name,
            town_name,
            SUM(number_of_people_served) AS total_ppl_serv
        FROM combined_analysis_table
        GROUP BY province_name, town_name
    )
    
    SELECT
        ct.province_name,
        ct.town_name,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'river' THEN ct.number_of_people_served ELSE 0 END)* 100.0 / tt.total_ppl_serv, 0) AS river,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'shared_tap' THEN ct.number_of_people_served ELSE 0 END)* 100.0 / tt.total_ppl_serv, 0) AS shared_tap,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'tap_in_home' THEN ct.number_of_people_served ELSE 0 END)* 100.0 / tt.total_ppl_serv, 0) AS tap_in_home,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'tap_in_home_broken' THEN ct.number_of_people_served ELSE 0 END)* 100.0 / tt.total_ppl_serv, 0) AS tap_in_home_broken,
        ROUND(SUM(CASE WHEN ct.type_of_water_source = 'well' THEN ct.number_of_people_served ELSE 0 END)* 100.0 / tt.total_ppl_serv, 0) AS well
    FROM combined_analysis_table ct
    JOIN town_totals tt
        ON ct.province_name = tt.province_name
        AND ct.town_name = tt.town_name
    GROUP BY
        ct.province_name,
        ct.town_name,
        tt.total_ppl_serv
    ORDER BY
        ct.province_name,
        ct.town_name;
        
    
    ```
    
- **get insights from specific Towne**
    
    ```sql
    SELECT
    province_name,
    town_name,
    ROUND(tap_in_home_broken / (tap_in_home_broken + tap_in_home) *100,0) AS Pct_broken_taps
    FROM
    town_aggregated_water_access
    ```
    
- **Project progress query**
    
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
    
- `final_action_plan`
    
    ```sql
    WITH final_action_plan AS (
        SELECT
            location.address,
            location.town_name,
            location.province_name,
            water_source.source_id,
            water_source.type_of_water_source,
            water_source.number_of_people_served,
            well_pollution.results AS pollution_results,
            visits.time_in_queue,
    
            CASE
                WHEN well_pollution.results = 'Contaminated: Chemical'
                    THEN 'INSTALL RO FILTER'
    
                WHEN well_pollution.results = 'Contaminated: Biological'
                    THEN 'INSTALL UV AND RO FILTER'
    
                WHEN water_source.type_of_water_source = 'tap_in_home_broken'
                    THEN 'DIAGNOSE LOCAL INFRASTRUCTURE'
    
                WHEN water_source.type_of_water_source = 'shared_tap'
                     AND visits.time_in_queue >= 30
                    THEN 'INSTALL ADDITIONAL TAPS'
    
                WHEN water_source.type_of_water_source = 'river'
                    THEN 'DRILL WELL'
    
                ELSE NULL
            END AS recommended_intervention
    
        FROM water_source
    
        LEFT JOIN well_pollution
            ON water_source.source_id = well_pollution.source_id
    
        INNER JOIN visits
            ON water_source.source_id = visits.source_id
    
        INNER JOIN location
            ON location.location_id = visits.location_id
    
        WHERE visits.visit_count = 1
        AND (
            well_pollution.results IN (
                'Contaminated: Chemical',
                'Contaminated: Biological'
            )
            OR water_source.type_of_water_source IN (
                'river',
                'tap_in_home_broken'
            )
            OR (
                water_source.type_of_water_source = 'shared_tap'
                AND visits.time_in_queue >= 30
            )
        )
    )
    
    SELECT
        recommended_intervention,
        COUNT(*) AS sources_count,
        SUM(number_of_people_served) AS people_affected
    FROM final_action_plan
    GROUP BY recommended_intervention
    ORDER BY sources_count DESC;
    ```
    

### Auditor's Report “make sure from employees score”

> Integrating the Auditor's Report to make sure from employees score  ‘Auditor's Report including 1620 sites that they re-visited’
> 
- **put it together to see if there difference in the scores and patterns?**
    
    ```sql
    SELECT 
    	ar.location_id,
        v.record_id,
        e.employee_name,
        ar.true_water_source_score as auditor_score,
        wq.subjective_quality_score as subjectiv_score
    from 
    	auditor_report ar
    join
    	visits v
    on 
    	ar.location_id =  v.location_id
    join
    	water_quality wq
    on 
    	v.record_id = wq.record_id
    join
    	employee e
    on 
    	v.assigned_employee_id = e.assigned_employee_id;
    ```
    
- **difference scores and know the employees that make these mistakes**
    
    
    ```sql
    SELECT 
    	  ar.location_id,
        v.record_id,
        e.employee_name,
        ar.true_water_source_score as auditor_score,
        wq.subjective_quality_score as subjectiv_score
    from 
    	auditor_report ar
    join
    	visits v
    on 
    	ar.location_id =  v.location_id
    join
    	water_quality wq
    on 
    	v.record_id = wq.record_id
    join
    	employee e
    on 
    	v.assigned_employee_id = e.assigned_employee_id
    where
    	ar.true_water_source_score != wq.subjective_quality_score
    and 
    	v.visit_count = 1;
    ```
    
    > 193 row(s) returned
    > 
    > 
    > including duplicates
    > 
    
    > 102 row(s) returned
    without duplicates
    > 
- **put it in view to get some knowledge depend on it**
    - **get a unique list of employees from this table and numbers of mistakes**
        
        ```sql
        create view incorect_scores as
        (
        SELECT 
        	ar.location_id,
            v.record_id,
            e.employee_name,
            ar.true_water_source_score as auditor_score,
            wq.subjective_quality_score as subjectiv_score,
            ar.statements
        from 
        	auditor_report ar
        join
        	visits v
        on 
        	ar.location_id =  v.location_id
        join
        	water_quality wq
        on 
        	v.record_id = wq.record_id
        join
        	employee e
        on 
        	v.assigned_employee_id = e.assigned_employee_id
        where
        	ar.true_water_source_score != wq.subjective_quality_score
            and v.visit_count = 1);
        
        select 
        	employee_name,
          count(*)
        from 
        	incorect_scores
        group by
        	employee_name
        order by 
        	count(*) desc;
        ```
        
    - **how making a lot of "mistakes", more than average**
        
        ```sql
        create view incorect_scores as
        (
        SELECT 
        	ar.location_id,
            v.record_id,
            e.employee_name,
            ar.true_water_source_score as auditor_score,
            wq.subjective_quality_score as subjectiv_score,
            ar.statements
        from 
        	auditor_report ar
        join
        	visits v
        on 
        	ar.location_id =  v.location_id
        join
        	water_quality wq
        on 
        	v.record_id = wq.record_id
        join
        	employee e
        on 
        	v.assigned_employee_id = e.assigned_employee_id
        where
        	ar.true_water_source_score != wq.subjective_quality_score
            and v.visit_count = 1);
        
        with erorr_count as (
        select 
        	employee_name,
            count(*) as count_m
        from 
        	incorect_scores
        group by
        	employee_name
        order by 
        	count(*) desc),
        
        suspect_list as (
        select employee_name,count_m
        from erorr_count
        where count_m > (
        				select round(avg (count_m)) avg_numper
        				from erorr_count))
        
        select *
        from suspect_list;
            
        ```
        
    - **to get the statements**
        
        ```sql
        CREATE OR REPLACE VIEW incorect_scores AS 
        (
            SELECT 
                ar.location_id,
                v.record_id,
                e.employee_name,
                ar.true_water_source_score as auditor_score,
                wq.subjective_quality_score as subjectiv_score,
                ar.statements
            FROM 
                auditor_report ar
            JOIN 
                visits v ON ar.location_id = v.location_id
            JOIN 
                water_quality wq ON v.record_id = wq.record_id
            JOIN 
                employee e ON v.assigned_employee_id = e.assigned_employee_id
            WHERE 
                ar.true_water_source_score != wq.subjective_quality_score
                AND v.visit_count = 1
        );
        
        with erorr_count as (
        select 
        	employee_name,
            count(*) as count_m
        from 
        	incorect_scores
        group by
        	employee_name
        order by 
        	count(*) desc),
        
        suspect_list as (
        select employee_name,count_m
        from erorr_count
        where count_m > (
        				select round(avg (count_m)) avg_numper
        				from erorr_count))
        
        select *
        from incorect_scores
        where employee_name in (select employee_name
        						from suspect_list);
            
        ```
        
    - **there are any one take "cash”**
        
        ```sql
        CREATE OR REPLACE VIEW incorect_scores AS 
        (
            SELECT 
                ar.location_id,
                v.record_id,
                e.employee_name,
                ar.true_water_source_score as auditor_score,
                wq.subjective_quality_score as subjectiv_score,
                ar.statements
            FROM 
                auditor_report ar
            JOIN 
                visits v ON ar.location_id = v.location_id
            JOIN 
                water_quality wq ON v.record_id = wq.record_id
            JOIN 
                employee e ON v.assigned_employee_id = e.assigned_employee_id
            WHERE 
                ar.true_water_source_score != wq.subjective_quality_score
                AND v.visit_count = 1
        );
        
        with erorr_count as (
        select 
        	employee_name,
            count(*) as count_m
        from 
        	incorect_scores
        group by
        	employee_name
        order by 
        	count(*) desc),
        
        suspect_list as (
        select employee_name,count_m
        from erorr_count
        where count_m > (
        				select round(avg (count_m)) avg_numper
        				from erorr_count))
        
        select *
        from incorect_scores
        where employee_name in (select employee_name
        						from suspect_list)
        and statements like('%cash%');
            
        ```