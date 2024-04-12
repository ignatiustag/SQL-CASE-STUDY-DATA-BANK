# Data Allocation Challenge
For this challenge question — I have been requested to generate the data elements needed to help the Data Bank team estimate how much data will be needed for each option.

I adopted a conversion of;

  - Customers with a positive balance are entitled to 3GB+500MB Bonus/$100

- Customers with a credit balance are entitled to 500MB

This means that for every $100, a customer with a positive balance receives 3.5GB while a customer in debt (Credit balance) receives 500MB

## Option 1: Data is allocated based off the amount of money at the end of the previous month
```pgsql
WITH prev_clb AS (
	SELECT
        *,
        LAG(closing_balance) OVER (PARTITION BY customer_id ORDER BY month_no) AS prev_closing_balance
     FROM Customer_closing_balance_each_month
),

data_conversion AS (
	SELECT 
		*,
 		CASE 
 			WHEN prev_closing_balance < 0 THEN 0.5
 			ELSE ((prev_closing_balance/100:: float)*3) + 0.5
 			END AS data_alloc_gb
 	FROM prev_clb
)

SELECT 
	month_name,
	CAST(SUM(data_alloc_gb) AS NUMERIC (10,2)) AS Total_data
FROM data_conversion
GROUP BY 1
ORDER BY 2 DESC;
```
<details><summary>Query Result</summary>

![alt text](<.images/Screenshot (62).png>)
</details>

## Option 2: Data is allocated on the average amount of money kept in the account in the previous 30 days
```pgsql
WITH Avg_amount AS (
	SELECT
		customer_id,
		month_no,
		ROUND(AVG(running_balance), 1) AS avg_running_balance
	FROM customer_running_balance
	GROUP BY 1,2
	ORDER BY 1,2
),

prev_arb AS (
	SELECT
        *,
        LAG(avg_running_balance) OVER (PARTITION BY customer_id ORDER BY month_no) AS prev_avg_running_balance
     FROM Avg_amount
),

data_conversion AS (
	SELECT 
		*,
 		CASE 
 			WHEN prev_avg_running_balance < 0 THEN 0.5
 			ELSE ((prev_avg_running_balance/100:: float)*3) + 0.5
 			END AS data_alloc_gb
 	FROM prev_arb
)

SELECT 
	TO_CHAR(TO_DATE(month_no::text, 'MM'), 'Month') AS month_name,
	CAST(SUM(data_alloc_gb) AS NUMERIC (10,2)) AS Total_data
FROM data_conversion
GROUP BY 1
ORDER BY 2 DESC;
```
<details><summary>Query Result</summary>

![alt text](<.images/Screenshot (64).png>)
</details>

## Option 3: Data is updated real-time
```pgsql
WITH data_conversion AS (
	SELECT 
		*,
 		CASE 
 			WHEN running_balance < 0 THEN 0.5
 			ELSE ((running_balance/100:: float)*3) + 0.5
 			END AS data_alloc_gb
 	FROM customer_running_balance
)

SELECT 
	TO_CHAR(TO_DATE(month_no::text, 'MM'), 'Month') AS month_name,
	CAST(SUM(data_alloc_gb) AS NUMERIC (10,2)) AS Total_data
FROM data_conversion
GROUP BY 1
ORDER BY 2 DESC;
```
<details><summary>Query Result</summary>

![alt text](<.images/Screenshot (66).png>)
</details>

## Observations/Interpretation*

Based on the 3 approaches, data allocation is directly proportional to the financial activity of our customers. Months with higher account balances result in larger data allocations, while lower account balances correspond to smaller allocations.

Unlike Option 1, where allocation is tied solely to the end-of-month balance, Option 2 considers the average balance maintained by customers over a rolling 30-day period. This provides a more nuanced and dynamic reflection of account activity.

Option 3 represents the most dynamic approach to data allocation, where data is updated in real time based on account activity. This cutting-edge strategy aims to provide customers with immediate access to data resources as their financial situation evolves.

*NB: There’s no data allocation in the month of January for options 1 & 2, because there were no previous transactions before it.*