# Task III — Seller and Inventory Management System

## Objective

Create Seller and Inventory tables, link sellers to products and stock, maintain seller-product data, track availability, and generate inventory reports.

## MySQL commands

```sql
CREATE TABLE Seller (
    seller_id INT PRIMARY KEY AUTO_INCREMENT,
    seller_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    phone VARCHAR(15),
    city VARCHAR(50)
);

CREATE TABLE Inventory (
    inventory_id INT PRIMARY KEY AUTO_INCREMENT,
    seller_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity_available INT NOT NULL DEFAULT 0,
    availability_status VARCHAR(20) NOT NULL,
    last_updated DATE NOT NULL,
    FOREIGN KEY (seller_id) REFERENCES Seller(seller_id),
    FOREIGN KEY (product_id) REFERENCES Product(product_id)
);

INSERT INTO Seller (seller_name, email, phone, city) VALUES
('Tech World', 'techworld@gmail.com', '9000000001', 'Chennai'),
('Mobile Hub', 'mobilehub@gmail.com', '9000000002', 'Bengaluru'),
('Fashion Store', 'fashionstore@gmail.com', '9000000003', 'Mumbai');

INSERT INTO Inventory
(seller_id, product_id, quantity_available, availability_status, last_updated)
VALUES
(1, 3, 50, 'Available', '2026-08-10'),
(1, 4, 15, 'Available', '2026-08-10'),
(2, 1, 25, 'Available', '2026-08-10'),
(2, 2, 0, 'Unavailable', '2026-08-10'),
(3, 5, 100, 'Available', '2026-08-10');

SELECT
    s.seller_name,
    p.product_name,
    i.quantity_available,
    i.availability_status,
    i.last_updated
FROM Inventory i
JOIN Seller s ON i.seller_id = s.seller_id
JOIN Product p ON i.product_id = p.product_id
ORDER BY s.seller_name, p.product_name;

SELECT
    p.product_name,
    i.quantity_available,
    i.availability_status
FROM Inventory i
JOIN Product p ON i.product_id = p.product_id
WHERE i.availability_status = 'Available';

SELECT
    p.product_name,
    i.quantity_available,
    i.availability_status
FROM Inventory i
JOIN Product p ON i.product_id = p.product_id
WHERE i.availability_status = 'Unavailable';

SELECT
    s.seller_name,
    COUNT(i.inventory_id) AS products_managed,
    SUM(i.quantity_available) AS total_stock
FROM Seller s
LEFT JOIN Inventory i ON s.seller_id = i.seller_id
GROUP BY s.seller_id, s.seller_name
ORDER BY s.seller_name;
```

