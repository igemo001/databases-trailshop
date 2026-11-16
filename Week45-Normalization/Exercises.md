# Week 45 — Normalization: Exercises

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

---

## 1. TrailShop Project Task

### Task 1.1: Analyze a Denormalized Table

Consider this denormalized table that TrailShop's old system used:

```
supplier_products(
    supplier_id, supplier_name, supplier_email, supplier_city,
    product_id, product_name, category_id, category_name,
    supply_price, lead_time_days
)
```

Sample data:

| supplier_id | supplier_name | supplier_email | supplier_city | product_id | product_name | category_id | category_name | supply_price | lead_time_days |
|---|---|---|---|---|---|---|---|---|---|
| 1 | NordGear Oy | info@nordgear.fi | Helsinki | 101 | Trail Runner X | 10 | Footwear | 45.00 | 7 |
| 1 | NordGear Oy | info@nordgear.fi | Helsinki | 102 | Summit Boot | 10 | Footwear | 62.00 | 7 |
| 2 | OutdoorPro AB | sales@outdoorpro.se | Stockholm | 201 | Rain Jacket Pro | 30 | Clothing | 55.00 | 14 |
| 2 | OutdoorPro AB | sales@outdoorpro.se | Stockholm | 101 | Trail Runner X | 10 | Footwear | 43.00 | 10 |

**Tasks:**

1. Identify the primary key of this table.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- The primary key is a composite key:
PRIMARY KEY (supplier_id, product_id)
>
>
> ```
2. List ALL functional dependencies.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Partial Dependencies (depend on part of the PK):
supplier_id -> supplier_name, supplier_email, supplier_city
product_id -> product_name, category_id, category_name

-- Transitive Dependencies:
category_id -> category_name

-- Full Dependencies (depend on the entire PK):
(supplier_id, product_id) -> supply_price, lead_time_days
>
>
> ```
3. Identify any insertion, update, and deletion anomalies (give specific examples).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> Insertion Anomaly: You cannot add a new supplier to the database unless they already supply a product (because product_id is part of the Primary Key and cannot be NULL).
Update Anomaly: If 'NordGear Oy' moves their office from Helsinki to Tampere, you must update the city in every single row where they supply a product. Missing one row creates inconsistent data.
Deletion Anomaly: If you stop selling the 'Rain Jacket Pro' (Product 201) and delete its row, you lose all information about 'OutdoorPro AB' if that was the only product they supplied.
>
>
> ```
4. Normalize this table to 3NF. Show each step (1NF → 2NF → 3NF) with the resulting tables.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Step 1 (1NF): The original table is in 1NF as it has atomic values and a defined PK.
-- Step 2 (2NF): Remove partial dependencies into separate tables.
suppliers(supplier_id, supplier_name, supplier_email, supplier_city)
products_2NF(product_id, product_name, category_id, category_name)
supplier_pricing(supplier_id, product_id, supply_price, lead_time_days)

-- Step 3 (3NF): Remove transitive dependencies (category_id -> category_name in products).
suppliers(supplier_id [PK], supplier_name, supplier_email, supplier_city)
categories(category_id [PK], category_name)
products(product_id [PK], product_name, category_id [FK])
supplier_pricing(supplier_id [FK], product_id [FK], supply_price, lead_time_days) 
-- PK for supplier_pricing is (supplier_id, product_id)
>
>
> ```
5. Draw a dependency diagram for the original table showing partial and transitive dependencies.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> (supplier_id, product_id) ────────────> supply_price, lead_time_days
      │             │
      │             └─> product_name, category_id ──> category_name
      │
      └─> supplier_name, supplier_email, supplier_city
>
>
> ```

### Task 1.2: Review Your Existing TrailShop Schema

1. List all functional dependencies in your current TrailShop schema (all tables).
> [!NOTE]
> ***Your SQL***
>
> ```sql
> categories: category_id -> category_name
products: product_id -> product_name, category_id, price
orders: order_id -> customer_id, order_date, total_amount
order_items: (order_id, product_id) -> quantity, unit_price
customers: customer_id -> first_name, last_name, email
>
>
> ```
2. For each table, confirm it is in 3NF by verifying:
> [!NOTE]
> ***Your SQL***
>
> ```sql
> All tables meet 3NF criteria:
- Single-column surrogate keys (like product_id) automatically satisfy 2NF as there can be no partial dependencies.
- The composite key in order_items (order_id, product_id) determines quantity and unit_price entirely.
- No non-key attributes determine other non-key attributes (no transitive dependencies).
>
>
> ```
   - No partial dependencies (check tables with composite keys or surrogate keys)
   - No transitive dependencies
3. Explain why `unit_price` in `order_items` is NOT a normalization violation.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> unit\_price is not a normalization violation because it stores the price of the product at the time the order was placed. Product prices may change in the future, but past orders must keep their original sale price. Therefore, unit\_price represents historical transaction data and is correctly stored in order\_items.
>
>
> ```

### Task 1.3: Add a Suppliers Table (Optional Extension)

If TrailShop wanted to track suppliers:

1. Design a normalized schema that includes suppliers. Consider:
   - Each product can have multiple suppliers
   - Each supplier offers different prices and lead times
   - Each supplier has contact info (name, email, city)
2. Write the `CREATE TABLE` statements.
> [!NOTE]
> ***Your SQL***
>
> ```sql
> CREATE TABLE suppliers (
    supplier_id SERIAL PRIMARY KEY,
    supplier_name VARCHAR(100) NOT NULL,
    supplier_email VARCHAR(100) NOT NULL,
    supplier_city VARCHAR(100) NOT NULL
);

CREATE TABLE product_suppliers (
    supplier_id INT REFERENCES suppliers(supplier_id) ON DELETE CASCADE,
    product_id INT REFERENCES products(product_id) ON DELETE CASCADE,
    supply_price NUMERIC(10,2) NOT NULL,
    lead_time_days INT NOT NULL,
    PRIMARY KEY (supplier_id, product_id)
);
>
>
> ```
3. Verify your design is in 3NF.

> [!NOTE]
> ***Your Answer***
>The design is in 3NF. The suppliers table has a single-column primary key (supplier_id), and all non-key attributes depend only on it. The product_suppliers table uses the composite key (supplier_id, product_id), and both supply_price and lead_time_days depend on the whole key. There are no partial or transitive dependencies, so the design satisfies 3NF
>
>

---

## 2. Theory Review Questions

Answer in your own words (2–4 sentences each):

1. What is normalization, and what problem does it solve?

> [!NOTE]
> ***Your Answer***
>Normalization is the process of organizing a database into well\-structured tables to reduce data redundancy and improve data integrity.

It solves problems such as duplicate data, update anomalies, insertion anomalies, and deletion anomalies. By storing each piece of information only once and linking tables with keys, normalization makes the database more consistent and easier to maintain.
> 
>
>
>
>
>


2. Explain the difference between an insertion anomaly and a deletion anomaly, using an example.

> [!NOTE]
> ***Your Answer***
>
> An insertion anomaly occurs when you cannot add information to a table without also adding unrelated data. For example, in the supplier\_products table, you cannot add a new supplier unless they already supply a product because product\_id is part of the primary key.

A deletion anomaly occurs when deleting a row unintentionally removes important information. For example, if the only product supplied by a supplier is removed, deleting that row also deletes all information about the supplier.


> 
>
>
>
>
3. What is a functional dependency? Give an example from a university context (students, courses, professors).

> [!NOTE]
> ***Your Answer***
>
> A functional dependency is a relationship where one attribute uniquely determines another attribute. In other words, if you know the value of one attribute, you can determine the value of another.

example:

student\_id → student\_name, major

This means each student\_id uniquely identifies a student's name and major. For example, if student\_id = 1001, it will always correspond to the same student and major.


> 
>
>
>
>
4. What is the difference between a partial dependency and a transitive dependency?

> [!NOTE]
> ***Your Answer***
>
> A partial dependency occurs when a non\-key attribute depends on only part of a composite primary key. For example, if (student\_id, course\_id) is the primary key, and student\_name depends only on student\_id, then student\_name has a partial dependency.

A transitive dependency occurs when a non\-key attribute depends on another non\-key attribute. For example:

student\_id → department\_id

department\_id → department\_name

Here, department\_name is transitively dependent on student\_id through department\_id.


>
>
>
>
>


5. State the "informal rule" for 3NF. Explain what each part means.

> [!NOTE]
> ***Your Answer***
>The informal rule for 3NF is:

"Every non\-key attribute must depend on the key, the whole key, and nothing but the key."

- The key: Every non\-key attribute must depend on a primary key.
- The whole key: Non\-key attributes must depend on the entire key, not just part of a composite key (no partial dependencies).
- Nothing but the key: Non\-key attributes must not depend on other non\-key attributes (no transitive dependencies).


>
>
>
>
>
6. Why can partial dependencies only occur in tables with composite primary keys?

> [!NOTE]
> ***Your Answer***
>
> Partial dependencies can only occur in tables with composite primary keys because there must be more than one key attribute for a non\-key attribute to depend on only part of the key.

For example, if (student\_id, course\_id) is the primary key and student\_name depends only on student\_id, then student\_name has a partial dependency. With a single\-column primary key, there is no "part" of the key to depend on, so partial dependencies cannot occur.


>
>
>
>
>


7. Explain Armstrong's transitivity axiom with a concrete example.

> [!NOTE]
> ***Your Answer***
>
> Armstrong's transitivity axiom states:

If A → B and B → C, then A → C.

Example in a university database:

student\_id → professor\_id

professor\_id → professor\_name

Using transitivity:

student\_id → professor\_name

This means that if a student ID determines a professor ID, and the professor ID determines the professor's name, then the student ID indirectly determines the professor's name.


> 
>
>
>
>
8. What is BCNF, and how does it differ from 3NF? In what rare case can a table be in 3NF but not BCNF?

> [!NOTE]
> ***Your Answer***
>
> BCNF (Boyce\-Codd Normal Form) is a stricter version of 3NF. A table is in BCNF if every determinant is a candidate key.

The difference is that 3NF allows some dependencies where a non\-key attribute depends on a candidate key indirectly, while BCNF does not. Therefore, every table that is in BCNF is also in 3NF, but not every 3NF table is in BCNF.

A rare case where a table is in 3NF but not BCNF occurs when a table has multiple candidate keys and one candidate key determines another candidate key. In this situation, the table satisfies 3NF but violates BCNF because the determinant is not a superkey.


> 9. Give two legitimate reasons to denormalize a database. 
>
1. Performance improvement: In systems with large amounts of data and frequent queries, denormalization can reduce the number of table joins needed, making queries run faster.
 2. Simplified reporting: Storing commonly used data together can make reports and analytics easier to generate without complex joins across multiple tables.
>
>
>



11. What is the closure of an attribute set, and how would you use it to determine if an attribute set is a candidate key?

> [!NOTE]
> ***Your Answer***
>
>The closure of an attribute set is the set of all attributes that can be determined from that attribute set using the functional dependencies in a database.

To determine whether an attribute set is a candidate key, compute its closure. If the closure contains all attributes in the table, the attribute set is a superkey. If no subset of that attribute set can also determine all attributes, it is a candidate key.

For example, if:

student\_id → student\_name, major

then the closure of \{student\_id\} is:

\{student\_id, student\_name, major\}

If these are all the attributes in the table, then student\_id is a candidate key.


>
>
>
>
>


> [!NOTE]
> ***Your SQL***
>
> ```sql
> loan_id -> member_id, isbn, loan_date, return_date
member_id -> member_name
isbn -> book_title
>
>table b
>course_id -> course_name, instructor_id
instructor_id -> instructor_name
room_id -> building
(room_id, time_slot) -> course_id   -- A room can only host one course at a given time
(instructor_id, time_slot) -> course_id -- An instructor can only teach one course at a given time
>table c
>emp_id -> emp_name, dept_id
dept_id -> dept_name, manager_id
(emp_id, project_id) -> hours
>
> ```

---

## 3. FD Identification Exercise

For each table below, examine the sample data and the business rules, then list all functional dependencies.

### Table A: library_loans

**Business rules**: Each book has one ISBN and one title. Each member has one name. A member can borrow the same book multiple times (on different dates).

| loan_id | member_id | member_name | isbn | book_title | loan_date | return_date |
|---|---|---|---|---|---|---|
| 1 | M01 | Sari Niemi | 978-1-234 | SQL Basics | 2025-01-10 | 2025-01-24 |
| 2 | M02 | Timo Korhonen | 978-1-234 | SQL Basics | 2025-01-15 | 2025-01-29 |
| 3 | M01 | Sari Niemi | 978-2-567 | Data Design | 2025-02-01 | NULL |

### Table B: course_schedule

**Business rules**: Each room is in one building. Each course has one instructor. An instructor can teach multiple courses. A course meets in one room at a specific time slot.

| course_id | course_name | instructor_id | instructor_name | room_id | building | time_slot |
|---|---|---|---|---|---|---|
| CS101 | Intro to DB | I01 | Dr. Mäkelä | R201 | Main Hall | Mon 10-12 |
| CS102 | Algorithms | I02 | Dr. Järvinen | R105 | Tech Wing | Tue 14-16 |
| CS101 | Intro to DB | I01 | Dr. Mäkelä | R201 | Main Hall | Wed 10-12 |

### Table C: employee_projects

**Business rules**: Each employee belongs to one department. Each department has one manager. An employee can work on multiple projects with different hours.

| emp_id | emp_name | dept_id | dept_name | manager_id | project_id | hours |
|---|---|---|---|---|---|---|
| E1 | Liisa | D10 | Engineering | E5 | P1 | 20 |
| E1 | Liisa | D10 | Engineering | E5 | P2 | 15 |
| E2 | Pekka | D20 | Marketing | E6 | P1 | 30 |

---

## 4. Normalization Exercise

Normalize each table to 3NF. Show your work for each step.

### Table 1: event_registrations

| event_id | event_name | event_date | venue_id | venue_name | venue_city | attendee_id | attendee_name | ticket_type | ticket_price |
|---|---|---|---|---|---|---|---|---|---|
| E1 | Trail Run 2025 | 2025-06-15 | V1 | City Park Arena | Helsinki | A1 | Matti Aho | VIP | 75.00 |
| E1 | Trail Run 2025 | 2025-06-15 | V1 | City Park Arena | Helsinki | A2 | Sara Lund | Standard | 35.00 |
| E2 | Mountain Fest | 2025-07-20 | V2 | Summit Center | Tampere | A1 | Matti Aho | Standard | 40.00 |

**Business rules:**
- Each event has one name, date, and venue.
- Each venue has one name and city.
- Each attendee has one name.
- Ticket price depends on the event and ticket type combination.

**Steps:**
1. Identify the PK
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
> (event_id, attendee_id)
>
>
> ```
2. List all FDs
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query here
>event_id -> event_name, event_date, venue_id
venue_id -> venue_name, venue_city
attendee_id -> attendee_name
(event_id, ticket_type) -> ticket_price
(event_id, attendee_id) -> ticket_type
>
> ```
3. Check 1NF, 2NF, 3NF
4. Decompose as needed
5. Write final CREATE TABLE statements
> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your query hereCREATE TABLE venues (
    venue_id VARCHAR(10) PRIMARY KEY,
    venue_name VARCHAR(100),
    venue_city VARCHAR(50)
);
CREATE TABLE events (
    event_id VARCHAR(10) PRIMARY KEY,
    event_name VARCHAR(100),
    event_date DATE,
    venue_id VARCHAR(10) REFERENCES venues(venue_id)
);
CREATE TABLE attendees (
    attendee_id VARCHAR(10) PRIMARY KEY,
    attendee_name VARCHAR(100)
);
CREATE TABLE event_tickets (
    event_id VARCHAR(10) REFERENCES events(event_id),
    ticket_type VARCHAR(50),
    ticket_price NUMERIC(8,2),
    PRIMARY KEY (event_id, ticket_type)
);
CREATE TABLE registrations (
    event_id VARCHAR(10),
    attendee_id VARCHAR(10) REFERENCES attendees(attendee_id),
    ticket_type VARCHAR(50),
    PRIMARY KEY (event_id, attendee_id),
    FOREIGN KEY (event_id, ticket_type) REFERENCES event_tickets(event_id, ticket_type)
);
>
>
> ```

### Table 2: store_inventory

| store_id | store_name | store_city | product_id | product_name | category | quantity | reorder_level | supplier_name |
|---|---|---|---|---|---|---|---|---|
| S1 | TrailShop Helsinki | Helsinki | 101 | Trail Runner X | Footwear | 50 | 10 | NordGear Oy |
| S1 | TrailShop Helsinki | Helsinki | 201 | Rain Jacket Pro | Clothing | 20 | 5 | OutdoorPro AB |
| S2 | TrailShop Tampere | Tampere | 101 | Trail Runner X | Footwear | 30 | 8 | NordGear Oy |

**Business rules:**
- Each store has one name and city.
- Each product has one name, category, and supplier.
- Quantity and reorder_level depend on the specific store-product combination.

**Steps:** Same as above.

---

## 5. True/False

For each statement, write TRUE or FALSE and briefly explain why.

1. A table with a single-column primary key can violate 2NF.
2. If a table is in 3NF, it is automatically in 2NF.
3. Normalization always improves query performance.
4. A functional dependency X → Y means that Y uniquely identifies X.
5. Storing a customer's current address in the orders table is always a normalization violation.
6. BCNF is stricter than 3NF — every table in BCNF is also in 3NF.
7. Armstrong's transitivity axiom states: if X → Y and Y → Z, then X → Z.
8. Denormalization should be done before normalization to save time.

ANSWERS
FALSE. 2NF specifically eliminates partial dependencies. A table with a single-column primary key cannot have a partial dependency, so if it's in 1NF, it's automatically in 2NF.

TRUE. Normalization is sequential. The definition of 3NF requires that the relation is already in 2NF.

FALSE. Normalization eliminates redundancy but often scatters data across many tables, requiring computationally expensive JOINs that can decrease read performance.

FALSE. A functional dependency X → Y means X uniquely identifies Y. (If you know X, you know Y).

FALSE. Storing a customer's address in the orders table preserves the historical fact of where the order was actually shipped at that moment in time, which is usually a strict business requirement, not an anomaly.

TRUE. BCNF eliminates all dependencies on non-candidate keys, closing a loophole in 3NF regarding overlapping composite keys.

TRUE. This is the exact definition of Armstrong's transitivity axiom.

FALSE. You must normalize first to create a mathematically sound, anomaly-free logical design. Denormalization is applied afterward only when specific query performance metrics prove it is necessary.
