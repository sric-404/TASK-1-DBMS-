# Task I — Requirement Analysis and Customer Database Module

## Objective

Analyze the E-Commerce Order Management System and create the base customer database used by later tasks.

The original requirement specification is retained in the repository root as `Amazon SRS E-Commerce Platform.docx`.

## Customer table and sample data

```sql
CREATE TABLE Customer (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    phone VARCHAR(15) NOT NULL,
    address VARCHAR(255) NOT NULL
);

INSERT INTO Customer (name, email, phone, address) VALUES
('Arun Kumar', 'arun@gmail.com', '9876543210', 'Chennai'),
('Priya Sharma', 'priya@gmail.com', '9876543211', 'Bengaluru'),
('Rahul Verma', 'rahul@gmail.com', '9876543212', 'Hyderabad'),
('Sneha Reddy', 'sneha@gmail.com', '9876543213', 'Mumbai'),
('Karthik Raj', 'karthik@gmail.com', '9876543214', 'Coimbatore');

SELECT * FROM Customer;
```

## Screenshots to upload

1. Requirement specification document
2. Customer table creation
3. Customer data after `SELECT * FROM Customer`
