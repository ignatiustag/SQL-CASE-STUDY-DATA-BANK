# Customer Node Exploration
This section contains the in-depth analysis carried out, following the guidelines highlighted in the challenge. 

Data bank has 500 customers spanning across 5 regions.

## 1. How many unique nodes are there on the Data Bank system?
```pgsl 
SELECT 
	Count(Distinct(node_id)) As Number_of_Unique_Nodes
FROM customer_nodes;
```
<details><summary>Query Result</summary>

![alt text](<.images/Screenshot (42).png>)
</details>

## 2. What is the number of nodes per region?
```pgsl 
SELECT 
	r.region_id,
	r.region_name, 
	Count(cn.node_id) As Number_of_Nodes
FROM customer_nodes cn
INNER JOIN regions r
ON cn.region_id = r.region_id
GROUP BY r.region_id,r.region_name
ORDER BY number_of_nodes DESC;
```
<details><summary>Query Result</summary>

![alt text](<.images/Screenshot (44).png>)
</details>

## 3. How many customers are allocated to each region?
```pgsql
SELECT 
	r.region_name, 
	Count(Distinct(cn.customer_id)) As Number_of_Customers
FROM customer_nodes cn
INNER JOIN regions r
ON cn.region_id = r.region_id
GROUP BY r.region_name
ORDER BY Number_of_Customers Desc;
```
<details><summary>Query Result</summary>

![alt text](<.images/Screenshot (46).png>)
</details>

## 4. How many days on average are customers reallocated to a different node?
```pgsql
WITH reallocation_dates AS (
    SELECT
        customer_id,
        node_id,
        start_date,
        end_date,
        LEAD(start_date) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_start_date
    FROM customer_nodes
)
SELECT
    EXTRACT(DAY FROM AVG(AGE(next_start_date, start_date))) AS average_days
FROM reallocation_dates
WHERE next_start_date IS NOT NULL;
```
<details><summary>Query Result</summary>

![alt text](<.images/Screenshot (48).png>)
</details>

## 5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?
```pgsql
WITH reallocation_dates AS (
    SELECT
        customer_id,
        node_id,
		region_id,
        start_date,
        end_date,
        LEAD(start_date) OVER (PARTITION BY customer_id ORDER BY start_date) AS next_start_date
    FROM data_bank.customer_nodes
)
SELECT
    r.region_name,
	PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY AGE(next_start_date, start_date)) AS median_days,
    PERCENTILE_CONT(0.8) WITHIN GROUP (ORDER BY AGE(next_start_date, start_date)) AS percentile_80_days,
    PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY AGE(next_start_date, start_date)) AS percentile_95_day
FROM reallocation_dates AS rd
Inner Join data_bank.regions r
ON rd.region_id = r.region_id
WHERE next_start_date IS NOT NULL
GROUP BY r.region_name;
```
<details><summary>Query Result</summary>

![alt text](<.images/Screenshot (74).png>)
</details>

## Observations/Interpretation*

Observations from the customer node exploration shows that Data bank has 5 unique nodes, with a total of 3500 nodes spread across the five (5) regions: Australia, America, Africa, Asia & Europe. Australia has the highest number of nodes at 770, Also Australia has the highest number of customers at 22% of the total number.

Data bank having a good security measure towards the risk of cyber crimes and hackers getting into the data bank system, reallocates customers to different nodes on an average of 15 days.

*NB: Nodes are bank branches that exist around the world.*