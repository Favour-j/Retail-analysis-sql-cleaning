# Retail Sales Analysis (SQL Server)

This project explores **retail transaction data** to uncover customer behavior, top-performing product categories, seasonal trends, and sales distribution across different times of the day.  
The analysis was performed using **SQL Server (SSMS)** and focuses on answering key business questions through efficient SQL queries.

---

##  Business Problem

A retail store was experiencing **inconsistent monthly sales** and needed clear insights to:
- Identify **which product categories drive the most revenue** 
- Determine **when (time of day)** most purchases occur 
- Discover **top spending customers** for loyalty programs  
- Understand **monthly and seasonal performance trends** 

This SQL project helps the business **make data-driven decisions** by uncovering these patterns and opportunities to increase sales and optimize marketing strategies.

---

##  Dataset Overview

- **File:** `SQL - Retail Sales Analysis_utf .csv`  
- **Records:** ~34,000 transactions  
- **Columns:**
  - `transaction_id`  
  - `sale_date`  
  - `sale_time`  
  - `customer_id`  
  - `gender`  
  - `age`  
  - `category`  
  - `quantity`  
  - `price_per_unit`  
  - `cogs`  
  - `total_sale`

---

##  Tools Used

- SQL Server 2022 (SSMS)  
- T-SQL for cleaning and analysis  
- CSV dataset for transactional data

---

##  Data Cleaning

```sql
-- Finding rows with NULL values
SELECT * 
FROM retail_sales
WHERE sale_date IS NULL 
   OR sale_time IS NULL 
   OR customer_id IS NULL 
   OR gender IS NULL 
   OR age IS NULL 
   OR category IS NULL 
   OR quantity IS NULL 
   OR price_per_unit IS NULL
   OR cogs IS NULL;

-- Renaming wrong column
EXEC sp_rename 'retail_sales.quantiy', 'quantity', 'COLUMN';

-- Deleting NULL rows
DELETE FROM retail_sales
WHERE sale_date IS NULL 
   OR sale_time IS NULL 
   OR customer_id IS NULL 
   OR gender IS NULL 
   OR age IS NULL 
   OR category IS NULL 
   OR quantity IS NULL 
   OR price_per_unit IS NULL 
   OR cogs IS NULL;

---
---

##    Key Analysis Queries

1.  Total Sales by Category
SELECT category,
       SUM(total_sale) AS net_sale,
       COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;

2.  Average Age of Beauty Category Customers
SELECT ROUND(AVG(age), 2) AS average_age
FROM retail_sales
WHERE category = 'Beauty';

3.  High-Value Transactions
SELECT *
FROM retail_sales
WHERE total_sale >= 1000;

4. Transactions by Gender and Category
SELECT category,
       gender,
       COUNT(*) AS total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;

5.  Best Selling Month by Year
SELECT 
    year,
    month,
    DATENAME(MONTH, DATEFROMPARTS(year, month, 1)) AS month_name,
    avg_sale
FROM 
(
    SELECT 
        YEAR(sale_date) AS year,
        MONTH(sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER(
            PARTITION BY YEAR(sale_date) 
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY YEAR(sale_date), MONTH(sale_date)
) AS date_transaction
WHERE rank = 1
ORDER BY year;

6.  Top 5 Customers by Total Sales
SELECT TOP 5
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC;

7.  Unique Customers per Category
SELECT 
    category,    
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;

8.  Orders by Shift (Morning, Afternoon, Evening)
WITH hourly_sale AS
(
    SELECT *,
        CASE 
            WHEN DATEPART(HOUR, sale_time) < 12 THEN 'Morning'
            WHEN DATEPART(HOUR, sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift
ORDER BY total_orders DESC;

---
---


##  Business Insights 

-  Clothing emerged as the top-performing category with the highest total sales.

-  Most purchases occur during the Afternoon shift (12 PM – 5 PM), indicating the best time to launch promotions and campaigns.

-  The top 5 customers contributed a significant portion of total revenue — ideal for loyalty or referral programs.

-  November was identified as the best-selling month across multiple years, suggesting seasonal shopping trends.

-  Beauty category customers have an average age in the mid-30s, helping define the target customer segment.

-  Clear category-level performance and shift-based order trends can guide better marketing and inventory planning.

---
---

##  Business Value

This analysis helps the retail business:

- Optimized marketing: Identify peak purchasing periods and launch targeted campaigns.

-  Customer loyalty: Spot and reward high-value customers to boost retention.

-  Inventory planning: Align stock levels with seasonal sales spikes and top-selling categories.

-  Data-driven decisions: Replace guesswork with actionable insights from real transaction data.

- Targeted strategy: Use customer age and category insights to segment audiences and personalize offers.

