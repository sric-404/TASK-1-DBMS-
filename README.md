# TASK-DBMS-
Task - I

Requirement Analysis and Customer Database Module

Objective:
```
Analyze the business requirements of the E-Commerce Order Management System and design the Customer module.
```
Deliverables:
```
->Requirement Analysis Report (SRS)

->Customer Entity

->Customer Attributes

->ER Diagram (Customer)

->Relational Schema
```
Customer Table:

| Attribute | Data Type | Key |
|-----------|-----------|-----|
| Customer_ID | INT | PK |
| Name | VARCHAR(100) | |
| Email | VARCHAR(100) | Unique |
| Mobile_Number | VARCHAR(15) | |
| Address | VARCHAR(255) | |
| Registration_Date | DATE | |

Schema:
```
CUSTOMER(
Customer_ID PK,
Name,
Email,
Mobile_Number,
Address,
Registration_Date
)
```

Task - II

Product and Category Management System

Objective:
```
Design Product and Category tables.
```
Tables:

CATEGORY

| Attribute | Data Type | Key |
|-----------|-----------|-----|
| Category_ID | INT | PK |
| Category_Name | VARCHAR(100) | |
| Description | VARCHAR(255) | |

PRODUCT

| Attribute | Data Type | Key |
|-----------|-----------|-----|
| Product_ID | INT | PK |
| Product_Name | VARCHAR(100) | |
| Price | DECIMAL(10,2) | |
| Stock_Quantity | INT | |
| Category_ID | INT | FK |
| Supplier_ID | INT | FK |

Relationship:
```
Category (1)
      |
      | 1:M
      |
Product (*)
```
Operations:
```
->Insert Product
->Update Product
->Delete Product
->Category-wise Product Report
```

Task - III

Seller and Inventory Management System

This project uses Supplier instead of Seller.

Inventory is managed using Stock_Quantity in Product.

Tables:

SUPPLIER

| Attribute | Data Type | Key |
|-----------|-----------|-----|
| Supplier_ID | INT | PK |
| Supplier_Name | VARCHAR(100) | |
| Contact_Information | VARCHAR(255) | |

PRODUCT
```
Product_ID
Product_Name
Price
Stock_Quantity
Supplier_ID
```
Relationship:
```
Supplier (1)
      |
      | 1:M
      |
Product (*)
```
Inventory Status
```
Available:
Stock_Quantity > 0

Unavailable:
Stock_Quantity = 0
```
Reports:
```
->Available Products
->Out-of-Stock Products
->Supplier Product List
```

Task - IV

Order Management System

Tables:

ORDERS

| Attribute | Data Type | Key |
|-----------|-----------|-----|
| Order_ID | INT | PK |
| Customer_ID | INT | FK |
| Order_Date | DATE | |
| Total_Amount | DECIMAL(10,2) | |
| Order_Status | VARCHAR(20) | |

ORDER_DETAILS

| Attribute | Data Type | Key |
|-----------|-----------|-----|
| Order_Detail_ID | INT | PK |
| Order_ID | INT | FK |
| Product_ID | INT | FK |
| Quantity | INT | |
| Unit_Price | DECIMAL(10,2) | |

Relationships
```
Customer (1)
      |
      | 1:M
      |
Orders (*)

Orders (1)
      |
      | 1:M
      |
Order_Details (*)

Product (1)
      |
      | 1:M
      |
Order_Details (*)
```

Operations:
```
->Insert Orders
->Modify Orders
->Customer Order History
->Order Details Report
```
Final Tables:
```
CUSTOMER
CATEGORY
SUPPLIER
PRODUCT
ORDERS
ORDER_DETAILS
PAYMENT
SHIPMENT
REVIEW
```

Task V – Payment Transaction Management System

1. Create Payment Table

```
CREATE TABLE Payment (
    payment_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    payment_method VARCHAR(30) NOT NULL,
    payment_date DATE NOT NULL,
    payment_status VARCHAR(20) NOT NULL,
    FOREIGN KEY (order_id)
        REFERENCES Orders(order_id)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,
    CHECK (payment_status IN ('Successful', 'Failed', 'Pending'))
);
```

Attributes:
```
| Attribute | Data Type | Key |
|---|---|---|
| `payment_id` | INT | PK |
| `order_id` | INT | FK |
| `payment_method` | VARCHAR(30) | |
| `payment_date` | DATE | |
| `payment_status` | VARCHAR(20) | |
```

2. Relationship:

```
Orders (1) ─────────── (1) Payment
```

Note: One order only has one payment transaction.

3. Basic Payment Queries

Display all payments
```
SELECT * FROM Payment;
```

Successful transactions
```
SELECT *
FROM Payment
WHERE payment_status = 'Successful';
```

Failed transactions
```
SELECT *
FROM Payment
WHERE payment_status = 'Failed';
```

Pending transactions
```
SELECT *
FROM Payment
WHERE payment_status = 'Pending';
```

4. Analyze Payment Methods

Count payments by method
```
SELECT payment_method, COUNT(*) AS total_transactions
FROM Payment
GROUP BY payment_method;
```

Count successful payments by method
```
SELECT payment_method, COUNT(*) AS successful_transactions
FROM Payment
WHERE payment_status = 'Successful'
GROUP BY payment_method;
```

5. Payment Transaction Report

```
SELECT
    p.payment_id,
    p.order_id,
    o.customer_id,
    p.payment_method,
    p.payment_date,
    p.payment_status,
    o.total_amount
FROM Payment p
JOIN Orders o
ON p.order_id = o.order_id;
```


