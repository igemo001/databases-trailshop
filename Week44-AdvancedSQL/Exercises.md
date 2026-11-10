# Week 44 — Advanced SQL: Exercises

> [!IMPORTANT]
> **_How to Complete These Exercises_**
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

---

## 1. TrailShop Project Task

Continue building the TrailShop database. This week, use advanced query techniques to extract meaningful insights.

### Task 1.1: Subqueries

Write the following queries using subqueries:

1. Find all products priced above the average price (use a scalar subquery).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > SELECT product_name, price
   > FROM products
   > WHERE price > (SELECT AVG(price) FROM products);
   >
   >
   > ```
2. Find all customers who have placed more than 2 orders (use a subquery in WHERE with IN).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > SELECT first_name, last_name, email
   > FROM customers
   > WHERE customer_id IN (
   > SELECT customer_id
   > FROM orders
   > GROUP BY customer_id
   > HAVING COUNT(order_id) > 2);
   >
   >
   > ```
3. Show each product's name, price, and how much it differs from its category's average price (use a correlated subquery or derived table).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > --Using a derived table (faster and generally cleaner for aggregates)
   > SELECT
   > p.product_name,
   > p.price,
   > ROUND(p.price - cat_avg.avg_price, 2) AS diff_from_category_avgFROM products pJOIN product_categories pc ON p.product_id = pc.product_idJOIN (
   > SELECT pc_inner.category_id, AVG(p_inner.price) AS avg_price
   > FROM products p_inner
   > JOIN product_categories pc_inner ON p_inner.product_id = pc_inner.product_id
   > GROUP BY pc_inner.category_id
   > ) cat_avg ON pc.category_id = cat_avg.category_id;
   >
   >
   > ```

### Task 1.2: EXISTS

1. Use `EXISTS` to find all customers who have ordered at least one product from the "Footwear" category.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > SELECT c.first_name, c.last_name
   > FROM customers c
   > WHERE EXISTS (
   > SELECT 1
   > FROM orders o
   > JOIN order_items oi ON o.order_id = oi.order_id
   > JOIN product_categories pc ON oi.product_id = pc.product_id
   > JOIN categories cat ON pc.category_id = cat.category_id
   > WHERE o.customer_id = c.customer_id
   >  AND cat.category_name = 'Footwear');
   >
   >
   > ```
3. Use `NOT EXISTS` to find all products that have never been ordered.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- SELECT p.product_name, p.price
   > FROM products p
   > WHERE NOT EXISTS (
   > SELECT 1
   > FROM order_items oi
   > WHERE oi.product_id = p.product_id
   > );
   >
   >
   > ```

### Task 1.3: CASE Expressions

1. Add a `price_tier` column to a product query that labels products as 'Budget' (< €25), 'Standard' (€25–€74.99), or 'Premium' (≥ €75).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > SELECT
   > product_name,
   > price,
   > CASE
   >     WHEN price < 25.00 THEN 'Budget'
   >     WHEN price < 75.00 THEN 'Standard'
   >     ELSE 'Premium'
   > END AS price_tier
   > FROM products;
   >
   >
   > ```
2. Write a query that counts orders by status using CASE inside COUNT.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > SELECT
   > COUNT(CASE WHEN status = 'pending' THEN 1 END) AS pending_orders,
   > COUNT(CASE WHEN status = 'shipped' THEN 1 END) AS shipped_orders,
   > COUNT(CASE WHEN status = 'delivered' THEN 1 END) AS delivered_orders,
   > COUNT(CASE WHEN status = 'cancelled' THEN 1 END) AS cancelled_orders
   > FROM orders;
   >
   >
   > ```

### Task 1.4: Create Three Views

Create the following views in your TrailShop database:

1. `v_product_catalog` — Shows product name, category name, price, and stock status ('In Stock', 'Low Stock' for < 5 units, 'Out of Stock' for 0). Join through `product_categories`. A product in two categories may appear twice (once per category).
2. `v_customer_summary` — Shows each customer's full name, email, number of orders, and total spent.
3. `v_monthly_revenue` — Shows revenue grouped by month.

Verify each view works with a simple SELECT.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- 1. Product Catalog View
> CREATE VIEW v_product_catalog AS
> SELECT
>   p.product_name,
    cat.category_name,
    p.price,
    CASE
        WHEN p.stock_quantity = 0 THEN 'Out of Stock'
        WHEN p.stock_quantity < 5 THEN 'Low Stock'
        ELSE 'In Stock'
    END AS stock_status
    FROM products p
    JOIN product_categories pc ON p.product_id = pc.product_id
    JOIN categories cat ON pc.category_id = cat.category_id;
    
    -- 2. Customer Summary View
    CREATE VIEW v_customer_summary AS
    SELECT
    c.first_name || ' ' || c.last_name AS full_name,
    c.email,
    COUNT(DISTINCT o.order_id) AS number_of_orders,
    COALESCE(SUM(oi.quantity * oi.unit_price), 0) AS total_spent
    FROM customers c
    LEFT JOIN orders o ON c.customer_id = o.customer_id
    LEFT JOIN order_items oi ON o.order_id = oi.order_id
    GROUP BY c.customer_id, c.first_name, c.last_name, c.email;
    
    -- 3. Monthly Revenue View
    CREATE VIEW v_monthly_revenue AS
    SELECT
    DATE_TRUNC('month', o.order_date) AS month,
    SUM(oi.quantity * oi.unit_price) AS total_revenue
    FROM orders o
    JOIN order_items oi ON o.order_id = oi.order_id
    GROUP BY DATE_TRUNC('month', o.order_date);
>
>
> ```

### Task 1.5: UNION

1. Write a UNION query that combines product names from two different categories into one list (without duplicates).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > SELECT p.product_name
   > FROM products p
   > JOIN product_categories pc ON p.product_id = pc.product_id
   > JOIN categories cat ON pc.category_id = cat.category_id
   > WHERE cat.category_name = 'Footwear'
   > UNION
   > SELECT p.product_name
   > FROM products p
   > JOIN product_categories pc ON p.product_id = pc.product_id
   > JOIN categories cat ON pc.category_id = cat.category_id
   > WHERE cat.category_name = 'Camping';
   >
   >
   > ```
2. Write a query using EXCEPT to find categories that have products but no orders.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- Categories with products
   > SELECT cat.category_name
   > FROM categories cat
   > JOIN product_categories pc ON cat.category_id = pc.category_id
   > EXCEPT
   > -- Categories with orders
   > SELECT cat.category_name
   > FROM categories cat
   > JOIN product_categories pc ON cat.category_id = pc.category_id
   > JOIN order_items oi ON pc.product_id = oi.product_id;
   >
   >
   > ```

### Task 1.6: CTE

1. Write a CTE that calculates total revenue per category, then use it to find categories with above-average revenue.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > WITH category_revenue AS (
   > SELECT
   > cat.category_name,
   > SUM(oi.quantity * oi.unit_price) AS revenue
   > FROM categories cat
   > JOIN product_categories pc ON cat.category_id = pc.category_id
   > JOIN order_items oi ON pc.product_id = oi.product_id
   > GROUP BY cat.category_name)
   > SELECT category_name, revenue
   > FROM category_revenue
   > WHERE revenue > (SELECT AVG(revenue) FROM category_revenue);
   >
   >
   > ```
3. Write a query with two CTEs: one for customer order counts, another for average order count, then find "top customers" (above average).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   >WITH customer_order_counts AS (
   > SELECT customer_id, COUNT(order_id) AS total_orders
   > FROM orders
   > GROUP BY customer_id),
   > avg_order_count AS (
   > SELECT AVG(total_orders) AS system_avg
   > FROM customer_order_counts)
   > SELECT
   > c.first_name,
   > c.last_name,
   > coc.total_orders
   > FROM customers c
   > JOIN customer_order_counts coc ON c.customer_id = coc.customer_id
   > CROSS JOIN avg_order_count aoc
   > WHERE coc.total_orders > aoc.system_avg;
   >
   >
   > ```

---

## 2. Theory Review Questions

Answer these in your own words (2–4 sentences each):

1. What is the difference between an INNER JOIN and a LEFT JOIN? When would you choose LEFT JOIN?

> [!NOTE]
> **_Your Answer_**
>
>An INNER JOIN returns only the rows that have matching values in both tables. If there is no match between the tables, that row is excluded from the result. A LEFT JOIN returns all rows from the left table and any matching rows from the right table, showing NULL values where no match exists. I would choose a LEFT JOIN when I need to keep all records from the left table, even if some of them do not have related data in the other table, such as listing all customers including those who have never placed an order.

2. Explain what a correlated subquery is. Why can it be slower than an uncorrelated subquery?

> [!NOTE]
> **_Your Answer_**
>A correlated subquery is a subquery that depends on values from the outer query. It cannot run independently because it uses a column from the outer query to produce its result. For each row processed by the outer query, the correlated subquery is executed again.

A correlated subquery can be slower than an uncorrelated subquery because it may run repeatedly, once for every row in the outer query. An uncorrelated subquery runs only once and returns a result that the main query can use directly. Because correlated subqueries require more repeated processing, they often use more time and resources, especially on large datasets.
>
3. What is the difference between `IN` and `EXISTS`? When might you prefer EXISTS?

> [!NOTE]
> **_Your Answer_**
>
> IN and EXISTS are both used to check whether related data exists in another query, but they work differently. IN compares a value with a list of values returned by a subquery, while EXISTS simply checks whether the subquery returns any rows. If at least one matching row is found, EXISTS returns true.

You might prefer EXISTS when working with large tables because it can stop searching as soon as it finds a match, making it more efficient in some cases. It is commonly used with correlated subqueries where you only need to know whether related records exist, rather than comparing against a complete list of values.

4. What rules must two SELECT statements follow to be combined with UNION?

> [!NOTE]
> **_Your Answer_**
>
> To combine two SELECT statements using UNION, both queries must return the same number of columns, and the corresponding columns must have compatible data types. The columns should also appear in the same order because SQL matches the first column of one query with the first column of the other, the second with the second, and so on.

When these rules are followed, UNION combines the results of both queries into a single result set and automatically removes duplicate rows. If you want to keep duplicates, you can use UNION ALL instead.

5. Explain the difference between UNION and UNION ALL. When would you use each?

> [!NOTE]
> **_Your Answer_**
>
>UNION combines the results of two or more SELECT statements and automatically removes duplicate rows. This is useful when you want a list of unique results and do not want the same row to appear more than once.

UNION ALL also combines the results of multiple SELECT statements, but it keeps all rows, including duplicates. Because it does not spend time removing duplicates, it is usually faster than UNION.

I would use UNION when I need only unique records, such as creating a combined list of customers from different tables without repetition. I would use UNION ALL when duplicates are important or when I want better performance and know that removing duplicates is unnecessary.

6. What is a view? Name two advantages of using views.

> [!NOTE]
> **_Your Answer_**
>
>A view is a virtual table that is created from the result of a SQL query. It does not store the data itself but displays data from one or more underlying tables whenever it is queried.

One advantage of using views is that they simplify complex queries by saving commonly used joins or calculations in a reusable form. Another advantage is that they improve security by allowing users to access only specific columns or rows without giving them access to the entire table.
>
> 7. What does `WITH CHECK OPTION` do on an updatable view?

> [!NOTE]
> **_Your Answer_**
>
>WITH CHECK OPTION is used when creating an updatable view to ensure that any data inserted or updated through the view still satisfies the view's conditions. If a change would cause a row to no longer meet the view's criteria, SQL rejects the operation.

For example, if a view shows only employees with a salary greater than 3000, WITH CHECK OPTION prevents users from updating a salary to 2500 through that view. This helps maintain data consistency and ensures that all rows visible through the view continue to meet its filtering rules.

8. How does a CTE differ from a derived table (subquery in FROM)? What's the main advantage of CTEs?

> [!NOTE]
> **_Your Answer_**
>
> A CTE (Common Table Expression) and a derived table both create temporary result sets that can be used in a query. The main difference is that a derived table is written inside the FROM clause, while a CTE is defined at the beginning of the query using the WITH keyword.
> The main advantage of CTEs is that they make complex queries easier to read, organize, and maintain. A CTE can also be referenced multiple times within the same query, which helps avoid repeating the same subquery and makes the SQL code more understandable.



## 3. Query Writing Exercises (Optional)

> [!TIP]
> **Recommended practice.** Do this section. It is not required to finish the TrailShop project. It prepares you for the exams.

Write SQL queries for the following (use the TrailShop schema):

1. Find the most expensive product in each category using a correlated subquery.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > SELECT cat.category_name, p.product_name, p.price
   > FROM products p
   > JOIN product_categories pc ON p.product_id = pc.product_id
   > JOIN categories cat ON pc.category_id = cat.category_id
   > WHERE p.price = (
   > SELECT MAX(p2.price)
   > FROM products p2
   > JOIN product_categories pc2 ON p2.product_id = pc2.product_id
   > WHERE pc2.category_id = pc.category_id);
   >
   >
   > ```
2. List all customers along with their most recent order date. Include customers who have never ordered (show NULL).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > SELECT c.first_name, c.last_name, MAX(o.order_date) AS most_recent_order
   > FROM customers c
   > LEFT JOIN orders o ON c.customer_id = o.customer_id
   > GROUP BY c.customer_id, c.first_name, c.last_name;
   >
   >
   > ```
3. Find categories where the total revenue exceeds €500 (use a subquery or CTE).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > WITH cat_revenue AS (
   > SELECT cat.category_name, SUM(oi.quantity * oi.unit_price) AS total_revenue
   > FROM categories cat
   > JOIN product_categories pc ON cat.category_id = pc.category_id
   > JOIN order_items oi ON pc.product_id = oi.product_id
   > GROUP BY cat.category_name)
   > SELECT category_name, total_revenue
   > FROM cat_revenue
   > WHERE total_revenue > 500;
   >
   >
   > ```
4. Write a query that shows each order's total and whether it's 'Small' (< €50), 'Medium' (€50–€149.99), or 'Large' (≥ €150).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   >SELECT
   > order_id,
   > SUM(quantity * unit_price) AS order_total,
   > CASE
   > WHEN SUM(quantity * unit_price) < 50 THEN 'Small'
   > WHEN SUM(quantity * unit_price) < 150 THEN 'Medium'
   > ELSE 'Large'
   > END AS order_size
   > FROM order_items
   > GROUP BY order_id;
   >
   >
   > ```
5. Create a view called `v_never_ordered` that shows products that have never appeared in an order_item.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > CREATE VIEW v_never_ordered AS
   > SELECT p.product_id, p.product_name, p.price
   > FROM products p
   > LEFT JOIN order_items oi ON p.product_id = oi.product_id
   > WHERE oi.order_item_id IS NULL;
   >
   >
   > ```
6. Use EXCEPT to find products that exist in the products table but have never been ordered.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   >SELECT product_id, product_name FROM products
   > EXCEPT
   > SELECT p.product_id, p.product_name
   > FROM products p
   > JOIN order_items oi ON p.product_id = oi.product_id;
   >
   >
   > ```
7. Write a CTE that ranks categories by number of products, then selects only the top 3.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > WITH category_counts AS (
   > SELECT cat.category_name, COUNT(pc.product_id) AS product_count
   > FROM categories cat
   > LEFT JOIN product_categories pc ON cat.category_id = pc.category_id
   > GROUP BY cat.category_name)
   > SELECT category_name, product_count
   > FROM category_counts
   > ORDER BY product_count DESC
   > LIMIT 3;
   >
   >
   > ```
8. Use `NOT EXISTS` to find categories that have no products.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   >SELECT cat.category_name
   > FROM categories cat
   > WHERE NOT EXISTS (
   >  SELECT 1
   >  FROM product_categories pc
   > WHERE pc.category_id = cat.category_id);
   >
   >
   > ```
9. Write a query using CASE that creates a "customer loyalty" label: 'New' (1 order), 'Regular' (2–5 orders), 'VIP' (6+ orders).
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > SELECT
   > c.first_name,
   > c.last_name,
   > COUNT(o.order_id) AS order_count,
   > CASE
   > WHEN COUNT(o.order_id) = 1 THEN 'New'
   > WHEN COUNT(o.order_id) BETWEEN 2 AND 5 THEN 'Regular'
   > WHEN COUNT(o.order_id) >= 6 THEN 'VIP'
   >ELSE 'No Orders'
   > END AS loyalty_status
   >FROM customers c
   > LEFT JOIN orders o ON c.customer_id = o.customer_id
   > GROUP BY c.customer_id, c.first_name, c.last_name;
   >
   >
   > ```
10. Use GREATEST to find the higher value between each product's current price and the average price of all products in its category.
    > [!NOTE]
    > **_Your SQL_**
    >
    > ```sql
    > WITH cat_avg AS (
    > SELECT pc.category_id, AVG(p.price) AS avg_price
    > FROM products p
    > JOIN product_categories pc ON p.product_id = pc.product_id
    > GROUP BY pc.category_id
    > )
    > SELECT
    >     p.product_name,
    >     p.price AS current_price,
    >     ROUND(ca.avg_price, 2) AS category_average,
    >     GREATEST(p.price, ROUND(ca.avg_price, 2)) AS higher_value
    > FROM products p
    > JOIN product_categories pc ON p.product_id = pc.product_id
    > JOIN cat_avg ca ON pc.category_id = ca.category_id;
    >
    >
    > ```
11. Write a query using COALESCE that shows order shipping dates, substituting 'Not yet shipped' for NULL values.
    > [!NOTE]
    > **_Your SQL_**
    >
    > ```sql
    > -- Assuming 'status' helps define this, or using a hypothetical 'ship_date' column.
    > -- If ship_date doesn't exist in TrailShop, we can simulate COALESCE on status.
    > -- Adjusting to fit standard schema patterns:
    > SELECT
    >     order_id,
    >     order_date,
    >     COALESCE(CAST(order_date + interval '3 days' AS TEXT), 'Not yet shipped') AS ship_date_status
    > FROM orders;
    >
    >
    > ```
12. Use a scalar subquery in the SELECT clause to show each product alongside the total number of times it has been ordered.
    > [!NOTE]
    > **_Your SQL_**
    >
    > ```sql
    > SELECT
    > product_name,
    > (SELECT COUNT(*) FROM order_items oi WHERE oi.product_id = p.product_id) AS times_ordered
    > FROM products p;
    >
    >
    > ```

---

## 4. Query Comparison Exercise (Optional)

> [!TIP]
> **Recommended practice.** Do this section. It is not required to finish the TrailShop project. It prepares you for the exams.

For each pair of queries below, both produce the same result. Explain which approach you'd prefer and why (consider readability, performance, and maintainability).

> [!NOTE]
> **_Your Answer_**
>
> _(Write your answer here.)_

### Pair A: Finding customers with orders

**Version 1 — Subquery with IN:**

```sql
SELECT first_name, last_name
FROM customers
WHERE customer_id IN (SELECT customer_id FROM orders);
```

**Version 2 — JOIN:**

```sql
SELECT DISTINCT c.first_name, c.last_name
FROM customers c
JOIN orders o ON o.customer_id = c.customer_id;
```

### Pair B: Finding products never ordered

**Version 1 — LEFT JOIN + IS NULL:**

```sql
SELECT p.product_name
FROM products p
LEFT JOIN order_items oi ON oi.product_id = p.product_id
WHERE oi.order_item_id IS NULL;
```

**Version 2 — NOT EXISTS:**

```sql
SELECT p.product_name
FROM products p
WHERE NOT EXISTS (
    SELECT 1 FROM order_items oi WHERE oi.product_id = p.product_id
);
```

### Pair C: Customer total spending

**Version 1 — Derived table (subquery in FROM):**

```sql
SELECT sub.full_name, sub.total_spent
FROM (
    SELECT c.first_name || ' ' || c.last_name AS full_name,
           SUM(oi.quantity * oi.unit_price) AS total_spent
    FROM customers c
    JOIN orders o ON o.customer_id = c.customer_id
    JOIN order_items oi ON oi.order_id = o.order_id
    GROUP BY c.customer_id, c.first_name, c.last_name
) sub
WHERE sub.total_spent > 200;
```

**Version 2 — CTE:**

```sql
WITH customer_totals AS (
    SELECT c.first_name || ' ' || c.last_name AS full_name,
           SUM(oi.quantity * oi.unit_price) AS total_spent
    FROM customers c
    JOIN orders o ON o.customer_id = c.customer_id
    JOIN order_items oi ON oi.order_id = o.order_id
    GROUP BY c.customer_id, c.first_name, c.last_name
)
SELECT full_name, total_spent
FROM customer_totals
WHERE total_spent > 200;
```

For each pair, discuss: readability, performance (in PostgreSQL), and when you'd choose one over the other.

> [!NOTE]
> **_Your Answer_**
>
>
> pair A
> Version 2 (JOIN) is typically preferable. While Version 1 (IN) is highly readable for a quick check, Version 2 scales better and PostgreSQL's query optimizer handles joins incredibly efficiently. Furthermore, if you later realize you need to pull in details from the orders table (like order dates), Version 2 is already structured to accommodate that, making it much more maintainable.
>
> Pair B
>Version 2 (NOT EXISTS) is usually better for performance and readability when hunting for missing relations. Using LEFT JOIN + IS NULL requires the database engine to perform the full join and construct the memory block for the result set before filtering out the nulls. NOT EXISTS operates as a boolean check and will stop processing a product the exact moment it spots a single order_item_id, making it much faster on large tables.
>
> Pair C
> 
> Version 2 (CTE) is the absolute winner for readability and maintainability. Version 1 (Derived table) forces the reader to look inside-out to understand the core logic before figuring out the WHERE > 200 filter. The CTE reads logically from top to bottom (define the math, then filter). In modern PostgreSQL versions, performance between the two is identical because the optimizer automatically inlines CTEs, meaning you get the benefit of readability without any execution cost.

---

## Submission Checklist

**Required**

- [x ] TrailShop queries written for subqueries, EXISTS, and CASE expressions
- [x ] Three views created (`v_product_catalog`, `v_customer_summary`, `v_monthly_revenue`)
- [x ] UNION/EXCEPT and CTE queries written
- [x ] Theory review questions answered

**Recommended practice**

- [x ] Query writing exercises completed
- [x ] Query comparison exercise completed
