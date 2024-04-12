# Customer Transactions Exploration
## 1. What is the unique count and total amount for each transaction type?
```pgsql
SELECT 
	txn_type,
	COUNT(txn_type) AS Unique_count,
	SUM(txn_amount) AS Total_amount
FROM data_bank.customer_transactions
GROUP BY txn_type
ORDER BY Total_amount DESC;
```
<details><summary>Query Result</summary>

![alt text](<Screenshot (51).png>)
</details>

## 2. What is the average total historical deposit counts and amounts for all customers?
```pgsql
WITH Historical_Deposits AS (
	SELECT  
		customer_id,
		txn_type,
		COUNT(txn_type) AS deposit_Count,
		SUM(txn_amount) AS Total_amount
	FROM data_bank.customer_transactions
	WHERE txn_type = 'deposit'
	GROUP BY customer_id, txn_type
)
SELECT 
	ROUND(AVG(deposit_Count), 0) AS Average_deposit_Count,
	ROUND(AVG(Total_amount), 0) AS Average_Total_amount
FROM Historical_Deposits;
```
<details><summary>Query Result</summary>

![alt text](<Screenshot (53).png>)
</details>

## 3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
```pgsql
WITH txn_detail AS(
	SELECT
    	customer_id,
    	EXTRACT (MONTH FROM txn_date) AS month_no,
    	TO_CHAR(txn_date,'Month') AS month_name,
    	SUM(CASE WHEN txn_type = 'deposit' THEN 1 ELSE 0 END) AS deposit_count,
    	SUM(CASE WHEN txn_type = 'withdrawal' THEN 1 ELSE 0 END) AS withdrawal_count,
    	SUM(CASE WHEN txn_type = 'purchase' THEN 1 ELSE 0 END) AS purchase_count
	FROM
    	data_bank.customer_transactions
	GROUP BY 1,2,3
	ORDER BY 1,2
)

SELECT 
    month_no,
    month_name,
    COUNT(DISTINCT customer_id) AS active_customers
FROM
    txn_detail
WHERE
    deposit_count > 1 
    AND (withdrawal_count > 0 or purchase_count > 0)
GROUP BY 1,2
ORDER BY 1,2;
```
<details><summary>Query Result</summary>

![alt text](<Screenshot (72).png>)
</details>

## 4. What is the closing balance for each customer at the end of the month?
```pgsql
CREATE TABLE Customer_closing_balance_each_month AS
WITH Txn_details AS (
	SELECT 
		customer_id,
		EXTRACT (MONTH FROM txn_date) AS month_no,
		TO_CHAR (txn_date, 'Month') AS month_name,
		SUM(CASE WHEN txn_type = 'deposit' THEN txn_amount ELSE 0 END) AS total_deposit,
		SUM(CASE WHEN txn_type = 'purchase' THEN txn_amount ELSE 0 END) AS total_purchase,
		SUM(CASE WHEN txn_type = 'withdrawal' THEN txn_amount ELSE 0 END) AS total_withdrawal
	FROM customer_transactions
	GROUP BY 1, 2, 3
	ORDER BY 1, 2, 3
)

SELECT 
	customer_id,
	month_no,
	month_name,
	total_deposit,
	total_purchase,
	total_withdrawal,
	SUM(total_deposit - (total_purchase + total_withdrawal)) AS Closing_balance
FROM Txn_details
GROUP BY 1, 2, 3, 4, 5, 6
ORDER BY 1, 2, 3;
```
<details><summary>Query Result</summary>
Below is a sample of the 12 rows of the query.

![alt text](<Screenshot (58).png>)
</details>

## 5. What is the percentage of customers who increase their closing balance by more than 5%?
```pgsql
CREATE TABLE Customer_rollover_closing_balance AS
SELECT 
	customer_id,
	month_no,
	month_name,
	SUM(closing_balance) OVER (PARTITION BY customer_id ORDER BY month_no) AS customer_closing_balance
FROM Customer_closing_balance_each_month
GROUP BY 1,2,3,Closing_balance
ORDER BY 1,2;

WITH first_last_closing_balance AS (		
	SELECT
 		customer_id,
 		MAX(CASE WHEN month_no = 1 THEN customer_closing_balance END) AS first_closing_balance,
 		MAX(CASE WHEN month_no = (SELECT MAX(month_no) FROM Customer_rollover_closing_balance WHERE customer_id = c.customer_id ) THEN customer_closing_balance END) AS last_closing_balance
 	FROM Customer_rollover_closing_balance c
 	GROUP BY customer_id
 	ORDER BY 1
),
customer_increase_count AS (
    SELECT
     	COUNT(*) FILTER (WHERE ((last_closing_balance - first_closing_balance) / ABS(NULLIF(first_closing_balance, 0))) * 100 > 5) AS num_customers_increase
    FROM first_last_closing_balance
)

SELECT 
    num_customers_increase,
    ROUND((num_customers_increase * 100.0) / 500, 2) AS percentage_increase
FROM (SELECT COUNT(*) AS total_customers FROM first_last_closing_balance) AS total, customer_increase_count;
```
<details><summary>Query Result</summary>

![alt text](<Screenshot (60).png>)
</details>

## Observations/Interpretation*

From the customer transactions exploration, a total of 5868 transactions were made. $1.3M+ of deposits were made, with $806,537 in purchases & $793,003 of withdrawals made from January 2020 to April 2020.

Additionally, there was an average deposit amount of $2,718 made. This shows that there are engagements from all customers, boosting the positive image of the enterprise. Also, analysis shows that 33% of customers increase their closing balance by more than 5%, thereby fostering building trust with Data bank.