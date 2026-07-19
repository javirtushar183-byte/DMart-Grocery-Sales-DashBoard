# 🛍️ Product Sales Analysis | SQL + Power BI

## 📌 Project Overview
This project analyzes product data to identify pricing trends, discount patterns, brand performance, inventory insights, and revenue contribution.

The goal is to perform SQL-based analysis and build an interactive Power BI dashboard for business decision-making.

---

## 🛠 Tools & Technologies Used
- SQL (PostgreSQL / MySQL)
- Power BI
- Data Cleaning (REGEX, CAST)
- Window Functions
- Data Visualization

---

## 🗄 Database Setup
```sql
CREATE DATABASE RetailStore;
USE RetailStore;
SELECT * FROM products;
```

---
## 1️⃣ Products with Discounted Price < 500
**Business Requirement:** Identify low-cost products to target price-sensitive customers and improve sales volume.
```sql
CREATE VIEW Products_Under_500 AS
SELECT name
FROM products
WHERE discounted_price < 500;
```

---

## 2️⃣ Nike Products Sorted by Price (High → Low)
**Business Requirement:** Analyze premium pricing strategy of Nike products.
```sql
CREATE VIEW Nike_Products_Sorted AS
SELECT name, brand, price
FROM products
WHERE brand = 'Nike'
ORDER BY price DESC;
```

---

## 3️⃣ Selected Columns (Name, Price, Discounted Price)
**Business Requirement:** Provide a simplified pricing view for reporting and dashboards.
```sql
CREATE VIEW Product_Pricing_View AS
SELECT name, price, discounted_price
FROM products;
```

---

## 4️⃣ Total Products per Category
**Business Requirement:** Understand product distribution across categories for inventory planning.
```sql
CREATE VIEW Products_Per_Category AS
SELECT category, COUNT(*) AS total_products
FROM products
GROUP BY category;
```

---

## 5️⃣ Average Discounted Price per Brand
**Business Requirement:** Compare brand pricing strategies and positioning.
```sql
CREATE VIEW Avg_Discounted_Price_Brand AS
SELECT brand, AVG(discounted_price) AS avg_discounted_price
FROM products
GROUP BY brand;
```

---

## 6️⃣ Total Quantity per Sub-Category
**Business Requirement:** Identify high-stock areas to manage inventory efficiently.
```sql
CREATE VIEW Quantity_By_SubCategory AS
SELECT sub_category,
       SUM(CAST(REGEXP_REPLACE(quantity, '[^0-9]', '', 'g') AS INT)) AS total_quantity_available
FROM products
GROUP BY sub_category
ORDER BY total_quantity_available DESC;
```

---

## 7️⃣ Discount Percentage per Product
**Business Requirement:** Evaluate discount effectiveness across products.
```sql
CREATE VIEW Discount_Percentage AS
SELECT name,
       price,
       discounted_price,
       ROUND(((price - discounted_price) * 100.0 / NULLIF(price, 0)), 2) AS discount_percentage
FROM products;
```

---

## 8️⃣ Products with Discount > 1000
**Business Requirement:** Identify heavily discounted products for promotional analysis.
```sql
CREATE VIEW High_Discount_Products AS
SELECT name, price, discounted_price
FROM products
WHERE (price - discounted_price) > 1000;
```

---

## 9️⃣ Top 5 Products by Discount %
**Business Requirement:** Highlight best deals to attract customers.
```sql
CREATE VIEW Top_Discount_Products AS
SELECT name,
       ROUND(((price - discounted_price) * 100.0 / NULLIF(price, 0)), 2) AS discount_percentage
FROM products
ORDER BY discount_percentage DESC
LIMIT 5;
```

---

## 🔟 Brand with Highest Number of Products
**Business Requirement:** Identify dominant brands in the inventory.
```sql
CREATE VIEW Top_Brand AS
SELECT brand, COUNT(*) AS total_products
FROM products
WHERE brand IS NOT NULL
GROUP BY brand
ORDER BY total_products DESC
LIMIT 1;
```

---

## 1️⃣1️⃣ Category with Highest Quantity
**Business Requirement:** Find categories with maximum stock for supply chain planning.
```sql
CREATE VIEW Top_Category_By_Quantity AS
SELECT category,
       SUM(CAST(REGEXP_REPLACE(quantity, '[^0-9]', '', 'g') AS INT)) AS total_quantity
FROM products
GROUP BY category
ORDER BY total_quantity DESC
LIMIT 1;
```

---

## 1️⃣2️⃣ Products with 'USA' in Description
**Business Requirement:** Identify products associated with specific regions or origin.
```sql
CREATE VIEW USA_Products AS
SELECT name
FROM products
WHERE description ILIKE '%usa%';
```

---

## 1️⃣3️⃣ Rank Products within Category
**Business Requirement:** Rank products to support pricing and recommendation strategies.
```sql
CREATE VIEW Product_Ranking AS
SELECT name,
       category,
       brand,
       discounted_price,
       ROW_NUMBER() OVER (PARTITION BY category ORDER BY discounted_price ASC) AS rank_in_category
FROM products;
```

---

## 1️⃣4️⃣ Top 3 Expensive Products per Category
**Business Requirement:** Identify premium products within each category.
```sql
CREATE VIEW Top_Expensive_Products AS
SELECT name, category, price
FROM (
    SELECT name,
           category,
           price,
           ROW_NUMBER() OVER (PARTITION BY category ORDER BY price DESC) AS rn
    FROM products
) t
WHERE rn <= 3;
```

---

## 1️⃣5️⃣ Highest Revenue Generating Category
**Business Requirement:** Identify top revenue-driving category for strategic focus.
```sql
CREATE VIEW Top_Revenue_Category AS
SELECT category,
       SUM(
           discounted_price *
           COALESCE(
               CAST(NULLIF(REGEXP_REPLACE(quantity, '[^0-9]', '', 'g'), '') AS INT),
               0
           )
       ) AS total_revenue
FROM products
GROUP BY category
ORDER BY total_revenue DESC
LIMIT 1;
```

---

## 📌 DMart Grocery Sales Dashboard
```sql

<img width="1307" height="732" alt="DMart Dashboard" src="https://github.com/user-attachments/assets/b93dce37-4945-489c-b801-073769abf20a" />

---
