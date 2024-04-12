# Extra challenge(Data Growth)
Data Bank wants to try another option which is a bit more difficult to implement — they want to calculate data growth using an interest calculation, just like in a traditional savings account you might have with a bank.

If the annual interest rate is set at 6% and the Data Bank team wants to reward its customers by increasing their data allocation based off the interest calculated on a daily basis at the end of each day, how much data would be required for this option on a monthly basis?

```pgsql
WITH Iinterest_cl AS (
	SELECT 
		*,
		running_balance * 0.000164 as sim_intr
	FROM customer_running_balance
), 
data_conversion AS (
	SELECT 
		*,
 		CASE 
 			WHEN sim_intr < 0 THEN 0
 			ELSE ((sim_intr/100:: float)*3) 
 			END AS data_alloc_gb
 	FROM Iinterest_cl
)
SELECT 
	TO_CHAR(TO_DATE(month_no::text, 'MM'), 'Month') AS month_name,
	CAST(SUM(data_alloc_gb) AS NUMERIC (10,2)) AS Total_data
FROM data_conversion
GROUP BY 1
ORDER BY 2 DESC;
```
<details><summary>Query Result</summary>

![alt text](<Screenshot (68)-1.png>)
</details>

