# Task VI - Product Review and Rating Management System

This task extends the existing E-Commerce Order Management System. Run this after completing the Customer and Product tables because `Review` and `Rating` reference `Customer(customer_id)` and `Product(product_id)`.

## 1. Create Review and Rating tables

```sql
CREATE TABLE Review (
    review_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    product_id INT NOT NULL,
    review_text VARCHAR(500) NOT NULL,
    review_date DATE NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES Customer(customer_id),
    FOREIGN KEY (product_id) REFERENCES Product(product_id)
);

CREATE TABLE Rating (
    rating_id INT PRIMARY KEY AUTO_INCREMENT,
    review_id INT NOT NULL,
    customer_id INT NOT NULL,
    product_id INT NOT NULL,
    rating_value INT NOT NULL,
    rating_date DATE NOT NULL,
    FOREIGN KEY (review_id) REFERENCES Review(review_id),
    FOREIGN KEY (customer_id) REFERENCES Customer(customer_id),
    FOREIGN KEY (product_id) REFERENCES Product(product_id)
);
```

## 2. Store customer feedback and ratings

```sql
INSERT INTO Review
(customer_id, product_id, review_text, review_date)
VALUES
(1, 1, 'Excellent phone with great camera quality.', '2026-08-17'),
(2, 2, 'Laptop performance is very good for daily work.', '2026-08-18'),
(3, 3, 'Headphones are comfortable and sound quality is clear.', '2026-08-19'),
(4, 4, 'Smart watch battery backup is good.', '2026-08-20'),
(5, 5, 'Book quality is nice and delivery was fast.', '2026-08-21');

SELECT * FROM Review;
```

```sql
INSERT INTO Rating
(review_id, customer_id, product_id, rating_value, rating_date)
VALUES
(1, 1, 1, 5, '2026-08-17'),
(2, 2, 2, 4, '2026-08-18'),
(3, 3, 3, 4, '2026-08-19'),
(4, 4, 4, 5, '2026-08-20'),
(5, 5, 5, 3, '2026-08-21');

SELECT * FROM Rating;
```

> If your existing `Customer` or `Product` table uses different IDs, replace the sample IDs with IDs that exist in your tables.

## 3. Retrieve product review details

```sql
SELECT
    p.product_id,
    p.product_name,
    c.customer_id,
    c.name AS customer_name,
    r.review_text,
    r.review_date,
    rt.rating_value
FROM Product p
JOIN Review r
    ON p.product_id = r.product_id
JOIN Customer c
    ON r.customer_id = c.customer_id
JOIN Rating rt
    ON r.review_id = rt.review_id
ORDER BY p.product_id;
```

## 4. Calculate average product ratings using aggregate functions

```sql
SELECT
    p.product_id,
    p.product_name,
    COUNT(rt.rating_id) AS total_ratings,
    AVG(rt.rating_value) AS average_rating
FROM Product p
JOIN Rating rt
    ON p.product_id = rt.product_id
GROUP BY p.product_id, p.product_name
ORDER BY average_rating DESC;
```

## 5. Identify highly rated products

```sql
SELECT
    p.product_id,
    p.product_name,
    AVG(rt.rating_value) AS average_rating
FROM Product p
JOIN Rating rt
    ON p.product_id = rt.product_id
GROUP BY p.product_id, p.product_name
HAVING AVG(rt.rating_value) >= 4
ORDER BY average_rating DESC;
```

## 6. Product review and rating report

```sql
SELECT
    c.name AS customer_name,
    p.product_name,
    r.review_text,
    rt.rating_value,
    r.review_date
FROM Customer c
JOIN Review r
    ON c.customer_id = r.customer_id
JOIN Product p
    ON r.product_id = p.product_id
JOIN Rating rt
    ON r.review_id = rt.review_id
ORDER BY rt.rating_value DESC, r.review_date;
```
