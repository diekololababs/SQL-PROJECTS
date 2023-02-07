## 8 Weeks SQL Challenge

![Case 1 pix](https://user-images.githubusercontent.com/123111536/213601909-8a1c9873-c037-4884-aea6-664680608cc2.png)

## :bookmark_tabs: Table of Contents
- 🛠️ Problem Statement
- 📂 Dataset
- 📙 Case Study Questions
- 🏆 Solutions

## :hammer_and_wrench: Problem Statement
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, 
how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers 
will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally 
he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are 
enough for you to write fully functioning SQL queries to help him answer his questions!

## :open_file_folder: Dataset
We were provided with three datasets:
 - Sales
 <details><summary>View Table</summary>
<p>
  
 | customer_id | order_date | product_id |
| :---         | :---      |     :--- |
| A   | 2021-01-01  | 1  |
| A   | 2021-01-01  | 2  |
| A   | 2021-01-07  | 2  |
| A   | 2021-01-10  | 3  |
| A   | 2021-01-11  | 3  |
| A   | 2021-01-11  | 3  |
| B   | 2021-01-01  | 2  |
| B   | 2021-01-02  | 2  |
| B   | 2021-01-04  | 1  |
| B   | 2021-01-11  | 1  |
| B   | 2021-01-16  | 3  |
| B   | 2021-02-01  | 3  |
| C   | 2021-01-01  | 1  |
| C   | 2021-01-01  | 3  |
| C   | 2021-02-07  | 3  |
  
</p>
</details>

 - Menu
 <details><summary>View Table</summary>
<p>
  
 | product_id | product_name | price |
| :---         | :---      |     :--- |
| 1   | sushi  | 10  |
| 2   | curry  | 15  |
| 3   | ramen  | 12  |

  </p>
</details>

 - Members
 <details><summary>View Table</summary>
<p> 
  
 | customer_id | join_date |
| :---         | :---      |  
| A   | 2021-01-07  |
| B   | 2021-01-09  |

</p>
</details>

## :closed_book: Case Study Questions
Each of the following case study questions can be answered using a single SQL statement:
 <details><summary>View List of Questions</summary>
<p> 

  1. What is the total amount each customer spent at the restaurant?
  2. How many days has each customer visited the restaurant?
  3. What was the first item from the menu purchased by each customer?
  4. What is the most purchased item on the menu and how many times was it purchased by all customers?
  5. Which item was the most popular for each customer?
  6. Which item was purchased first by the customer after they became a member?
  7. Which item was purchased just before the customer became a member?
  8. What is the total items and amount spent for each member before they became a member?
  9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
  10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
  11. Use the available data to create a comprehensive data using the Join function.
  12. Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.

</p>
</details>

 ## 	:trophy: Solutions
 <details><summary>View Solution</summary>
<p> 
  
   1. What is the total amount each customer spent at the restaurant?
   
   ```bash
SELECT S.[Customer ID], Sum([Price]) AS TotalAmountSpent
FROM  [dbo].[SALES]  AS S
JOIN [dbo].[MENU] as M
ON S.[Product ID] = M.[Product ID]
GROUP  BY [Customer ID] 
```
  2. How many days has each customer visited the restaurant?
   
```bash
SELECT [Customer ID], COUNT(DISTINCT[Order Date]) as Days_Visited
FROM [dbo].[SALES]
GROUP BY [Customer ID]
ORDER BY Days_Visited DESC
```
   
  3. What was the first item from the menu purchased by each customer?
   
```bash
SELECT [Order Date] ,[Customer ID],[Product Name]      
FROM   (SELECT S.[Customer ID],S.[Order Date], MN.[Product Name],
DENSE_RANK()
OVER( PARTITION BY S.[Customer ID]
ORDER BY S.[Order Date]) AS menu_rank
        FROM   [dbo].[SALES] AS S
               INNER JOIN [dbo].[MENU] AS MN
                       ON S.[Product ID] =MN.[Product ID] 
        GROUP  BY [Customer ID],[Product Name],[Order Date]) t
WHERE menu_rank = 1; 
```
   
  4. What is the most purchased item on the menu and how many times was it purchased by all customers?
   
```bash
 SELECT MN.[Product Name], COUNT(S.[Product ID]) AS CountOfPurchase
FROM [dbo].[MENU] AS MN
LEFT JOIN [dbo].[SALES] AS S
ON MN.[Product ID] = S.[Product ID]
GROUP BY [Product Name]
ORDER BY CountOfPurchase DESC 
```
   
  5. Which item was the most popular for each customer?
   
```bash
WITH NEW AS (
SELECT S.[Customer ID],MN.[Product Name], COUNT(S.[Product ID]) AS CountOfOrders,
RANK ()
OVER( PARTITION BY S.[Customer ID]
ORDER BY COUNT(S.[Product ID]) DESC) AS RankOfOrders
FROM  [dbo].[SALES] AS S
JOIN [dbo].[MENU] AS MN
ON S.[Product ID]= MN.[Product ID]
GROUP BY [Customer ID],[Product Name])

SELECT [Customer ID],[Product Name],CountOfOrders,RankOfOrders
FROM NEW
WHERE  RankOfOrders = 1
```
    
   6. Which item was purchased first by the customer after they became a member?
   
```bash
SELECT NEW.[Customer ID],NEW.[Product Name]
FROM (SELECT S.[Customer ID],S.[Order Date],MN.[Product Name]
FROM [dbo].[SALES]AS S
JOIN [dbo].[MENU]AS MN
ON S.[Product ID] = MN.[Product ID]) NEW
JOIN [dbo].[MEMBERS] AS MB
ON NEW.[Customer ID] = MB.[Customer ID]
WHERE NEW.[Order Date] >= MB.[Join Date]
```
   
  7. Which item was purchased just before the customer became a member?
   
```bash
WITH NM AS (
SELECT S.[Customer ID],MN.[Product Name],
DENSE_RANK()
OVER( PARTITION BY S.[Customer ID]
ORDER BY S.[Order Date]) AS RankOfOrder
FROM [dbo].[SALES] AS S
JOIN [dbo].[MENU] AS MN
ON S.[Product ID] = MN.[Product ID]
JOIN [dbo].[MEMBERS] AS MB
ON S.[Customer ID] = MB.[Customer ID] 
WHERE S.[Order Date] <MB.[Join Date])
SELECT [Customer ID],[Product Name],RankOfOrder
FROM NM
WHERE RankOfOrder = 1
```
   
  8. What is the total items and amount spent for each member before they became a member?
   
```bash
SELECT S.[Customer ID], COUNT(S.[Product ID]) AS ProductCount,SUM(MN.[Price]) AS TotalAmount
FROM [dbo].[SALES] AS S
JOIN [dbo].[MENU] AS MN
ON S.[Product ID] = MN.[Product ID]
JOIN [dbo].[MEMBERS] AS MB
ON S.[Customer ID] = MB.[Customer ID]
WHERE S.[Order Date] < MB.[Join Date]
GROUP BY S.[Customer ID]
ORDER BY S.[Customer ID] ASC
```
   
  9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
   
```bash
SELECT S.[Customer ID],MN.[Product Name],
SUM(
CASE
WHEN MN.[Product Name] = 'SUSHI'
THEN MN.[Price] *20
ELSE MN.[Price] * 10
END)
AS ProductPoints
FROM [dbo].[SALES] AS S
JOIN [dbo].[MENU] AS MN
ON S.[Product ID] = MN.[Product ID]
GROUP BY S.[Customer ID], MN.[Product Name]
```
   
 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
   
```bash
WITH dates
     AS (SELECT *,
                Date_add(join_date, interval 7 day) AS valid_date,
                Last_day(join_date)                 AS last_date
         FROM   members)
SELECT s.customer_id,
       SUM(CASE
             WHEN s.order_date BETWEEN d.join_date AND d.valid_date THEN
             m.price * 20
           END) AS total_points
FROM   dates d
       join sales s
         ON s.customer_id = d.customer_id
       join menu m
         ON m.product_id = s.product_id
WHERE  s.order_date <= d.last_date
GROUP  BY s.customer_id; 
```
   
  11. Use the available data to create a comprehensive data using the Join function.
   
```bash
SELECT S.[Customer ID],S.[Order Date],MN.[Product Name],MN.[Price]
(CASE
WHEN S.[Order Date] >= MB.[Join Date] THEN 'JOINED'
ELSE 'NOT JOINED'
END)
AS JoinStatus
FROM [dbo].[SALES] AS S
JOIN [dbo].[MENU] AS MN
ON S.[Product ID] = MN.[Product ID]
JOIN [dbo].[MEMBERS] AS MB
ON S.[Customer ID] = MB.[Customer ID]
```

  12. Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.
   
```bash
WITH new_table
     AS (SELECT s.customer_id,
                s.order_date,
                m.product_name,
                m.price,
                ( CASE
                    WHEN s.order_date >= mb.join_date THEN 'Y'
                    ELSE 'N'
                  END ) AS member
         FROM   sales s
                LEFT JOIN menu m
                       ON s.product_id = m.product_id
                LEFT JOIN members mb
                       ON mb.customer_id = s.customer_id)
SELECT *,
       ( CASE
           WHEN member = "n" THEN "null"
           ELSE Rank()
                  OVER (
                    partition BY customer_id, member
                    ORDER BY order_date)
         END ) AS ranking
FROM   new_table; 
```
 
  </p>
</details>
