# Task II — Product and Category Management System

## Objective

Create Category and Product tables, define their relationship, manage product data, and generate a category-wise report.

## MySQL commands

```sql
CREATE TABLE Category (
    category_id INT PRIMARY KEY AUTO_INCREMENT,
    category_name VARCHAR(100) NOT NULL UNIQUE,
    description VARCHAR(225)
);

CREATE TABLE Product (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(100) NOT NULL,
    category_id INT NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    stock INT NOT NULL,
    brand VARCHAR(25),
    FOREIGN KEY (category_id) REFERENCES Category(category_id)
);

INSERT INTO Category (category_name, description) VALUES
('Electronics', 'Electronic Products and Accessories'),
('Mobiles', 'Mobile Phones and Tablets'),
('Fashion', 'Clothing and Fashion Products'),
('Appliances', 'Home and Kitchen Appliances');

INSERT INTO Product (product_name, category_id, price, stock, brand) VALUES
('iPhone 15', 2, 69999.00, 25, 'Apple'),
('Galaxy S24', 2, 64999.00, 30, 'Samsung'),
('Wireless Headphones', 1, 2999.00, 50, 'Sony'),
('Smart TV 43 inch', 1, 32999.00, 15, 'LG'),
('Men Casual Shirt', 3, 399.00, 100, 'Roadster'),
('Washing Machine', 4, 28999.00, 15, 'Whirlpool');

UPDATE Category
SET category_name = 'Fashion'
WHERE category_id = 3;

DELETE FROM Product
WHERE product_id = 6;

SELECT * FROM Category;
SELECT * FROM Product;

SELECT
    c.category_name,
    COUNT(p.product_id) AS total_products
FROM Category c
LEFT JOIN Product p
    ON c.category_id = p.category_id
GROUP BY c.category_id, c.category_name
ORDER BY c.category_name;
```

## Screenshots included

The existing images show the table results, update/delete operation, relationships, and category-wise report.
