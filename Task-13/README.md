# Task XIII - Database Security and Performance Optimization

This task demonstrates MySQL access control and query-performance improvements for the E-Commerce Order Management Database.

> Run account-management commands only if you have MySQL administrator permission. Replace `ecommerce_db` with your actual database name and replace example passwords with strong private passwords.

## 1. Create database users and roles

```sql
CREATE ROLE IF NOT EXISTS 'ecommerce_reporter';

CREATE USER IF NOT EXISTS 'report_user'@'localhost'
IDENTIFIED BY 'ReplaceWithAStrongPassword';
```

## 2. Implement GRANT commands

Give the reporting role read-only access to the project database, then assign that role to the reporting user.

```sql
GRANT SELECT
ON ecommerce_db.*
TO 'ecommerce_reporter';

GRANT 'ecommerce_reporter'
TO 'report_user'@'localhost';

SET DEFAULT ROLE 'ecommerce_reporter'
TO 'report_user'@'localhost';
```

Verify the assigned permissions:

```sql
SHOW GRANTS FOR 'report_user'@'localhost';
```

## 3. Apply access control with REVOKE

The following command removes the reporting role from the user. Run it only when you want to remove their access.

```sql
REVOKE 'ecommerce_reporter'
FROM 'report_user'@'localhost';
```

To allow a staff member to update order status but not delete records:

```sql
CREATE USER IF NOT EXISTS 'order_staff'@'localhost'
IDENTIFIED BY 'ReplaceWithAnotherStrongPassword';

GRANT SELECT, INSERT, UPDATE
ON ecommerce_db.Orders
TO 'order_staff'@'localhost';

GRANT SELECT, INSERT, UPDATE
ON ecommerce_db.Order_Details
TO 'order_staff'@'localhost';

SHOW GRANTS FOR 'order_staff'@'localhost';
```

## 4. Create indexes for faster searching

Create each index once.

```sql
CREATE INDEX idx_product_category_price
ON Product(category_id, price);

CREATE INDEX idx_orders_customer_date
ON Orders(customer_id, order_date);

CREATE INDEX idx_order_details_product
ON Order_Details(product_id);

CREATE INDEX idx_payment_order_status
ON Payment(order_id, payment_status);
```

## 5. Analyze and optimize query performance

Use `EXPLAIN` before and after adding relevant indexes to see how MySQL plans the query.

```sql
EXPLAIN
SELECT
    p.product_name,
    p.price,
    p.stock
FROM Product p
WHERE p.category_id = 1
  AND p.price >= 1000
ORDER BY p.price;
```

```sql
EXPLAIN
SELECT
    o.order_id,
    o.order_date,
    o.total_amount
FROM Orders o
WHERE o.customer_id = 1
ORDER BY o.order_date DESC;
```

### Useful performance query

```sql
SELECT
    p.product_id,
    p.product_name,
    SUM(od.quantity) AS total_quantity_sold
FROM Product p
JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY p.product_id, p.product_name
ORDER BY total_quantity_sold DESC;
```

## Testing checklist

```sql
SHOW INDEX FROM Product;
SHOW INDEX FROM Orders;
SHOW INDEX FROM Order_Details;
SHOW INDEX FROM Payment;
```

Take screenshots of `SHOW GRANTS`, at least one `SHOW INDEX` result, and one `EXPLAIN` result.

## Viva questions and answers

**What is a role?**  
A role is a named collection of database permissions that can be assigned to users.

**What does GRANT do?**  
It gives a user or role permission to perform selected database operations.

**What does REVOKE do?**  
It removes a previously granted permission or role assignment.

**What is an index?**  
An index is a database structure that can make searching and joining faster.

**Why use EXPLAIN?**  
It shows how MySQL plans to execute a query, helping identify whether indexes are being used.
