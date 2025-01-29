## Maven Analytics - MySQL Ecommerce and Web Analytics Project
### In-Depth Analysis of Maven Ecommerce and Web-Related Data

### This project is divided into two sections:

1. Middle Part: Data Analysis & Intermediate Queries
2. Final Part: Comprehensive Insights & Final Queries
### Middle Part

```sql
use mavenfuzzyfactory;
```

1.	Gsearch seems to be the biggest driver of our business. Could you pull monthly trends for gsearch sessions and orders so that we can showcase the growth there? 

```sql
SELECT
	YEAR(website_sessions.created_at) AS yr, 
	MONTH(website_sessions.created_at) AS mo, 
	COUNT(DISTINCT website_sessions.website_session_id) AS sessions, 
	COUNT(DISTINCT orders.order_id) AS orders, 
	COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT website_sessions.website_session_id) AS conv_rate
FROM website_sessions
	LEFT JOIN orders 
		ON orders.website_session_id = website_sessions.website_session_id
WHERE website_sessions.created_at < '2012-11-27'
	AND website_sessions.utm_source = 'gsearch'
GROUP BY 1,2;
```
`Result`
| yr   | mo | sessions | orders | conv_rate |
|------|----|----------|--------|-----------|
| 2012 | 3  | 1860     | 60     | 0.0323    |
| 2012 | 4  | 3574     | 92     | 0.0257    |
| 2012 | 5  | 3410     | 97     | 0.0284    |
| 2012 | 6  | 3578     | 121    | 0.0338    |
| 2012 | 7  | 3811     | 145    | 0.038     |
| 2012 | 8  | 4877     | 184    | 0.0377    |
| 2012 | 9  | 4491     | 188    | 0.0419    |
| 2012 | 10 | 5534     | 234    | 0.0423    |
| 2012 | 11 | 8889     | 373    | 0.042     |


2.	Next, it would be great to see a similar monthly trend for Gsearch, but this time splitting out nonbrand and brand campaigns separately. I am wondering if brand is picking up at all. If so, this is a good story to tell. 

```sql

SELECT
	YEAR(website_sessions.created_at) AS yr, 
    MONTH(website_sessions.created_at) AS mo, 
    COUNT(DISTINCT CASE WHEN utm_campaign = 'nonbrand' THEN website_sessions.website_session_id ELSE NULL END) AS nonbrand_sessions, 
    COUNT(DISTINCT CASE WHEN utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) AS nonbrand_orders,
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN website_sessions.website_session_id ELSE NULL END) AS brand_sessions, 
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN orders.order_id ELSE NULL END) AS brand_orders
FROM website_sessions
	LEFT JOIN orders 
		ON orders.website_session_id = website_sessions.website_session_id
WHERE website_sessions.created_at < '2012-11-27'
	AND website_sessions.utm_source = 'gsearch'
GROUP BY 1,2;
```
`Result`
| yr   | mo | nonbrand_sessions | nonbrand_orders | brand_sessions | brand_orders |
|------|----|-------------------|-----------------|----------------|--------------|
| 2012 | 3  | 1852              | 60              | 8              | 0            |
| 2012 | 4  | 3509              | 86              | 65             | 6            |
| 2012 | 5  | 3295              | 91              | 115            | 6            |
| 2012 | 6  | 3439              | 114             | 139            | 7            |
| 2012 | 7  | 3660              | 136             | 151            | 9            |
| 2012 | 8  | 4673              | 174             | 204            | 10           |
| 2012 | 9  | 4227              | 172             | 264            | 16           |
| 2012 | 10 | 5197              | 219             | 337            | 15           |
| 2012 | 11 | 8506              | 356             | 383            | 17           |



3.	While we’re on Gsearch, could you dive into nonbrand, and pull monthly sessions and orders split by device type? I want to flex our analytical muscles a little and show the board we really know our traffic sources. 

```sql
SELECT
	YEAR(website_sessions.created_at) AS yr, 
    MONTH(website_sessions.created_at) AS mo, 
    COUNT(DISTINCT CASE WHEN device_type = 'desktop' THEN website_sessions.website_session_id ELSE NULL END) AS desktop_sessions, 
    COUNT(DISTINCT CASE WHEN device_type = 'desktop' THEN orders.order_id ELSE NULL END) AS desktop_orders,
    COUNT(DISTINCT CASE WHEN device_type = 'mobile' THEN website_sessions.website_session_id ELSE NULL END) AS mobile_sessions, 
    COUNT(DISTINCT CASE WHEN device_type = 'mobile' THEN orders.order_id ELSE NULL END) AS mobile_orders
FROM website_sessions
	LEFT JOIN orders 
		ON orders.website_session_id = website_sessions.website_session_id
WHERE website_sessions.created_at < '2012-11-27'
	AND website_sessions.utm_source = 'gsearch'
    AND website_sessions.utm_campaign = 'nonbrand'
GROUP BY 1,2;
```
`Result`
| yr   | mo | desktop_sessions | desktop_orders | mobile_sessions | mobile_orders |
|------|----|------------------|----------------|-----------------|---------------|
| 2012 | 3  | 1128             | 50             | 724             | 10            |
| 2012 | 4  | 2139             | 75             | 1370            | 11            |
| 2012 | 5  | 2276             | 83             | 1019            | 8             |
| 2012 | 6  | 2673             | 106            | 766             | 8             |
| 2012 | 7  | 2774             | 122            | 886             | 14            |
| 2012 | 8  | 3515             | 165            | 1158            | 9             |
| 2012 | 9  | 3171             | 155            | 1056            | 17            |
| 2012 | 10 | 3934             | 201            | 1263            | 18            |
| 2012 | 11 | 6457             | 323            | 2049            | 33            |


4.	I’m worried that one of our more pessimistic board members may be concerned about the large % of traffic from Gsearch. Can you pull monthly trends for Gsearch, alongside monthly trends for each of our other channels?
 
```sql

-- first, finding the various utm sources and referers to see the traffic we're getting

SELECT DISTINCT 
	utm_source,
    utm_campaign, 
    http_referer
FROM website_sessions
WHERE website_sessions.created_at < '2012-11-27';
```
`Result`
| utm_source | utm_campaign | http_referer            |
|------------|--------------|-------------------------|
| gsearch    | nonbrand     | https://www.gsearch.com |
| NULL       | NULL         | NULL                    |
| gsearch    | brand        | https://www.gsearch.com |
| NULL       | NULL         | https://www.gsearch.com |
| bsearch    | brand        | https://www.bsearch.com |
| NULL       | NULL         | https://www.bsearch.com |
| bsearch    | nonbrand     | https://www.bsearch.com |

```sql

SELECT
	YEAR(website_sessions.created_at) AS yr, 
    MONTH(website_sessions.created_at) AS mo, 
    COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' THEN website_sessions.website_session_id ELSE NULL END) AS gsearch_paid_sessions,
    COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' THEN website_sessions.website_session_id ELSE NULL END) AS bsearch_paid_sessions,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN website_sessions.website_session_id ELSE NULL END) AS organic_search_sessions,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN website_sessions.website_session_id ELSE NULL END) AS direct_type_in_sessions
FROM website_sessions
	LEFT JOIN orders 
		ON orders.website_session_id = website_sessions.website_session_id
WHERE website_sessions.created_at < '2012-11-27'
GROUP BY 1,2;
```
`Result`
| yr   | mo | gsearch_paid_sessions | bsearch_paid_sessions | organic_search_sessions | direct_type_in_sessions |
|------|----|-----------------------|-----------------------|-------------------------|-------------------------|
| 2012 | 3  | 1860                  | 2                     | 8                       | 9                       |
| 2012 | 4  | 3574                  | 11                    | 78                      | 71                      |
| 2012 | 5  | 3410                  | 25                    | 150                     | 151                     |
| 2012 | 6  | 3578                  | 25                    | 190                     | 170                     |
| 2012 | 7  | 3811                  | 44                    | 207                     | 187                     |
| 2012 | 8  | 4877                  | 705                   | 265                     | 250                     |
| 2012 | 9  | 4491                  | 1439                  | 331                     | 285                     |
| 2012 | 10 | 5534                  | 1781                  | 428                     | 440                     |
| 2012 | 11 | 8889                  | 2840                  | 536                     | 485                     |



5.	I’d like to tell the story of our website performance improvements over the course of the first 8 months. Could you pull session to order conversion rates, by month? 

```sql
SELECT
	YEAR(website_sessions.created_at) AS yr, 
    MONTH(website_sessions.created_at) AS mo, 
    COUNT(DISTINCT website_sessions.website_session_id) AS sessions, 
    COUNT(DISTINCT orders.order_id) AS orders, 
    COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT website_sessions.website_session_id) AS conversion_rate    
FROM website_sessions
	LEFT JOIN orders 
		ON orders.website_session_id = website_sessions.website_session_id
WHERE website_sessions.created_at < '2012-11-27'
GROUP BY 1,2;
```
`Result`
| yr   | mo | sessions | orders | conversion_rate |
|------|----|----------|--------|-----------------|
| 2012 | 3  | 1879     | 60     | 0.0319          |
| 2012 | 4  | 3734     | 99     | 0.0265          |
| 2012 | 5  | 3736     | 108    | 0.0289          |
| 2012 | 6  | 3963     | 140    | 0.0353          |
| 2012 | 7  | 4249     | 169    | 0.0398          |
| 2012 | 8  | 6097     | 228    | 0.0374          |
| 2012 | 9  | 6546     | 287    | 0.0438          |
| 2012 | 10 | 8183     | 371    | 0.0453          |
| 2012 | 11 | 12750    | 561    | 0.044           |


6.	For the gsearch lander test, please estimate the revenue that test earned us (Hint: Look at the increase in CVR from the test (Jun 19 – Jul 28), and use nonbrand sessions and revenue since then to calculate incremental value)

```sql

SELECT
	MIN(website_pageview_id) AS first_test_pv
FROM website_pageviews
WHERE pageview_url = '/lander-1';
```
`Result`
| first_test_pv |
|---------------|
| 23504         |


```sql
-- for this step, we'll find the first pageview id 

CREATE TEMPORARY TABLE first_test_pageviews
SELECT
	website_pageviews.website_session_id, 
    MIN(website_pageviews.website_pageview_id) AS min_pageview_id
FROM website_pageviews 
	INNER JOIN website_sessions 
		ON website_sessions.website_session_id = website_pageviews.website_session_id
		AND website_sessions.created_at < '2012-07-28' -- prescribed by the assignment
		AND website_pageviews.website_pageview_id >= 23504 -- first page_view
        AND utm_source = 'gsearch'
        AND utm_campaign = 'nonbrand'
GROUP BY 
	website_pageviews.website_session_id;
```


```sql
-- next, we'll bring in the landing page to each session, like last time, but restricting to home or lander-1 this time

CREATE TEMPORARY TABLE nonbrand_test_sessions_w_landing_pages
SELECT 
	first_test_pageviews.website_session_id, 
    website_pageviews.pageview_url AS landing_page
FROM first_test_pageviews
	LEFT JOIN website_pageviews 
		ON website_pageviews.website_pageview_id = first_test_pageviews.min_pageview_id
WHERE website_pageviews.pageview_url IN ('/home','/lander-1');

--SELECT * FROM nonbrand_test_sessions_w_landing_pages limit 10;
```



```sql
-- then we make a table to bring in orders

CREATE TEMPORARY TABLE nonbrand_test_sessions_w_orders
SELECT
	nonbrand_test_sessions_w_landing_pages.website_session_id, 
    nonbrand_test_sessions_w_landing_pages.landing_page, 
    orders.order_id AS order_id

FROM nonbrand_test_sessions_w_landing_pages
LEFT JOIN orders 
	ON orders.website_session_id = nonbrand_test_sessions_w_landing_pages.website_session_id;
```

```sql

SELECT * FROM nonbrand_test_sessions_w_orders limit 10;
```
`Result`
| website_session_id | landing_page | order_id |
|--------------------|--------------|----------|
| 11683              | /lander-1    | NULL     |
| 11684              | /home        | NULL     |
| 11685              | /lander-1    | NULL     |
| 11686              | /lander-1    | NULL     |
| 11687              | /home        | NULL     |
| 11688              | /home        | NULL     |
| 11689              | /lander-1    | NULL     |
| 11690              | /home        | NULL     |
| 11691              | /lander-1    | NULL     |
| 11692              | /lander-1    | NULL     |

```sql

-- to find the difference between conversion rates

SELECT
	landing_page, 
    COUNT(DISTINCT website_session_id) AS sessions, 
    COUNT(DISTINCT order_id) AS orders,
    COUNT(DISTINCT order_id)/COUNT(DISTINCT website_session_id) AS conv_rate
FROM nonbrand_test_sessions_w_orders
GROUP BY 1; 

-- .0319 for /home, vs .0406 for /lander-1

-- .0087 additional orders per session
```
`Result`
| landing_page | sessions | orders | conv_rate |
|--------------|----------|--------|-----------|
| /home        | 2261     | 72     | 0.0318    |
| /lander-1    | 2316     | 94     | 0.0406    |


```sql
-- finding the most reent pageview for gsearch nonbrand where the traffic was sent to /home

SELECT 
	MAX(website_sessions.website_session_id) AS most_recent_gsearch_nonbrand_home_pageview 
FROM website_sessions 
	LEFT JOIN website_pageviews 
		ON website_pageviews.website_session_id = website_sessions.website_session_id
WHERE utm_source = 'gsearch'
	AND utm_campaign = 'nonbrand'
    AND pageview_url = '/home'
    AND website_sessions.created_at < '2012-11-27';
-- max website_session_id = 17145

-- 22,972 website sessions since the test

-- X .0087 incremental conversion = 202 incremental orders since 7/29

-- roughly 4 months, so roughly 50 extra orders per month. Not bad!
```
`Result`    
| most_recent_gsearch_nonbrand_home_pageview |
|--------------------------------------------|
| 17145                                      |


7.	For the landing page test you analyzed previously, it would be great to show a full conversion funnel from each of the two pages to orders. You can use the same time period you analyzed last time (Jun 19 – Jul 28).
 

```sql
SELECT
	website_sessions.website_session_id, 
    website_pageviews.pageview_url, 
    -- website_pageviews.created_at AS pageview_created_at, 
    CASE WHEN pageview_url = '/home' THEN 1 ELSE 0 END AS homepage,
    CASE WHEN pageview_url = '/lander-1' THEN 1 ELSE 0 END AS custom_lander,
    CASE WHEN pageview_url = '/products' THEN 1 ELSE 0 END AS products_page,
    CASE WHEN pageview_url = '/the-original-mr-fuzzy' THEN 1 ELSE 0 END AS mrfuzzy_page, 
    CASE WHEN pageview_url = '/cart' THEN 1 ELSE 0 END AS cart_page,
    CASE WHEN pageview_url = '/shipping' THEN 1 ELSE 0 END AS shipping_page,
    CASE WHEN pageview_url = '/billing' THEN 1 ELSE 0 END AS billing_page,
    CASE WHEN pageview_url = '/thank-you-for-your-order' THEN 1 ELSE 0 END AS thankyou_page
FROM website_sessions 
	LEFT JOIN website_pageviews 
		ON website_sessions.website_session_id = website_pageviews.website_session_id
WHERE website_sessions.utm_source = 'gsearch' 
	AND website_sessions.utm_campaign = 'nonbrand' 
    AND website_sessions.created_at < '2012-07-28'
		AND website_sessions.created_at > '2012-06-19'
ORDER BY 
	website_sessions.website_session_id,
    website_pageviews.created_at
    limit 10;
```
`Result`
| website_session_id | pageview_url           | homepage | custom_lander | products_page | mrfuzzy_page | cart_page | shipping_page | billing_page | thankyou_page |
|--------------------|------------------------|----------|---------------|---------------|--------------|-----------|---------------|--------------|---------------|
| 11683              | /lander-1              | 0        | 1             | 0             | 0            | 0         | 0             | 0            | 0             |
| 11684              | /home                  | 1        | 0             | 0             | 0            | 0         | 0             | 0            | 0             |
| 11685              | /lander-1              | 0        | 1             | 0             | 0            | 0         | 0             | 0            | 0             |
| 11686              | /lander-1              | 0        | 1             | 0             | 0            | 0         | 0             | 0            | 0             |
| 11686              | /products              | 0        | 0             | 1             | 0            | 0         | 0             | 0            | 0             |
| 11687              | /home                  | 1        | 0             | 0             | 0            | 0         | 0             | 0            | 0             |
| 11688              | /home                  | 1        | 0             | 0             | 0            | 0         | 0             | 0            | 0             |
| 11689              | /lander-1              | 0        | 1             | 0             | 0            | 0         | 0             | 0            | 0             |
| 11689              | /products              | 0        | 0             | 1             | 0            | 0         | 0             | 0            | 0             |
| 11689              | /the-original-mr-fuzzy | 0        | 0             | 0             | 1            | 0         | 0             | 0            | 0             |


```sql

CREATE TEMPORARY TABLE session_level_made_it_flagged
SELECT
	website_session_id, 
    MAX(homepage) AS saw_homepage, 
    MAX(custom_lander) AS saw_custom_lander,
    MAX(products_page) AS product_made_it, 
    MAX(mrfuzzy_page) AS mrfuzzy_made_it, 
    MAX(cart_page) AS cart_made_it,
    MAX(shipping_page) AS shipping_made_it,
    MAX(billing_page) AS billing_made_it,
    MAX(thankyou_page) AS thankyou_made_it
FROM(
SELECT
	website_sessions.website_session_id, 
    website_pageviews.pageview_url, 
    -- website_pageviews.created_at AS pageview_created_at, 
    CASE WHEN pageview_url = '/home' THEN 1 ELSE 0 END AS homepage,
    CASE WHEN pageview_url = '/lander-1' THEN 1 ELSE 0 END AS custom_lander,
    CASE WHEN pageview_url = '/products' THEN 1 ELSE 0 END AS products_page,
    CASE WHEN pageview_url = '/the-original-mr-fuzzy' THEN 1 ELSE 0 END AS mrfuzzy_page, 
    CASE WHEN pageview_url = '/cart' THEN 1 ELSE 0 END AS cart_page,
    CASE WHEN pageview_url = '/shipping' THEN 1 ELSE 0 END AS shipping_page,
    CASE WHEN pageview_url = '/billing' THEN 1 ELSE 0 END AS billing_page,
    CASE WHEN pageview_url = '/thank-you-for-your-order' THEN 1 ELSE 0 END AS thankyou_page
FROM website_sessions 
	LEFT JOIN website_pageviews 
		ON website_sessions.website_session_id = website_pageviews.website_session_id
WHERE website_sessions.utm_source = 'gsearch' 
	AND website_sessions.utm_campaign = 'nonbrand' 
    AND website_sessions.created_at < '2012-07-28'
		AND website_sessions.created_at > '2012-06-19'
ORDER BY 
	website_sessions.website_session_id,
    website_pageviews.created_at
) AS pageview_level

GROUP BY 
	website_session_id;
```
 
```sql

-- then this would produce the final output, part 1

SELECT
	CASE 
		WHEN saw_homepage = 1 THEN 'saw_homepage'
        WHEN saw_custom_lander = 1 THEN 'saw_custom_lander'
        ELSE 'uh oh... check logic' 
	END AS segment, 
    COUNT(DISTINCT website_session_id) AS sessions,
    COUNT(DISTINCT CASE WHEN product_made_it = 1 THEN website_session_id ELSE NULL END) AS to_products,
    COUNT(DISTINCT CASE WHEN mrfuzzy_made_it = 1 THEN website_session_id ELSE NULL END) AS to_mrfuzzy,
    COUNT(DISTINCT CASE WHEN cart_made_it = 1 THEN website_session_id ELSE NULL END) AS to_cart,
    COUNT(DISTINCT CASE WHEN shipping_made_it = 1 THEN website_session_id ELSE NULL END) AS to_shipping,
    COUNT(DISTINCT CASE WHEN billing_made_it = 1 THEN website_session_id ELSE NULL END) AS to_billing,
    COUNT(DISTINCT CASE WHEN thankyou_made_it = 1 THEN website_session_id ELSE NULL END) AS to_thankyou
FROM session_level_made_it_flagged 
GROUP BY 1;
```
`Result`
| segment           | sessions | to_products | to_mrfuzzy | to_cart | to_shipping | to_billing | to_thankyou |
|-------------------|----------|-------------|------------|---------|-------------|------------|-------------|
| saw_custom_lander | 2316     | 1083        | 772        | 348     | 231         | 197        | 94          |
| saw_homepage      | 2261     | 942         | 684        | 296     | 200         | 168        | 72          |


```sql
-- then this as final output part 2 

SELECT
	CASE 
		WHEN saw_homepage = 1 THEN 'saw_homepage'
        WHEN saw_custom_lander = 1 THEN 'saw_custom_lander'
        ELSE 'uh oh... check logic' 
	END AS segment, 
	COUNT(DISTINCT CASE WHEN product_made_it = 1 THEN website_session_id ELSE NULL END)/COUNT(DISTINCT website_session_id) AS lander_click_rt,
    COUNT(DISTINCT CASE WHEN mrfuzzy_made_it = 1 THEN website_session_id ELSE NULL END)/COUNT(DISTINCT CASE WHEN product_made_it = 1 THEN website_session_id ELSE NULL END) AS products_click_rt,
    COUNT(DISTINCT CASE WHEN cart_made_it = 1 THEN website_session_id ELSE NULL END)/COUNT(DISTINCT CASE WHEN mrfuzzy_made_it = 1 THEN website_session_id ELSE NULL END) AS mrfuzzy_click_rt,
    COUNT(DISTINCT CASE WHEN shipping_made_it = 1 THEN website_session_id ELSE NULL END)/COUNT(DISTINCT CASE WHEN cart_made_it = 1 THEN website_session_id ELSE NULL END) AS cart_click_rt,
    COUNT(DISTINCT CASE WHEN billing_made_it = 1 THEN website_session_id ELSE NULL END)/COUNT(DISTINCT CASE WHEN shipping_made_it = 1 THEN website_session_id ELSE NULL END) AS shipping_click_rt,
    COUNT(DISTINCT CASE WHEN thankyou_made_it = 1 THEN website_session_id ELSE NULL END)/COUNT(DISTINCT CASE WHEN billing_made_it = 1 THEN website_session_id ELSE NULL END) AS billing_click_rt
FROM session_level_made_it_flagged
GROUP BY 1;
```
`Result`
| segment           | sessions | to_products | to_mrfuzzy | to_cart | to_shipping | to_billing | to_thankyou |
|-------------------|----------|-------------|------------|---------|-------------|------------|-------------|
| saw_custom_lander | 2316     | 1083        | 772        | 348     | 231         | 197        | 94          |
| saw_homepage      | 2261     | 942         | 684        | 296     | 200         | 168        | 72          |


8.	I’d love for you to quantify the impact of our billing test, as well. Please analyze the lift generated from the test (Sep 10 – Nov 10), in terms of revenue per billing page session, and then pull the number of billing page sessions for the past month to understand monthly impact.


```sql

SELECT
	billing_version_seen, 
    COUNT(DISTINCT website_session_id) AS sessions, 
    SUM(price_usd)/COUNT(DISTINCT website_session_id) AS revenue_per_billing_page_seen
 FROM( 
SELECT 
	website_pageviews.website_session_id, 
    website_pageviews.pageview_url AS billing_version_seen, 
    orders.order_id, 
    orders.price_usd
FROM website_pageviews 
	LEFT JOIN orders
		ON orders.website_session_id = website_pageviews.website_session_id
WHERE website_pageviews.created_at > '2012-09-10' -- prescribed in assignment
	AND website_pageviews.created_at < '2012-11-10' -- prescribed in assignment
    AND website_pageviews.pageview_url IN ('/billing','/billing-2')
) AS billing_pageviews_and_order_data
GROUP BY 1
;
-- $22.83 revenue per billing page seen for the old version
-- $31.34 for the new version
-- LIFT: $8.51 per billing page view
```
`Result`
| billing_version_seen | sessions | revenue_per_billing_page_seen |
|----------------------|----------|-------------------------------|
| /billing             | 657      | 22.826484                     |
| /billing-2           | 654      | 31.339297                     |


```sql
SELECT 
	COUNT(website_session_id) AS billing_sessions_past_month
FROM website_pageviews 
WHERE website_pageviews.pageview_url IN ('/billing','/billing-2') 
	AND created_at BETWEEN '2012-10-27' AND '2012-11-27' -- past month

-- 1,194 billing sessions past month
-- LIFT: $8.51 per billing session
-- VALUE OF BILLING TEST: $10,160 over the past month
```
`Result`
| billing_sessions_past_month |
|-----------------------------|
| 1193                        |



## Final Project
```sql
USE mavenfuzzyfactory;
```


1. First, I’d like to show our volume growth. Can you pull overall session and order volume, trended by quarter for the life of the business? Since the most recent quarter is incomplete, you can decide how to handle it.


```sql

SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
	COUNT(DISTINCT website_sessions.website_session_id) AS sessions, 
    COUNT(DISTINCT orders.order_id) AS orders
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2;
```
`Result`
| yr   | qtr | sessions | orders |
|------|-----|----------|--------|
| 2012 | 1   | 1879     | 60     |
| 2012 | 2   | 11433    | 347    |
| 2012 | 3   | 16892    | 684    |
| 2012 | 4   | 32266    | 1495   |
| 2013 | 1   | 19833    | 1273   |
| 2013 | 2   | 24745    | 1718   |
| 2013 | 3   | 27663    | 1840   |
| 2013 | 4   | 40540    | 2616   |
| 2014 | 1   | 46779    | 3069   |
| 2014 | 2   | 53129    | 3848   |
| 2014 | 3   | 57141    | 4035   |
| 2014 | 4   | 76373    | 5908   |
| 2015 | 1   | 64198    | 5420   |


2. Next, let’s showcase all of our efficiency improvements. I would love to show quarterly figures since we launched, for session-to-order conversion rate, revenue per order, and revenue per session. 


```sql

SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
	COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT website_sessions.website_session_id) AS session_to_order_conv_rate, 
    SUM(price_usd)/COUNT(DISTINCT orders.order_id) AS revenue_per_order, 
    SUM(price_usd)/COUNT(DISTINCT website_sessions.website_session_id) AS revenue_per_session
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2;
```
`Result`
| yr   | qtr | session_to_order_conv_rate | revenue_per_order | revenue_per_session |
|------|-----|----------------------------|-------------------|---------------------|
| 2012 | 1   | 0.0319                     | 49.99             | 1.596275            |
| 2012 | 2   | 0.0304                     | 49.99             | 1.517233            |
| 2012 | 3   | 0.0405                     | 49.99             | 2.024222            |
| 2012 | 4   | 0.0463                     | 49.99             | 2.316217            |
| 2013 | 1   | 0.0642                     | 52.142396         | 3.346809            |
| 2013 | 2   | 0.0694                     | 51.538312         | 3.578211            |
| 2013 | 3   | 0.0665                     | 51.734533         | 3.441114            |
| 2013 | 4   | 0.0645                     | 54.715688         | 3.530741            |
| 2014 | 1   | 0.0656                     | 62.160684         | 4.078136            |
| 2014 | 2   | 0.0724                     | 64.374207         | 4.662462            |
| 2014 | 3   | 0.0706                     | 64.494949         | 4.554298            |
| 2014 | 4   | 0.0774                     | 63.793497         | 4.934885            |
| 2015 | 1   | 0.0844                     | 62.799917         | 5.301965            |


3. I’d like to show how we’ve grown specific channels. Could you pull a quarterly view of orders from Gsearch nonbrand, Bsearch nonbrand, brand search overall, organic search, and direct type-in?


```sql

SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
    COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) AS gsearch_nonbrand_orders, 
    COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) AS bsearch_nonbrand_orders, 
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN orders.order_id ELSE NULL END) AS brand_search_orders,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN orders.order_id ELSE NULL END) AS organic_search_orders,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN orders.order_id ELSE NULL END) AS direct_type_in_orders
    
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2;
```
`Result`
| yr   | qtr | gsearch_nonbrand_orders | bsearch_nonbrand_orders | brand_search_orders | organic_search_orders | direct_type_in_orders |
|------|-----|-------------------------|-------------------------|---------------------|-----------------------|-----------------------|
| 2012 | 1   | 60                      | 0                       | 0                   | 0                     | 0                     |
| 2012 | 2   | 291                     | 0                       | 20                  | 15                    | 21                    |
| 2012 | 3   | 482                     | 82                      | 48                  | 40                    | 32                    |
| 2012 | 4   | 913                     | 311                     | 88                  | 94                    | 89                    |
| 2013 | 1   | 766                     | 183                     | 108                 | 125                   | 91                    |
| 2013 | 2   | 1114                    | 237                     | 114                 | 134                   | 119                   |
| 2013 | 3   | 1132                    | 245                     | 153                 | 167                   | 143                   |
| 2013 | 4   | 1657                    | 291                     | 248                 | 223                   | 197                   |
| 2014 | 1   | 1667                    | 344                     | 354                 | 338                   | 311                   |
| 2014 | 2   | 2208                    | 427                     | 410                 | 436                   | 367                   |
| 2014 | 3   | 2259                    | 434                     | 432                 | 445                   | 402                   |
| 2014 | 4   | 3248                    | 683                     | 615                 | 605                   | 532                   |
| 2015 | 1   | 3025                    | 581                     | 622                 | 640                   | 552                   |



4. Next, let’s show the overall session-to-order conversion rate trends for those same channels, by quarter. Please also make a note of any periods where we made major improvements or optimizations.


```sql

SELECT 
	YEAR(website_sessions.created_at) AS yr,
	QUARTER(website_sessions.created_at) AS qtr, 
    COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END)
		/COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' AND utm_campaign = 'nonbrand' THEN website_sessions.website_session_id ELSE NULL END) AS gsearch_nonbrand_conv_rt, 
    COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' AND utm_campaign = 'nonbrand' THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' AND utm_campaign = 'nonbrand' THEN website_sessions.website_session_id ELSE NULL END) AS bsearch_nonbrand_conv_rt, 
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN website_sessions.website_session_id ELSE NULL END) AS brand_search_conv_rt,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NOT NULL THEN website_sessions.website_session_id ELSE NULL END) AS organic_search_conv_rt,
    COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN orders.order_id ELSE NULL END) 
		/COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN website_sessions.website_session_id ELSE NULL END) AS direct_type_in_conv_rt
FROM website_sessions 
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
GROUP BY 1,2
ORDER BY 1,2;
```
`Result`
| yr   | qtr | gsearch_nonbrand_conv_rt | bsearch_nonbrand_conv_rt | brand_search_conv_rt | organic_search_conv_rt | direct_type_in_conv_rt |
|------|-----|--------------------------|--------------------------|----------------------|------------------------|------------------------|
| 2012 | 1   | 0.0324                   | NULL                     | 0                    | 0                      | 0                      |
| 2012 | 2   | 0.0284                   | NULL                     | 0.0526               | 0.0359                 | 0.0536                 |
| 2012 | 3   | 0.0384                   | 0.0408                   | 0.0602               | 0.0498                 | 0.0443                 |
| 2012 | 4   | 0.0436                   | 0.0497                   | 0.0531               | 0.0539                 | 0.0537                 |
| 2013 | 1   | 0.0612                   | 0.0693                   | 0.0703               | 0.0753                 | 0.0614                 |
| 2013 | 2   | 0.0685                   | 0.069                    | 0.0679               | 0.076                  | 0.0735                 |
| 2013 | 3   | 0.0639                   | 0.0697                   | 0.0703               | 0.0734                 | 0.0719                 |
| 2013 | 4   | 0.0629                   | 0.0601                   | 0.0801               | 0.0694                 | 0.0647                 |
| 2014 | 1   | 0.0693                   | 0.0704                   | 0.0839               | 0.0756                 | 0.0765                 |
| 2014 | 2   | 0.0702                   | 0.0695                   | 0.0804               | 0.0797                 | 0.0738                 |
| 2014 | 3   | 0.0703                   | 0.0698                   | 0.0756               | 0.0733                 | 0.0702                 |
| 2014 | 4   | 0.0782                   | 0.0841                   | 0.0812               | 0.0784                 | 0.0748                 |
| 2015 | 1   | 0.0861                   | 0.085                    | 0.0852               | 0.0821                 | 0.0775                 |



5. We’ve come a long way since the days of selling a single product. Let’s pull monthly trending for revenue and margin by product, along with total sales and revenue. Note anything you notice about seasonality.


```sql

SELECT
	YEAR(created_at) AS yr, 
    MONTH(created_at) AS mo, 
    SUM(CASE WHEN product_id = 1 THEN price_usd ELSE NULL END) AS mrfuzzy_rev,
    SUM(CASE WHEN product_id = 1 THEN price_usd - cogs_usd ELSE NULL END) AS mrfuzzy_marg,
    SUM(CASE WHEN product_id = 2 THEN price_usd ELSE NULL END) AS lovebear_rev,
    SUM(CASE WHEN product_id = 2 THEN price_usd - cogs_usd ELSE NULL END) AS lovebear_marg,
    SUM(CASE WHEN product_id = 3 THEN price_usd ELSE NULL END) AS birthdaybear_rev,
    SUM(CASE WHEN product_id = 3 THEN price_usd - cogs_usd ELSE NULL END) AS birthdaybear_marg,
    SUM(CASE WHEN product_id = 4 THEN price_usd ELSE NULL END) AS minibear_rev,
    SUM(CASE WHEN product_id = 4 THEN price_usd - cogs_usd ELSE NULL END) AS minibear_marg,
    SUM(price_usd) AS total_revenue,  
    SUM(price_usd - cogs_usd) AS total_margin
FROM order_items 
GROUP BY 1,2
ORDER BY 1,2
limit 10;
```
`Result`
| yr   | mo | mrfuzzy_rev | mrfuzzy_marg | lovebear_rev | lovebear_marg | birthdaybear_rev | birthdaybear_marg | minibear_rev | minibear_marg | total_revenue | total_margin |
|------|----|-------------|--------------|--------------|---------------|------------------|-------------------|--------------|---------------|---------------|--------------|
| 2012 | 3  | 2999.4      | 1830         | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 2999.4        | 1830         |
| 2012 | 4  | 4949.01     | 3019.5       | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 4949.01       | 3019.5       |
| 2012 | 5  | 5398.92     | 3294         | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 5398.92       | 3294         |
| 2012 | 6  | 6998.6      | 4270         | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 6998.6        | 4270         |
| 2012 | 7  | 8448.31     | 5154.5       | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 8448.31       | 5154.5       |
| 2012 | 8  | 11397.72    | 6954         | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 11397.72      | 6954         |
| 2012 | 9  | 14347.13    | 8753.5       | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 14347.13      | 8753.5       |
| 2012 | 10 | 18546.29    | 11315.5      | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 18546.29      | 11315.5      |
| 2012 | 11 | 30893.82    | 18849        | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 30893.82      | 18849        |
| 2012 | 12 | 25294.94    | 15433        | NULL         | NULL          | NULL             | NULL              | NULL         | NULL          | 25294.94      | 15433        |


6. Let’s dive deeper into the impact of introducing new products. Please pull monthly sessions to the /products page, and show how the % of those sessions clicking through another page has changed over time, along with a view of how conversion from /products to placing an order has improved.


```sql

-- first, identifying all the views of the /products page
CREATE TEMPORARY TABLE products_pageviews
SELECT
	website_session_id, 
    website_pageview_id, 
    created_at AS saw_product_page_at

FROM website_pageviews 
WHERE pageview_url = '/products';
```

```sql

SELECT 
	YEAR(saw_product_page_at) AS yr, 
    MONTH(saw_product_page_at) AS mo,
    COUNT(DISTINCT products_pageviews.website_session_id) AS sessions_to_product_page, 
    COUNT(DISTINCT website_pageviews.website_session_id) AS clicked_to_next_page, 
    COUNT(DISTINCT website_pageviews.website_session_id)/COUNT(DISTINCT products_pageviews.website_session_id) AS clickthrough_rt,
    COUNT(DISTINCT orders.order_id) AS orders,
    COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT products_pageviews.website_session_id) AS products_to_order_rt
FROM products_pageviews
	LEFT JOIN website_pageviews 
		ON website_pageviews.website_session_id = products_pageviews.website_session_id -- same session
        AND website_pageviews.website_pageview_id > products_pageviews.website_pageview_id -- they had another page AFTER
	LEFT JOIN orders 
		ON orders.website_session_id = products_pageviews.website_session_id
GROUP BY 1,2
limit 10;
```
`Result`
| yr   | mo | sessions_to_product_page | clicked_to_next_page | clickthrough_rt | orders | products_to_order_rt |
|------|----|--------------------------|----------------------|-----------------|--------|----------------------|
| 2012 | 3  | 743                      | 530                  | 0.7133          | 60     | 0.0808               |
| 2012 | 4  | 1447                     | 1029                 | 0.7111          | 99     | 0.0684               |
| 2012 | 5  | 1584                     | 1135                 | 0.7165          | 108    | 0.0682               |
| 2012 | 6  | 1752                     | 1247                 | 0.7118          | 140    | 0.0799               |
| 2012 | 7  | 2018                     | 1438                 | 0.7126          | 169    | 0.0837               |
| 2012 | 8  | 3012                     | 2198                 | 0.7297          | 228    | 0.0757               |
| 2012 | 9  | 3126                     | 2258                 | 0.7223          | 287    | 0.0918               |
| 2012 | 10 | 4030                     | 2948                 | 0.7315          | 371    | 0.0921               |
| 2012 | 11 | 6743                     | 4849                 | 0.7191          | 618    | 0.0917               |
| 2012 | 12 | 5013                     | 3620                 | 0.7221          | 506    | 0.1009               |


7. We made our 4th product available as a primary product on December 05, 2014 (it was previously only a cross-sell item). Could you please pull sales data since then, and show how well each product cross-sells from one another?


```sql

CREATE TEMPORARY TABLE primary_products
SELECT 
	order_id, 
    primary_product_id, 
    created_at AS ordered_at
FROM orders 
WHERE created_at > '2014-12-05' -- when the 4th product was added (says so in question);
```

```sql

SELECT
	primary_products.*, 
    order_items.product_id AS cross_sell_product_id
FROM primary_products
	LEFT JOIN order_items 
		ON order_items.order_id = primary_products.order_id
        AND order_items.is_primary_item = 0
        limit 10; -- only bringing in cross-sells;
```
`Result`
| order_id | primary_product_id | ordered_at     | cross_sell_product_id |
|----------|--------------------|----------------|-----------------------|
| 25060    | 1                  | 12/5/2014 1:10 | 4                     |
| 25061    | 1                  | 12/5/2014 1:18 | 4                     |
| 25062    | 1                  | 12/5/2014 2:31 | 2                     |
| 25063    | 3                  | 12/5/2014 2:50 | NULL                  |
| 25064    | 1                  | 12/5/2014 2:54 | 3                     |
| 25065    | 1                  | 12/5/2014 4:04 | 3                     |
| 25066    | 1                  | 12/5/2014 4:17 | 4                     |
| 25067    | 1                  | 12/5/2014 5:29 | 3                     |
| 25068    | 2                  | 12/5/2014 5:51 | NULL                  |
| 25069    | 2                  | 12/5/2014 6:42 | NULL                  |


```sql

SELECT 
	primary_product_id, 
    COUNT(DISTINCT order_id) AS total_orders, 
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 1 THEN order_id ELSE NULL END) AS _xsold_p1,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 2 THEN order_id ELSE NULL END) AS _xsold_p2,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 3 THEN order_id ELSE NULL END) AS _xsold_p3,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 4 THEN order_id ELSE NULL END) AS _xsold_p4,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 1 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p1_xsell_rt,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 2 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p2_xsell_rt,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 3 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p3_xsell_rt,
    COUNT(DISTINCT CASE WHEN cross_sell_product_id = 4 THEN order_id ELSE NULL END)/COUNT(DISTINCT order_id) AS p4_xsell_rt
FROM
(
SELECT
	primary_products.*, 
    order_items.product_id AS cross_sell_product_id
FROM primary_products
	LEFT JOIN order_items 
		ON order_items.order_id = primary_products.order_id
        AND order_items.is_primary_item = 0 -- only bringing in cross-sells
) AS primary_w_cross_sell
GROUP BY 1;
```
`Result`
| primary_product_id | total_orders | _xsold_p1 | _xsold_p2 | _xsold_p3 | _xsold_p4 | p1_xsell_rt | p2_xsell_rt | p3_xsell_rt | p4_xsell_rt |
|--------------------|--------------|-----------|-----------|-----------|-----------|-------------|-------------|-------------|-------------|
| 1                  | 4467         | 0         | 238       | 553       | 933       | 0           | 0.0533      | 0.1238      | 0.2089      |
| 2                  | 1277         | 25        | 0         | 40        | 260       | 0.0196      | 0           | 0.0313      | 0.2036      |
| 3                  | 929          | 84        | 40        | 0         | 208       | 0.0904      | 0.0431      | 0           | 0.2239      |
| 4                  | 581          | 16        | 9         | 22        | 0         | 0.0275      | 0.0155      | 0.0379      | 0           |
