# Insights from Data with BigQuery: Challenge Lab

>  Launch the lab [here](https://google.qwiklabs.com/focuses/11988?parent=catalog)

## Your challenge

You're part of a public health organization which is tasked with identifying answers to queries related to the Covid-19 pandemic. Obtaining the right answers will help the organization in planning and focusing healthcare efforts and awareness programs appropriately.

The dataset and table that will be used for this analysis will be : `bigquery-public-data.covid19_open_data.covid19_open_data`. This repository contains country-level datasets of daily time-series data related to COVID-19 globally. It includes data relating to demographics, economy, epidemiology, geography, health, hospitalizations, mobility, government response, and weather.

* Go to **BigQuery**  
* Go to **Resources** > Add Data > Explore public datasets
* Select **COVID-19 Open Data** > **VIEW DATASET**

### Query 1: Total Confirmed Cases

```sql
SELECT sum(cumulative_confirmed) as total_cases_worldwide FROM `bigquery-public-data.covid19_open_data.covid19_open_data` where date='2020-04-15'
```

### Query 2: Worst Affected Areas

```sql
WITH deaths_by_states as (
SELECT subregion1_name as state, sum(cumulative_deceased) as death_count
FROM `bigquery-public-data.covid19_open_data.covid19_open_data` 
WHERE country_name="United States of America" and date='2020-04-10' and subregion1_name is NOT NULL
GROUP BY subregion1_name
)

SELECT count(*) as count_of_states
FROM deaths_by_states
WHERE death_count > 100
```

### Query 3: Identifying Hotspots

```sql
SELECT subregion1_name as state, sum(cumulative_confirmed) as total_confirmed_cases 
FROM `bigquery-public-data.covid19_open_data.covid19_open_data` 
WHERE country_name="United States of America" and date='2020-04-10' and subregion1_name is NOT NULL
GROUP BY subregion1_name
HAVING total_confirmed_cases > 1000
ORDER BY total_confirmed_cases DESC
```

### Query 4: Fatality Ratio

```sql
SELECT sum(cumulative_confirmed) as total_confirmed_cases, sum(cumulative_deceased) as total_deaths, (sum(cumulative_deceased)/sum(cumulative_confirmed))*100 as case_fatality_ratio
FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE country_name="Italy" and date BETWEEN "2020-04-01" AND "2020-04-30"
```

### Query 5: Identifying specific day

```sql
SELECT date
FROM `bigquery-public-data.covid19_open_data.covid19_open_data` 
WHERE country_name="Italy" and cumulative_deceased > 10000
ORDER BY date ASC
LIMIT 1
```

### Query 6: Finding days with zero net new cases

```sql
WITH india_cases_by_date AS (
  SELECT date, SUM( cumulative_confirmed ) AS cases
  FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE country_name ="India" AND date between '2020-02-21' and '2020-03-15'
  GROUP BY date
  ORDER BY date ASC 
), india_previous_day_comparison AS (
 SELECT date, cases, LAG(cases) OVER(ORDER BY date) AS previous_day, cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases
 FROM india_cases_by_date
)
 
SELECT count(*)
FROM india_previous_day_comparison
WHERE net_new_cases = 0
```

### Query 7: Doubling rate

```sql
WITH us_cases_by_date AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name="United States of America"
    AND date between '2020-03-22' and '2020-04-20'
  GROUP BY
    date
  ORDER BY
    date ASC 
 )
, us_previous_day_comparison AS 
(SELECT
  date,
  cases,
  LAG(cases) OVER(ORDER BY date) AS previous_day,
  cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases,
  (cases - LAG(cases) OVER(ORDER BY date))*100/LAG(cases) OVER(ORDER BY date) AS percentage_increase
FROM us_cases_by_date
)
SELECT Date, cases as Confirmed_Cases_On_Day, previous_day as Confirmed_Cases_Previous_Day, percentage_increase as Percentage_Increase_In_Cases
FROM us_previous_day_comparison
WHERE percentage_increase > 10
```

### Query 8: Recovery rate

```sql
WITH cases_by_country AS (
  SELECT
    country_name AS country,
    sum(cumulative_confirmed) AS cases,
    sum(cumulative_recovered) AS recovered_cases
  FROM
    bigquery-public-data.covid19_open_data.covid19_open_data
  WHERE
    date = '2020-05-10'
  GROUP BY
    country_name
 )
, recovered_rate AS 
(SELECT
  country, cases, recovered_cases,
  (recovered_cases * 100)/cases AS recovery_rate
FROM cases_by_country
)
SELECT country, cases AS confirmed_cases, recovered_cases, recovery_rate
FROM recovered_rate
WHERE cases > 50000
ORDER BY recovery_rate desc
LIMIT 10
```

### Query 9: CDGR - Cumulative Daily Growth Rate

```sql
WITH
  france_cases AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS total_cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name="France"
    AND date IN ('2020-01-24',
      '2020-05-10')
  GROUP BY
    date
  ORDER BY
    date)
, summary as (
SELECT
  total_cases AS first_day_cases,
  LEAD(total_cases) OVER(ORDER BY date) AS last_day_cases,
  DATE_DIFF(LEAD(date) OVER(ORDER BY date),date, day) AS days_diff
FROM
  france_cases
LIMIT 1
)

select first_day_cases, last_day_cases, days_diff, POW((last_day_cases/first_day_cases),(1/days_diff))-1 as cdgr
from summary
```

### Create a Datastudio report

```sql
SELECT
  date, SUM(cumulative_confirmed) AS country_cases,
  SUM(cumulative_deceased) AS country_deaths
FROM
  `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE
  date BETWEEN '2020-03-15'
  AND '2020-04-30'
  AND country_name ="United States of America"
GROUP BY date
```

* Open EXPLORE DATA
* Select **Explore with Data Studio**
* AUTHORIZE
* You can view the chart here