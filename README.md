# 🛒 Amazon Sales Analysis – SQL Project

## 📌 Project Overview

This project is an **Amazon Sales Analysis** project developed using SQL.

The goal of this project is to analyze Amazon-style sales data and answer practical business questions using SQL. The analysis covers products, categories, customers, sellers, orders, order items, payments, shipping, inventory, sales performance, customer value, returns, payment status, shipping delays, and seller performance.

The project uses a relational database with multiple connected tables:

- `category`
- `customers`
- `sellers`
- `products`
- `orders`
- `order_items`
- `payments`
- `shippings`
- `inventory`

The database and tables are created in SQL, followed by data exploration and analytical queries.

The database is created as `amazon_sales`.

> **Note:** The README documents the SQL project as written in the uploaded source SQL. The source uses `CREATE DATABASE` / `USE` statements together with PostgreSQL-style syntax such as `::numeric`, `EXTRACT()`, `INTERVAL`, and `CURRENT_DATE`. The README does not silently rewrite the original queries.

---

## 🎯 Project Objectives

The main objectives of this project are to:

- Design a relational Amazon sales database
- Create tables with primary keys and foreign keys
- Explore customers, products, sellers, orders, payments, shipping, and inventory
- Identify top-selling products
- Analyze revenue by product category
- Calculate average order value for customers
- Analyze monthly sales trends
- Identify customers with no purchases
- Find the least-selling category by state
- Calculate Customer Lifetime Value (CLTV)
- Identify products with low inventory
- Analyze payment success and payment-status distribution
- Identify the most returned products
- Find orders with shipping delays
- Identify top-performing sellers

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| **SQL** | Database creation, exploration and analysis |
| **Relational Database** | Store connected Amazon sales data |
| **CSV Dataset** | Source data imported into the project |
| **SQL Joins** | Combine related business tables |
| **Window Functions** | Ranking and trend analysis |
| **CTEs & Subqueries** | Advanced analytical queries |

---

# 🗄️ Database Schema

The project contains **9 related tables**.

### 1. `category`

| Column | Description |
|---|---|
| `category_id` | Unique category ID and primary key |
| `category_name` | Product category name |

### 2. `customers`

| Column | Description |
|---|---|
| `customer_id` | Unique customer ID and primary key |
| `first_name` | Customer first name |
| `last_name` | Customer last name |
| `state` | Customer state |
| `address` | Customer address |

### 3. `sellers`

| Column | Description |
|---|---|
| `seller_id` | Unique seller ID and primary key |
| `seller_name` | Seller name |
| `origin` | Seller origin |

### 4. `products`

| Column | Description |
|---|---|
| `product_id` | Unique product ID and primary key |
| `product_name` | Product name |
| `price` | Product selling price |
| `cogs` | Cost of goods sold |
| `category_id` | Foreign key referencing `category` |

### 5. `orders`

| Column | Description |
|---|---|
| `order_id` | Unique order ID and primary key |
| `order_date` | Date of order |
| `customer_id` | Foreign key referencing `customers` |
| `seller_id` | Foreign key referencing `sellers` |
| `order_status` | Current order status |

### 6. `order_items`

| Column | Description |
|---|---|
| `order_item_id` | Unique order-item ID and primary key |
| `order_id` | Foreign key referencing `orders` |
| `product_id` | Foreign key referencing `products` |
| `quantity` | Quantity purchased |
| `price_per_unit` | Price per unit |
| `total_sales` | Calculated sales value |

### 7. `payments`

| Column | Description |
|---|---|
| `payment_id` | Unique payment ID and primary key |
| `order_id` | Foreign key referencing `orders` |
| `payment_date` | Payment date |
| `payment_status` | Payment status |

### 8. `shippings`

| Column | Description |
|---|---|
| `shipping_id` | Unique shipping ID and primary key |
| `order_id` | Foreign key referencing `orders` |
| `shipping_date` | Shipping date |
| `return_date` | Return date, when applicable |
| `shipping_providers` | Shipping provider |
| `delivery_status` | Delivery status |

### 9. `inventory`

| Column | Description |
|---|---|
| `inventory_id` | Unique inventory ID and primary key |
| `product_id` | Foreign key referencing `products` |
| `stock` | Current stock quantity |
| `warehouse_id` | Warehouse identifier |
| `last_stock_date` | Last stock date |

---

## 🔗 Table Relationships

```text
amazon_sales
│
├── category
│     └── products
│            ├── order_items
│            │      └── orders
│            │             ├── customers
│            │             ├── sellers
│            │             ├── payments
│            │             └── shippings
│            │
│            └── inventory
│
└── customers
```

### Main Relationships

```text
category.category_id
        ↓
products.category_id

customers.customer_id
        ↓
orders.customer_id

sellers.seller_id
        ↓
orders.seller_id

orders.order_id
        ↓
order_items.order_id

products.product_id
        ↓
order_items.product_id

orders.order_id
        ↓
payments.order_id

orders.order_id
        ↓
shippings.order_id

products.product_id
        ↓
inventory.product_id
```

---

# 📥 Database and Table Creation

The project starts by creating the `amazon_sales` database.

```sql
CREATE DATABASE amazon_sales;
USE amazon_sales;
```

### Category Table

```sql
CREATE TABLE category
(
    category_id INT PRIMARY KEY,
    category_name VARCHAR(30)
);
```

### Customers Table

```sql
CREATE TABLE customers
(
    customer_id INT PRIMARY KEY,
    first_name VARCHAR(20),
    last_name VARCHAR(20),
    state VARCHAR(20),
    address VARCHAR(5) DEFAULT ('xxxx')
);
```

### Sellers Table

```sql
CREATE TABLE sellers
(
    seller_id INT PRIMARY KEY,
    seller_name VARCHAR(25),
    origin VARCHAR(10)
);
```

### Products Table

```sql
CREATE TABLE products
(
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    price FLOAT,
    cogs FLOAT,
    category_id INT,
    CONSTRAINT product_fk_category
        FOREIGN KEY (category_id)
        REFERENCES category(category_id)
);
```

### Orders Table

```sql
CREATE TABLE orders
(
    order_id INT PRIMARY KEY,
    order_date DATE,
    customer_id INT,
    seller_id INT,
    order_status VARCHAR(50),
    CONSTRAINT orders_fk_customers
        FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id),
    CONSTRAINT orders_fk_sellers
        FOREIGN KEY (seller_id)
        REFERENCES sellers(seller_id)
);
```

### Order Items Table

```sql
CREATE TABLE order_items
(
    order_item_id INT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    price_per_unit FLOAT,
    CONSTRAINT order_items_fk_orders
        FOREIGN KEY (order_id)
        REFERENCES orders(order_id),
    CONSTRAINT order_items_fk_products
        FOREIGN KEY (product_id)
        REFERENCES products(product_id)
);
```

### Payments Table

```sql
CREATE TABLE payments
(
    payment_id INT PRIMARY KEY,
    order_id INT,
    payment_date DATE,
    payment_status VARCHAR(100),
    CONSTRAINT payments_fk_orders
        FOREIGN KEY (order_id)
        REFERENCES orders(order_id)
);
```

### Shippings Table

```sql
CREATE TABLE shippings
(
    shipping_id INT PRIMARY KEY,
    order_id INT,
    shipping_date DATE,
    return_date DATE,
    shipping_providers VARCHAR(15),
    delivery_status VARCHAR(15),
    CONSTRAINT shipping_fk_orders
        FOREIGN KEY (order_id)
        REFERENCES orders(order_id)
);
```

### Inventory Table

```sql
CREATE TABLE inventory
(
    inventory_id INT PRIMARY KEY,
    product_id INT,
    stock INT,
    warehouse_id INT,
    last_stock_date DATE,
    CONSTRAINT inventory_fk_products
        FOREIGN KEY (product_id)
        REFERENCES products(product_id)
);
```

---

# 🔎 Exploratory Data Analysis (EDA)

The project begins with basic exploration of the tables.

```sql
SELECT * FROM category;
SELECT * FROM customers;
SELECT * FROM inventory;
SELECT * FROM order_items;
SELECT * FROM orders;
SELECT * FROM payments;
SELECT * FROM products;
SELECT * FROM sellers;
SELECT * FROM shippings;
```

### Distinct Payment Status

```sql
SELECT DISTINCT payment_status
FROM payments;
```

### Products That Have Been Returned

```sql
SELECT *
FROM shippings
WHERE return_date IS NOT NULL;
```

EDA helps understand the structure, values, statuses, and relationships before performing business analysis.

---

# 📊 Business Analysis & SQL Solutions

## 1. Top Selling Products

### Business Question

What are the top 10 products based on total sales value?

### Challenge

Include:

- Product name
- Total sales
- Total unit orders

### SQL Concepts Used

- `JOIN`
- `SUM()`
- `COUNT()`
- `GROUP BY`
- `ORDER BY`
- `LIMIT`
- `ALTER TABLE`
- `UPDATE`

### Step 1: Create Total Sales Column

```sql
ALTER TABLE order_items
ADD COLUMN total_sales FLOAT;
```

### Step 2: Calculate Total Sales

```sql
UPDATE order_items
SET total_sales = quantity * price_per_unit;
```

### Step 3: Analyze Top-Selling Products

```sql
SELECT
    oi.product_id,
    p.product_name,
    SUM(oi.total_sales) AS total_sales,
    COUNT(o.order_id) AS total_unit_orders
FROM orders AS o
JOIN order_items AS oi
    ON oi.order_id = o.order_id
JOIN products AS p
    ON p.product_id = oi.product_id
GROUP BY 1, 2
ORDER BY 3 DESC
LIMIT 10;
```

### Business Use

This analysis helps identify products generating the highest sales value.

---

## 2. Revenue by Category

### Business Question

How much revenue is generated by each product category?

### Challenge

Include the percentage contribution of each category to total revenue.

### SQL Concepts Used

- `JOIN`
- `LEFT JOIN`
- `SUM()`
- Subquery
- `GROUP BY`
- Percentage calculation
- `ORDER BY`

```sql
SELECT
    p.category_id,
    c.category_name,
    SUM(oi.total_sales) AS total_sales,
    SUM(oi.total_sales) /
        (SELECT SUM(total_sales) FROM order_items) * 100
        AS percentage_contribution
FROM order_items AS oi
JOIN products AS p
    ON p.product_id = oi.product_id
LEFT JOIN category AS c
    ON c.category_id = p.category_id
GROUP BY 1, 2
ORDER BY 3 DESC;
```

### Business Use

Helps understand which product categories contribute most to overall sales.

---

## 3. Average Order Value (AOV)

### Business Question

What is the average order value for customers with more than 5 orders?

### SQL Concepts Used

- `JOIN`
- `CONCAT()`
- `SUM()`
- `COUNT()`
- `GROUP BY`
- `HAVING`

```sql
SELECT
    c.customer_id,
    CONCAT(c.first_name, ' ', c.last_name) AS full_name,
    SUM(total_sales) / COUNT(o.order_id) AS avg_order_value,
    COUNT(o.order_id) AS total_orders
FROM orders AS o
JOIN customers AS c
    ON c.customer_id = o.order_id
JOIN order_items AS oi
    ON oi.order_id = o.order_id
GROUP BY 1, 2
HAVING COUNT(o.order_id) > 5;
```

### Business Use

AOV helps analyze the average amount associated with customer orders.

> **Source note:** The join condition above is documented as written in the source SQL.

---

## 4. Monthly Sales Trend

### Business Question

What is the monthly sales trend over the past year?

### Challenge

Display:

- Current month sales
- Previous month sales

### SQL Concepts Used

- `EXTRACT()`
- `SUM()`
- `ROUND()`
- `LAG()`
- Window function
- `GROUP BY`
- Date filtering
- Subquery

```sql
SELECT
    month,
    year,
    total_sales AS current_month_sales,
    LAG(total_sales, 1)
        OVER (ORDER BY year, month) AS last_month_sale
FROM
(
    SELECT
        EXTRACT(MONTH FROM o.order_date) AS month,
        EXTRACT(YEAR FROM o.order_date) AS year,
        ROUND(SUM(oi.total_sales::NUMERIC), 2) AS total_sales
    FROM orders AS o
    JOIN order_items AS oi
        ON oi.order_id = o.order_id
    WHERE o.order_date >= CURRENT_DATE - INTERVAL '1 year'
    GROUP BY 1, 2
) AS t1
ORDER BY year, month ASC;
```

### Business Use

Helps identify changes in monthly sales and compare each month with the previous month.

---

## 5. Customers with No Purchases

### Business Question

Which registered customers have never placed an order?

### SQL Concepts Used

- Subquery
- `DISTINCT`
- `NOT IN`
- `LEFT JOIN`
- `IS NULL`

### Approach 1: Using `NOT IN`

```sql
SELECT *
FROM customers
WHERE customer_id NOT IN
(
    SELECT DISTINCT customer_id
    FROM orders
);
```

### Approach 2: Using `LEFT JOIN`

```sql
SELECT *
FROM customers AS c
LEFT JOIN orders AS o
    ON o.customer_id = c.customer_id
WHERE o.customer_id IS NULL;
```

### Business Use

Helps identify registered customers who have not yet made a purchase.

---

## 6. Least-Selling Categories by State

### Business Question

What is the least-selling product category for each state?

### Challenge

Include total sales for that category within each state.

### SQL Concepts Used

- CTE
- Multiple `JOIN`s
- `SUM()`
- `RANK()`
- `PARTITION BY`
- `GROUP BY`

```sql
WITH ranking_table AS
(
    SELECT
        c.state,
        cat.category_name,
        SUM(oi.total_sales) AS total_sales,
        RANK() OVER
        (
            PARTITION BY c.state
            ORDER BY SUM(oi.total_sales) ASC
        ) AS rank
    FROM orders AS o
    JOIN customers AS c
        ON o.customer_id = c.customer_id
    JOIN order_items AS oi
        ON o.order_id = oi.order_id
    JOIN products AS p
        ON oi.product_id = p.product_id
    JOIN category AS cat
        ON cat.category_id = p.category_id
    GROUP BY c.state, cat.category_name
)
SELECT *
FROM ranking_table
WHERE rank = 1;
```

### Business Use

Shows categories with the lowest sales contribution within each state.

---

## 7. Customer Lifetime Value (CLTV)

### Business Question

What is the total value of orders placed by each customer over their lifetime?

### Challenge

Rank customers based on their CLTV.

### SQL Concepts Used

- CTE
- `SUM()`
- `CONCAT()`
- `RANK()`
- `GROUP BY`
- `JOIN`

```sql
WITH customer_lifetime_value AS
(
    SELECT
        c.customer_id,
        CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
        SUM(oi.total_sales) AS lifetime_value
    FROM customers AS c
    JOIN orders AS o
        ON c.customer_id = o.customer_id
    JOIN order_items AS oi
        ON o.order_id = oi.order_id
    GROUP BY c.customer_id, c.first_name, c.last_name
)
SELECT
    customer_id,
    customer_name,
    lifetime_value,
    RANK() OVER
    (
        ORDER BY lifetime_value DESC
    ) AS rank
FROM customer_lifetime_value;
```

### Business Use

CLTV helps identify customers based on the total sales value associated with their orders.

---

## 8. Inventory Stock Alerts

### Business Question

Which products have stock levels below 10 units?

### Challenge

Include:

- Current stock
- Last stock date
- Warehouse information

### SQL Concepts Used

- `JOIN`
- `WHERE`
- Filtering

```sql
SELECT
    i.inventory_id,
    p.product_name,
    i.stock AS current_stock_left,
    i.last_stock_date,
    i.warehouse_id
FROM inventory AS i
JOIN products AS p
    ON p.product_id = i.product_id
WHERE stock < 10;
```

### Business Use

Helps identify products that may require inventory attention.

---

## 9. Payment Success Rate

### Business Question

What percentage of payments fall into each payment status?

### Challenge

Include a breakdown such as:

- Successful
- Failed
- Pending

### SQL Concepts Used

- `JOIN`
- `COUNT()`
- `GROUP BY`
- Percentage calculation
- Subquery

```sql
SELECT
    p.payment_status,
    COUNT(*) AS total_cnt,
    COUNT(*)::NUMERIC /
        (SELECT COUNT(*) FROM payments)::NUMERIC * 100
        AS percentage
FROM orders AS o
JOIN payments AS p
    ON o.order_id = p.order_id
GROUP BY 1;
```

### Business Use

Helps understand the distribution of payment outcomes.

---

## 10. Most Returned Products

### Business Question

Which products have the highest return percentage?

### Challenge

Include:

- Total units sold
- Total returned
- Return percentage

### SQL Concepts Used

- `JOIN`
- `COUNT()`
- `SUM()`
- `CASE`
- Conditional aggregation
- Percentage calculation
- `GROUP BY`
- `ORDER BY`

```sql
SELECT
    p.product_id,
    p.product_name,
    COUNT(*) AS total_unit_sold,
    SUM(
        CASE
            WHEN o.order_status = 'Returned'
            THEN 1
            ELSE 0
        END
    ) AS total_returned,
    SUM(
        CASE
            WHEN o.order_status = 'Returned'
            THEN 1
            ELSE 0
        END
    )::NUMERIC / COUNT(*)::NUMERIC * 100
    AS return_percentage
FROM order_items AS oi
JOIN products AS p
    ON oi.product_id = p.product_id
JOIN orders AS o
    ON o.order_id = oi.order_id
GROUP BY 1, 2
ORDER BY 5 DESC;
```

### Business Use

Helps identify products with comparatively high return percentages.

---

## 11. Shipping Delays

### Business Question

Which orders were shipped more than 3 days after the order date?

### Challenge

Include customer, order, and shipping information.

### SQL Concepts Used

- `JOIN`
- Date subtraction
- `WHERE`
- Aliases

```sql
SELECT *,
       s.shipping_date - o.order_date AS shipped_after
FROM orders AS o
JOIN customers AS c
    ON c.customer_id = o.customer_id
JOIN shippings AS s
    ON o.order_id = s.order_id
WHERE (s.shipping_date - o.order_date) > 3;
```

### Business Use

Helps identify orders with longer shipping delays.

---

## 12. Top Performing Sellers

### Business Question

Which are the top 5 sellers based on total sales value?

### SQL Concepts Used

- Multiple `JOIN`s
- `SUM()`
- `GROUP BY`
- `ORDER BY`
- `LIMIT`

```sql
SELECT
    se.seller_id,
    se.seller_name,
    SUM(oi.total_sales) AS total_sales
FROM order_items AS oi
JOIN orders AS o
    ON oi.order_id = o.order_id
JOIN products AS p
    ON oi.product_id = p.product_id
JOIN sellers AS se
    ON o.seller_id = se.seller_id
GROUP BY 1, 2
ORDER BY 3 DESC
LIMIT 5;
```

### Business Use

Helps identify sellers associated with the highest total sales value.

---

# 🧠 SQL Concepts Demonstrated

This project demonstrates practical SQL skills including:

```text
CREATE DATABASE
USE
CREATE TABLE
PRIMARY KEY
FOREIGN KEY
ALTER TABLE
UPDATE
SELECT
WHERE
IS NULL
DISTINCT
JOIN
INNER JOIN
LEFT JOIN
COUNT()
SUM()
ROUND()
GROUP BY
HAVING
ORDER BY
LIMIT
CASE
CTE
Subqueries
RANK()
LAG()
PARTITION BY
CONCAT()
EXTRACT()
CURRENT_DATE
INTERVAL
Type Casting
Date Calculations
Conditional Aggregation
```

---

# 📈 Key Analytical Areas

### 🛍️ Product Analysis

- Top-selling products
- Total sales by product
- Returned products
- Inventory stock alerts

### 📂 Category Analysis

- Revenue by category
- Category contribution to total revenue
- Least-selling category by state

### 👥 Customer Analysis

- Average Order Value
- Customers with no purchases
- Customer Lifetime Value
- Customer ranking

### 💰 Sales Analysis

- Total sales
- Monthly sales trends
- Previous-month comparison
- Top-performing sellers

### 💳 Payment Analysis

- Payment status distribution
- Payment percentage analysis

### 🚚 Shipping Analysis

- Shipping delays
- Returned orders
- Shipping provider information

### 📦 Inventory Analysis

- Current stock levels
- Low-stock products
- Warehouse information
- Last stock date

---

# 📚 Complete SQL Query Reference

The project contains **12 business-analysis questions** covering sales, products, customers, categories, payments, shipping, inventory, and sellers.

| # | Analysis | Main SQL Concepts |
|---|---|---|
| 1 | Top Selling Products | `JOIN`, `SUM`, `COUNT`, `GROUP BY`, `LIMIT` |
| 2 | Revenue by Category | `JOIN`, `SUM`, Subquery, Percentage |
| 3 | Average Order Value | `JOIN`, `CONCAT`, `SUM`, `COUNT`, `HAVING` |
| 4 | Monthly Sales Trend | `LAG`, `EXTRACT`, `ROUND`, Date filtering |
| 5 | Customers with No Purchases | `NOT IN`, `LEFT JOIN`, `IS NULL` |
| 6 | Least-Selling Category by State | CTE, `RANK`, `PARTITION BY` |
| 7 | Customer Lifetime Value | CTE, `SUM`, `RANK` |
| 8 | Inventory Stock Alerts | `JOIN`, `WHERE` |
| 9 | Payment Success Rate | `COUNT`, Subquery, Percentage |
| 10 | Most Returned Products | `CASE`, `SUM`, `COUNT` |
| 11 | Shipping Delays | `JOIN`, Date calculation |
| 12 | Top Performing Sellers | `JOIN`, `SUM`, `GROUP BY`, `LIMIT` |

---

# 💼 Skills Demonstrated

This project demonstrates my ability to:

- Design a relational database
- Create SQL databases and tables
- Define primary keys and foreign keys
- Import and explore datasets
- Perform exploratory data analysis
- Write analytical SQL queries
- Work with multiple related tables
- Use different types of SQL joins
- Filter and aggregate data
- Use `GROUP BY`, `HAVING`, and `ORDER BY`
- Use aggregate functions
- Calculate percentages
- Perform date-based analysis
- Use CTEs and subqueries
- Use window functions
- Perform ranking analysis
- Analyze customer behavior
- Analyze product and category performance
- Analyze payments and shipping
- Analyze inventory levels
- Solve practical business-analysis problems using SQL

---

# 🔍 SQL Query Techniques Used

The project demonstrates practical use of:

- `SELECT` for data retrieval
- `WHERE` for filtering
- `DISTINCT` for unique values
- `JOIN` for combining tables
- `LEFT JOIN` for retaining unmatched records
- `GROUP BY` for aggregation
- `HAVING` for filtering grouped results
- `ORDER BY` for sorting
- `LIMIT` for top-N analysis
- `COUNT()` for record counting
- `SUM()` for sales calculations
- `ROUND()` for numerical formatting
- `CASE` for conditional logic
- CTEs using `WITH`
- Subqueries for nested analysis
- `RANK()` for ranking
- `LAG()` for previous-period comparison
- `PARTITION BY` for grouped ranking
- `CONCAT()` for combining customer names
- `EXTRACT()` for date components
- `CURRENT_DATE` and `INTERVAL` for date filtering
- Type casting for numerical calculations
- Date subtraction for shipping-delay analysis

---

# 🚀 Project Outcome

This project converts raw Amazon sales data into structured business analysis using SQL.

It demonstrates practical experience in:

**Database design, data exploration, relational joins, sales analysis, customer analysis, product analysis, category analysis, inventory analysis, payment analysis, shipping analysis, aggregation, ranking, CTEs, subqueries, window functions, and business-oriented problem solving.**

The project provides a practical SQL portfolio example for demonstrating **Data Analyst skills** in interviews and on GitHub.

---

# 📁 Project Files

```text
Amazon-Sales-Analysis/
│
├── README.md
├── amazon_sales.sql
└── Dataset files
```

> Add the CSV files used for the Amazon sales database to the repository if you want the complete dataset and SQL project to be available together.

---

# 👨‍💻 Author

**Solomon Isaac**

Aspiring Data Analyst | SQL | Excel | Power BI | Python

---

⭐ If you find this project useful, feel free to explore the SQL queries and analysis.
