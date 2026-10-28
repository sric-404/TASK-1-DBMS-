# Task XIV - Business Intelligence and AI Data Preparation

This task creates analytical SQL reports and machine-learning-ready datasets from the E-Commerce Order Management Database.

> Window functions require MySQL 8.0 or later.

## 1. Create analytical sales reports

```sql
SELECT
    DATE_FORMAT(order_date, '%Y-%m') AS sales_month,
    COUNT(order_id) AS total_orders,
    SUM(total_amount) AS total_sales,
    AVG(total_amount) AS average_order_value
FROM Orders
GROUP BY DATE_FORMAT(order_date, '%Y-%m')
ORDER BY sales_month;
```

### Category sales report

```sql
SELECT
    c.category_name,
    SUM(od.quantity) AS items_sold,
    SUM(od.quantity * od.unit_price) AS category_sales
FROM Category c
JOIN Product p
    ON c.category_id = p.category_id
JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY c.category_id, c.category_name
ORDER BY category_sales DESC;
```

## 2. Apply window functions: RANK and ROW_NUMBER

### Rank products by sales within each category

```sql
WITH product_sales AS (
    SELECT
        c.category_name,
        p.product_id,
        p.product_name,
        SUM(od.quantity * od.unit_price) AS total_sales
    FROM Category c
    JOIN Product p
        ON c.category_id = p.category_id
    JOIN Order_Details od
        ON p.product_id = od.product_id
    GROUP BY c.category_id, c.category_name, p.product_id, p.product_name
)
SELECT
    category_name,
    product_name,
    total_sales,
    RANK() OVER (
        PARTITION BY category_name
        ORDER BY total_sales DESC
    ) AS sales_rank_in_category
FROM product_sales
ORDER BY category_name, sales_rank_in_category;
```

### Give every customer one position based on total spending

```sql
WITH customer_sales AS (
    SELECT
        c.customer_id,
        c.name AS customer_name,
        COALESCE(SUM(o.total_amount), 0) AS total_spent
    FROM Customer c
    LEFT JOIN Orders o
        ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.name
)
SELECT
    customer_id,
    customer_name,
    total_spent,
    ROW_NUMBER() OVER (
        ORDER BY total_spent DESC, customer_id
    ) AS customer_position
FROM customer_sales;
```

## 3. Generate customer segmentation data

```sql
WITH customer_metrics AS (
    SELECT
        c.customer_id,
        c.name AS customer_name,
        COUNT(o.order_id) AS total_orders,
        COALESCE(SUM(o.total_amount), 0) AS total_spent,
        COALESCE(AVG(o.total_amount), 0) AS average_order_value,
        MAX(o.order_date) AS last_order_date
    FROM Customer c
    LEFT JOIN Orders o
        ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.name
)
SELECT
    customer_id,
    customer_name,
    total_orders,
    total_spent,
    average_order_value,
    last_order_date,
    CASE
        WHEN total_spent >= 50000 THEN 'High Value'
        WHEN total_spent >= 10000 THEN 'Medium Value'
        WHEN total_orders > 0 THEN 'Low Value'
        ELSE 'No Purchase'
    END AS customer_segment
FROM customer_metrics
ORDER BY total_spent DESC;
```

> Adjust the segmentation amount thresholds if your own product prices are very different.

## 4. Prepare a dataset for AI models

Each row below represents one customer-product purchase. It is suitable as a starting point for recommendation, sales-prediction, or customer-behaviour analysis.

```sql
SELECT
    o.order_id,
    o.order_date,
    c.customer_id,
    p.product_id,
    p.category_id,
    od.quantity,
    od.unit_price,
    (od.quantity * od.unit_price) AS item_total,
    o.order_status
FROM Orders o
JOIN Customer c
    ON o.customer_id = c.customer_id
JOIN Order_Details od
    ON o.order_id = od.order_id
JOIN Product p
    ON od.product_id = p.product_id
ORDER BY o.order_date, o.order_id;
```

## 5. Perform feature extraction using SQL

This query turns raw order data into useful numeric features for each customer.

```sql
SELECT
    c.customer_id,
    COUNT(DISTINCT o.order_id) AS total_orders,
    COALESCE(SUM(od.quantity), 0) AS total_items_purchased,
    COALESCE(SUM(od.quantity * od.unit_price), 0) AS total_spent,
    COALESCE(AVG(od.quantity * od.unit_price), 0) AS average_item_value,
    COUNT(DISTINCT p.category_id) AS categories_purchased,
    MAX(o.order_date) AS most_recent_order_date
FROM Customer c
LEFT JOIN Orders o
    ON c.customer_id = o.customer_id
LEFT JOIN Order_Details od
    ON o.order_id = od.order_id
LEFT JOIN Product p
    ON od.product_id = p.product_id
GROUP BY c.customer_id
ORDER BY total_spent DESC;
```

Take screenshots of the window-function result, segmentation result, and AI dataset result.

## Viva questions and answers

**What is a window function?**  
It performs a calculation across related rows without combining those rows into one row.

**What is the difference between RANK and ROW_NUMBER?**  
`RANK` gives equal values the same rank; `ROW_NUMBER` always gives each row a unique sequence number.

**What is customer segmentation?**  
It groups customers by behaviour or value, such as high-value, medium-value, and low-value customers.

**What is feature extraction?**  
It converts raw database data into useful values such as total spent, order count, or categories purchased for analysis or AI models.
