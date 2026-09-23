# Week 37 — Exercises & Project Task

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

These exercises accompany the Week 37 Theory material. Complete all sections.

---

## Part 1: TrailShop Project Task

### Task 1: Identify Keys

Using the `products`, `categories`, and `customers` tables shown in Section 2 of this week's Theory material, answer:

1. What is the primary key of the `products` table? Why is it a good choice?

> [!NOTE]
> 
>
> The primary key of the products table is product_id.
It is a good choice because it uniquely identifies each product.
It never repeats and cannot be null, so it keeps the table consistent.
A numeric ID also makes searching and linking tables fast and efficient.
>
>
>
>


2. What is the primary key of the `categories` table?

> [!NOTE]
> ***Your Answer***
>
> The primary key of the categories table is category_id.
It is the best choice because it uniquely identifies each category, never repeats, and ensures reliable linking between products and their categories.
>
>
>
>


3. What is the foreign key in the `products` table? What does it reference?

> [!NOTE]
> ***Your Answer***
>
> The foreign key in the products table is category_id.
It references the category_id column in the categories table.
This link ensures every product is assigned to a valid category, maintaining relational integrity between the two tables.
>
>
>
>


4. Is `name` in `products` a candidate key? Under what assumption? What would make it unsuitable as a primary key?


> [!NOTE]
> ***Your Answer***
>
> 'name' in the products table can be a candidate key only if we assume every product name is unique.
However, product names often repeat or change over time, making them unreliable.
Names can also be long, inconsistent, or contain spelling variations.
Because of this, name is not suitable as a primary key compared to a stable numeric ID.
>
>
>
>
5. Give an example of a **superkey** for the `products` table that is NOT a candidate key. Explain why it's not minimal.

> [!NOTE]
> ***Your Answer***
>
> A superkey for the products table that is not a candidate key is {product_id, name}.
It is a superkey because it still uniquely identifies each product.
However, it is not minimal, removing name still keeps uniqueness.
Since it contains extra unnecessary attributes, it cannot be a candidate key.
>
>
>
>

6. Give an example of a **composite key** using a hypothetical `order_items` table. Explain why neither column alone would be sufficient.

> [!NOTE]
> ***Your Answer***
>
> A composite key in a hypothetical 'order_items' table could be order_id, product_id.
Together they uniquely identify each item in an order.
'order_id' alone is not enough because one order can contain many products.
'product_id' alone is not enough because the same product can appear in many different orders.
>
>
>
>

7s . I`email` in `customers` a candidate key? What makes it different from `customer_id` as a PK choice? *(See Section 6.9 on natural vs surrogate keys.)*

> [!NOTE]
> ***Your Answer***
>
> 'email' in the customers table can be a candidate key if we assume every customer has a unique email address.
Unlike 'customer_id', which is a surrogate key, email is a natural key taken from real‑world data.
Emails can change, be mistyped, or have duplicates, making them less stable.
Because of this, 'customer_id' is a safer and more reliable primary key.
>
>
>
>

### Task 2: Define Business Rules

List **5 business rules** for TrailShop. For each rule, specify:
- The rule in plain English
- Which constraint type(s) would enforce it
- Which table and column the constraint applies to
- The SQL syntax for the constraint

Example:

| Business Rule | Constraint Type | Table.Column | SQL |
|---|---|---|---|
| Every product must have a price greater than zero | CHECK | products.price | `CHECK (price > 0)` |
| ... | ... | ... | ... |

Think about rules for customers, orders, and categories — not just products.

> [!NOTE]
> ***Your Answer***
>
> 1. Every customer must have a unique email address  
Constraint Type: UNIQUE
Table.Column: customers.email
SQL: UNIQUE (email)

2. Every product must belong to a valid category  
Constraint Type: FOREIGN KEY
Table.Column: products.category_id
SQL: FOREIGN KEY (category_id) REFERENCES categories(category_id)

3. Stock quantity cannot be negative  
Constraint Type: CHECK
Table.Column: products.stock_quantity
SQL: CHECK (stock_quantity >= 0)

4. Category names must not be empty  
Constraint Type: CHECK
Table.Column: categories.category_name
SQL: CHECK (category_name <> '')

5. Customer city must be one of the known Finnish cities  
Constraint Type: CHECK
Table.Column: customers.city
SQL: CHECK (city IN ('Helsinki','Tampere','Turku','Oulu'))
>
>
>
>

### Task 3: Integrity Violations

For each SQL statement below, predict whether it will **succeed** or **fail**. If it fails, explain which integrity rule or constraint is violated and what error message you'd expect. Assume the schema from Section 9.8 of the Theory material.

```sql
-- Statement A
INSERT INTO categories (category_id, category_name)
VALUES (NULL, 'Cycling');

-- Statement B
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (109, 'AeroLite Tent', 279.00, 10, 2);

-- Statement C
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (110, 'BudgetBoots', -5.00, 25, 1);

-- Statement D
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (103, 'Duplicate Shoes', 99.99, 5, 3);

-- Statement E
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (111, 'CloudWalker Sandals', 65.00, 40, 10);

-- Statement F
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (112, NULL, 89.99, 20, 1);

-- Statement G
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (113, 'LightStep Shoes', 149.00, -3, 1);

-- Statement H
INSERT INTO order_items (order_id, product_id, quantity, unit_price)
VALUES (1001, 101, 0, 189.50);
```

> [!NOTE]
> ***Your Answer***
>
> Statement A - FAIL – violates PRIMARY KEY / NOT NULL.
Error: “ERROR: category_id cannot be NULL”.
>Statement B - SUCCEED – all values valid, category_id = 2 exists.
>Statement C - FAIL – violates CHECK (price > 0).
Error: “ERROR: price must be greater than zero”.
>Statement D - FAIL – violates PRIMARY KEY uniqueness.
product_id 103 already exists.
Error: “ERROR: duplicate key value violates unique constraint product_id”
>Statement E - FAIL – violates FOREIGN KEY (category_id).
category_id 10 does not exist.
Error: “ERROR: insert/update on table products violates foreign key constraint”
> Statement F - FAIL – violates NOT NULL constraint on name.
Error: “ERROR: name cannot be NULL”
> Statement G - FAIL – violates CHECK (stock_quantity >= 0).
Error: “ERROR: stock_quantity must be non‑negative
> Statement H - FAIL – violates CHECK (quantity > 0).
Error: “ERROR: quantity must be greater than zero”


### Task 4: Foreign Key Actions

Consider the following scenario using the schema from Theory Section 9.8:

1. You want to delete category 2 ("Camping") from the `categories` table. Products 102 and 106 reference this category. What happens with:
   - `ON DELETE RESTRICT`?
   - `ON DELETE CASCADE`?
   - `ON DELETE SET NULL`? (Assume `category_id` in `products` allows NULL for this question)
- 'ON DELETE RESTRICT'
- The delete fails.
Because products 102 and 106 still reference category_id = 2, the database blocks the deletion.
You would get an error like: “Cannot delete category: referenced by products.”
  - 'ON DELETE CASCADE'
  - The delete succeeds.
Products 102 and 106 are automatically deleted because their category is removed.
All dependent rows disappear with the parent row.
-'ON DELETE SET NULL'
-The delete succeeds.
Products 102 and 106 remain in the table, but their category_id becomes NULL.
This works only because you assumed category_id allows NULL.
  
2. Which foreign key action would you recommend for the TrailShop `products.category_id` → `categories.category_id` relationship? Justify your choice in 2–3 sentences.

> [!NOTE]
> ***Your Answer***
>
> I recommend ON DELETE RESTRICT for the products.category_id → categories.category_id relationship. Categories are core business data, and deleting one should not accidentally remove many products or leave them without a valid category. Restricting the delete forces staff to update or reassign products first, protecting inventory integrity.

>
>
>
>




---

## Part 2: Theory Review Questions

Answer each question in 2–4 sentences unless otherwise specified. Reference the Theory material sections as needed.

### Short-Answer Questions

**Q1.** Define the following terms in your own words: relation, tuple, attribute, domain. Give one TrailShop example for each.

> [!NOTE]
> ***Your Answer***
>
> relation  
A relation is a table in a database that stores related data.
TrailShop example: products is a relation because it stores all product records.

tuple  
A tuple is a single row in a table.
TrailShop example: The row for product 101 (“Alpine Pro Hiking Boots”) is a tuple.

attribute  
An attribute is a column that describes a property of the data.
TrailShop example: price in the products table is an attribute.

domain  
A domain is the set of allowed values for an attribute.
TrailShop example: The domain of category_id is the set {1, 2, 3, 4, 5}.
>
>
>
>

*(See Sections 2 and 3 of this week's Theory material.)*

**Q2.** What makes a candidate key different from a primary key? Can a table have more than one candidate key?


> [!NOTE]
> ***Your Answer***
>
> A candidate key is any attribute (or set of attributes) that can uniquely identify a row.
A primary key is the chosen candidate key that the database actually uses as the main identifier.
Yes, a table can have multiple candidate keys, but only one of them becomes the primary key.
>
>
>
>

*(See Section 6 of this week's Theory material.)*

**Q3.** Explain entity integrity in your own words. Why can't a primary key be NULL?


> [!NOTE]
> ***Your Answer***
>
> Entity integrity means every table must have a primary key, and that key must uniquely identify each row.
A primary key cannot be NULL because a NULL value does not identify anything, it has no meaning and cannot be used to distinguish one row from another.
If a primary key were NULL, the database would not be able to guarantee unique, valid records.
>
>
>
>

*(See Section 8.1 of this week's Theory material.)*

**Q4.** What happens when referential integrity is violated? Give a concrete TrailShop example — show the SQL statement and the expected error.

> [!NOTE]
> ***Your Answer***
>
> Referential integrity is violated when a foreign key points to a parent row that does not exist.  
The database rejects the operation to prevent “orphan” records.
>TrailShop example (products → categories):

sql
INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (120, 'TrailMaster Jacket', 129.00, 15, 99);
>Expected result: FAIL  
Reason: category_id = 99 does not exist in categories.
Error message: “ERROR: insert/update on table products violates foreign key constraint”.
>
>

*(See Section 8.2 of this week's Theory material.)*

**Q5.** Explain the difference between a surrogate key and a natural key. Give an example of each for a `books` table in a library database.

> [!NOTE]
> ***Your Answer***
>
> A surrogate key is an artificial identifier created by the system, usually a number with no real‑world meaning.
Example for books table: book_id (e.g., 5012).

A natural key is an identifier that comes from real‑world data and already uniquely identifies the item.
Example for books table: ISBN (e.g., '978‑0143127796').
>
>
>
>

*(See Section 6.8–6.9 of this week's Theory material.)*

**Q6.** What is a NULL value? Why is `WHERE price = NULL` wrong? What should you write instead?


> [!NOTE]
> ***Your Answer***
>
> A NULL value means “no value”, “unknown”, or “not applicable”.
WHERE price = NULL is wrong because NULL cannot be compared using = — it never returns true.
You must write:

sql
WHERE price IS NULL
This is the correct way to test for NULL in SQL.
>
>
>
>

*(See Section 7 of this week's Theory material.)*

**Q7.** What is a junction table? When is it needed? Give an example.

> [!NOTE]
> ***Your Answer***
>A junction table is a table used to link two other tables in a many‑to‑many relationship.
It is needed when one record in Table A can relate to many records in Table B, and vice versa.

Example:  
In TrailShop, products can belong to many categories, and categories can contain many products.
So we create a junction table:

product_categories(product_id, category_id)
>
>
>
>

*(See Section 12.3 of this week's Theory material.)*

**Q8.** Describe the three types of relationships (1:1, 1:N, M:N). For each, give one TrailShop example.

> [!NOTE]
> ***Your Answer***
>
> 1:1 (one‑to‑one)  
Each row in Table A matches at most one row in Table B.
TrailShop example: Each customer might have one loyalty profile, and each loyalty profile belongs to one customer.

1:N (one‑to‑many)  
One row in Table A can relate to many rows in Table B.
TrailShop example: One category (e.g., “Camping”) has many products.

M:N (many‑to‑many)  
Rows in Table A can relate to many rows in Table B, and vice versa.
TrailShop example: Products and categories — a product can belong to many categories, and a category can contain many products. This is implemented using a junction table like product_categories.
>
>
>
>

*(See Section 12 of this week's Theory material.)*

**Q9.** What is the difference between `ON DELETE CASCADE` and `ON DELETE RESTRICT`? When would you use each?


> [!NOTE]
> ***Your Answer***
>
> ON DELETE CASCADE  
If the parent row is deleted, all child rows that reference it are automatically deleted.
Use CASCADE when child rows have no meaning without the parent. For example, deleting an order should delete its order_items.

ON DELETE RESTRICT  
The delete fails if any child rows still reference the parent.
Use RESTRICT when deleting the parent would cause data loss or inconsistency. For example, categories should not be deleted while products still depend on them.
>
>
>
>

*(See Section 10 of this week's Theory material.)*

**Q10.** Explain what "atomic entries" means in the context of relation properties. Give an example of a violation.

> [!NOTE]
> ***Your Answer***
>
> Atomic entries” means each attribute in a table must store one single value, not multiple values packed together. Every cell should contain only one piece of information.

Violation example:  
A products table storing this in one column:

colors = 'red, blue, green'

This breaks atomicity because the attribute contains three values instead of one.
>
>
>
>

*(See Section 5.3 of this week's Theory material.)*

### True/False

For each statement, write **True** or **False** and correct any false statements.

1. A superkey is always a candidate key. False [ A superkey can contain extra unnecessary attributes; a candidate key is a minimal superkey.]
2. A primary key can consist of more than one column. True
3. NULL = NULL evaluates to TRUE in SQL. False [ NULL = NULL is UNKNOWN. You must use IS NULL]
4. A foreign key must always be NOT NULL. False [ Foreign keys can be NULL if the relationship allows “no parent” (optional relationship).] 
5. Referential integrity ensures that every FK value matches an existing PK value (or is NULL). True
6. The degree of a relation is the number of rows. False [ The degree is the number of attributes (columns). The number of rows is the cardinality.]

### Matching Exercise

Match each term (1–12) with its definition (A–L).

| # | Term |
|---|---|
| 1 | Superkey |
| 2 | Candidate key |
| 3 | Composite key |
| 4 | Foreign key |
| 5 | Alternate key |
| 6 | Surrogate key |
| 7 | Natural key |
| 8 | Orphan record |
| 9 | Domain |
| 10 | Junction table |
| 11 | Cardinality |
| 12 | COALESCE |

| Letter | Definition |
|---|---|
| A | The set of all permitted values for an attribute |
| B | A key composed of two or more attributes |
| C | A row whose FK references a non-existent PK — forbidden by referential integrity |
| D | An artificial key with no business meaning (e.g., auto-generated ID) |
| E | A candidate key not chosen as the primary key |
| F | Any set of attributes that uniquely identifies every tuple |
| G | A minimal superkey — no attribute can be removed without losing uniqueness |
| H | A column that references the primary key of another table |
| I | The number of tuples (rows) in a relation |
| J | A key drawn from real-world data with business meaning |
| K | A table implementing a many-to-many relationship |
| L | A SQL function that returns the first non-NULL argument |


> [!NOTE]
> ***Your Answers***
>
> | # | Your Match |
> |---|---|
> | 1 |F |
> | 2 |G |
> | 3 |B |
> | 4 |H |
> | 5 |E |
> | 6 |D |
> | 7 |J |
> | 8 |C |
> | 9 |A |
> | 10 |K |
> | 11 |I |
> | 12 |L |
>

---

## Part 3: SQL Practice — Constraints in Action

These exercises test your understanding of constraints. You do NOT need to run these in PostgreSQL (but you may if you'd like to verify your answers).

### Exercise 3.1: Predict the Outcome

Given the following table definitions:

```sql
CREATE TABLE departments (
    dept_id   INTEGER      PRIMARY KEY,
    dept_name VARCHAR(50)  NOT NULL UNIQUE
);

CREATE TABLE employees (
    emp_id    INTEGER       PRIMARY KEY,
    name      VARCHAR(100)  NOT NULL,
    salary    NUMERIC(10,2) NOT NULL CHECK (salary >= 0),
    dept_id   INTEGER       NOT NULL REFERENCES departments(dept_id)
);
```

Assume these rows already exist:

```sql
INSERT INTO departments VALUES (1, 'Engineering');
INSERT INTO departments VALUES (2, 'Marketing');
INSERT INTO employees VALUES (100, 'Alice', 75000, 1);
INSERT INTO employees VALUES (101, 'Bob', 65000, 2);
```

For each statement below, predict: **SUCCESS** or **FAIL**? If fail, name the violated constraint.

```sql
-- 1
INSERT INTO employees VALUES (102, 'Carol', 70000, 1);
SUCCESS  
Valid salary, valid dept_id, new emp_id.

-- 2
INSERT INTO employees VALUES (103, 'Dan', -5000, 1);
FAIL – violates CHECK (salary >= 0).

-- 3
INSERT INTO employees VALUES (100, 'Eve', 80000, 2);
FAIL – violates PRIMARY KEY (emp_id 100 already exists)

-- 4
INSERT INTO employees VALUES (104, 'Frank', 60000, 5);
FAIL – violates FOREIGN KEY (dept_id).
Department 5 does not exist.

-- 5
INSERT INTO departments VALUES (3, 'Engineering');
FAIL – violates UNIQUE (dept_name).
“Engineering” already exists.

-- 6
INSERT INTO employees VALUES (105, NULL, 55000, 2);
FAIL – violates NOT NULL (name).

-- 7
DELETE FROM departments WHERE dept_id = 1;
FAIL – violates referential integrity.
Employees (e.g., Alice) still reference dept_id = 1.

-- 8
INSERT INTO employees VALUES (106, 'Grace', 0, 2);
SUCCESS  
Salary 0 is allowed (salary >= 0), dept_id 2 exists.
```

### Exercise 3.2: Write the Constraints

Given these business rules for a **bookstore database**, write the `CREATE TABLE` statements with appropriate constraints:

1. Every book has a unique ISBN (13 characters), a title (required), a price (must be positive), and a publication year.
2. Every author has an ID, a first name (required), and a last name (required).
3. A book can have multiple authors, and an author can write multiple books.
4. Every book belongs to exactly one genre. Genres have an ID and a unique name.
5. Publication year must be between 1450 and the current year.

*(Hint: you'll need at least 4 tables, including a junction table for the M:N relationship.)*



## Part 4: Design Exercise — Library System

A small public library needs a database. Here is a description of their requirements:

> The library has a collection of **books**. Each book has an ISBN, a title, a publication year, and belongs to one genre (Fiction, Non-Fiction, Science, History, etc.). The library may own multiple **copies** of the same book — each copy has a unique barcode sticker.
>
> The library has registered **members**. Each member has a member number, name, email, and phone. Members can **borrow** copies. Each borrowing records which member borrowed which copy, the borrow date, the due date, and the return date (NULL if not yet returned).
>
> **Rules:**
> - A member can borrow at most 5 copies at any given time.
> - The due date is always 14 days after the borrow date.
> - A copy cannot be borrowed if it's currently not returned (return_date IS NULL).

### Your Tasks

1. **Identify the tables** you would need (list them with their columns).
2. **Identify the primary key** for each table. Are they surrogate or natural keys? Justify your choices.
3. **Identify all foreign keys** and the tables they reference.
4. **Identify any candidate keys** beyond the primary key (alternate keys).
5. **List the business rules** from the description and map each to a constraint type. Which rules cannot be enforced by simple constraints?


> [!NOTE]
> ***Your Answer***
>
1. Identify the tables and their columns

books: isbn, title, publication_year, genre
copies: barcode, isbn
members: member_number, name, email, phone
borrowings: borrow_id, member_number, barcode, borrow_date, due_date, return_date

2. Identify the primary key for each table. Are they surrogate or natural?

books.isbn (Natural Key): The ISBN is an internationally recognized, real-world identifier for a book.
copies.barcode (Natural Key): The barcode is a physical sticker placed on the book in the real world. (Note: It could be considered surrogate if the library generates it purely for the database, but since it exists as a physical scannable object, it is treated as a natural business key).
members.member_number (Surrogate Key): This is an artificial ID created by the library system solely to identify a person. It has no external meaning.
borrowings.borrow_id (Surrogate Key): An auto-incrementing artificial integer to uniquely identify the specific transaction. (While a composite key of barcode + borrow_date is possible, a surrogate ID is much cleaner for tracking transactions).

3. Identify all foreign keys and the tables they reference

copies.isbn references books.isbn (Links a physical copy to its abstract book details).
borrowings.member_number references members.member_number (Links the transaction to the user).
borrowings.barcode references copies.barcode (Links the transaction to the specific physical book borrowed).

4. Identify any candidate keys beyond the primary key (alternate keys)

members.email: Assuming the library requires unique emails for login or contact purposes, this is a candidate key.
borrowings.(barcode, borrow_date): A specific physical copy of a book can only be checked out once on any given exact timestamp. This combination must be unique.

5. List the business rules and map each to a constraint type

Rule: A member can borrow at most 5 copies at any given time.
Constraint Type: Cannot be enforced by simple constraints. This requires application logic or a database trigger to count rows where return_date IS NULL before allowing an insert.

Rule: The due date is always 14 days after the borrow date.
Constraint Type: CHECK (due_date = borrow_date + 14) or handled via DEFAULT (CURRENT_DATE + 14).

Rule: A copy cannot be borrowed if it's currently not returned.
Constraint Type: Cannot be enforced by simple constraints. Checking if a previous row for the same barcode has a NULL return date requires an advanced EXCLUDE constraint or a custom trigger.

Rule: Return date is empty if the book is not yet returned.
Constraint Type: Allow NULL (Do not apply NOT NULL to return_date).
>
>
>
6. **Write the CREATE TABLE statements** for at least the `books`, `copies`, and `borrowings` tables with full constraints.
> [!NOTE]
> ***Your Answer***
>
CREATE TABLE members (
    member_number INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    phone VARCHAR(20)
);

CREATE TABLE books (
    isbn VARCHAR(13) PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    publication_year INTEGER NOT NULL,
    genre VARCHAR(50) NOT NULL
);

CREATE TABLE copies (
    barcode VARCHAR(50) PRIMARY KEY,
    isbn VARCHAR(13) NOT NULL REFERENCES books(isbn) ON DELETE RESTRICT
);

CREATE TABLE borrowings (
    borrow_id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    member_number INTEGER NOT NULL REFERENCES members(member_number),
    barcode VARCHAR(50) NOT NULL REFERENCES copies(barcode),
    borrow_date DATE NOT NULL DEFAULT CURRENT_DATE,
    due_date DATE NOT NULL,
    return_date DATE, -- Allows NULL for unreturned books

-- Ensures the due date is exactly 14 days after borrow date
    CHECK (due_date = borrow_date + 14),
-- Ensures the return date (if provided) isn't before the borrow date
    CHECK (return_date IS NULL OR return_date >= borrow_date)
> 
>
>
>
>
---

## Submission Checklist

- [x ] Task 1: Key identification answers (Part 1)
- [x ] Task 2: Business rules table with 5 rules (Part 1)
- [x ] Task 3: Integrity violation predictions with explanations (Part 1)
- [x ] Task 4: Foreign key action analysis (Part 1)
- [x ] Theory Review Questions answered (Part 2)
- [x ] SQL Practice — constraint predictions and bookstore CREATE TABLE (Part 3)
- [x ] Library System design exercise (Part 4)
