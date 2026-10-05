# Week 39 — Logical Database Design: Exercises

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

These exercises accompany the Week 39 Theory material. Refer to the theory sections indicated in brackets when you need help.

---

## Exercise 1: TrailShop Project Task — Build the Schema

**Goal:** Convert the TrailShop ER diagram (from Week 38) into a complete PostgreSQL relational schema.

### Instructions

Write `CREATE TABLE` statements for all six TrailShop tables:

1. `categories`
2. `customers`
3. `products`
4. `product_categories`
5. `orders`
6. `order_items`

### Requirements

For each table, you must:

- Choose appropriate PostgreSQL data types for every column (justify at least 3 choices in writing)
- Define primary keys (surrogate or composite as appropriate)
- Define foreign keys with explicit `ON DELETE` and `ON UPDATE` actions (justify each choice)
- Add `NOT NULL`, `UNIQUE`, `CHECK`, and `DEFAULT` constraints where appropriate
- Create tables in the correct dependency order
- Follow the naming conventions from Theory Section 8

### Deliverables

1. A single `.sql` file with all six `CREATE TABLE` statements (executable in PostgreSQL)
2. A short justification for data types, FK actions and design decisions:
   - Justification for 3 data type choices (e.g., why `NUMERIC(10,2)` for price instead of `REAL`)
   - Justification for each FK action choice (e.g., why CASCADE on `order_items.order_id`)
   - One design decision you made that wasn't specified in the requirements (e.g., whether shipping address is optional)

### Bonus Challenge

After creating the tables, insert sample data:
- At least 5 categories
- At least 8 products (across at least 3 categories)
- At least one product assigned to **two or more** categories via `product_categories`
- At least 3 customers
- At least 4 orders (across at least 2 customers)
- At least 10 order items

Verify that your constraints work by attempting at least 2 invalid inserts and showing the error messages.

> [!NOTE]
> ***Your SQL***
>
> ```sql
-- Dependency Order: categories -> customers -> products -> product_categories -> orders -> order_items

CREATE TABLE categories (
    category_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    phone VARCHAR(30),
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    name VARCHAR(150) NOT NULL,
    description TEXT,
    price NUMERIC(10, 2) NOT NULL CHECK (price >= 0),
    stock_quantity INT NOT NULL DEFAULT 0 CHECK (stock_quantity >= 0),
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE product_categories (
    product_id INT NOT NULL REFERENCES products(product_id) ON DELETE CASCADE ON UPDATE CASCADE,
    category_id INT NOT NULL REFERENCES categories(category_id) ON DELETE CASCADE ON UPDATE CASCADE,
    PRIMARY KEY (product_id, category_id)
);

CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT NOT NULL REFERENCES customers(customer_id) ON DELETE RESTRICT ON UPDATE CASCADE,
    order_date TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    status VARCHAR(30) NOT NULL DEFAULT 'new' CHECK (status IN ('new', 'confirmed', 'shipped', 'delivered', 'returned')),
    shipping_address TEXT NOT NULL
);

CREATE TABLE order_items (
    order_item_id SERIAL PRIMARY KEY,
    order_id INT NOT NULL REFERENCES orders(order_id) ON DELETE CASCADE ON UPDATE CASCADE,
    product_id INT NOT NULL REFERENCES products(product_id) ON DELETE RESTRICT ON UPDATE CASCADE,
    quantity INT NOT NULL CHECK (quantity > 0),
    unit_price NUMERIC(10, 2) NOT NULL CHECK (unit_price >= 0)
);
>
>
> ```

> [!NOTE]
> ***Your Answer***
>
Data Type Justifications:

NUMERIC(10,2) for Price & Unit Price: Chosen over floating-point types (REAL) because exact precision is mandatory for financial transactions to prevent cumulative rounding errors.
TIMESTAMPTZ for Timestamps (order_date, created_at): Stores date and time with time zone awareness, automatically normalizing to UTC. This ensures consistency for users ordering across multiple global time zones.
VARCHAR(255) for Email: Efficiently accommodates standard email length limitations while restricting invalid oversized inputs.

Foreign Key Action Justifications:

product_categories and order_items.order_id (ON DELETE CASCADE): If a product or order is deleted, its mapping rows or dependent item rows become orphaned and should be automatically cleaned up.
orders.customer_id and order_items.product_id (ON DELETE RESTRICT): Prevents deleting a customer who has existing orders or deleting a product that has been ordered, preserving historical integrity and audit trails.

Additional Design Decision:

Mandatory Shipping Address: shipping_address is defined as NOT NULL on the orders table to ensure every placed order has a valid physical destination for delivery prior to checkout completion.
>
>
>
>

## Exercise 2: Theory Review Questions

Answer each question in 2–4 sentences. Reference the relevant theory section.

1. List the seven phases of the database development lifecycle in order. Which phase is this week's focus? *(Section 1)*

> [!NOTE]
> ***Your Answer***
>
Seven phases of the DDL: (1) Planning, (2) System Definition, (3) Requirements Collection and Analysis, (4) Database Design (Conceptual, Logical, Physical), (5) Implementation, (6) Testing, (7) Operational Maintenance. This week focuses on Logical Database Design.
>
>
>
>

2. Explain the transformation rule for mapping a 1:N relationship to the relational model. Why is the foreign key placed on the "many" side? *(Section 3.2)*

> [!NOTE]
> ***Your Answer***
>
1:N mapping: The primary key from the "one" side is copied as a foreign key into the table on the "many" side. It belongs on the "many" side because a single entity on the "one" side relates to multiple entities on the "many" side, which cannot be modeled in a single foreign key column on the parent table without breaking atomicity.
>
>
>
>

3. What is a junction table? When is it needed? Give an example not from TrailShop. *(Section 3.3)*

> [!NOTE]
> ***Your Answer***
>
Junction table: A junction table is a cross-reference table used to resolve many-to-many (M:N) relationships into two 1:N relationships. It is required when two entities can relate to multiple instances of each other (e.g., student_courses linking students and courses).
>
>
>
>

4. When mapping a 1:1 relationship, how do you decide which table gets the foreign key? *(Section 3.4)*

> [!NOTE]
> ***Your Answer***
>
1:1 mapping: The foreign key should be placed in the table that participates optionally (or the dependent child table) to minimize NULL values. If both participate mandatorily, merging them into a single relation is often preferred
>
>
>
>

5. How does the mapping of a weak entity differ from a strong entity? What happens to the primary key? *(Section 3.5)*

> [!NOTE]
> ***Your Answer***
>
Weak vs. strong entity: A weak entity cannot exist independently and relies on a strong entity for its identity. Its primary key is a composite key combining its own partial key with the primary key (foreign key) of its owner strong entity.
>
>
>
>

6. Why should you never use `REAL` or `DOUBLE PRECISION` for monetary values? What should you use instead? *(Section 4.1)*

> [!NOTE]
> ***Your Answer***
>
Monetary values data type: Floating-point types (REAL, DOUBLE PRECISION) use binary approximations that cannot precisely represent certain decimal fractions, causing rounding errors. Always use exact numeric types like NUMERIC(p, s).
>
>
>
>

7. What is the difference between `TIMESTAMP` and `TIMESTAMPTZ`? Which should you prefer and why? *(Section 4.3)*
> [!NOTE]
> ***Your Answer***
>
TIMESTAMP vs. TIMESTAMPTZ: TIMESTAMP lacks time zone context, whereas TIMESTAMPTZ stores UTC time and adjusts for the client's local time zone. TIMESTAMPTZ is preferred for applications supporting global users.




8. Explain the difference between `CASCADE` and `RESTRICT` as foreign key delete actions. Give a scenario where each is appropriate. *(Section 6)*
> [!NOTE]
> ***Your Answer***
>
CASCADE vs. RESTRICT: CASCADE automatically deletes dependent child records when the parent is deleted, whereas RESTRICT blocks parent deletion if any child records depend on it. CASCADE is suited for dependent child items (e.g., order items), while RESTRICT protects vital records (e.g., customers with active orders).
>




9. What is an insertion anomaly? Give an example and explain how proper schema design prevents it. *(Section 7)*
> [!NOTE]
> ***Your Answer***
>
Insertion anomaly: An insertion anomaly occurs when certain data cannot be added without also inserting unrelated data due to structural coupling. Proper schema design prevents this by normalizing and decoupling independent entities into separate relations.
>




10. What is the difference between a surrogate key and a natural key? Give one advantage of each. *(Section 9)*
> [!NOTE]
> ***Your Answer***
>
Surrogate vs. natural key: A surrogate key is an artificial, system-generated identifier (e.g., auto-incrementing integer), while a natural key is an existing real-world attribute (e.g., passport number). Surrogate keys provide stability since they never change; natural keys enforce real-world business constraints.
>




11. Why does PostgreSQL fold unquoted identifiers to lowercase? How does `snake_case` naming help? *(Section 8)*

> [!NOTE]
> ***Your Answer***
>
Identifier folding & snake_case: PostgreSQL automatically folds unquoted identifiers to lowercase to align with SQL standards. Using snake_case naming (e.g., customer_id) enhances readability and prevents case-sensitivity syntax errors in complex queries.
>
>
>
>

12. What does `SET NULL` do as a foreign key action? When would you use it instead of `CASCADE`? *(Section 6)*
> [!NOTE]
> ***Your Answer***
>
SET NULL: SET NULL updates the foreign key column in child records to NULL when the parent record is deleted. It is used instead of CASCADE when the child record should be preserved even if the parent entity is removed (e.g., unassigning an employee when their department is deleted).
>




---

## Exercise 3: Transformation Exercise — Hotel Booking System

### Given ER Diagram

A hotel booking system has the following entities and relationships:

**Entities:**

1. **Hotel** — hotel_id (PK), name, city, star_rating, phone
2. **Room** (weak entity, owned by Hotel) — room_number (partial key), room_type, floor, price_per_night, has_balcony
3. **Guest** — guest_id (PK), first_name, last_name, email, phone, passport_number
4. **Booking** — booking_id (PK), check_in_date, check_out_date, total_amount, status
5. **Service** — service_id (PK), name, description, price (e.g., "Room Service", "Spa", "Airport Shuttle")

**Relationships:**

- Hotel (1) → Room (N): A hotel has many rooms. Each room belongs to exactly one hotel. (Identifying relationship — Room is weak.)
- Guest (1) → Booking (N): A guest can make many bookings. Each booking belongs to one guest.
- Booking (M) ↔ Room (N): A booking can include multiple rooms, and a room can appear in many bookings (over time). The junction records the specific dates.
- Booking (M) ↔ Service (N): A booking can use multiple services, and a service can be used by many bookings. The junction records the date used and quantity.

### Task

1. Write `CREATE TABLE` statements for ALL tables (including junction tables).
2. For each table:
   - Choose appropriate data types
   - Define PK, FK, NOT NULL, UNIQUE, CHECK, and DEFAULT constraints
   - Specify ON DELETE and ON UPDATE actions for all FKs
3. Create the tables in the correct dependency order.
4. Explain why Room is a weak entity and how its PK reflects this.

> [!NOTE]
> ***Your SQL***
>
> ```sql
CREATE TABLE hotels (
    hotel_id SERIAL PRIMARY KEY,
    name VARCHAR(150) NOT NULL,
    city VARCHAR(100) NOT NULL,
    star_rating INT CHECK (star_rating BETWEEN 1 AND 5),
    phone VARCHAR(30)
);

CREATE TABLE rooms (
    hotel_id INT NOT NULL REFERENCES hotels(hotel_id) ON DELETE CASCADE ON UPDATE CASCADE,
    room_number VARCHAR(20) NOT NULL,
    room_type VARCHAR(50) NOT NULL,
    floor INT NOT NULL,
    price_per_night NUMERIC(10, 2) NOT NULL CHECK (price_per_night >= 0),
    has_balcony BOOLEAN NOT NULL DEFAULT FALSE,
    PRIMARY KEY (hotel_id, room_number)
);

CREATE TABLE guests (
    guest_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    phone VARCHAR(30),
    passport_number VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE bookings (
    booking_id SERIAL PRIMARY KEY,
    guest_id INT NOT NULL REFERENCES guests(guest_id) ON DELETE RESTRICT ON UPDATE CASCADE,
    check_in_date DATE NOT NULL,
    check_out_date DATE NOT NULL,
    total_amount NUMERIC(10, 2) NOT NULL CHECK (total_amount >= 0),
    status VARCHAR(30) NOT NULL DEFAULT 'confirmed' CHECK (status IN ('confirmed', 'checked_in', 'checked_out', 'cancelled')),
    CONSTRAINT chk_dates CHECK (check_out_date > check_in_date)
);

CREATE TABLE services (
    service_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    price NUMERIC(10, 2) NOT NULL CHECK (price >= 0)
);

CREATE TABLE booking_rooms (
    booking_id INT NOT NULL REFERENCES bookings(booking_id) ON DELETE CASCADE ON UPDATE CASCADE,
    hotel_id INT NOT NULL,
    room_number VARCHAR(20) NOT NULL,
    check_in_date DATE NOT NULL,
    check_out_date DATE NOT NULL,
    PRIMARY KEY (booking_id, hotel_id, room_number),
    FOREIGN KEY (hotel_id, room_number) REFERENCES rooms(hotel_id, room_number) ON DELETE RESTRICT ON UPDATE CASCADE
);

CREATE TABLE booking_services (
    booking_id INT NOT NULL REFERENCES bookings(booking_id) ON DELETE CASCADE ON UPDATE CASCADE,
    service_id INT NOT NULL REFERENCES services(service_id) ON DELETE RESTRICT ON UPDATE CASCADE,
    date_used DATE NOT NULL,
    quantity INT NOT NULL DEFAULT 1 CHECK (quantity > 0),
    PRIMARY KEY (booking_id, service_id, date_used)
);
>
>
> ```

> [!NOTE]
> ***Your Answer***
>
Room is a weak entity because its existence depends entirely on a parent Hotel, and its local identifier (room_number) is only unique within that specific hotel (multiple hotels can share "Room 101"). Its primary key reflects this by being a composite key consisting of hotel_id (the foreign key referencing the owner hotel) and room_number (its partial key).
>
>
>
>

---

## Exercise 4: Data Type Selection

> [!NOTE]
> ***Your Answers***
> Fill in the **Your Data Type** and **Justification** columns in the table below.
>

For each column described below, choose the best PostgreSQL data type and write a brief justification (1–2 sentences). Do NOT just pick `VARCHAR` or `TEXT` for everything — think carefully about validation, storage, and query needs.

| # | Column Description | Your Data Type | Justification |
|---|---|---|---|
| 1 | Employee salary (exact, up to €999,999.99) |NUMERIC(8, 2) |Exact precision is mandatory for currency to avoid binary rounding errors, with 8 total digits and 2 decimal places. |
| 2 | Number of items in stock (never negative, max ~50,000) |SMALLINT |SMALLINT safely handles integer values up to 32,768, or standard INT for up to 50,000+, paired with a non-negative check constraint. |
| 3 | Whether a user's email is verified |BOOLEAN |Perfectly suited for binary true/false state flags with minimal storage overhead. |
| 4 | Customer's date of birth |DATE |Represents calendar dates efficiently without unnecessary time or time zone components. |
| 5 | Product description (variable length, could be several paragraphs) |TEXT |TEXT allows variable-length character storage without arbitrary string length limits. |
| 6 | Country code (always exactly 2 letters, like "FI", "US") |CHAR(2) |CHAR(2) enforces a fixed length of exactly 2 characters for standardized country codes. |
| 7 | IP address of a login attempt |INET |PostgreSQL provides dedicated network address types (INET) that validate and store IPv4/IPv6 addresses efficiently. |
| 8 | Order total (exact, up to €9,999,999.99) |NUMERIC(10, 2) |Provides exact decimal arithmetic required for financial order totals up to 10 million euros. |
| 9 | GPS latitude of a store location |NUMERIC(9, 6) |Stores precise decimal coordinates (-90 to +90) with enough decimal places for centimeter-level GPS accuracy. |
| 10 | A unique identifier for API tokens that must be globally unique across distributed systems |UUID |Universally Unique Identifiers guarantee global uniqueness across distributed systems without centralized sequence coordination. |
| 11 | Duration of a video in seconds (always a whole number) |INT |Whole numbers of seconds are cleanly represented using standard integer types. |
| 12 | Timestamp of when a record was last modified (users in multiple time zones) |TIMESTAMPTZ |Stores timestamps with time zone information, automatically normalizing to UTC for consistent global tracking. |
| 13 | A Finnish phone number like "+358 40 123 4567" |VARCHAR(30) |Phone numbers vary in length and include formatting characters like spaces and plus signs. |
| 14 | A percentage discount (0.00% to 100.00%) |NUMERIC(5, 2) |Allows exact percentage values up to 100.00 with two decimal places of precision. |
| 15 | A product's color options (e.g., a product comes in "red", "blue", "green") |VARCHAR(50) |Stores individual color option names as variable-length text strings. |

---

## Exercise 5: Constraint Design

For each business rule below, write the appropriate PostgreSQL constraint. Provide the constraint as it would appear inside a `CREATE TABLE` statement or as an `ALTER TABLE` statement.

### Part A: Single-Column Constraints

1. "A product's weight must be greater than zero (if provided)."

2. "Every customer must have an email address."

3. "Product names must be unique."

4. "An employee's hire date defaults to today if not specified."

5. "Order status can only be one of: 'new', 'confirmed', 'shipped', 'delivered', 'returned'."

> [!NOTE]
> ***Your SQL***
>
> ```sql
-- 1. Product weight greater than zero (if provided)
weight NUMERIC(10, 2) CHECK (weight IS NULL OR weight > 0)

-- 2. Every customer must have an email address
email VARCHAR(255) NOT NULL

-- 3. Product names must be unique
name VARCHAR(150) UNIQUE

-- 4. Employee hire date defaults to today if not specified
hire_date DATE NOT NULL DEFAULT CURRENT_DATE

-- 5. Order status check constraint
status VARCHAR(30) NOT NULL CHECK (status IN ('new', 'confirmed', 'shipped', 'delivered', 'returned'))
>
>
> ```

### Part B: Multi-Column Constraints

6. "A flight's arrival time must be after its departure time."

7. "In the `enrollments` table, the combination of `student_id` and `course_id` must be unique (a student can only enroll in a course once)."

8. "A discount percentage must be between 0 and 100, inclusive."

> [!NOTE]
> ***Your SQL***
>
> ```sql
-- 6. Arrival time after departure time
CONSTRAINT chk_flight_times CHECK (arrival_time > departure_time)

-- 7. Unique combination of student and course in enrollments
CONSTRAINT uk_student_course UNIQUE (student_id, course_id)

-- 8. Discount percentage between 0 and 100
CONSTRAINT chk_discount CHECK (discount_percentage BETWEEN 0 AND 100)
>
>
> ```

### Part C: Foreign Key Constraints with Actions

9. "When a department is deleted, all employees in that department should have their `department_id` set to NULL (they become unassigned)."

10. "When a customer is deleted, prevent the deletion if the customer has any orders."

11. "When an author is deleted, all their blog posts should be deleted automatically."

12. "When a course is deleted, all enrollments for that course should be removed."

> [!NOTE]
> ***Your SQL***
>
-- 9. Department deleted -> employee department_id set to NULL
department_id INT REFERENCES departments(department_id) ON DELETE SET NULL ON UPDATE CASCADE

-- 10. Customer deleted -> prevent deletion if orders exist
CONSTRAINT fk_customer FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE RESTRICT ON UPDATE CASCADE

-- 11. Author deleted -> blog posts deleted automatically
author_id INT REFERENCES authors(author_id) ON DELETE CASCADE ON UPDATE CASCADE

-- 12. Course deleted -> enrollments removed automatically
course_id INT REFERENCES courses(course_id) ON DELETE CASCADE ON UPDATE CASCADE




>
>
> ```

---

## Submission Checklist

- [x ] Exercise 1: `.sql` file with all CREATE TABLE statements + written justifications
- [x ] Exercise 2: All 12 theory review answers
- [x ] Exercise 3: Hotel booking schema with all tables and explanations
- [x ] Exercise 4: Data type selections with justifications for all 15 columns
- [x ] Exercise 5: All 12 constraints written in valid PostgreSQL syntax
