# Week 41 — Exercises: Querying Data

> [!IMPORTANT]
> **_How to Complete These Exercises_**
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

## Exercise 1: TrailShop Project Task — Business Questions

Use the TrailShop database you created in Week 40. Write SQL queries to answer each business question below. Run each query and verify the results make sense.

> [!IMPORTANT]
> **_Tools to use_**
> You can use both the pgAdmin or psql (terminal) to test the queries

### Basic Queries (SELECT + WHERE)

1. List all products in the 'Footwear' category (show name, price, stock).

   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- Write your query here
   > SELECT P.name, P.price, p.stock_quality
   > FROM products p
   > INNER JOIN product_categories pc ON p.product_id = pc.product_id
   > INNER JOIN categories c ON pc.category_id = c.category_id
   > WHERE c.category_name = 'Footware';
   >
   >
   > ```

2. Find all products priced between €50 and €150, sorted by price ascending.

   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- Write your query here
   > SELECT name, price
   > FROM products
   > WHERE price BETWEEN 50 AND 150
   > ORDER BY price ASC;
   >
   >
   > ```

3. Show all customers whose last name starts with the letter 'M' or 'K'.

   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- Write your query here
   > SELECT first_name, last_name, email
   > From customers
   > WHERE last_name LIKE 'M%' OR last_name LIKE  'K%';
   >
   >
   > ```

4. List all orders with status 'pending' or 'shipped', sorted by order date (most recent first).

   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- Write your query here
   > SELECT order_id, order_date, status
   > FROM orders
   > WHERE status IN ('pending', 'shipped')
   > ORDER BY order_date DESC;
   >
   >
   > ```

5. Find all products that have the word 'Pro' or 'pro' somewhere in their name.
   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- Write your query here
   > SELECT name, price
   > FROM products
   > WHERE name ILIKE '%pro%';
   >
   >
   > ```

### Aggregate Queries

6. What is the total number of products in the database?

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT COUNT(*) AS total_products
> FROM products;
>
>
> ```

7. What is the average price of all products? Round to 2 decimal places.

   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- Write your query here
   > SELECT ROUND (AVG(price), 2) AS average_price
   > FROM products;
   >
   >
   > ```

8. Which is the most expensive product and which is the cheapest? Show both in one query.

   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- Write your query here
   > SELECT MAX(price) AS most_expensive, MIN(price) AS cheapest
   > FROM products;
   >
   >
   > ```

9. How many orders does each customer have? Show customer name and order count, sorted by count descending.

   > [!NOTE]
   > **_Your SQL_**
   >
   > ```sql
   > -- Write your query here
   > SELECT c.first_name || '' || c.last_name AS customer_name, COUNT(o.order_id) AS order_count
   > FROM customers c
   > LEFT JOIN orders o ON c.customer_id = o.customer_id
   > GROUP BY c.customer_id, c.first_name, c.last_name
   > ORDER BY order_count DESC;
   >
   >
   > ```

10. What is the total revenue (sum of quantity × unit_price from order_items) for each order status?

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT o.status, SUM(oi.quantity * oi.unit_price) AS total_revenue
> FROM orders o
> INNER JOIN order_items oi ON o.order_id = oi.order_id
> GROUP BY o.status;
>
>
> ```

### JOIN Queries

11. List all products with their category names (join through `product_categories`). A product in two categories should appear twice. Sort by category name, then product name.

    > [!NOTE]
    > **_Your SQL_**
    >
    > ```sql
    > -- Write your query here
    > SELECT c.category_name, p.name AS product_name
    > FROM products p
    > INNER JOIN product_categories pc ON p.product_id = pc.product_id
    > INNER JOIN categories c ON pc.category_id
    > ORDER BY c.categoty_name ASC, p.name ASC;
    >
    >
    > ```

12. Show each order with the customer's full name, order date, and status.

    > [!NOTE]
    > **_Your SQL_**
    >
    > ```sql
    > -- Write your query here
    > SELECT o.order_id, c.first_name || ' ' || c.last_name AS customer_name, o.order_date, o.status
    > FROM orders o
    > INNER JOIN customers c ON o.customer_id = c.customer_id;
    >
    >
    > ```

13. Show a detailed breakdown of order #1: product name, quantity, unit price, and line total.

    > [!NOTE]
    > **_Your SQL_**
    >
    > ```sql
    > -- Write your query here
    > SELECT p.name AS product_name, oi.quantity, oi.unit_price, (oi.quantity * oi.unit_price) AS line_total
    > FROM order_items oi
    > INNER JOIN products p ON oi.product_id = p.product_id
    > WHERE oi.order_id = 1;
    >
    >
    > ```

14. Find all customers who have NOT placed any orders. (Hint: use LEFT JOIN + IS NULL pattern.)

    > [!NOTE]
    > **_Your SQL_**
    >
    > ```sql
    > -- Write your query here
    > SELECT c.first_name, c.lastname, c.email
    > FROM customers c
    > LEFT JOIN orders o ON c.customer_id = o.customer_id
    > WHERE o.order_id IS NULL;
    >
    >
    > ```

15. For each category, show the category name, number of products, average price, and total inventory value (price × stock summed). Only include categories with total inventory value greater than €500.
    > [!NOTE]
    > **_Your SQL_**
    >
    > ```sql
    > -- Write your query here
    > SELECT c.category_name, COUNT(p.product_id) AS product_count, ROUND(AVG(p.price), 2) AS avg_price, SUM(p.price * p.stock_quantity) AS total_inventory_value
    > FROM categories c
    > INNER JOIN product_categories pc ON c.category_id = pc.category_id
    > INNER JOIN products p ON pc.product_id = p.product_id
    > GROUP BY c.category_id, c.category_name
    > HAVING SUM(p.price * p.stock_quantity) > 500;
    >
    >
    > ```

---

## Exercise 2: Theory Review Questions

Answer in your own words:

1. What is the logical execution order of a SQL query? Why does it matter?

> [!NOTE]
> **_Your Answer_**
>
> _The logical execution order is: FROM/JOIN -> WHERE -> GROUP BY -> HAVING -> SELECT -> DISTINCT -> ORDER BY -> LIMIT/OFFSET. This matters heavily because it dictates when aliases and aggregations become available. For instance, you cannot use a SELECT column alias in your WHERE clause because the WHERE filter runs before the SELECT calculation occurs_

2. What is the difference between WHERE and HAVING? Give an example of when you would use each.

> [!NOTE]
> **_Your Answer_**
>
> _WHERE filters individual rows before they are grouped together, while HAVING filters aggregated groups after they have been grouped. You would use WHERE stock > 0 to exclude out-of-stock items early on, and HAVING SUM(stock) > 100 to filter down to categories that have a large combined inventory._

3. Explain the difference between COUNT(\*), COUNT(column), and COUNT(DISTINCT column).

> [!NOTE]
> **_Your Answer_**
>
> _COUNT(*) tallies every single row in a result set regardless of NULL values. COUNT(column) only counts rows where that specific column contains actual data (ignoring NULLs). COUNT(DISTINCT column) counts only the unique, non-repeating, non-NULL values present in that column._

4. What is the difference between INNER JOIN and LEFT JOIN? When would you choose one over the other?

> [!NOTE]
> **_Your Answer_**
>
> _An INNER JOIN only returns rows that have matching records in both tables, while a LEFT JOIN returns everything from the first (left) table and fills in NULLs if no matching record exists in the right table. You choose INNER JOIN when you only want customers who have ordered, and LEFT JOIN when you want to see all customers whether they have ordered or not._

5. Why should you avoid SELECT \* in production code?

> [!NOTE]
> **_Your Answer_**
>
> _You should avoid SELECT * because it retrieves more data than necessary, harming performance. Additionally, if the table schema changes (columns are added or removed), the application expecting specific columns in a specific order will likely break._

6. What does DISTINCT do? On what level does it operate (columns or entire rows)?

> [!NOTE]
> **_Your Answer_**
>
> _DISTINCT removes duplicate records from the output. It operates at the entire row level; two rows must have identical data across every single column specified in the SELECT statement in order for one to be discarded as a duplicate._

7. Can you use a column alias in a WHERE clause? Why or why not?

> [!NOTE]
> **_Your Answer_**
>
> _No, you cannot. Based on the logical execution order of SQL, the WHERE clause runs to filter rows before the SELECT clause actually assigns the aliases. The database doesn't know the alias exists yet._
>
> 8. Explain what happens when you GROUP BY a column and there's a column in SELECT that isn't aggregated and isn't in GROUP BY.

> [!NOTE]
> **_Your Answer_**
>
> _PostgreSQL will throw an error. If you collapse multiple rows into one group based on a column (like category), the database does not know which product name to display unless you specify an aggregate function (like MAX) or include that column in the GROUP BY logic._

---

## Exercise 3: Query Writing Exercises (Optional)

> [!TIP]
> **Recommended practice.** Do this section. It is not required to finish the TrailShop project. It prepares you for the exams.

Write the SQL for each task. Use the TrailShop schema (categories, customers, products, product_categories, orders, order_items).

### Simple SELECT + WHERE

**3.1** — Select product names and prices for all products with stock less than 15.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT name, price FROM products WHERE stock_quantity < 15;
>
>
> ```

**3.2** — Find all customers who registered (created_at) in 2026. Show first name, last name, and registration date.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT first_name, last_name, registered_at FROM customers WHERE EXTRACT(YEAR FROM registered_at) = 2026;
>
>
> ```

**3.3** — Show all products that do NOT have a description (description IS NULL).

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT name FROM products WHERE description IS NULL;
>
>
> ```

### Multi-condition Filtering

**3.4** — Find products in category 1 OR category 2, priced above €100, with stock greater than 0. Sort by price descending.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT p.name, p.price
> FROM products p
> INNER JOIN product_categories pc ON p.product_id = pc.product_id
> WHERE pc.category_id IN (1, 2) AND p.price > 100 AND p.stock_quantity > 0
> ORDER BY p.price DESC;
>
>
> ```

**3.5** — Find orders that are either 'delivered' or placed by customer_id 1. Show order_id, customer_id, status.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT order_id, customer_id, status FROM orders WHERE status = 'delivered' OR customer_id = 1;
>
>
> ```

### Aggregation

**3.6** — For each category, show the category name, minimum, maximum, and average product price. Round averages to 2 decimal places. Join through `product_categories`.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT c.category_name, MIN(p.price), MAX(p.price), ROUND(AVG(p.price), 2)
> FROM categories c
> INNER JOIN product_categories pc ON c.category_id = pc.category_id
> INNER JOIN products p ON pc.product_id = p.product_id
> GROUP BY c.category_id, c.category_name;
>
>
> ```

**3.7** — Count how many distinct customers have placed at least one order.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT COUNT(DISTINCT customer_id) FROM orders;
>
>
> ```

**3.8** — Find the total quantity of items sold across all orders.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT SUM(quantity) FROM order_items;
>
>
> ```

### JOIN Queries

**3.9** — Show each product name alongside its category name. Include all products (even if somehow a category was deleted — use LEFT JOIN).

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT p.name, c.category_name
> FROM products p
> LEFT JOIN product_categories pc ON p.product_id = pc.product_id
> LEFT JOIN categories c ON pc.category_id = c.category_id;
>
>
> ```

**3.10** — List all orders showing: order_id, customer full name, order date, number of items in the order, and order total cost.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT o.order_id, c.first_name || ' ' || c.last_name AS full_name, o.order_date, SUM(oi.quantity) AS num_items, SUM(oi.quantity * oi.unit_price) AS total_cost
> FROM orders o
> INNER JOIN customers c ON o.customer_id = c.customer_id
> INNER JOIN order_items oi ON o.order_id = oi.order_id
> GROUP BY o.order_id, c.first_name, c.last_name, o.order_date;
>
>
> ```

**3.11** — Show all products that have NEVER been ordered. (Hint: LEFT JOIN order_items, then IS NULL on order_item_id.)

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT p.name
> FROM products p
> LEFT JOIN order_items oi ON p.product_id = oi.product_id
> WHERE oi.product_id IS NULL;
>
>
> ```

### GROUP BY + HAVING

**3.12** — Show categories where the average product price exceeds €100. Display category name and average price.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT c.category_name, AVG(p.price) AS avg_price
>  FROM categories c
> INNER JOIN product_categories pc ON c.category_id = pc.category_id
> INNER JOIN products p ON pc.product_id = p.product_id
> GROUP BY c.category_id, c.category_name
> HAVING AVG(p.price) > 100;
>
>
> ```

**3.13** — Find customers who have placed more than 1 order. Show their name and order count.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT c.first_name, c.last_name, COUNT(o.order_id) AS order_count
> FROM customers c
> INNER JOIN orders o ON c.customer_id = o.customer_id
> GROUP BY c.customer_id, c.first_name, c.last_name
> HAVING COUNT(o.order_id) > 1;
>
>
> ```

### Pagination

**3.14** — Write a paginated query that returns products 4 through 6 (page 2, page size 3), ordered by product_id.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT * FROM products ORDER BY product_id LIMIT 3 OFFSET 3;
>
>
> ```

### Complex

**3.15** — Write a "sales report" query that shows: category name, total units sold (from order_items), total revenue, and number of distinct products sold — for each category. Sort by revenue descending.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> SELECT c.category_name, SUM(oi.quantity) AS units_sold, SUM(oi.quantity * oi.unit_price) AS total_revenue, COUNT(DISTINCT oi.product_id) AS distinct_products_sold
> FROM categories c
> INNER JOIN product_categories pc ON c.category_id = pc.category_id
> INNER JOIN order_items oi ON pc.product_id = oi.product_id
> GROUP BY c.category_id, c.category_name
> ORDER BY total_revenue DESC;
>
>
> ```

---

## Exercise 4: Query Reading Exercise (Optional)

> [!TIP]
> **Recommended practice.** Do this section. It is not required to finish the TrailShop project. It prepares you for the exams.

For each query below, explain in **plain English** what it does and what the result would look like.

### 4.1

```sql
SELECT c.name, COUNT(p.product_id) AS num_products
FROM categories c
LEFT JOIN product_categories pc ON pc.category_id = c.category_id
LEFT JOIN products p ON p.product_id = pc.product_id
GROUP BY c.name
ORDER BY num_products DESC;
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _This query calculates how many products belong to each category. Because it uses LEFT JOIN, it will include all categories in the list (even empty ones, outputting 0 products) rather than omitting them entirely. It sorts the results to show the categories with the most products at the top._
>
> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.2

```sql
SELECT first_name, last_name
FROM customers
WHERE customer_id NOT IN (
    SELECT DISTINCT customer_id FROM orders
);
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _This uses a subquery approach to find customers who have not bought anything. It queries the names of customers whose ID cannot be found in the distinct list of all IDs present in the orders table._
>
> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.3

```sql
SELECT p.name, p.price, p.stock,
       p.price * p.stock AS inventory_value
FROM products p
WHERE p.stock > 0
ORDER BY inventory_value DESC
LIMIT 3;
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _This query calculates the total inventory value tied up in each product (price multiplied by stock) for items currently in stock. By ordering descending and limiting to 3, it displays the top 3 products with the highest total financial inventory value._
>
> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.4

```sql
SELECT o.order_id,
       SUM(oi.quantity * oi.unit_price) AS order_total
FROM orders o
INNER JOIN order_items oi ON o.order_id = oi.order_id
WHERE o.status <> 'cancelled'
GROUP BY o.order_id
HAVING SUM(oi.quantity * oi.unit_price) > 200
ORDER BY order_total DESC;
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _This analyzes valid sales. It removes cancelled orders up front, calculates the final bill total for the remaining orders, and returns only the heavy-hitter orders that generated more than $200 in revenue, sorted by largest revenue first._
>
> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.5

```sql
SELECT c.first_name || ' ' || c.last_name AS customer,
       COUNT(DISTINCT o.order_id) AS num_orders,
       SUM(oi.quantity) AS total_items,
       ROUND(SUM(oi.quantity * oi.unit_price), 2) AS total_spent
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
LEFT JOIN order_items oi ON o.order_id = oi.order_id
GROUP BY c.customer_id, c.first_name, c.last_name
ORDER BY total_spent DESC NULLS LAST;
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _This acts as a comprehensive Customer Lifetime Value report. It gathers every customer (including those who haven't ordered), tallies up their total separate orders, sums the raw quantity of total physical items they've purchased, and calculates their lifetime spending rounding to two decimals. It sorts the highest spenders to the top._
>
> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

---

## Submission Checklist

**Required**

- [x ] All 15 business questions answered with working SQL
- [x ] Theory review questions answered in your own words
- [x ] Results of the business questions verified by running them against your TrailShop database

**Recommended practice**

- [x ] All 15 query writing exercises completed
- [x ] All 5 query reading explanations written
