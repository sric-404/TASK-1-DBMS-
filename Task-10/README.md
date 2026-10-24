# Task X - Advanced SQL Query System

This task uses advanced SQL features in the existing E-Commerce Order Management Database.

Run these queries after completing the `Customer`, `Category`, `Product`, `Orders`, and `Order_Details` tables. No new table is required.

## 1. Implement subqueries and nested queries

### Products that have been ordered

```sql
SELECT
    product_id,
    product_name,
    price,
    stock
FROM Product
WHERE product_id IN (
    SELECT DISTINCT product_id
    FROM Order_Details
);
```

### Customers who have placed at least one order

```sql
SELECT
    customer_id,
    name,
    email,
    phone
FROM Customer
WHERE customer_id IN (
    SELECT DISTINCT customer_id
    FROM Orders
);
```

Take a screenshot of the result.

## 2. Find products above average price

```sql
SELECT
    product_id,
    product_name,
    price,
    stock
FROM Product
WHERE price > (
    SELECT AVG(price)
    FROM Product
)
ORDER BY price DESC;
```

### Show the average price with each higher-priced product

```sql
SELECT
    product_id,
    product_name,
    price,
    (
        SELECT AVG(price)
        FROM Product
    ) AS average_product_price
FROM Product
WHERE price > (
    SELECT AVG(price)
    FROM Product
)
ORDER BY price DESC;
```

Take a screenshot of this report.

## 3. Identify customers with maximum purchases

This query finds the customer or customers with the highest total purchase amount. It supports ties.

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    SUM(o.total_amount) AS total_purchase_amount
FROM Customer c
JOIN Orders o
    ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name
HAVING SUM(o.total_amount) >= ALL (
    SELECT
        SUM(total_amount)
    FROM Orders
    GROUP BY customer_id
);
```

### Customer orders above their own average order amount

```sql
SELECT
    o.order_id,
    c.name AS customer_name,
    o.order_date,
    o.total_amount
FROM Orders o
JOIN Customer c
    ON o.customer_id = c.customer_id
WHERE o.total_amount > (
    SELECT AVG(o2.total_amount)
    FROM Orders o2
    WHERE o2.customer_id = o.customer_id
)
ORDER BY o.total_amount DESC;
```

Take a screenshot of the maximum-purchase customer query.

## 4. Generate complex business queries

### Products never ordered by any customer

```sql
SELECT
    p.product_id,
    p.product_name,
    p.price,
    p.stock
FROM Product p
WHERE NOT EXISTS (
    SELECT 1
    FROM Order_Details od
    WHERE od.product_id = p.product_id
);
```

### Products sold more than the average quantity sold per product

```sql
SELECT
    p.product_id,
    p.product_name,
    SUM(od.quantity) AS total_quantity_sold
FROM Product p
JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY p.product_id, p.product_name
HAVING SUM(od.quantity) > (
    SELECT AVG(product_quantity)
    FROM (
        SELECT
            SUM(quantity) AS product_quantity
        FROM Order_Details
        GROUP BY product_id
    ) AS sales_by_product
)
ORDER BY total_quantity_sold DESC;
```

### Categories whose sales are above the average category sales

```sql
SELECT
    c.category_id,
    c.category_name,
    SUM(od.quantity * od.unit_price) AS category_sales
FROM Category c
JOIN Product p
    ON c.category_id = p.category_id
JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY c.category_id, c.category_name
HAVING SUM(od.quantity * od.unit_price) > (
    SELECT AVG(category_total)
    FROM (
        SELECT
            SUM(od2.quantity * od2.unit_price) AS category_total
        FROM Category c2
        JOIN Product p2
            ON c2.category_id = p2.category_id
        JOIN Order_Details od2
            ON p2.product_id = od2.product_id
        GROUP BY c2.category_id
    ) AS sales_by_category
)
ORDER BY category_sales DESC;
```

Take screenshots of any two complex business queries.

## 5. Advanced SQL report

This report displays each customer’s total spending, their number of orders, and their most recent order date.

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    (
        SELECT COUNT(*)
        FROM Orders o
        WHERE o.customer_id = c.customer_id
    ) AS total_orders,
    (
        SELECT COALESCE(SUM(o.total_amount), 0)
        FROM Orders o
        WHERE o.customer_id = c.customer_id
    ) AS total_spent,
    (
        SELECT MAX(o.order_date)
        FROM Orders o
        WHERE o.customer_id = c.customer_id
    ) AS latest_order_date
FROM Customer c
ORDER BY total_spent DESC, customer_name;
```

### Advanced product sales report

```sql
SELECT
    p.product_id,
    p.product_name,
    c.category_name,
    p.price,
    p.stock,
    COALESCE((
        SELECT SUM(od.quantity)
        FROM Order_Details od
        WHERE od.product_id = p.product_id
    ), 0) AS total_quantity_sold,
    COALESCE((
        SELECT SUM(od.quantity * od.unit_price)
        FROM Order_Details od
        WHERE od.product_id = p.product_id
    ), 0) AS total_sales_amount
FROM Product p
JOIN Category c
    ON p.category_id = c.category_id
ORDER BY total_sales_amount DESC, total_quantity_sold DESC;
```

Take a screenshot of one advanced report for your GitHub folder.

## Viva questions and answers

**1. What is a subquery?**  
A subquery is a query written inside another SQL query. The outer query uses the result of the inner query.

**2. What is a nested query?**  
A nested query is another name for a subquery, especially when there are multiple levels of queries.

**3. Why is a subquery used to find products above average price?**  
The inner query calculates the average product price, and the outer query selects products whose price is greater than that value.

**4. What does IN do in a subquery?**  
`IN` checks whether a value matches any value returned by a subquery.

**5. What does NOT EXISTS do?**  
`NOT EXISTS` checks whether the inner query returns no rows. It is used here to find products that have never been ordered.

**6. Why is GROUP BY used in the maximum-purchase query?**  
It calculates one total purchase amount for each customer.

**7. What is a correlated subquery?**  
It is a subquery that uses a value from the outer query. In this task, the customer’s own ID is used to compare an order with that customer’s average order value.
