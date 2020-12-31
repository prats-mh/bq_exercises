# bq_exercises

1. Write a query to get the metrics :- total users, sessions, pageviews, pageviews/sessions on 23rd June 2017

```sql
SELECT
 format_date('%Y%m',parse_date('%Y%m%d', date)) AS month_of_year,
 COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))) AS sessions,
 COUNT(DISTINCT fullVisitorId) AS users,
 SUM(totals.pageviews) AS pageviews,
 ROUND(SUM(totals.pageviews) / COUNT(DISTINCT CONCAT(fullVisitorId, CAST(visitStartTime AS string))),2) AS pages_per_sessions
FROM 
 `bigquery-public-data.google_analytics_sample.ga_sessions_20170623`
WHERE
 totals.visits = 1
GROUP BY
 1
```
