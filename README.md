#  Retail Sales Analysis (SQL Server)

This project explores **retail transaction data** to uncover customer behavior, top-performing product categories, seasonal trends, and order patterns across different times of the day. The analysis was done using **SQL Server (SSMS)** with T-SQL queries to answer business questions and deliver actionable insights.

---

##  Business Problem

A retail store was experiencing **inconsistent monthly sales** and needed clear insights to:
- Identify **which product categories drive the most revenue**
- Determine **when (time of day)** most purchases occur
- Discover **top spending customers** for loyalty programs
- Understand **monthly and seasonal performance trends**

This project provides **data-driven insights** to help increase sales, optimize marketing, and improve inventory planning.

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
-- 1) Inspect total rows
SELECT COUNT(*) FROM retail_sales;

-- 2) Find rows with NULL values in key columns
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

-- 3) Rename misspelled column (example)
EXEC sp_rename 'retail_sales.quantiy', 'quantity', 'COLUMN';

-- 4) Delete rows with NULLs
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
```

---

##  Key Analysis Queries

### 1.  Total Sales by Category
```sql
SELECT category,
       SUM(total_sale) AS net_sale,
       COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

### 2.  Average Age of Customers in Beauty Category
```sql
SELECT ROUND(AVG(age), 2) AS average_age
FROM retail_sales
WHERE category = 'Beauty';
```

### 3.  High-Value Transactions (>= 1000)
```sql
SELECT *
FROM retail_sales
WHERE total_sale >= 1000;
```

### 4.  Transactions by Gender and Category
```sql
SELECT category,
       gender,
       COUNT(*) AS total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

### 5.  Best Selling Month in Each Year
```sql
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
```

### 6.  Top 5 Customers by Total Sales
```sql
SELECT TOP 5
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC;
```

### 7.  Unique Customers per Category
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
```

### 8.  Orders by Shift (Morning, Afternoon, Evening)
```sql
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
```

---

##  Business Insights

-  **Clothing** is the top-performing category with the highest total sales.  
-  **Afternoon (12 PM – 5 PM)** is the busiest shift—best time for promotions.  
-  The **top 5 customers** account for a significant share of revenue.  
-  **November** consistently appears as a peak month.  
-  **Beauty** customers average in the mid-30s.  
-  Shift and category patterns help optimize staffing, promotions, and inventory.

---

##  Business Value

-  **Optimize marketing** by targeting peak hours and months.  
-  **Increase retention** by identifying and rewarding high-value customers.  
-  **Improve inventory planning** with seasonal and category demand.  
-  **Make data-driven decisions** for promos, pricing, and stock.  
-  **Personalize offers** using demographic and category insights.

---

##  Future Improvements

- Build Power BI / Tableau dashboard for visualization.  
- Add stored procedures for recurring reports.  
- Automate reporting with SQL Agent Jobs.  
- Add segmentation and lifetime value analysis.

---



⭐ If this project helped you, please consider giving it a **star** on GitHub!
