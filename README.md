# KMS_Capstone_Project_SQL_Analysis


I am excited to share my latest SQL capstone project explores the Kultra Mega Stores Inventory sales to extract actionable insights on customer behavior, product performance, regional sales trends, shipping costs, and profitability. The goal is to provide KMS management with data-driven recommendations to boost revenue, retain valuable customers, and improve business operations.


## 🧭 Table of Contents

1. [Project Overview](#project-overview)
2. [Business Objectives](#business-objectives)
3. [Tools Used](#tools-used)
4. [Database Creation & Dataset Import](#database-creation--dataset-import)
5. [Business Questions & SQL Queries
](#business-questions--sql-queries)
6. [Key Insights & Findings](#key-insights--findings)
7. [Recommendations](#recommendations)
8. [Summary](#summary)
9. [Conclusion](#conclusion)

---

## 📖 Project Overview

This project aims to provide strategic insights to KMS management by analyzing sales, customer behavior, and returns to recommend strategies for increasing revenue and profitability.


- ---

## 🎯 Business Objectives

Identify top-performing products and regions.

Understand customer segmentation and behaviors.

Discover cost-intensive shipping methods.

Analyze return behaviors.


---

## 🛠️ Tools Used

| Tool            | Purpose                          |
|----------------|----------------------------------|
| SQL Server      | Database setup and SQL queries  |
| SSMS            | SQL Server Management Interface for query execution| 

---

## 🗃 Database Creation & Dataset Import


Before running the queries, I created a SQL Server database named:

```  SQL
CREATE DATABASE Capstone_Project;
```

Then I imported the cleaned CSV dataset into a table named KMS using the Import Flat File.

❓ Business Questions & SQL Queries

1. Which product category had the highest sales?

``` SQL

SELECT Product_Category, SUM(Sales) AS TotalSales
FROM KMS
GROUP BY Product_Category
ORDER BY TotalSales DESC;

```

📊 Result:

Technology: $5,984,248.30

Furniture: $5,178,590.51

Office Supplies: $3,752,762.10

**Insight:** Top 1 product category *Technology* drives the highest sales with total sales of $5,984,248.30, indicating strong demand and potential for further growth.

**Recommendation:** Allocate more marketing resources and inventory to the Technology category to maximize sales and revenue potential.


---
2. What are the Top 3 and Bottom 3 regions in terms of sales?

```  SQL

-- Top 3
SELECT TOP 3 Region, SUM(Sales) AS TotalSales
FROM KMS
GROUP BY Region
ORDER BY TotalSales DESC;

```

📊 Top Regions:

West: $3,597,549.33

Ontario: $3,063,212.55

Prairie: $2,837,304.59


```   SQL

-- Bottom 3
SELECT TOP 3 Region, SUM(Sales) AS TotalSales
FROM KMS
GROUP BY Region
ORDER BY TotalSales ASC;

```

📉 Bottom Regions:

Nunavut: $116,376.47

NW Territories: $800,847.34

Yukon: $975,867.39


**Insight:** Sales in Nunavut and NW Territories are significantly low.

**Recommendation:** Investigate barriers to sales in low-performing regions.


---

3. Total sales of appliances in Ontario?

```  SQL 
SELECT SUM(Sales) AS TotalApplianceSale
FROM KMS
WHERE Product_Sub_Category = 'Appliances' AND Region = 'Ontario';
```
📊 Result: $202,346.84

**Insight:** Appliances make a decent contribution in Ontario.

**Recommendation:** Consider bundling offers for appliances in high-volume regions.

---

4. Bottom 10 customers by sales

``` SQL 
SELECT TOP 10 Customer_Name, SUM(Sales) AS TotalSales
FROM KMS
GROUP BY Customer_Name
ORDER BY TotalSales ASC;

```

📉 Customers with very low lifetime sales (e.g. Jeremy Farry: $85.72)

**Insight:** The bottom 10 customers have very low total sales, indicating potential dormancy or one-time buyers.

**Recommendation:** Offer personalized outreach, exclusive discounts, and targeted product recommendations to re-engage these customers and increase revenue.


---

5. Which shipping mode incurred the highest cost?

```  SQL
SELECT TOP 1 Ship_Mode, SUM(Shipping_Cost) AS TotalShippingCost
FROM KMS
GROUP BY Ship_Mode
ORDER BY TotalShippingCost DESC;
```

📦 Delivery Truck: $51,971.94

**Insight:** Most expensive shipping mode.

**Recommendation:**  Negotiate better contracts or optimize shipping frequency.


---

6. Most valuable customers and their purchases

``` SQL

SELECT TOP 10 Customer_Name, SUM(Sales) AS TotalSales, COUNT(DISTINCT Product_Name) AS UniqueProducts
FROM KMS
GROUP BY Customer_Name
ORDER BY TotalSales DESC;
```
**Top:** Emily Phan ($117,124.43, 10 unique products)

**Insight:**  Loyal, high-value customers buy across categories.

**Recommendation:** Implement a loyalty program to retain top 10 customers.


---

7. Highest sales from Small Business customers
   
``` SQL 
SELECT TOP 1 Customer_Name, SUM(Sales) AS TotalSales
FROM KMS
WHERE Customer_Segment = 'Small Business'
GROUP BY Customer_Name
ORDER BY TotalSales DESC;
```

👤 Dennis Kane: $75,967.59

 **Insight:** small business customers are valuable Dennis Kane generated $75,967.59 in sales.

**Recommendation:** Target small business customers, develop targeted marketing campaigns.

---

8. Corporate customer with most orders (2009–2012)

```  SQL 
SELECT TOP 1 Customer_Name, COUNT(DISTINCT Order_ID) AS OrderCount
FROM KMS
WHERE Customer_Segment = 'Corporate'
GROUP BY Customer_Name
ORDER BY OrderCount DESC;
```

📦 Adam Hart: 18 orders

**Insight:** Corporate customer loyalty, Adam Hart placed 18 orders between 2009-2012.

**Recommendation:** Nurture Corporate relationship, retain and expand relationships with loyal customers.

---

9. Most profitable Consumer customer

``` SQL 
SELECT TOP 1 Customer_Name, SUM(Profit) AS TotalProfit
FROM KMS
WHERE Customer_Segment = 'Consumer'
GROUP BY Customer_Name
ORDER BY TotalProfit DESC;
```

💰 Emily Phan: $34,005.44

**Insight:** Profitable Consumer customers, Emily Phan generated $34,005.44 in profit.

**Recommendation:**  Personalized engagement, offer loyalty programs to high-value Consumer customers.

---

10. Customers who returned items and their segment

``` SQL 
-- View Creation
CREATE VIEW VW_KMSOrderStatus_tbl AS
SELECT k.*, CASE 
    WHEN os.Order_ID IS NOT NULL THEN 'Returned'
    ELSE 'Not Returned' 
END AS Return_Status
FROM KMS k
LEFT JOIN Order_Status os ON k.Order_ID = os.Order_ID;

```

``` Query

SELECT DISTINCT Customer_Name, Customer_Segment, Return_Status
FROM VW_KMSOrderStatus_tbl
WHERE Return_Status = 'Returned';
```
🔁 **Result:** 419 unique customers returned items.

**Insight:** Returns may indicate issues with product quality, sizing, or customer expectations.

**Recommendation:** Analyze return reasons, improve product quality, enhance customer experience, and develop segment-specific strategies to reduce returns and improve customer satisfaction.

---

11. Was the shipping cost spent appropriately based on Order Priority?

```  SQL

SELECT Order_Priority, Ship_Mode,
       COUNT(*) AS OrderCount,
       SUM(Shipping_Cost) AS TotalShippingCost
FROM KMS
GROUP BY Order_Priority, Ship_Mode
ORDER BY Order_Priority, TotalShippingCost DESC;

```


**Insight:** The company's shipping cost allocation doesn't fully align with order priorities, as the fastest shipping method (Express Air) is used less often for critical orders (200 orders, $1742.10).  while the slowest but most economical method (Delivery Truck) is used more often for critical orders (228 orders, $10783.82).


**Recommendation:** To optimize, KMS should increase Express Air usage for critical orders to ensure timely delivery, while reserving Delivery Truck for lower-priority orders to minimize costs, thereby balancing shipping speed and expense according to order priority.


---

📌 Key Insights & Findings

Technology is the top revenue driver.

West and Ontario dominate regional sales.

High-value customers like Emily Phan and Deborah Brumfield contribute a large portion of revenue.

Returns are spread across all customer segments.

Delivery Truck is the costliest shipping method.

---

📈 Recommendations

1. Focus marketing and inventory on high-performing product categories and regions.


2. Re-engage bottom 10 customers with personalized promotions to reactivate their buying behavior.


3. Evaluate shipping partnerships — particularly for delivery truck usage — to reduce costs.


4. Incentivize top customers through loyalty programs targeting top-tier customers like Emily Phan.


5. Investigate return reasons to reduce losses and increase satisfaction by conduct customer satisfaction surveys for high-return segments.

---

🧾 Summary

This project highlights the value of SQL in data analysis, segmentation, profitability tracking, and decision-making. It demonstrates how structured queries can uncover actionable insights to boost revenue and improve customer experience.


---

✅ Conclusion

The analysis reveals the importance of customer segmentation, shipping optimization, and regional performance monitoring. By acting on these insights, KMS can boost profitability and build stronger relationships with its customers.

---

🔧 Author

**Name:** Oluwabukola Aba

**Role:**  Data Analyst | SQL | Excel | Power BI | Tableau | ODK | Google Form | Commcare 

**Connect:** [linkedin][https://www.linkedin.com/in/oluwabukolaaba]



