# TASK-1-DBMS-
Week 1–3
Requirement Analysis
Project Title
E-Commerce Order Management Database System

1. Introduction:
The E-Commerce Order Management Database System is designed to support the daily operations of an online shopping platform similar to Amazon. The system manages customer information, product catalog, suppliers, inventory, orders, payments, shipments, and customer reviews. It provides a centralized database that ensures efficient data storage, retrieval, and management while maintaining data integrity and minimizing redundancy.

2. Problem Statement:
Traditional methods of managing customer orders, inventory, and payments using spreadsheets or manual records are inefficient and prone to errors. As the number of customers and products increases, it becomes difficult to maintain accurate records and generate reports.
The proposed database system addresses these issues by providing a structured and reliable database that automates business operations and improves data consistency.

3. Objectives:
The objectives of the project are:
To maintain customer information securely.
To organize products into categories.
To manage supplier information.
To track product inventory.
To process customer orders.
To record payment transactions.
To manage shipment details.
To store customer reviews and ratings.
To generate sales and business reports.

4. Business Requirements:
The system should support the following operations:
Customer Management
Register new customers.
Store customer details.
Maintain customer profiles.
Product Management
Store product information.
Categorize products.
Track product stock.
Update product prices.
Supplier Management
Maintain supplier details.
Associate suppliers with products.
Order Management
Place customer orders.
Maintain order history.
Store order details.
Update order status.
Payment Management
Record payment details.
Maintain payment status.
Support multiple payment methods.
Shipment Management
Record shipment details.
Track delivery status.
Maintain delivery addresses.
Review Management
Store customer ratings.
Store customer comments.
Reporting
Generate monthly sales reports.
Generate category-wise sales reports.
Generate customer purchase history.
Generate product-wise revenue reports.

5. Scope of the System:
The system includes the following modules:
Customer Management
Category Management
Product Management
Supplier Management
Order Management
Payment Management
Shipment Management
Review Management
Business Reporting

7. Entity Identification:
   
| Entity | Primary Key | Important Attributes |
|---------|-------------|----------------------|
| Customer | Customer_ID | Name, Email, Mobile_Number, Address, Registration_Date |
| Category | Category_ID | Category_Name, Description |
| Product | Product_ID | Product_Name, Price, Stock_Quantity |
| Supplier | Supplier_ID | Supplier_Name, Contact_Information |
| Orders | Order_ID | Customer_ID, Order_Date, Total_Amount, Order_Status |
| Order_Details | Order_Detail_ID | Order_ID, Product_ID, Quantity, Unit_Price |
| Payment | Payment_ID | Order_ID, Payment_Method, Payment_Date, Payment_Status |
| Shipment | Shipment_ID | Order_ID, Delivery_Address, Shipment_Date, Delivery_Status |
| Review | Review_ID | Customer_ID, Product_ID, Rating, Comments |

8. Relationship Identification
One-to-One Relationships
| Entity | Entity |
|---------|--------|
| Orders | Payment |
| Orders | Shipment |

One-to-Many Relationships
| Parent | Child |
|---------|-------|
| Customer | Orders |
| Category | Product |
| Supplier | Product |
| Orders | Order_Details |
| Customer | Review |
| Product | Review |

Many-to-Many Relationships
| Entity | Entity | Resolved By |
|---------|--------|-------------|
| Orders | Product | Order_Details |

9. Conceptual ER Diagram

Customer
   |
 Places
   |
   V
 Orders -------- Payment
   |
 Contains
   |
 Order_Details
   |
 References
   |
 Product -------- Category
   |
 Supplied By
   |
 Supplier

Customer
   |
Writes
   |
 Review
   |
Reviews
   |
 Product

Orders
   |
Shipped Through
   |
Shipment

10. Logical ER Diagram:

Customer
---------
Customer_ID (PK)
Name
Email
Mobile_Number
Address
Registration_Date

Orders
---------
Order_ID (PK)
Customer_ID (FK)
Order_Date
Total_Amount
Order_Status

Order_Details
--------------
Order_Detail_ID (PK)
Order_ID (FK)
Product_ID (FK)
Quantity
Unit_Price

Product
---------
Product_ID (PK)
Product_Name
Price
Stock_Quantity
Category_ID (FK)
Supplier_ID (FK)

Category
----------
Category_ID (PK)
Category_Name
Description

Supplier
----------
Supplier_ID (PK)
Supplier_Name
Contact_Information

Payment
---------
Payment_ID (PK)
Order_ID (FK)
Payment_Method
Payment_Date
Payment_Status

Shipment
----------
Shipment_ID (PK)
Order_ID (FK)
Delivery_Address
Shipment_Date
Delivery_Status

Review
---------
Review_ID (PK)
Customer_ID (FK)
Product_ID (FK)
Rating
Comments

11. Relational Schema Design:
    
CUSTOMER
(Customer_ID, Name, Email, Mobile_Number, Address, Registration_Date)

CATEGORY
(Category_ID, Category_Name, Description)

SUPPLIER
(Supplier_ID, Supplier_Name, Contact_Information)

PRODUCT
(Product_ID, Product_Name, Price, Stock_Quantity, Category_ID, Supplier_ID)

ORDERS
(Order_ID, Customer_ID, Order_Date, Total_Amount, Order_Status)

ORDER_DETAILS
(Order_Detail_ID, Order_ID, Product_ID, Quantity, Unit_Price)

PAYMENT
(Payment_ID, Order_ID, Payment_Method, Payment_Date, Payment_Status)

SHIPMENT
(Shipment_ID, Order_ID, Delivery_Address, Shipment_Date, Delivery_Status)

REVIEW
(Review_ID, Customer_ID, Product_ID, Rating, Comments)

