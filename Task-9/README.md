# Task IX - Sales and Customer Analytics System

This task performs sales and customer analysis using the existing E-Commerce Order Management Database. Run it after completing the `Customer`, `Category`, `Product`, `Orders`, and `Order_Details` tables.

No new table is required for this task.

## 1. Apply COUNT(), SUM(), AVG(), MIN(), and MAX()

The following query displays the total number of orders and summary statistics for order values.

```sql
SELECT
    COUNT(order_id) AS total_orders,
    SUM(total_amount) AS total_sales,
    AVG(total_amount) AS average_order_value,
    MIN(total_amount) AS minimum_order_value,
    MAX(total_amount) AS maximum_order_value
FROM Orders;
```

Take a screenshot of the query and its result.

## 2. Generate total sales reports

### Overall sales report

```sql
SELECT
    COUNT(order_id) AS total_orders,
    SUM(total_amount) AS total_sales,
    AVG(total_amount) AS average_order_value
FROM Orders;
```

### Date-wise sales report

```sql
SELECT
    order_date,
    COUNT(order_id) AS total_orders,
    SUM(total_amount) AS daily_sales
FROM Orders
GROUP BY order_date
ORDER BY order_date;
```

### Order-status-wise sales report

```sql
SELECT
    order_status,
    COUNT(order_id) AS total_orders,
    SUM(total_amount) AS total_sales
FROM Orders
GROUP BY order_status
ORDER BY total_sales DESC;
```

Take a screenshot of at least one total sales report.

## 3. Find top customers based on purchase amount

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    COUNT(o.order_id) AS total_orders,
    SUM(o.total_amount) AS total_purchase_amount
FROM Customer c
JOIN Orders o
    ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name
ORDER BY total_purchase_amount DESC;
```

### Display only the top three customers

```sql
SELECT
    c.customer_id,
    c.name AS customer_name,
    SUM(o.total_amount) AS total_purchase_amount
FROM Customer c
JOIN Orders o
    ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name
ORDER BY total_purchase_amount DESC
LIMIT 3;
```

Take a screenshot of the top-customer report.

## 4. Identify best-selling products

This report ranks products according to the total quantity sold.

```sql
SELECT
    p.product_id,
    p.product_name,
    SUM(od.quantity) AS total_quantity_sold,
    SUM(od.quantity * od.unit_price) AS product_sales_amount
FROM Product p
JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY p.product_id, p.product_name
ORDER BY total_quantity_sold DESC, product_sales_amount DESC;
```

### Display only the best-selling product

```sql
SELECT
    p.product_id,
    p.product_name,
    SUM(od.quantity) AS total_quantity_sold
FROM Product p
JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY p.product_id, p.product_name
ORDER BY total_quantity_sold DESC
LIMIT 1;
```

Take a screenshot of the best-selling-product report.

## 5. Perform category-wise sales analysis

```sql
SELECT
    c.category_id,
    c.category_name,
    COUNT(DISTINCT od.order_id) AS total_orders,
    SUM(od.quantity) AS total_items_sold,
    SUM(od.quantity * od.unit_price) AS category_sales_amount,
    AVG(od.unit_price) AS average_selling_price,
    MIN(od.unit_price) AS minimum_selling_price,
    MAX(od.unit_price) AS maximum_selling_price
FROM Category c
JOIN Product p
    ON c.category_id = p.category_id
JOIN Order_Details od
    ON p.product_id = od.product_id
GROUP BY c.category_id, c.category_name
ORDER BY category_sales_amount DESC;
```

Take a screenshot of the category-wise sales report.

## 6. Complete sales and customer business report

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
ORDER BY o.order_date, o.order_id;
```

This final query combines customer, order, product, and category information into one basic business report.

## Viva questions and answers

**1. What is an aggregate function?**  
An aggregate function performs a calculation on multiple rows and returns one summarized value.

**2. What do COUNT(), SUM(), AVG(), MIN(), and MAX() do?**  
`COUNT()` counts rows, `SUM()` adds values, `AVG()` calculates the average, `MIN()` returns the smallest value, and `MAX()` returns the largest value.

**3. Why is GROUP BY used?**  
`GROUP BY` collects rows with the same value so an aggregate result can be calculated for each customer, product, category, or date.

**4. How are top customers identified?**  
Their order amounts are added using `SUM()`, grouped by customer, and sorted from highest to lowest using `ORDER BY ... DESC`.

**5. How is the best-selling product identified?**  
The quantities in `Order_Details` are added for each product, then the results are sorted from the highest quantity to the lowest.

**6. Why is unit_price stored in Order_Details?**  
It preserves the product price at the time of purchase even if the current price in the Product table changes later.

**7. What is the difference between WHERE and HAVING?**  
`WHERE` filters rows before grouping, while `HAVING` filters grouped aggregate results.
