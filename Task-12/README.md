# Task XII - Transaction Management System

This task demonstrates safe order, payment, and stock-update operations using MySQL transactions.

Run it after creating the `Orders`, `Order_Details`, `Payment`, and `Inventory` tables.

## 1. Implement an order-payment-stock transaction

The procedure below locks one inventory record, checks stock, records the payment, updates the order status, and reduces that exact inventory record. All changes succeed together or none are saved.

```sql
DELIMITER $$

CREATE PROCEDURE sp_complete_payment_and_reduce_stock(
    IN p_order_id INT,
    IN p_inventory_id INT,
    IN p_payment_mode VARCHAR(30),
    IN p_transaction_reference VARCHAR(100)
)
BEGIN
    DECLARE v_product_id INT;
    DECLARE v_stock INT;
    DECLARE v_quantity_needed INT;
    DECLARE v_order_total DECIMAL(10,2);

    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        RESIGNAL;
    END;

    START TRANSACTION;

    SELECT product_id, quantity_available
    INTO v_product_id, v_stock
    FROM Inventory
    WHERE inventory_id = p_inventory_id
    FOR UPDATE;

    IF v_product_id IS NULL THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Inventory record does not exist';
    END IF;

    SELECT COALESCE(SUM(quantity), 0)
    INTO v_quantity_needed
    FROM Order_Details
    WHERE order_id = p_order_id
      AND product_id = v_product_id;

    IF v_quantity_needed = 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'This inventory product is not in the selected order';
    END IF;

    IF v_stock < v_quantity_needed THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Insufficient stock';
    END IF;

    SELECT total_amount
    INTO v_order_total
    FROM Orders
    WHERE order_id = p_order_id
    FOR UPDATE;

    IF v_order_total IS NULL THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Order does not exist';
    END IF;

    UPDATE Inventory
    SET quantity_available = quantity_available - v_quantity_needed,
        availability_status = CASE
            WHEN quantity_available - v_quantity_needed = 0 THEN 'Unavailable'
            ELSE 'Available'
        END,
        last_updated = CURDATE()
    WHERE inventory_id = p_inventory_id;

    INSERT INTO Payment
        (order_id, payment_mode, payment_date, amount, payment_status, transaction_reference)
    VALUES
        (p_order_id, p_payment_mode, CURDATE(), v_order_total, 'Successful', p_transaction_reference);

    UPDATE Orders
    SET order_status = 'Confirmed'
    WHERE order_id = p_order_id;

    COMMIT;
END $$

DELIMITER ;
```

### Test the transaction

Use an `order_id` and an `inventory_id` where the inventory product is present in that order.

```sql
CALL sp_complete_payment_and_reduce_stock(
    1,
    1,
    'UPI',
    'TXN-ORDER-1-001'
);

SELECT * FROM Payment
WHERE transaction_reference = 'TXN-ORDER-1-001';

SELECT * FROM Inventory
WHERE inventory_id = 1;
```

## 2. Demonstrate COMMIT

```sql
START TRANSACTION;

UPDATE Orders
SET order_status = 'Shipped'
WHERE order_id = 1;

COMMIT;

SELECT order_id, order_status
FROM Orders
WHERE order_id = 1;
```

## 3. Demonstrate ROLLBACK

This temporary change is cancelled, so the customer's phone number remains unchanged.

```sql
START TRANSACTION;

UPDATE Customer
SET phone = '0000000000'
WHERE customer_id = 1;

ROLLBACK;

SELECT customer_id, name, phone
FROM Customer
WHERE customer_id = 1;
```

## 4. Handle failed payments safely

A failed payment can be recorded without changing stock or marking the order as confirmed.

```sql
INSERT INTO Payment
    (order_id, payment_mode, payment_date, amount, payment_status, transaction_reference)
SELECT
    order_id,
    'Card',
    CURDATE(),
    total_amount,
    'Failed',
    'FAILED-ORDER-2-001'
FROM Orders
WHERE order_id = 2;
```

```sql
SELECT
    payment_id,
    order_id,
    payment_mode,
    amount,
    payment_status,
    transaction_reference
FROM Payment
ORDER BY payment_id DESC;
```

## 5. ACID properties

| Property | Meaning in this system |
|---|---|
| Atomicity | Payment, stock update, and order-status update either all succeed or all roll back. |
| Consistency | Foreign keys and stock checks prevent invalid data. |
| Isolation | `FOR UPDATE` locks the selected inventory and order rows during payment processing. |
| Durability | After `COMMIT`, successful changes remain stored in the database. |

Take screenshots of the COMMIT result, the ROLLBACK result, and one payment/stock result.

## Viva questions and answers

**What is a transaction?**  
A transaction is a group of SQL operations treated as one unit of work.

**What does COMMIT do?**  
It permanently saves all changes made in the current transaction.

**What does ROLLBACK do?**  
It cancels all uncommitted changes in the current transaction.

**Why use FOR UPDATE?**  
It locks the chosen row until the transaction finishes, helping prevent two payments from using the same stock at the same time.
