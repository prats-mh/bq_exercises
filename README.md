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
