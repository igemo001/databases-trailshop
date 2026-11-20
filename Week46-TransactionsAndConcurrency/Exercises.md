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
> ***Your Answer***
>
> Primary key of products: product_id. It is a good choice because it is a unique, non-NULL, immutable surrogate key (an integer). It will never change even if a product is renamed or repriced, and integers are compact and fast for indexing and foreign key joins.
> 
>
>
>
>


2. What is the primary key of the `categories` table?

> [!NOTE]
> ***Your Answer***
>
> Primary key of categories: category_id.
>
>
>
>


3. What is the foreign key in the `products` table? What does it reference?

> [!NOTE]
> ***Your Answer***
>
>Foreign key in products: category_id, which references categories(category_id).
>
>
>
>


4. Is `name` in `products` a candidate key? Under what assumption? What would make it unsuitable as a primary key?


> [!NOTE]
> ***Your Answer***
>
> Yes, only under the assumption that no two products can ever share the exact same name. It is unsuitable as a primary key because product names can change (rebranding, fixing typos), two different brands might eventually sell items with the same name, and text strings are less efficient than integers for indexing and joins.
>
>
>
>
5. Give an example of a **superkey** for the `products` table that is NOT a candidate key. Explain why it's not minimal.

> [!NOTE]
> ***Your Answer***
>
>(product_id, name). It uniquely identifies every row, so it is a superkey, but it is not minimal because product_id alone already guarantees uniqueness—removing name still leaves a valid unique identifier.
>
>
>
>

6. Give an example of a **composite key** using a hypothetical `order_items` table. Explain why neither column alone would be sufficient.

> [!NOTE]
> ***Your Answer***
>
> (order_id, product_id). Neither column alone is sufficient because a single order (order_id) can contain multiple different products, and a single product (product_id) can appear across many different orders. Combining them uniquely identifies each specific line item in an order.
>
>
>
>

7. Is `email` in `customers` a candidate key? What makes it different from `customer_id` as a PK choice? *(See Section 6.9 on natural vs surrogate keys.)*

> [!NOTE]
> ***Your Answer***
>
> Is email in customers a candidate key? Yes, assuming every customer account must have a unique email address. Unlike customer_id (a surrogate key), email is a natural key with real-world meaning. customer_id is usually the better PK choice because customers can change their email addresses, and updating a primary key that is referenced by foreign keys in other tables is error-prone.
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
> Every customer must have a unique email address:
> constraint type: unique, not null
> table.column: customers.email
> sql: email VARCHAR(255) NOT NULL UNIQUE
>
> Every category name must be unique and cannot be blank.
> constraint type: unique, not null
> table.column: categories.category_name
> sql: category_name VARCHAR(100) NOT NULL UNIQUE
>
> product stock quantity cannot be negative
> constraint type: check
> table.column: product.stock_quantity
> sql: CHECK (stock_quantity>= 0)
>
> every order must belong to an existing customer.
> constrant type: FOREIGN KEY, NOT NULL
> table.column: orders.customer_id
> sql: customer_id INTEGER NOT NULL REFRENCES customers(customer_id)
>
> order item quantity must be at least 1
> constrant type: check
> table.column: order_items.quantity
> sql: CHECK (quantity 0)
> 
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
> Statement A — FAIL: Violates Entity Integrity (PRIMARY KEY / NOT NULL constraint) on categories.category_id. A primary key cannot be NULL.
> Expected error: ERROR: null value in column "category_id" violates not-null constraint
> Statement B — SUCCESS: All constraints are satisfied (product_id 109 is unique, name is not NULL, price is positive, stock_quantity is non-negative, and category_id 2 exists in categories).
> Statement C — FAIL: Violates the CHECK constraint on products.price (price > 0), because -5.00 is negative.
> Expected error: ERROR: new row for relation "products" violates check constraint "products_price_check"
> Statement D — FAIL: Violates Entity Integrity (PRIMARY KEY uniqueness constraint) on products.product_id, because 103 already exists.
> Expected error: ERROR: duplicate key value violates unique constraint "products_pkey"
> Statement E — FAIL: Violates Referential Integrity (FOREIGN KEY constraint) on products.category_id, because category 10 does not exist in categories.
> Expected error: ERROR: insert or update on table "products" violates foreign key constraint "products_category_id_fkey"
> Statement F — FAIL: Violates the NOT NULL constraint on products.name.
> Expected error: ERROR: null value in column "name" violates not-null constraint
> Statement G — FAIL: Violates the CHECK constraint on products.stock_quantity (stock_quantity >= 0), because -3 is negative.
> Expected error: ERROR: new row for relation "products" violates check constraint "products_stock_quantity_check"
> Statement H — FAIL: Violates the CHECK constraint on order_items.quantity (quantity > 0), because 0 is not a valid order quantity.
> Expected error: ERROR: new row for relation "order_items" violates check constraint "order_items_quantity_check"
> 
> 
> 
> 
>
>
>
>

### Task 4: Foreign Key Actions

Consider the following scenario using the schema from Theory Section 9.8:

1. You want to delete category 2 ("Camping") from the `categories` table. Products 102 and 106 reference this category. What happens with:
   - `ON DELETE RESTRICT`?
   - `ON DELETE CASCADE`?
   - `ON DELETE SET NULL`? (Assume `category_id` in `products` allows NULL for this question)

2. Which foreign key action would you recommend for the TrailShop `products.category_id` → `categories.category_id` relationship? Justify your choice in 2–3 sentences.

> [!NOTE]
> ***Your Answer***
>
> Deleting Category 2 ("Camping"):
> ON DELETE RESTRICT: The database blocks the DELETE statement and raises a foreign key violation error because products 102 and 106 still reference category 2.
> ON DELETE CASCADE: Category 2 is deleted, and the database automatically deletes products 102 and 106 from the product table aswell.
> ON DELETE SET NULL: Category 2 is deleted, and the category_id column for products 102 and 106 is automatically updated to NULL, leaving them uncategorized.
>
> Recommendation: I recommend ON DELETE RESTRICT for products.category_id referencing categories.category_id. Using CASCADE is dangerous in e-commerce because accidentally deleting a category would wipe out all its products (and potentially break historical order records). RESTRICT forces staff to explicitly reassign products 102 and 106 to another category before the old category can be removed, preventing accidental data loss.
> 
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
> Relation: A table made up of rows and columns representing a specific entity type (e.g., the products table).
> Tuple: A single row in a table representing one specific record (e.g., the row for product 101, "TrailBlazer Boots").
> Attribute: A single column in a table representing a property of the entity (e.g., price in products).
> Domain: The set of all permitted values and data types for an attribute (e.g., positive decimal numbers for products.price).
>
>
>
>

*(See Sections 2 and 3 of this week's Theory material.)*

**Q2.** What makes a candidate key different from a primary key? Can a table have more than one candidate key?


> [!NOTE]
> ***Your Answer***
>
> A candidate key is any minimal set of attributes that uniquely identifies every row in a table. A primary key is the single candidate key chosen by the database designer to serve as the main identifier for that table. Yes, a table can have multiple candidate keys (the ones not chosen as the primary key are called alternate keys).
>
>
>
>

*(See Section 6 of this week's Theory material.)*

**Q3.** Explain entity integrity in your own words. Why can't a primary key be NULL?


> [!NOTE]
> ***Your Answer***
>
> Entity integrity is the rule stating that every table must have a primary key, and no part of that primary key can be NULL. A primary key cannot be NULL because NULL represents a missing or unknown value—if a row's identifier were unknown, you could not reliably distinguish it from other rows or reference it from another table.
>
>
>
>

*(See Section 8.1 of this week's Theory material.)*

**Q4.** What happens when referential integrity is violated? Give a concrete TrailShop example — show the SQL statement and the expected error.

> [!NOTE]
> ***Your Answer***
>
>When referential integrity is violated, the database rejects the operation to prevent orphan records (foreign keys pointing to non-existent primary keys). For example, inserting a product with a non-existent category:
> INSERT INTO products (product_id, name, price, stock_quantity, category_id)
VALUES (200, 'Alpine Hat', 25.00, 10, 999);
> ERROR: insert or update on table "products" violates foreign key constraint "products_category_id_fkey" — Key (category_id)=(999) is not present in table "categories".
>
>
>
>

*(See Section 8.2 of this week's Theory material.)*

**Q5.** Explain the difference between a surrogate key and a natural key. Give an example of each for a `books` table in a library database.

> [!NOTE]
> ***Your Answer***
>
> A natural key is a key derived from real-world data that already has business meaning, whereas a surrogate key is an artificial, system-generated identifier (like an auto-incrementing integer) with no business meaning. In a library books table, isbn is a natural key, while book_id (e.g., SERIAL or IDENTITY) is a surrogate key.
>
>
>
>

*(See Section 6.8–6.9 of this week's Theory material.)*

**Q6.** What is a NULL value? Why is `WHERE price = NULL` wrong? What should you write instead?


> [!NOTE]
> ***Your Answer***
>
> NULL represents a missing, unknown, or inapplicable value—it is not zero or an empty string. WHERE price = NULL is wrong because in SQL's three-valued logic, comparing anything to NULL with = evaluates to UNKNOWN (not TRUE), so no rows are returned. Instead, you must write WHERE price IS NULL.
>
>
>
>

*(See Section 7 of this week's Theory material.)*

**Q7.** What is a junction table? When is it needed? Give an example.

> [!NOTE]
> ***Your Answer***
>
> A junction table (or associative/bridge table) is a table used to break a many-to-many (M:N) relationship into two one-to-many (1:N) relationships. It is needed because relational databases cannot directly store M:N relationships between two tables. For example, order_items is a junction table linking orders and products.
>
>
>
>

*(See Section 12.3 of this week's Theory material.)*

**Q8.** Describe the three types of relationships (1:1, 1:N, M:N). For each, give one TrailShop example.

> [!NOTE]
> ***Your Answer***
>
> One-to-One (1:1): Each row in Table A matches at most one row in Table B, and vice versa (e.g., one customer has one loyalty_profile).
> One-to-Many (1:N): One row in Table A can match many rows in Table B, but each row in Table B matches only one row in Table A (e.g., one category contains many products).
> Many-to-Many (M:N): Rows in Table A can match many rows in Table B, and vice versa (e.g., one order can contain many products, and one product can appear in many orders).
>
>
>
>

*(See Section 12 of this week's Theory material.)*

**Q9.** What is the difference between `ON DELETE CASCADE` and `ON DELETE RESTRICT`? When would you use each?


> [!NOTE]
> ***Your Answer***
>
> ON DELETE RESTRICT prevents a parent row from being deleted if any child rows still reference it, whereas ON DELETE CASCADE automatically deletes all referencing child rows when the parent row is deleted. Use RESTRICT when child records are important independent data (like products in a category), and use CASCADE when child records have no meaning without the parent (like order_items belonging to an order).
>
>
>
>

*(See Section 10 of this week's Theory material.)*

**Q10.** Explain what "atomic entries" means in the context of relation properties. Give an example of a violation.

> [!NOTE]
> ***Your Answer***
>
> Atomic entries means that every cell (the intersection of a row and a column) must hold a single, indivisible value rather than a list or set of values. A violation would be storing multiple phone numbers in one cell, such as '040-1234567, 050-7654321' in a customers.phone column.
>
>
>
>

*(See Section 5.3 of this week's Theory material.)*

### True/False

For each statement, write **True** or **False** and correct any false statements.

1. A superkey is always a candidate key. false (A candidate key is always a superkey, but a superkey is only a candidate key if it is minimal (no extra attributes).)
2. A primary key can consist of more than one column.true
3. NULL = NULL evaluates to TRUE in SQL. false (NULL = NULL evaluates to UNKNOWN in SQL, not TRUE.)
4. A foreign key must always be NOT NULL.false (A foreign key can be NULL (representing an optional relationship) unless explicitly restricted with a NOT NULL constraint.)
5. Referential integrity ensures that every FK value matches an existing PK value (or is NULL). true
6. The degree of a relation is the number of rows.false (The degree of a relation is the number of columns (attributes); the number of rows is the cardinality.)

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
> | 1 |f |
> | 2 |g |
> | 3 |b |
> | 4 |h |
> | 5 |e |
> | 6 |d |
> | 7 |j |
> | 8 |c |
> | 9 |a |
> | 10 |k |
> | 11 |i |
> | 12 |l |
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

-- 2
INSERT INTO employees VALUES (103, 'Dan', -5000, 1);

-- 3
INSERT INTO employees VALUES (100, 'Eve', 80000, 2);

-- 4
INSERT INTO employees VALUES (104, 'Frank', 60000, 5);

-- 5
INSERT INTO departments VALUES (3, 'Engineering');

-- 6
INSERT INTO employees VALUES (105, NULL, 55000, 2);

-- 7
DELETE FROM departments WHERE dept_id = 1;

-- 8
INSERT INTO employees VALUES (106, 'Grace', 0, 2);
```

### Exercise 3.2: Write the Constraints

Given these business rules for a **bookstore database**, write the `CREATE TABLE` statements with appropriate constraints:

1. Every book has a unique ISBN (13 characters), a title (required), a price (must be positive), and a publication year.
2. Every author has an ID, a first name (required), and a last name (required).
3. A book can have multiple authors, and an author can write multiple books.
4. Every book belongs to exactly one genre. Genres have an ID and a unique name.
5. Publication year must be between 1450 and the current year.

*(Hint: you'll need at least 4 tables, including a junction table for the M:N relationship.)*
> [!NOTE]
> ***Your Answer***
>
> CREATE TABLE genres (
    genre_id   SERIAL       PRIMARY KEY,
    genre_name VARCHAR(50)  NOT NULL UNIQUE
);

CREATE TABLE books (
    isbn             CHAR(13)      PRIMARY KEY,
    title            VARCHAR(200)  NOT NULL,
    price            NUMERIC(10,2) NOT NULL CHECK (price > 0),
    publication_year INTEGER       CHECK (publication_year >= 1450 AND publication_year <= EXTRACT(YEAR FROM CURRENT_DATE)),
    genre_id         INTEGER       NOT NULL REFERENCES genres(genre_id) ON DELETE RESTRICT
);

CREATE TABLE authors (
    author_id  SERIAL       PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name  VARCHAR(100) NOT NULL
);

CREATE TABLE book_authors (
    isbn      CHAR(13) NOT NULL REFERENCES books(isbn) ON DELETE CASCADE,
    author_id INTEGER  NOT NULL REFERENCES authors(author_id) ON DELETE CASCADE,
    PRIMARY KEY (isbn, author_id)
);
>
>
>
---

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
> 1. Tables & Columns
genres: genre_id, genre_name

books: book_id, isbn, title, publication_year, genre_id

copies: copy_id, barcode, book_id

members: member_id, member_number, name, email, phone

borrowings: borrowing_id, member_id, copy_id, borrow_date, due_date, return_date

2. Primary Keys (Surrogate vs. Natural)
genres.genre_id, books.book_id, copies.copy_id, members.member_id, borrowings.borrowing_id: All surrogate keys (SERIAL / INTEGER).

Justification: Surrogate keys are compact, immutable, and prevent cascading updates if a barcode sticker is replaced, an ISBN has a typo fixed, or a member number format changes. (Alternatively, books.isbn or copies.barcode could be used as natural keys, but surrogate keys keep joins uniform and resilient.)

3. Foreign Keys
books.genre_id references genres(genre_id)

copies.book_id references books(book_id)

borrowings.member_id references members(member_id)

borrowings.copy_id references copies(copy_id)

4. Candidate Keys (Alternate Keys)
genres.genre_name

books.isbn

copies.barcode

members.member_number and members.email

5. Business Rules & Enforcement
Each book has a unique ISBN: UNIQUE, NOT NULL on books.isbn.

Each copy has a unique barcode: UNIQUE, NOT NULL on copies.barcode.

Each book belongs to one genre: FOREIGN KEY, NOT NULL on books.genre_id.

Due date is always 14 days after borrow date: CHECK (due_date = borrow_date + INTERVAL '14 days') (or a generated column).

Return date cannot be before borrow date: CHECK (return_date IS NULL OR return_date >= borrow_date).

Rules that CANNOT be enforced by simple table constraints:

A member can borrow at most 5 copies at any given time: Requires counting rows across the borrowings table where return_date IS NULL. Simple CHECK constraints cannot run subqueries across multiple rows—this requires a database trigger or application-level logic.

A copy cannot be borrowed if it is currently not returned (return_date IS NULL): Cannot be enforced by a standard UNIQUE constraint (since a copy can be borrowed many times over its lifetime). In PostgreSQL, this can be enforced using a partial unique index (CREATE UNIQUE INDEX ON borrowings (copy_id) WHERE return_date IS NULL;) or a trigger.
>
>
>
6. **Write the CREATE TABLE statements** for at least the `books`, `copies`, and `borrowings` tables with full constraints.

CREATE TABLE genres (
    genre_id   SERIAL      PRIMARY KEY,
    genre_name VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE books (
    book_id          SERIAL       PRIMARY KEY,
    isbn             CHAR(13)     NOT NULL UNIQUE,
    title            VARCHAR(200) NOT NULL,
    publication_year INTEGER      CHECK (publication_year >= 1450 AND publication_year <= EXTRACT(YEAR FROM CURRENT_DATE)),
    genre_id         INTEGER      NOT NULL REFERENCES genres(genre_id) ON DELETE RESTRICT
);

CREATE TABLE copies (
    copy_id SERIAL      PRIMARY KEY,
    barcode VARCHAR(50) NOT NULL UNIQUE,
    book_id INTEGER     NOT NULL REFERENCES books(book_id) ON DELETE RESTRICT
);

CREATE TABLE members (
    member_id     SERIAL       PRIMARY KEY,
    member_number VARCHAR(20)  NOT NULL UNIQUE,
    name          VARCHAR(100) NOT NULL,
    email         VARCHAR(255) NOT NULL UNIQUE,
    phone         VARCHAR(30)
);

CREATE TABLE borrowings (
    borrowing_id SERIAL  PRIMARY KEY,
    member_id    INTEGER NOT NULL REFERENCES members(member_id) ON DELETE RESTRICT,
    copy_id      INTEGER NOT NULL REFERENCES copies(copy_id) ON DELETE RESTRICT,
    borrow_date  DATE    NOT NULL DEFAULT CURRENT_DATE,
    due_date     DATE    NOT NULL,
    return_date  DATE,
    CHECK (due_date = borrow_date + INTERVAL '14 days'),
    CHECK (return_date IS NULL OR return_date >= borrow_date)
);

-- Prevents borrowing a copy that is currently checked out
CREATE UNIQUE INDEX idx_one_active_borrowing_per_copy
ON borrowings (copy_id)
WHERE return_date IS NULL;
---

## Submission Checklist

- [ ] Task 1: Key identification answers (Part 1)
- [ ] Task 2: Business rules table with 5 rules (Part 1)
- [ ] Task 3: Integrity violation predictions with explanations (Part 1)
- [ ] Task 4: Foreign key action analysis (Part 1)
- [ ] Theory Review Questions answered (Part 2)
- [ ] SQL Practice — constraint predictions and bookstore CREATE TABLE (Part 3)
- [ ] Library System design exercise (Part 4)
