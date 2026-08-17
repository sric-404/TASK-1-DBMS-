# Task IV — Order Management System

## Objective

Create Orders and Order_Details tables, manage customer product orders, perform order insertion and modification, and generate order-history reports.

> Run Task I and Task II first: this task uses `Customer` and `Product`.

## MySQL commands

```sql
CREATE TABLE Orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date DATE NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL,
    order_status VARCHAR(20) NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES Customer(customer_id)
);

CREATE TABLE Order_Details (
    order_detail_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (product_id) REFERENCES Product(product_id)
);

INSERT INTO Orders (customer_id, order_date, total_amount, order_status) VALUES
(1, '2026-08-10', 72998.00, 'Confirmed'),
(2, '2026-08-11', 64999.00, 'Confirmed'),
(3, '2026-08-12', 2999.00, 'Confirmed'),
(4, '2026-08-13', 32999.00, 'Confirmed'),
(5, '2026-08-14', 399.00, 'Confirmed');

INSERT INTO Order_Details (order_id, product_id, quantity, unit_price) VALUES
(1, 1, 1, 69999.00),
(1, 3, 1, 2999.00),
(2, 2, 1, 64999.00),
(3, 3, 1, 2999.00),
(4, 4, 1, 32999.00),
(5, 5, 1, 399.00);

INSERT INTO Orders (customer_id, order_date, total_amount, order_status)
VALUES (1, '2026-08-15', 2999.00, 'Confirmed');

INSERT INTO Order_Details (order_id, product_id, quantity, unit_price)
VALUES (6, 3, 1, 2999.00);

UPDATE Orders
SET order_status = 'Shipped'
WHERE order_id = 6;

UPDATE Order_Details
SET quantity = 2
WHERE order_id = 6 AND product_id = 3;

UPDATE Orders
SET total_amount = 5998.00
WHERE order_id = 6;

SELECT
    c.name AS customer_name,
    o.order_id,
    p.product_name,
    od.quantity,
    od.unit_price,
    o.total_amount,
    o.order_status
FROM Customer c
JOIN Orders o ON c.customer_id = o.customer_id
JOIN Order_Details od ON o.order_id = od.order_id
JOIN Product p ON od.product_id = p.product_id
ORDER BY o.order_id;

SELECT
    c.customer_id,
    c.name AS customer_name,
    o.order_id,
    o.order_date,
    p.product_name,
    od.quantity,
    od.unit_price,
    o.total_amount,
    o.order_status
FROM Customer c
JOIN Orders o ON c.customer_id = o.customer_id
JOIN Order_Details od ON o.order_id = od.order_id
JOIN Product p ON od.product_id = p.product_id
ORDER BY c.customer_id, o.order_date;
```

## Screenshots included

Existing screenshots show the Orders/Order_Details design and customer product orders. Add screenshots of the insertion/modification commands and the order-history report after executing them.
