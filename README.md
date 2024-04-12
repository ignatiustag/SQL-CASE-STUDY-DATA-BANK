# SQL CASE STUDY — DATA BANK “Decentralizing finance, securing data.”
![alt text](SQL_Case_Study_4.png)

# Table of Contents

- [Introdution/Enterprise Overview](#section-1)
- [Data Schema](#section-2)
- [Schema Diagram](#section-3)
- [Case Study](#section-4)
- [Insights & Conclusion](#section-5)

## Introdution/Enterprise Overview
<a name="section-1"></a>
This repository is an SQL case study #4 from Data with Danny's 8-week SQL challenge. It is an in-depth SQL analysis of the metrics, growth, and business of Data bank in its forecast and plan for their future developments.

Data Bank is an innovative decentralized e-banking enterprise that offers a secure cloud data storage platform to its customers. This Enterprise has revolutionized traditional banking services by providing an integrated system for financial management and data storage. Data Bank runs just like any other digital bank — but it isn’t only for banking activities, they also have the world’s most secure distributed data storage platform.

Customers of Data Bank will be allocated cloud data storage that is correlated with their account balance, ensuring a seamless and secure banking experience.

## Data Schema
<a name="section-2"></a>
There are three tables provided in the data model for the case study, which are linked to each other (this enables simplified SQL queries and table joining).

- Table 1: Regions — The regions table contains the region_id and their respective region_name values
<details><summary>View Table</summary>
Below are the 5 regions in data_bank.regions

| region_id	 | region_name |
| ---------- | ----------- |
| 1	         | Africa      |
| 2	         | America     |
| 3	         | Asia        |
| 4	         | Europe      |
| 5	         | Oceania     |
</details>

- Table 2: Customer_nodes —The customer_nodes table contains the customer_id ,region_id ,node_id ,start_date &end_date . Customers are randomly distributed across the nodes according to their region — this also specifies exactly which node contains both their cash and data.
<details>
<summary>View Table</summary>
Below is a sample of the top 10 rows of the data_bank.customer_nodes

| customer_id | region_id | node_id | start_date | end_date   |
| ----------- | --------- | ------- | ---------- | ---------- |
| 1           | 3         | 4       | 2020-01-02 | 2020-01-03 |
| 2           | 3         | 5       | 2020-01-03 | 2020-01-17 |
| 3           | 5         | 4       | 2020-01-27 | 2020-02-18 |
| 4           | 5         | 4       | 2020-01-07 | 2020-01-19 |
| 5           | 3         | 3       | 2020-01-15 | 2020-01-23 |
| 6           | 1         | 1       | 2020-01-11 | 2020-02-06 |
| 7           | 2         | 5       | 2020-01-20 | 2020-02-04 |
| 8           | 1         | 2       | 2020-01-15 | 2020-01-28 |
| 9           | 4         | 5       | 2020-01-21 | 2020-01-25 |
| 10          | 3         | 4       | 2020-01-13 | 2020-01-14 |
</details>

- Table 3: Customer Transactions — The customer_transactions table contains the customer_id , txn_date, txn_type& txn_amount.This table stores all customer deposits, withdrawals, and purchases made using their Data Bank debit card.
<details><summary>View Table</summary>
Below is a sample of the 10 rows of the data_bank.customer_transactions

| customer_id | txn_date   | txn_type | txn_amount |
| ----------- | ---------- | -------- | ---------- |
| 429         | 2020-01-21 | deposit  | 82         |
| 155         | 2020-01-10 | deposit  | 712        |
| 398         | 2020-01-01 | deposit  | 196        |
| 255         | 2020-01-14 | deposit  | 563        |
| 185         | 2020-01-29 | deposit  | 626        |
| 309         | 2020-01-13 | deposit  | 995        |
| 312         | 2020-01-20 | deposit  | 485        |
| 376         | 2020-01-03 | deposit  | 706        |
| 188         | 2020-01-13 | deposit  | 601        |
| 138         | 2020-01-11 | deposit  | 520        |
</details>

## Schema Diagram
<a name="section-3"></a>
![alt text](<Screenshot (40).png>)

## Case Study
<a name="section-4"></a>
The management team at Data Bank wants to increase its total customer base — but also needs some help tracking just how much data storage their customers will need.

Secondly, to generate insights that Data Bank might use to market its world-leading security features to potential investors and customers, and also create a presentation slide that contains all the relevant information about the various options for the data provisioning so the Data Bank management team can make an informed decision.

The following case study questions are given below which include some general data exploration analysis for the nodes and transactions before diving right into the core business questions and finishes with a challenging final request!

### [A. Customer Nodes Exploration](Customer_node_Exploration.md)
1. How many unique nodes are there on the Data Bank system?
2. What is the number of nodes per region?
3. How many customers are allocated to each region?
4. How many days on average are customers reallocated to a different node?
5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?
   
### B. [Customer Transactions](Customer_transactions_Exploration.md)
1. What is the unique count and total amount for each transaction type?
2. What is the average total historical deposit counts and amounts for all customers?
3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
4. What is the closing balance for each customer at the end of the month?
5. What is the percentage of customers who increase their closing balance by more than 5%?

### C. [Data Allocation Challenge](Data_allocation_challenge.md)
To test out a few different hypotheses - the Data Bank team wants to run an experiment where different groups of customers would be allocated data using 3 different options:

- Option 1: data is allocated based off the amount of money at the end of the previous month
- Option 2: data is allocated on the average amount of money kept in the account in the previous 30 days
- Option 3: data is updated real-time
  
For this multi-part challenge question - you have been requested to generate the following data elements to help the Data Bank team estimate how much data will need to be provisioned for each option:
- running customer balance column that includes the impact  0f each transaction
- customer balance at the end of each month
- minimum, average and maximum values of the running balance for each customer

Using all of the data available - how much data would have been required for each option on a monthly basis?

### D. [Extra Challenge](Extra_challenge.md)
Data Bank wants to try another option which is a bit more difficult to implement - they want to calculate data growth using an interest calculation, just like in a traditional savings account you might have with a bank.

If the annual interest rate is set at 6% and the Data Bank team wants to reward its customers by increasing their data allocation based off the interest calculated on a daily basis at the end of each day, how much data would be required for this option on a monthly basis?

### E. [Extension Request](Extention_request.md)
The Data Bank team wants you to use the outputs generated from the above sections to create a quick Powerpoint presentation which will be used as marketing materials for both external investors who might want to buy Data Bank shares and new prospective customers who might want to bank with Data Bank.

- Using the outputs generated from the customer node questions, generate a few headline insights which Data Bank might use to market it’s world-leading security features to potential investors and customers.

- With the transaction analysis - prepare a 1 page presentation slide which contains all the relevant information about the various options for the data provisioning so the Data Bank management team can make an informed decision.

## Insights & Conclusion
<a name="section-5"></a>
In the exploration of Data Bank, several key insights have emerged that underscore Data Bank's commitment to excellence and innovation.

- Data Bank’s world-leading security features, including Decentralization of nodes, node reallocation, and secure data storage, form the cornerstone of our commitment to protecting customer data. These robust security measures not only enhance customer trust and confidence but also ensure regulatory compliance and industry-leading standards.
  
- Our customer node analysis revealed increased customer confidence, showcasing Data Bank’s resilience and adaptability in an ever-evolving landscape.
  
- The analysis of data allocation options presents unique benefits and challenges, informing strategic decision-making and resource allocation strategies.

#### In conclusion, Data Bank stands at the forefront of innovation and excellence in the banking industry and cloud data storage platform. 

*NB: To view the SQL exploration on the Case Study questions, click on that case study section (eg. A. Customer Nodes Exploration)*