# Week 44 — Advanced SQL: Exercises

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

---

## 1. TrailShop Project Task

Continue building the TrailShop database. This week, use advanced query techniques to extract meaningful insights.

### Task 1.1: Subqueries

Write the following queries using subqueries:

1. Find all products priced above the average price (use a scalar subquery).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
2. Find all customers who have placed more than 2 orders (use a subquery in WHERE with IN).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
3. Show each product's name, price, and how much it differs from its category's average price (use a correlated subquery or derived table).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```

### Task 1.2: EXISTS

1. Use `EXISTS` to find all customers who have ordered at least one product from the "Footwear" category.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
2. Use `NOT EXISTS` to find all products that have never been ordered.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```

### Task 1.3: CASE Expressions

1. Add a `price_tier` column to a product query that labels products as 'Budget' (< €25), 'Standard' (€25–€74.99), or 'Premium' (≥ €75).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
2. Write a query that counts orders by status using CASE inside COUNT.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```

### Task 1.4: Create Three Views

Create the following views in your TrailShop database:

1. `v_product_catalog` — Shows product name, category name, price, and stock status ('In Stock', 'Low Stock' for < 5 units, 'Out of Stock' for 0).
2. `v_customer_summary` — Shows each customer's full name, email, number of orders, and total spent.
3. `v_monthly_revenue` — Shows revenue grouped by month.

Verify each view works with a simple SELECT.

> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your CREATE VIEW statements here
>
>
> ```

### Task 1.5: UNION

1. Write a UNION query that combines product names from two different categories into one list (without duplicates).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
2. Write a query using EXCEPT to find categories that have products but no orders.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```

### Task 1.6: CTE

1. Write a CTE that calculates total revenue per category, then use it to find categories with above-average revenue.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
2. Write a query with two CTEs: one for customer order counts, another for average order count, then find "top customers" (above average).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```

---

## 2. Theory Review Questions

Answer these in your own words (2–4 sentences each):

1. What is the difference between an INNER JOIN and a LEFT JOIN? When would you choose LEFT JOIN?

> [!NOTE]
> ***Your Answer***
>
> An INNER JOIN returns only the rows that have matching values in both tables. If there is no match between the tables, that row is excluded from the result. A LEFT JOIN returns all rows from the left table and any matching rows from the right table, showing NULL values where no match exists. I would choose a LEFT JOIN when I need to keep all records from the left table, even if some of them do not have related data in the other table, such as listing all customers including those who have never placed an order.
>
>
>
>


2. Explain what a correlated subquery is. Why can it be slower than an uncorrelated subquery?

> [!NOTE]
> ***Your Answer***
>
> A correlated subquery is a subquery that depends on values from the outer query. It cannot run independently because it uses a column from the outer query to produce its result. For each row processed by the outer query, the correlated subquery is executed again.

A correlated subquery can be slower than an uncorrelated subquery because it may run repeatedly, once for every row in the outer query. An uncorrelated subquery runs only once and returns a result that the main query can use directly. Because correlated subqueries require more repeated processing, they often use more time and resources, especially on large datasets.
>
>
>
>


3. What is the difference between `IN` and `EXISTS`? When might you prefer EXISTS?

> [!NOTE]
> ***Your Answer***
>
> IN and EXISTS are both used to check whether related data exists in another query, but they work differently. IN compares a value with a list of values returned by a subquery, while EXISTS simply checks whether the subquery returns any rows. If at least one matching row is found, EXISTS returns true.

You might prefer EXISTS when working with large tables because it can stop searching as soon as it finds a match, making it more efficient in some cases. It is commonly used with correlated subqueries where you only need to know whether related records exist, rather than comparing against a complete list of values.
>
>
>
>


4. What rules must two SELECT statements follow to be combined with UNION?

> [!NOTE]
> ***Your Answer***
>
> To combine two SELECT statements using UNION, both queries must return the same number of columns, and the corresponding columns must have compatible data types. The columns should also appear in the same order because SQL matches the first column of one query with the first column of the other, the second with the second, and so on.

When these rules are followed, UNION combines the results of both queries into a single result set and automatically removes duplicate rows. If you want to keep duplicates, you can use UNION ALL instead.
>
>
>
>


5. Explain the difference between UNION and UNION ALL. When would you use each?

> [!NOTE]
> ***Your Answer***
>
> UNION combines the results of two or more SELECT statements and automatically removes duplicate rows. This is useful when you want a list of unique results and do not want the same row to appear more than once.

UNION ALL also combines the results of multiple SELECT statements, but it keeps all rows, including duplicates. Because it does not spend time removing duplicates, it is usually faster than UNION.

I would use UNION when I need only unique records, such as creating a combined list of customers from different tables without repetition. I would use UNION ALL when duplicates are important or when I want better performance and know that removing duplicates is unnecessary.
>
>
>
>


6. What is a view? Name two advantages of using views.

> [!NOTE]
> ***Your Answer***
>
> A view is a virtual table that is created from the result of a SQL query. It does not store the data itself but displays data from one or more underlying tables whenever it is queried.

One advantage of using views is that they simplify complex queries by saving commonly used joins or calculations in a reusable form. Another advantage is that they improve security by allowing users to access only specific columns or rows without giving them access to the entire table.
>
>
>
>
7. What does `WITH CHECK OPTION` do on an updatable view?

> [!NOTE]
> ***Your Answer***
>
>WITH CHECK OPTION is used when creating an updatable view to ensure that any data inserted or updated through the view still satisfies the view's conditions. If a change would cause a row to no longer meet the view's criteria, SQL rejects the operation.

For example, if a view shows only employees with a salary greater than 3000, WITH CHECK OPTION prevents users from updating a salary to 2500 through that view. This helps maintain data consistency and ensures that all rows visible through the view continue to meet its filtering rules.
>
>
>
>


8. How does a CTE differ from a derived table (subquery in FROM)? What's the main advantage of CTEs?

> [!NOTE]
> ***Your Answer***
>
> A CTE (Common Table Expression) and a derived table both create temporary result sets that can be used in a query. The main difference is that a derived table is written inside the FROM clause, while a CTE is defined at the beginning of the query using the WITH keyword.

The main advantage of CTEs is that they make complex queries easier to read, organize, and maintain. A CTE can also be referenced multiple times within the same query, which helps avoid repeating the same subquery and makes the SQL code more understandable.
>
>
>

---

## 3. Query Writing Exercises

Write SQL queries for the following (use the TrailShop schema):

1. Find the most expensive product in each category using a correlated subquery.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
2. List all customers along with their most recent order date. Include customers who have never ordered (show NULL).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
3. Find categories where the total revenue exceeds €500 (use a subquery or CTE).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
4. Write a query that shows each order's total and whether it's 'Small' (< €50), 'Medium' (€50–€149.99), or 'Large' (≥ €150).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
5. Create a view called `v_never_ordered` that shows products that have never appeared in an order_item.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
6. Use EXCEPT to find products that exist in the products table but have never been ordered.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
7. Write a CTE that ranks categories by number of products, then selects only the top 3.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
8. Use `NOT EXISTS` to find categories that have no products.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
9. Write a query using CASE that creates a "customer loyalty" label: 'New' (1 order), 'Regular' (2–5 orders), 'VIP' (6+ orders).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
10. Use GREATEST to find the higher value between each product's current price and the average price of all products in its category.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
11. Write a query using COALESCE that shows order shipping dates, substituting 'Not yet shipped' for NULL values.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```
12. Use a scalar subquery in the SELECT clause to show each product alongside the total number of times it has been ordered.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>
>
> ```

---

## 4. Query Comparison Exercise

For each pair of queries below, both produce the same result. Explain which approach you'd prefer and why (consider readability, performance, and maintainability).
> [!NOTE]
> ***Your Answer***
>
> *(Write your answer here.)*
>
>
>
>

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
> ***Your Answer***
>
> *(Write your answer here.)*
>
>
>
>
