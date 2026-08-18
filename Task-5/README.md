# Task V — Payment Transaction Management System

This task extends the existing E-Commerce Order Management System. Run this after completing the Order Management System because `Payment` references `Orders(order_id)`.

## 1. Create the Payment table

```sql
CREATE TABLE Payment (
    payment_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    payment_mode VARCHAR(30) NOT NULL,
    payment_date DATE NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    payment_status VARCHAR(20) NOT NULL,
    transaction_reference VARCHAR(100) UNIQUE,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id)
);
```

## 2. Store payment details

```sql
INSERT INTO Payment
(order_id, payment_mode, payment_date, amount, payment_status, transaction_reference)
VALUES
(1, 'UPI', '2026-08-10', 72998.00, 'Successful', 'UPI-10001'),
(2, 'Credit Card', '2026-08-11', 64999.00, 'Successful', 'CC-10002'),
(3, 'Debit Card', '2026-08-12', 2999.00, 'Failed', 'DC-10003'),
(4, 'Cash on Delivery', '2026-08-13', 32999.00, 'Successful', 'COD-10004'),
(5, 'UPI', '2026-08-14', 399.00, 'Failed', 'UPI-10005'),
(6, 'Net Banking', '2026-08-15', 5998.00, 'Successful', 'NB-10006');

SELECT * FROM Payment;
```

> If your existing `Orders` table uses different order IDs, replace the order IDs in the sample data with IDs that exist in your table.

## 3. Manage successful and failed transactions

### Successful payments

```sql
SELECT *
FROM Payment
WHERE payment_status = 'Successful';
```

### Failed payments

```sql
SELECT *
FROM Payment
WHERE payment_status = 'Failed';
```

### Update a failed transaction after a successful retry

```sql
UPDATE Payment
SET payment_status = 'Successful',
    payment_mode = 'UPI',
    payment_date = '2026-08-16',
    transaction_reference = 'UPI-10003-R'
WHERE payment_id = 3;

SELECT * FROM Payment
WHERE payment_id = 3;
```

## 4. Analyze payment methods used by customers

```sql
SELECT
    p.payment_mode,
    COUNT(*) AS transactions_count,
    COUNT(DISTINCT o.customer_id) AS customers_count,
    SUM(CASE WHEN p.payment_status = 'Successful' THEN p.amount ELSE 0 END) AS successful_amount
FROM Payment p
JOIN Orders o
    ON p.order_id = o.order_id
GROUP BY p.payment_mode
ORDER BY transactions_count DESC;
```

## 5. Payment transaction report

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    o.order_id,
    o.order_date,
    p.payment_id,
    p.payment_mode,
    p.amount,
    p.payment_date,
    p.payment_status,
    p.transaction_reference
FROM Customer c
JOIN Orders o
    ON c.customer_id = o.customer_id
JOIN Payment p
    ON o.order_id = p.order_id
ORDER BY p.payment_date, p.payment_id;
```

