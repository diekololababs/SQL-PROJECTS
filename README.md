## Olympics Data Analysis
This Project explores a historical Olympics dataset using SQL to uncover trends in global sporting performance.

![Case 1 pix](https://specials-images.forbesimg.com/dam/imageserve/852989230/960x0.jpg?fit=scale)


## :bookmark_tabs: Table of Contents
- 🛠️ Problem Statement
- 📂 Dataset
- 📙 Case Study Questions
- 🏆 Solutions

## :hammer_and_wrench: Problem Statement
The Olympics Games, held every four years, showcase the pinnacle of International athletic performance and sportmanship. With decades of historical data available, Analysing Olympic performance trends provides valuable insights into global sporting dynamics and country-wise achievements.

The goal of this project is to analyse a comprehensive Olympics Dataset using SQL to answer Key Questions.

By Using SQL to extract, filter, group and aggregate data, this project aims to provide clear, data-driven insights into Olympic history, highlight dominant nations and athletes and identify trends that may inform fuy=uture analyses or predictions.


## :open_file_folder: Dataset


</p>
</details>

## :closed_book: Case Study Questions
Each of the following case study questions can be answered using a single SQL statement:
 <details><summary>View List of Questions</summary>
<p> 

  1. 	How many olympics games have been held?
  2.	List down all Olympics games held so far
  3. Mention the total no of nations who participated in each olympics game?
  4. Which year saw the highest and lowest no of countries participating in olympics?
  5. Which nation has participated in all of the olympic games?
  6. Identify the sport which was played in all summer olympics
  7. Which Sports were just played only once in the olympics?
  8. Fetch the total no of sports played in each olympic games
  9. Fetch details of the oldest athletes to win a gold medal.
  10. Find the Ratio of male and female athletes participated in all olympic games.
  11. Fetch the top 5 athletes who have won the most gold medals.
  12. Fetch the top 5 athletes who have won the most medals (gold/silver/bronze).
  13. Fetch the top 5 most successful countries in olympics. Success is defined by no of medals won.
  14.  List down total gold, silver and broze medals won by each country
  15.  List down total gold, silver and broze medals won by each country corresponding to each olympic games.
  16. 	Which countries have never won gold medal but have won silver/bronze medals?

</p>
</details>

 ## 	:trophy: Solutions
 <details><summary>View Solution</summary>
<p> 
  
   1. How many olympics games have been held
   
   ```bash
SELECT COUNT(DISTINCT [Games]) AS TotalGamesHeld
FROM [dbo].[athlete_events$]

SELECT [Games],count([Games])AS GameFrequency
FROM [dbo].[athlete_events$]
GROUP BY [Games]
```
  2. List down all Olympics games held so far?
   
```bash
SELECT DISTINCT [Games]
FROM [dbo].[athlete_events$]
ORDER BY [Games] ASC
```
   
  3. Total no of nations who participated in each olympics game
   
```bash
 SELECT [Games], COUNT (DISTINCT [Team]) AS Nations
FROM [dbo].[athlete_events$]
GROUP BY [Games]
ORDER BY Nations DESC
--OR--
SELECT a.[Games],  COUNT (DISTINCT b.[region]) AS Nations
FROM [dbo].[athlete_events$] a
JOIN [dbo].[noc_regions$] b
ON a.[NOC] =b.[NOC]
GROUP BY [Games]
ORDER BY Nations DESC
```
   
  4.  Year with highest and lowest no. of countries participating in olympics
   
```bash
WITH CountryCount AS (
	SELECT [Year], COUNT(DISTINCT[Team]) AS Country_Count
	FROM [dbo].[athlete_events$]
	GROUP BY [Year]
	)
SELECT [Year], Country_Count,
CASE 
WHEN Country_Count =(SELECT MAX(Country_Count) FROM CountryCount)
THEN 'Highest'
WHEN Country_Count =(SELECT MIN(Country_Count) FROM CountryCount)
THEN 'Lowest'
END AS CATEGORY
FROM CountryCount
WHERE Country_Count =(SELECT MAX(Country_Count) FROM CountryCount) 
OR 
Country_Count =(SELECT MIN(Country_Count) FROM CountryCount)
```
   
  5. Which nation has participated in all of the olympic games?
   
```bash
SELECT [Team]
FROM [dbo].[athlete_events$]
GROUP BY [Team]
HAVING COUNT (DISTINCT [Games]) =
	(SELECT  COUNT (DISTINCT [Games]) FROM [dbo].[athlete_events$])
```
    
   6. The sport which was played in all summer olympics
   
```bash
SELECT distinct([Sport])
FROM [dbo].[athlete_events$]
where [Season] = 'Summer'
GROUP BY [Sport]
having count(distinct Games) = (select count(distinct Games)
								from [dbo].[athlete_events$])
```
   
  7. Which Sports were just played only once in the olympics?
     
```bash
SELECT [Sport]
FROM [dbo].[athlete_events$]
GROUP BY [Sport]
HAVING COUNT(DISTINCT[Year])=1
```
   
  8. Total no of sports played in each olympic games
   
```bash
SELECT DISTINCT [Games],  COUNT(DISTINCT[Sport]) AS TotalSportPlayed
FROM [dbo].[athlete_events$]
GROUP BY [Games]
ORDER BY TotalSportPlayed DESC
```
   
  9. Details of the oldest athletes to win a gold medal
      
```bash
SELECT *
FROM [dbo].[athlete_events$]
WHERE [Age] = (SELECT MAX([Age]) FROM [dbo].[athlete_events$] where  [Medal]= 'Gold')
--or--
SELECT TOP 1 *
FROM [dbo].[athlete_events$]
WHERE [Medal]= 'Gold'
ORDER BY [Age] DESC
```
   
 10. Ratio of male and female athletes participated in all olympic games
   
```bash
SELECT [Sex], count([Sex]) as Gender_Ratio
FROM [dbo].[athlete_events$]
GROUP BY [Sex]
```
   
  11. Top 5 athletes who have won the most gold medals
   
```bash
SELECT TOP 5 [Name],COUNT([Medal]) AS medalWon
FROM [dbo].[athlete_events$]
WHERE [Medal] ='Gold'
GROUP  BY [Name]
order by medalWon DESC
```

  12. Top 5 athletes who have won the most medals (gold/silver/bronze)
   
```bash
SELECT TOP 5 [Name],[Medal],COUNT([Medal]) AS medalWon
FROM [dbo].[athlete_events$]
WHERE [Medal] IN ('Gold', 'Silver', 'Bronze')
GROUP  BY [Name],[Medal]
ORDER BY medalWon DESC
```

  13. Top 5 most successful countries in olympics. Success is defined by no of medals won
   
```bash
SELECT TOP 5 [Team], [Medal], COUNT([Medal]) AS MedalCount
FROM [dbo].[athlete_events$]
WHERE [Medal] <> 'NA'
GROUP BY [Team],[Medal]
ORDER BY MedalCount DESC
```
  14. Total gold, silver and bronze medals won by each country
   
```bash
SELECT DISTINCT[Team],[Medal], COUNT([Medal]) AS TotalMedal
FROM [dbo].[athlete_events$]
WHERE [Medal] <> 'NA'
GROUP BY [Medal],[Team]
ORDER BY TotalMedal DESC
--OR--
SELECT [Team],
SUM(CASE WHEN [Medal]='Gold'
THEN 1 ELSE 0 END) AS GOLD,
SUM(CASE WHEN [Medal]='Silver'
THEN 1 ELSE 0 END) AS SILVER,
SUM(CASE WHEN [Medal]='Bronze'
THEN 1 ELSE 0 END) AS BRONZE,
COUNT(CASE WHEN [Medal] <> 'NA' THEN 1 ELSE 0 END) AS MedalCount
FROM [dbo].[athlete_events$]
GROUP BY [Team]
ORDER BY GOLD DESC, SILVER DESC, BRONZE DESC

```
 15. Total gold, silver and broze medals won by each country corresponding to each olympic games
     
```bash
SELECT DISTINCT([Games]),[Team] AS Country,
SUM(CASE WHEN [Medal]='Gold'
THEN 1 ELSE 0 END) AS GOLD,
SUM(CASE WHEN [Medal]='Silver'
THEN 1 ELSE 0 END) AS SILVER,
SUM(CASE WHEN [Medal]='Bronze'
THEN 1 ELSE 0 END) AS BRONZE,
COUNT(CASE WHEN [Medal] <> 'NA' THEN 1 ELSE 0 END) AS MedalCount
FROM [dbo].[athlete_events$]
GROUP BY [Team],[Games]
ORDER BY GOLD DESC, SILVER DESC, BRONZE DESC
```

16. Which countries have never won gold medal but have won silver/bronze medals
```bash
SELECT [Team] AS Country
FROM [dbo].[athlete_events$]
GROUP BY [Team]
HAVING 
SUM(CASE WHEN [Medal] = 'Gold' 
THEN 1 ELSE 0 END) =0
AND SUM(CASE WHEN [Medal] = 'Silver' 
THEN 1 ELSE 0 END) >0
AND SUM(CASE WHEN [Medal] = 'Bronze' 
THEN 1 ELSE 0 END) >0
```
  </p>
</details>


## Danny's Diner Data Analysis

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
