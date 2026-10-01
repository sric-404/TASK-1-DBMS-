# Task VIII — Database Relationship Analysis Using Joins

## Objective

Analyze the relationships in the existing E-Commerce Order Management Database using SQL joins. This task uses the tables created in earlier tasks:

`Customer` → `Orders` → `Order_Details` → `Product`

`Orders` → `Payment`

> Run Tasks I, II, IV, and V first. In the `Payment` table, the payment type column is `payment_mode`.

## MySQL commands

### 1. INNER JOIN — customers and their orders

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    o.order_id,
    o.order_date,
    o.total_amount,
    o.order_status
FROM Customer c
INNER JOIN Orders o
    ON c.customer_id = o.customer_id
ORDER BY o.order_id;
```

### 2. LEFT JOIN — all customers, including customers without orders

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    o.order_id,
    o.order_date,
    o.total_amount,
    o.order_status
FROM Customer c
LEFT JOIN Orders o
    ON c.customer_id = o.customer_id
ORDER BY c.customer_id, o.order_date;
```

### 3. RIGHT JOIN — all products, including products not ordered yet

```sql
SELECT
    p.product_id,
    p.product_name,
    od.order_id,
    od.quantity,
    od.unit_price
FROM Order_Details od
RIGHT JOIN Product p
    ON od.product_id = p.product_id
ORDER BY p.product_id, od.order_id;
```

### 4. Complete order details

```sql
SELECT
    o.order_id,
    o.order_date,
    c.name AS customer_name,
    p.product_name,
    od.quantity,
    od.unit_price,
    (od.quantity * od.unit_price) AS line_total,
    o.total_amount,
    o.order_status
FROM Orders o
INNER JOIN Customer c
    ON o.customer_id = c.customer_id
INNER JOIN Order_Details od
    ON o.order_id = od.order_id
INNER JOIN Product p
    ON od.product_id = p.product_id
ORDER BY o.order_id, od.order_detail_id;
```

### 5. Customer purchase history

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    o.order_id,
    o.order_date,
    p.product_name,
    od.quantity,
    od.unit_price,
    (od.quantity * od.unit_price) AS line_total,
    o.total_amount,
    o.order_status
FROM Customer c
LEFT JOIN Orders o
    ON c.customer_id = o.customer_id
LEFT JOIN Order_Details od
    ON o.order_id = od.order_id
LEFT JOIN Product p
    ON od.product_id = p.product_id
ORDER BY c.customer_id, o.order_date, o.order_id;
```

### 6. Multi-table order and payment report

```sql
SELECT
    c.name AS customer_name,
    o.order_id,
    o.order_date,
    p.product_name,
    od.quantity,
    od.unit_price,
    o.total_amount,
    o.order_status,
    pay.payment_mode,
    pay.payment_date,
    pay.amount AS payment_amount,
    pay.payment_status,
    pay.transaction_reference
FROM Customer c
INNER JOIN Orders o
    ON c.customer_id = o.customer_id
INNER JOIN Order_Details od
    ON o.order_id = od.order_id
INNER JOIN Product p
    ON od.product_id = p.product_id
LEFT JOIN Payment pay
    ON o.order_id = pay.order_id
ORDER BY c.name, o.order_id, od.order_detail_id;
```

## Result

The relationships among `Customer`, `Orders`, `Order_Details`, `Product`, and `Payment` were analyzed successfully. The queries demonstrate `INNER JOIN`, `LEFT JOIN`, and `RIGHT JOIN`, provide complete order details and customer purchase history, and generate a combined order-and-payment report. The Payment table uses `payment_mode`, not `payment_method`.
