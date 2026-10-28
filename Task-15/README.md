# Task XV - Industry Capstone Project: E-Commerce Intelligence Platform

This final task combines the completed E-Commerce Order Management Database modules into one practical database project.

## 1. Integrated database modules

The finished platform contains these connected modules:

| Module | Main tables |
|---|---|
| Customer management | Customer |
| Product management | Category, Product |
| Seller and stock management | Seller, Inventory |
| Order management | Orders, Order_Details |
| Payment management | Payment |
| Feedback management | Review, Rating |

## 2. ER diagram and normalized schema

### Relationship summary

```text
Customer 1 ───< Orders 1 ───< Order_Details >─── 1 Product >─── 1 Category
Seller   1 ───< Inventory >─── 1 Product
Orders   1 ───< Payment
Customer 1 ───< Review >─── 1 Product
Review   1 ─── 1 Rating
```

### Normalization summary

- Each table has a primary key.
- Foreign keys connect related records.
- Product category information is stored in Category instead of repeating it in Product.
- Order headers are stored in Orders, while individual products are stored in Order_Details.
- Payment details are stored separately from orders.
- This structure avoids unnecessary duplication and supports Third Normal Form (3NF).

## 3. Insert realistic business data

Use the sample records already inserted in Tasks I–VI. Before adding new records, check the current data to avoid duplicate primary keys.

```sql
SELECT * FROM Customer;
SELECT * FROM Category;
SELECT * FROM Product;
SELECT * FROM Seller;
SELECT * FROM Inventory;
SELECT * FROM Orders;
SELECT * FROM Order_Details;
SELECT * FROM Payment;
```

## 4. Basic and advanced SQL report

```sql
SELECT
    c.name AS customer_name,
    o.order_id,
    o.order_date,
    p.product_name,
    cat.category_name,
    od.quantity,
    od.unit_price,
    (od.quantity * od.unit_price) AS item_total,
    o.order_status
FROM Customer c
JOIN Orders o
    ON c.customer_id = o.customer_id
JOIN Order_Details od
    ON o.order_id = od.order_id
JOIN Product p
    ON od.product_id = p.product_id
JOIN Category cat
    ON p.category_id = cat.category_id
ORDER BY o.order_date DESC, o.order_id;
```

### Top customers

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    SUM(o.total_amount) AS total_spent
FROM Customer c
JOIN Orders o
    ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name
ORDER BY total_spent DESC
LIMIT 5;
```

## 5. View, procedure, trigger, and index

### Capstone sales view

```sql
CREATE VIEW vw_capstone_sales_dashboard AS
SELECT
    p.product_id,
    p.product_name,
    c.category_name,
    COALESCE(SUM(od.quantity), 0) AS total_quantity_sold,
    COALESCE(SUM(od.quantity * od.unit_price), 0) AS total_sales_amount
FROM Product p
JOIN Category c
    ON p.category_id = c.category_id
LEFT JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY p.product_id, p.product_name, c.category_name;
```

```sql
SELECT * FROM vw_capstone_sales_dashboard
ORDER BY total_sales_amount DESC;
```

### Procedure to update an order status

```sql
DELIMITER $$

CREATE PROCEDURE sp_update_order_status(
    IN p_order_id INT,
    IN p_order_status VARCHAR(20)
)
BEGIN
    UPDATE Orders
    SET order_status = p_order_status
    WHERE order_id = p_order_id;
END $$

DELIMITER ;
```

```sql
CALL sp_update_order_status(1, 'Shipped');
```

### Trigger to validate ratings

Run this only after the Rating table from Task VI has been created.

```sql
DELIMITER $$

CREATE TRIGGER trg_validate_rating
BEFORE INSERT ON Rating
FOR EACH ROW
BEGIN
    IF NEW.rating_value < 1 OR NEW.rating_value > 5 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Rating value must be between 1 and 5';
    END IF;
END $$

DELIMITER ;
```

### Index for frequent order-detail product searches

```sql
CREATE INDEX idx_capstone_order_details_product
ON Order_Details(product_id);
```

> If you already created an index on `Order_Details(product_id)` in Task XIII, do not create this duplicate index.

## 6. Generate business insights

```sql
SELECT
    c.category_name,
    SUM(od.quantity * od.unit_price) AS category_sales,
    SUM(od.quantity) AS items_sold
FROM Category c
JOIN Product p
    ON c.category_id = p.category_id
JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY c.category_id, c.category_name
ORDER BY category_sales DESC;
```

Use the results to explain:

- Which category earns the highest sales.
- Which product has the highest quantity sold.
- Which customer has the highest purchase amount.
- Which products need restocking based on Inventory quantity.

## 7. Final presentation checklist

- Show the GitHub folder structure for Tasks I–XV.
- Show the ER relationship summary.
- Run one basic report and one advanced report.
- Demonstrate the view and stored procedure.
- Show the payment and inventory transaction from Task XII.
- Show the index and `EXPLAIN` report from Task XIII.
- Explain one business insight from your data.

## Viva questions and answers

**Why is this called an intelligence platform?**  
It combines transaction data with reports, customer analysis, product analysis, and business insights.

**What is the main benefit of normalization?**  
It reduces duplicated data and keeps updates consistent.

**How does the platform support decision-making?**  
Reports reveal sales trends, top customers, popular products, and inventory needs.

**Why are views, procedures, triggers, and indexes used?**  
They make common tasks reusable, automate validation or operations, and improve performance.
