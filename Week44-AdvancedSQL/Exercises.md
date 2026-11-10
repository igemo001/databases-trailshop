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
> An INNER JOIN only returns rows where there is a match in both tables. A LEFT JOIN returns all rows from the left table, and includes matched data from the right table (filling in NULLs if no match exists). You choose a LEFT JOIN when you want to preserve all records from your primary table, such as finding customers who have never placed an order.

2. Explain what a correlated subquery is. Why can it be slower than an uncorrelated subquery?

> [!NOTE]
> **_Your Answer_**
>
> A correlated subquery is a nested query that references a column from the outer query. It can be significantly slower because the database engine must execute the inner query once for every single row processed by the outer query, acting like a loop, whereas an uncorrelated subquery only executes once.

3. What is the difference between `IN` and `EXISTS`? When might you prefer EXISTS?

> [!NOTE]
> **_Your Answer_**
>
> IN checks if a specific value matches any value within a generated list, while EXISTS simply checks if a subquery returns any rows at all (evaluating to true/false). EXISTS is highly preferred for performance on large datasets because it "short-circuits" and stops searching the moment it finds the first matching row.

4. What rules must two SELECT statements follow to be combined with UNION?

> [!NOTE]
> **_Your Answer_**
>
> Both SELECT statements must return the exact same number of columns. Furthermore, the corresponding columns in each query must have compatible data types in the same sequential order.

5. Explain the difference between UNION and UNION ALL. When would you use each?

> [!NOTE]
> **_Your Answer_**
>
> NION combines result sets and automatically removes duplicate rows, whereas UNION ALL combines them but keeps all duplicates. You should use UNION ALL when you explicitly want to see duplicates or when you already know the datasets don't overlap, as skipping the deduplication step makes it run much faster.

6. What is a view? Name two advantages of using views.

> [!NOTE]
> **_Your Answer_**
>
> A view is a saved, named SQL query that acts as a virtual table in the database. Two major advantages are that it simplifies complex queries for end-users (abstraction) and enhances security by restricting access to only specific columns or rows instead of the underlying base tables.
>
> 7. What does `WITH CHECK OPTION` do on an updatable view?

> [!NOTE]
> **_Your Answer_**
>
>WITH CHECK OPTION prevents users from executing an INSERT or UPDATE statement through the view if the resulting row would violate the view's WHERE clause. This ensures that any data modified through the view remains visible within that view after the modification.

8. How does a CTE differ from a derived table (subquery in FROM)? What's the main advantage of CTEs?

> [!NOTE]
> **_Your Answer_**
>
> A derived table is written directly inline within the FROM clause and cannot be reused, while a CTE is defined at the very top of the query using the WITH clause. The main advantage of a CTE is that it significantly improves readability by structuring logic sequentially, and it can be referenced multiple times throughout the same main query.

---

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
