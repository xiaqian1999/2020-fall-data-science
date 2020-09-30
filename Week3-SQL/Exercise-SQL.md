
# SQL:  Structured Query Language  Exercise

### Getting Started
1. Go to BigQuery UI https://console.cloud.google.com/bigquery
2. Add in the public data sets. 
	3. Click the Add Data icon
	4. Add any dataset
	5. `bigquery-public-data` should become visible and populate in the BigQuery UI. 
3. Add your queries where it says [YOUR QUERY HERE].
4. Make sure you add your query in between the triple tick marks. 
---

For this section of the exercise we will be using the `bigquery-public-data.austin_311.311_service_requests`  table. 

5. Write a query that tells us how many rows are in the table. 
	```
    SELECT
      count(*)
    FROM
      `bigquery-public-data.austin_311.311_service_requests` 

	```

7. Write a query that tells us how many _distinct_ values there are in the complaint_description column.
	``` 
	SELECT
      count(DISTINCT complaint_description)
    FROM
      `bigquery-public-data.austin_311.311_service_requests` 
	```
  
8. Write a query that counts how many times each owning_department appears in the table and orders them from highest to lowest. 
	``` 
	SELECT
      owning_department,
      COUNT(owning_department) AS owning_counts,
      COUNT( DISTINCT owning_department) AS owning_department_counts
    FROM
      `bigquery-public-data.austin_311.311_service_requests`
    GROUP BY
      owning_department
    ORDER BY
      owning_counts DESC
	```

9. Write a query that lists the top 5 complaint_description that appear most and the amount of times they appear in this table. (hint... limit)
	```
	SELECT
      complaint_description,
      COUNT(complaint_description) AS complaint_counts,
    FROM
      `bigquery-public-data.austin_311.311_service_requests`
    GROUP BY
      complaint_description 
    ORDER BY
      complaint_counts DESC
    LIMIT 5
	  ```
10. Write a query that lists and counts all the complaint_description, just for the where the owning_department is 'Animal Services Office'.
	```
	SELECT
      complaint_description,
      COUNT(complaint_description) AS complaint_counts,
    FROM
      `bigquery-public-data.austin_311.311_service_requests`
    WHERE
      owning_department = 'Animal Services Office'
    GROUP BY
      complaint_description
    ORDER BY
      complaint_counts DESC
	```

11. Write a query to check if there are any duplicate values in the unique_key column (hint.. There are two was to do this, one is to use a temporary table for the groupby, then filter for values that have more than one count, or, using just one table but including the  `having` function). 
	```
	WITH
      T AS (
      SELECT
        unique_key,
        COUNT(unique_key) AS counts
      FROM
        `bigquery-public-data.austin_311.311_service_requests`
      GROUP BY
        unique_key)
    SELECT
      *
    FROM
      T
    WHERE
      counts > 1
	```


### For the next question, use the `census_bureau_usa` tables.

1. Write a query that returns each zipcode and their population for 2000 and 2010. 
	```
	WITH
      population_2000 AS(
      SELECT
        zipcode,
        SUM(population) AS count_2000,
      FROM
        `bigquery-public-data.census_bureau_usa.population_by_zip_2000`
      GROUP BY
        zipcode ),
      population_2010 AS(
      SELECT
        zipcode,
        SUM(population) AS count_2010,
      FROM
        `bigquery-public-data.census_bureau_usa.population_by_zip_2010`
      GROUP BY
        zipcode )
    SELECT
      A.zipcode,
      A.count_2000,
      B.count_2010
    FROM
      population_2000 AS A
    JOIN
      population_2010 AS B
    ON
      A.zipcode = B.zipcode
	```

### For the next section, use the  `bigquery-public-data.google_political_ads.advertiser_weekly_spend` table.
1. Using the `advertiser_weekly_spend` table, write a query that finds the advertiser_name that spent the most in usd. 
	```
	SELECT advertiser_name, spend_usd 
    FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend` 
    ORDER BY spend_usd DESC
    LIMIT 1
	```
2. Who was the 6th highest spender? (No need to insert query here, just type in the answer.)
	```
	MIKE BLOOMBERG 2020 INC
	```

3. What week_start_date had the highest spend? (No need to insert query here, just type in the answer.)
	```
	2020-02-23
	```

4. Using the `advertiser_weekly_spend` table, write a query that returns the sum of spend by week (using week_start_date) in usd for the month of August in 2020 only. 
	```
	WITH
      T AS(
      SELECT
        week_start_date,
        SUM(spend_usd) AS usd_count
      FROM
        `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
      GROUP BY
        week_start_date)
    SELECT
      *
    FROM
      T
    WHERE
      week_start_date >= '2020-08-01'
      AND week_start_date <= '2020-08-31'
    ORDER BY
      week_start_date
	```
6.  How many ads did the 'TOM STEYER 2020' campaign run? (No need to insert query here, just type in the answer.)
	```
	50
	```
7. Write a query that has, in the US region only, the total spend in usd for each advertiser_name and how many ads they ran. (Hint, you're going to have to join tables for this one). 
	```
		WITH
          ads_run AS(
          SELECT
            advertiser_name,
            COUNT(advertiser_name) AS ads
          FROM
            `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
          GROUP BY
            advertiser_name ),
          total_spend AS(
          SELECT
            advertiser_name,
            SUM(spend_usd) AS sumSpend
          FROM
            `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
          GROUP BY
            advertiser_name )
        SELECT
          A.advertiser_name,
          A.ads,
          B.sumSpend
        FROM
          ads_run AS A
        JOIN
          total_spend AS B
        ON
          A.advertiser_name = B.advertiser_name
	```
8. For each advertiser_name, find the average spend per ad. 
	```
	SELECT
      advertiser_name,
      AVG(spend_bgn+ spend_czk+spend_dkk+spend_eur +spend_gbp +spend_hrk +spend_huf +spend_inr +spend_nzd +spend_pln +spend_ron +spend_sek +spend_usd)/13.0 AS averageSpend
    FROM
      `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
    GROUP BY
      advertiser_name
    ORDER BY
      advertiser_name
	```
10. Which advertiser_name had the lowest average spend per ad that was at least above 0. 
	``` 
	WITH
      T AS (
      SELECT
        advertiser_name,
        AVG(spend_bgn+ spend_czk+spend_dkk+spend_eur +spend_gbp +spend_hrk +spend_huf +spend_inr +spend_nzd +spend_pln +spend_ron +spend_sek +spend_usd)/13.0 AS averageSpend
      FROM
        `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
      GROUP BY
        advertiser_name
      ORDER BY
        averageSpend )
    SELECT
      *
    FROM
      T
    WHERE
      averageSpend > 0
    LIMIT
      1
	```
## For this next section, use the `new_york_citibike` datasets.

1. Who went on more bike trips, Males or Females?
	```
	SELECT
    count(case when gender='male' then 1 end ) as male_count,
    count(case when gender='female' then 1 end) as female_count,
    FROM `bigquery-public-data.new_york_citibike.citibike_trips` 
	```
2. What was the average, shortest, and longest bike trip taken in minutes?
	```
	SELECT
      MAX(tripduration / 60) AS longestTrip,
      MIN(tripduration / 60) AS shortestTrip,
      AVG(tripduration / 60) AS averageTrip
    FROM
      `bigquery-public-data.new_york_citibike.citibike_trips`
	```

3. Write a query that, for every station_name, has the amount of trips that started there and the amount of trips that ended there. (Hint, use two temporary tables, one that counts the amount of starts, the other that counts the number of ends, and then join the two.) 
	```
	WITH
      starts AS(
      SELECT
        start_station_name,
        COUNT(start_station_name) AS start
      FROM
        `bigquery-public-data.new_york_citibike.citibike_trips`
      GROUP BY
        start_station_name),
      ends AS(
      SELECT
        end_station_name,
        COUNT(end_station_name) AS ended
      FROM
        `bigquery-public-data.new_york_citibike.citibike_trips`
      GROUP BY
        end_station_name)
    SELECT
      A.start_station_name,
      A.start,
      B.ended
    FROM
      starts AS A
    JOIN
      ends AS B
    ON
      A.start_station_name = B.end_station_name
	```
# The next section is the Google Colab section.  
1. Open up this [this Colab notebook](https://colab.research.google.com/drive/1kHdTtuHTPEaMH32GotVum41YVdeyzQ74?usp=sharing).
2. Save a copy of it in your drive. 
3. Rename your saved version with your initials. 
4. Click the 'Share' button on the top right.  
5. Change the permissions so anyone with link can view. 
6. Copy the link and paste it right below this line. 
	* YOUR LINK:  ________________________________
9. Complete the two questions in the colab notebook file. 
