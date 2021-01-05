# bq_exercises

1.a Write a query to get the metrics :- total users, sessions, pageviews, pageviews/sessions on 23rd June 2017

```sql
SELECT
 COUNT(DISTINCT fullVisitorId) AS users,
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) AS sessions,
 SUM(totals.pageviews) AS pageviews,
 ROUND(SUM(totals.pageviews) / COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))),2) AS pages_per_sessions
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170623`
WHERE
 totals.visits = 1
```

1.b Write a query to get the metrics :- total users, sessions, pageviews, pageviews/sessions from Jan'17 - Mar'17 using `_table_suffix_`

```sql
SELECT
 COUNT(DISTINCT fullVisitorId) AS users,
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) AS sessions,
 SUM(totals.pageviews) AS pageviews,
 ROUND(SUM(totals.pageviews) / COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))),2) AS pages_per_sessions
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '331'
 AND
 totals.visits = 1
```

2 Write a query to get the  metrics :- total users, sessions, pageviews, pageviews/sessions and dimensions :- Month, source/medium, device category for Jan’17-Mar’17. Order by month and then users in descending.
```sql
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 CONCAT(trafficSource.source, ' / ' ,trafficSource.medium) AS source_medium,
 device.deviceCategory AS device,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) AS sessions,
 SUM(totals.pageviews) AS pageviews,
 ROUND(SUM(totals.pageviews) / COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))),2) AS pages_per_sessions
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '331'
 AND
 totals.visits = 1
GROUP BY 
 1, 2, 3
ORDER BY 
 1,
 4 DESC
```

2.a Modify the query to include only desktop
```sql
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 CONCAT(trafficSource.source, ' / ' ,trafficSource.medium) AS source_medium,
 device.deviceCategory AS device,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) AS sessions,
 SUM(totals.pageviews) AS pageviews,
 ROUND(SUM(totals.pageviews) / COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))),2) AS pages_per_sessions
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '331'
 AND
 totals.visits = 1
 AND 
 device.deviceCategory = 'desktop'
GROUP BY 
 1, 2, 3
ORDER BY 
 1,
 4 DESC
```

2.b Modify the query to include data for Feb and organic traffic, or Mar and mobile traffic
```sql
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 CONCAT(trafficSource.source, ' / ' ,trafficSource.medium) AS source_medium,
 device.deviceCategory AS device,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) AS sessions,
 SUM(totals.pageviews) AS pageviews,
 ROUND(SUM(totals.pageviews) / COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))),2) AS pages_per_sessions
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '331'
 AND
 totals.visits = 1
 AND 
 ((format_date('%Y%m',parse_date('%Y%m%d', date)) = '201702' AND trafficSource.medium = 'organic') 
 OR (format_date('%Y%m',parse_date('%Y%m%d', date)) = '201703' AND device.deviceCategory = 'mobile'))
GROUP BY 
 1, 2, 3
ORDER BY 
 1,
 4 DESC

```
2.c Modify the query to include sessions greater than 1000
```sql
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 CONCAT(trafficSource.source, ' / ' ,trafficSource.medium) AS source_medium,
 device.deviceCategory AS device,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) AS sessions,
 SUM(totals.pageviews) AS pageviews,
 ROUND(SUM(totals.pageviews) / COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))),2) AS pages_per_sessions
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '331'
 AND
 totals.visits = 1
GROUP BY 
 1, 2, 3
HAVING
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) > 1000
ORDER BY 
 1,
 4 DESC

```

2.d Modify the query to include sessions greater than sessions coming from google / organic, desktop and for the month of feb
```sql
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 CONCAT(trafficSource.source, ' / ' ,trafficSource.medium) AS source_medium,
 device.deviceCategory AS device,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) AS sessions,
 SUM(totals.pageviews) AS pageviews,
 ROUND(SUM(totals.pageviews) / COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))),2) AS pages_per_sessions
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '331'
 AND
 totals.visits = 1
GROUP BY 
 1, 2, 3
HAVING
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) > 
  (SELECT
    COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string)))
   FROM
    `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
   WHERE
    _table_suffix BETWEEN '101' AND '331'
    AND
    totals.visits = 1
    AND trafficSource.source = 'google' AND trafficSource.medium = 'organic' AND format_date('%Y%m',parse_date('%Y%m%d', date)) = '201702' AND device.deviceCategory = 'desktop')
ORDER BY 
 1,
 4 DESC

```

3 Write a query to get the metric :- total users, and dimensions:- month for Jan’17-Jul’17
```sql
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '731'
 AND
 totals.visits = 1
GROUP BY 
 1
 ORDER BY
 1

```

3.a Modify the query to include device category. Now transpose/Pivot the query results so that months are the rows and the device categories are the columns.
```sql
WITH raw AS (
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 device.deviceCategory AS device,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '731'
 AND
 totals.visits = 1
GROUP BY 
 1, 2
 ORDER BY
 1)

SELECT
 month_of_year,
 SUM(users) AS users,
 SUM(CASE WHEN device = 'mobile' THEN users END) AS mobile_users,
 SUM(CASE WHEN device = 'desktop' THEN users END) AS desktop_users,
 SUM(CASE WHEN device = 'tablet' THEN users END) AS tablet_users,
FROM
 raw
GROUP BY 
 1
ORDER BY 
1

```

3.b Modify the query to add a percentage of total column field
```sql
WITH raw AS (
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '731'
 AND
 totals.visits = 1
GROUP BY 
 1
 ORDER BY
 1)

SELECT 
 month_of_year,
 users,
 (SELECT SUM(users) FROM raw) AS total_users,
 ROUND(users / (SELECT SUM(users) FROM raw),2) AS pct_of_total
FROM 
 raw

```

3.c.1 Modify the query to add a running total field using joins

```sql
WITH raw AS (
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '731'
 AND
 totals.visits = 1
GROUP BY 
 1
 ORDER BY
 1)

SELECT 
 x.month_of_year,
 x.users,
 SUM(y.users) AS running_total
FROM 
 raw x
JOIN
 raw y
ON 
  x.month_of_year >= y.month_of_year
GROUP BY
1, 2
ORDER BY 
x.month_of_year 
```

3.c.2 Modify the query to add a running total field using window functions

```sql
WITH raw AS (
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '731'
 AND
 totals.visits = 1
GROUP BY 
 1
 ORDER BY
 1)

SELECT
 month_of_year,
 users,
 SUM(users) OVER (ORDER BY month_of_year) AS running_total 
FROM 
 raw
```

3.d Modify the query to add a field that is difference between the current row and the preceding row
```sql
WITH raw AS (
SELECT
 -- Dimensions
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 -- Metrics
 COUNT(DISTINCT fullVisitorId) AS users,
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170*`
WHERE
 _table_suffix BETWEEN '101' AND '731'
 AND
 totals.visits = 1
GROUP BY 
 1
 ORDER BY
 1)

SELECT
 month_of_year,
 users,
 LAG(users) OVER (ORDER BY month_of_year) AS prev_month_users,
 users - LAG(users) OVER (ORDER BY month_of_year) AS diff_prev_month
FROM 
 raw
ORDER BY 
 month_of_year 

```
