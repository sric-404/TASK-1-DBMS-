# Task VII — SQL Query Implementation for E-Commerce Database

## Objective

Implement SQL queries for the existing E-Commerce Order Management Database using the tables created in earlier tasks.

This task demonstrates:

1. `SELECT`, `WHERE`, `ORDER BY`, and `DISTINCT` queries.
2. Product search based on price, category, and availability.
3. Retrieval of customer and product information.
4. Filtering using multiple conditions.
5. Basic business reports using joins and aggregate functions.

> Run the earlier tasks first because this task uses the existing `Customer`, `Category`, `Product`, `Orders`, and `Order_Details` tables.

## MySQL commands

The complete executable SQL is available in [`task7.mysql`](./task7.mysql).

### 1. SELECT, WHERE, ORDER BY and DISTINCT

```sql
SELECT * FROM Product;

SELECT *
FROM Product
WHERE price > 1000;

SELECT *
FROM Product
ORDER BY price DESC;

SELECT DISTINCT category_id
FROM Product;
```

### 2. Search products based on price, category and availability

```sql
SELECT product_id, product_name, price, stock, brand
FROM Product
WHERE price BETWEEN 500 AND 5000;

SELECT product_id, product_name, category_id, price, stock, brand
FROM Product
WHERE category_id = 1;

SELECT product_id, product_name, price, stock
FROM Product
WHERE stock > 0;

SELECT product_id, product_name, category_id, price, stock, brand
FROM Product
WHERE price < 5000
  AND category_id = 1
  AND stock > 0;
```

Category-name based search:

```sql
SELECT
    p.product_id,
    p.product_name,
    c.category_name,
    p.price,
    p.stock,
    p.brand
FROM Product p
JOIN Category c
    ON p.category_id = c.category_id
WHERE c.category_name = 'Electronics';
```

### 3. Retrieve customer and product information

```sql
SELECT
    customer_id,
    name,
    email,
    phone,
    address
FROM Customer;
```

```sql
SELECT
    p.product_id,
    p.product_name,
    c.category_name,
    p.price,
    p.stock,
    p.brand
FROM Product p
JOIN Category c
    ON p.category_id = c.category_id;
```

Customer-product order information:

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    o.order_id,
    o.order_date,
    p.product_id,
    p.product_name,
    od.quantity,
    od.unit_price,
    o.total_amount,
    o.order_status
FROM Customer c
JOIN Orders o
    ON c.customer_id = o.customer_id
JOIN Order_Details od
    ON o.order_id = od.order_id
JOIN Product p
    ON od.product_id = p.product_id
ORDER BY c.customer_id, o.order_date;
```

### 4. Apply filtering conditions

```sql
SELECT *
FROM Product
WHERE price > 1000
  AND stock >= 10;

SELECT
    p.product_name,
    c.category_name,
    p.price
FROM Product p
JOIN Category c
    ON p.category_id = c.category_id
WHERE c.category_name IN ('Electronics', 'Mobiles');

SELECT *
FROM Product
WHERE product_name LIKE '%Phone%';

SELECT
    order_id,
    customer_id,
    order_date,
    total_amount,
    order_status
FROM Orders
WHERE total_amount > 5000
  AND order_status IN ('Confirmed', 'Shipped')
ORDER BY total_amount DESC;
```

### 5. Basic business reports

#### Products per category

```sql
SELECT
    c.category_id,
    c.category_name,
    COUNT(p.product_id) AS total_products
FROM Category c
LEFT JOIN Product p
    ON c.category_id = p.category_id
GROUP BY c.category_id, c.category_name
ORDER BY c.category_name;
```

#### Average product price by category

```sql
SELECT
    c.category_name,
    ROUND(AVG(p.price), 2) AS average_price
FROM Category c
JOIN Product p
    ON c.category_id = p.category_id
GROUP BY c.category_id, c.category_name
ORDER BY average_price DESC;
```

#### Total stock by category

```sql
SELECT
    c.category_name,
    SUM(p.stock) AS total_stock
FROM Category c
JOIN Product p
    ON c.category_id = p.category_id
GROUP BY c.category_id, c.category_name
ORDER BY total_stock DESC;
```

#### Top 5 most expensive products

```sql
SELECT
    product_id,
    product_name,
    brand,
    price
FROM Product
ORDER BY price DESC
LIMIT 5;
```

#### Low-stock products

```sql
SELECT
    product_id,
    product_name,
    stock
FROM Product
WHERE stock < 20
ORDER BY stock ASC;
```

#### Customer order summary

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    COUNT(o.order_id) AS total_orders,
    COALESCE(SUM(o.total_amount), 0) AS total_spent
FROM Customer c
LEFT JOIN Orders o
    ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name
ORDER BY total_spent DESC;
```

#### Product sales summary

```sql
SELECT
    p.product_id,
    p.product_name,
    COALESCE(SUM(od.quantity), 0) AS total_quantity_sold,
    COALESCE(SUM(od.quantity * od.unit_price), 0) AS sales_value
FROM Product p
LEFT JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY p.product_id, p.product_name
ORDER BY total_quantity_sold DESC, sales_value DESC;
```

## Result

The required SQL query operations were implemented successfully for the E-Commerce database. The queries support product searching, customer and product retrieval, filtering, sorting, distinct values, joins, and basic business reporting.
