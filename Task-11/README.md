# Task XI - Database Automation using Views and Procedures

This task creates reusable database objects for the existing E-Commerce Order Management System. Run it after Tasks I to V.

> These commands use MySQL syntax. Run each `CREATE` command only once. If an object with the same name already exists, remove or rename that object only if you intend to replace it.

## 1. Create views for frequently used reports

### Customer order history view

```sql
CREATE VIEW vw_customer_order_history AS
SELECT
    c.customer_id,
    c.name AS customer_name,
    o.order_id,
    o.order_date,
    o.order_status,
    p.product_name,
    od.quantity,
    od.unit_price,
    (od.quantity * od.unit_price) AS item_total
FROM Customer c
JOIN Orders o
    ON c.customer_id = o.customer_id
JOIN Order_Details od
    ON o.order_id = od.order_id
JOIN Product p
    ON od.product_id = p.product_id;
```

```sql
SELECT * FROM vw_customer_order_history
ORDER BY customer_id, order_date, order_id;
```

### Inventory status view

```sql
CREATE VIEW vw_inventory_status AS
SELECT
    i.inventory_id,
    s.seller_name,
    p.product_name,
    i.quantity_available,
    i.availability_status,
    i.last_updated
FROM Inventory i
JOIN Seller s
    ON i.seller_id = s.seller_id
JOIN Product p
    ON i.product_id = p.product_id;
```

```sql
SELECT * FROM vw_inventory_status
ORDER BY quantity_available;
```

## 2. Develop a stored procedure for order processing

The procedure creates a one-product order and its order-detail line. It reads the product price from the Product table and saves that price in Order_Details.

```sql
DELIMITER $$

CREATE PROCEDURE sp_place_single_product_order(
    IN p_customer_id INT,
    IN p_product_id INT,
    IN p_quantity INT,
    OUT p_order_id INT
)
BEGIN
    DECLARE v_unit_price DECIMAL(10,2);

    IF p_quantity <= 0 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Quantity must be greater than zero';
    END IF;

    SELECT price
    INTO v_unit_price
    FROM Product
    WHERE product_id = p_product_id;

    IF v_unit_price IS NULL THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Product does not exist';
    END IF;

    INSERT INTO Orders
        (customer_id, order_date, total_amount, order_status)
    VALUES
        (p_customer_id, CURDATE(), v_unit_price * p_quantity, 'Confirmed');

    SET p_order_id = LAST_INSERT_ID();

    INSERT INTO Order_Details
        (order_id, product_id, quantity, unit_price)
    VALUES
        (p_order_id, p_product_id, p_quantity, v_unit_price);
END $$

DELIMITER ;
```

### Test the procedure

Replace the sample IDs only if those IDs do not exist in your tables.

```sql
CALL sp_place_single_product_order(1, 1, 1, @new_order_id);

SELECT @new_order_id AS new_order_id;

SELECT * FROM Orders
WHERE order_id = @new_order_id;

SELECT * FROM Order_Details
WHERE order_id = @new_order_id;
```

## 3. Create a function for sales calculations

```sql
DELIMITER $$

CREATE FUNCTION fn_order_total(p_order_id INT)
RETURNS DECIMAL(10,2)
READS SQL DATA
DETERMINISTIC
BEGIN
    DECLARE v_total DECIMAL(10,2);

    SELECT COALESCE(SUM(quantity * unit_price), 0)
    INTO v_total
    FROM Order_Details
    WHERE order_id = p_order_id;

    RETURN v_total;
END $$

DELIMITER ;
```

```sql
SELECT
    order_id,
    fn_order_total(order_id) AS calculated_order_total
FROM Orders
ORDER BY order_id;
```

## 4. Automate repetitive database operations

This procedure recalculates and updates the stored total of an existing order.

```sql
DELIMITER $$

CREATE PROCEDURE sp_refresh_order_total(IN p_order_id INT)
BEGIN
    UPDATE Orders
    SET total_amount = fn_order_total(p_order_id)
    WHERE order_id = p_order_id;
END $$

DELIMITER ;
```

```sql
CALL sp_refresh_order_total(1);

SELECT order_id, total_amount
FROM Orders
WHERE order_id = 1;
```

## 5. Test database objects

```sql
SELECT * FROM vw_customer_order_history;

SELECT * FROM vw_inventory_status;

CALL sp_refresh_order_total(1);

SELECT fn_order_total(1) AS order_1_calculated_total;
```

Take screenshots of the view results, procedure test, and function result.

## Viva questions and answers

**What is a view?**  
A view is a saved SQL query that behaves like a virtual table.

**What is a stored procedure?**  
A stored procedure is reusable SQL code stored in the database and run using `CALL`.

**What is a function?**  
A function accepts input and returns one value. Here, it returns an order total.

**Why store unit_price in Order_Details?**  
It preserves the price at the time of ordering, even if the Product price changes later.
