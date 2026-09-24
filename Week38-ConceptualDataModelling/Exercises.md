# Week 38 — Conceptual Data Modelling: Exercises

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

These exercises accompany the Week 38 Theory material. Refer to the theory sections indicated in brackets when you need help.

---

## Exercise 1: TrailShop Project Task — Create the ER Diagram

**Goal:** Create a complete Entity-Relationship diagram for the TrailShop database using crow's foot notation.

### Instructions

Using the entity descriptions from Theory Section 12, create an ER diagram that includes:

1. **All five entities**: Category, Product, Customer, Order, OrderItem
2. **All attributes** for each entity (as listed in Section 12.1)
3. **Primary keys** clearly marked (underline or "PK" label)
4. **Foreign keys** clearly marked (dashed underline or "FK" label)
5. **Relationships** between entities with:
   - Relationship name (verb)
   - Crow's foot notation showing cardinality and participation
6. **Identify weak entities** — mark OrderItem as a weak entity

### Requirements

- Use crow's foot notation (see Theory Section 9)
- You may use any tool: draw.io, Lucidchart, ERDPlus, dbdiagram.io, or even pen and paper (photograph and submit)
- The diagram must be readable — avoid crossing lines where possible
- Include a brief legend explaining your notation if using pen and paper

### Deliverables

- The ER diagram (image or link to online tool)
- A short written paragraph (3–5 sentences) explaining one design decision you made — for example, why OrderItem is a weak entity, or why `unit_price` is stored in OrderItem instead of being looked up from Product.

> [!NOTE]
> ***Your Answer***
>
> *(


Week 37 used a 1:N relationship where each product could belong to only one category through `products.category_id`. This is not sufficient because a product can belong to multiple categories, such as Clothing and Accessories. Therefore, Week 38 changes the relationship to M:N and uses the `ProductCategory` junction entity to connect products and categories. The `ProductCategory` entity uses a composite primary key of `product_id` and `category_id` to prevent duplicate product-category links.






https://imgur.com/a/exGCjzh

)*
>
>
>
>

---

## Exercise 2: Theory Review Questions

Answer each question in 2–4 sentences. Reference the relevant theory section.

1. Why should you create a conceptual data model before writing SQL? Give two specific reasons. *(Section 1)*

> [!NOTE]
> ***Your Answer***
>
> Avoids painful and costly restructuring later: Restructuring a database after it has already been implemented and loaded with production data is difficult, risky, and expensive.

Prevents poor structural design: Jumping straight into tables without a plan frequently leads to messy schemas—such as storing unrelated data in the wrong columns, creating bloated tables, or scattering duplicate data across tables with no clear relationships.
>
>
>
>

2. What is the difference between the conceptual level and the logical level of a data model? *(Section 2)*

> [!NOTE]
> ***Your Answer***
>
> Conceptual Level: The big-picture business view. It focuses purely on what data the business needs to track (like entities and relationships) without any technical details.

Logical Level: The table blueprint. It translates that big picture into tables, columns, and keys that fit a relational database structure (though it's still independent of specific software like PostgreSQL).
>
>
>
>

3. Explain logical data independence with an example. *(Section 3)*

> [!NOTE]
> ***Your Answer***
>
> logical data independence is the ability to change the conceptual schema (the overall structure of the data) without having to change the external schemas (the user views or applications that rely on it).

Simple Example:
Imagine you decide to split your products table into two separate tables: one for general product info and one for product details. Because of logical data independence, the warehouse team's view/app can still query the data seamlessly (for example, by using a JOIN behind the scenes) so that their screen or query keeps working without noticing any difference.
>
>
>
>

4. Explain physical data independence with an example. *(Section 3)*

> [!NOTE]
> ***Your Answer***
>
> Physical data independence is the ability to change how data is physically stored on disk without having to change the conceptual model or user views.
 Example:
If you add an index (like a search index) to the products.name column to make searches faster, or if you move the database to a faster hard drive, the database runs more efficiently. However, none of the SQL queries, tables, or user views need to change.
>
>
>
>

5. What is the difference between a strong entity and a weak entity? Give one example of each (not from TrailShop). *(Section 5)*

> [!NOTE]
> ***Your Answer***
>
> Strong Entity: An entity that can stand on its own and has its own unique identifier (primary key) without needing any other entity.

Example: Employee (uniquely identified by employee_id).

Weak Entity: An entity that cannot be uniquely identified on its own and depends on a parent (strong) entity for its existence and identity.

Example: Dependent (an employee's family member, which needs the employee_id combined with a dependent_name to be uniquely identified).
>
>
>

6. What is a composite attribute? How does it differ from a multivalued attribute? Give an example of each. *(Section 6)*

> [!NOTE]
> ***Your Answer***
>
>Composite Attribute: An attribute that can be broken down into smaller, meaningful sub-parts.

Example: An address attribute that is composed of street, city, postal_code, and country.

Multivalued Attribute: An attribute that can hold multiple separate values for a single item.

Example: A customer's phone_number attribute, where a single customer can have multiple numbers (home, mobile, and work).
>
>
>
>

7. What is a derived attribute? Why is it usually not stored in the database? *(Section 6)*

> [!NOTE]
> ***Your Answer***
>
> A derived attribute is an attribute whose value is calculated or computed from other attributes instead of being entered directly.

Example: total_price on an order item, which is calculated by multiplying quantity by unit_price.
>Saves space and keeps data accurate: Since it can be computed on the fly using a query, storing it is unnecessary.

Prevents inconsistencies: If you store a derived value and the underlying data changes later, the stored value could become outdated or incorrect unless constantly updated.
>
>
>

8. Explain the difference between a binary relationship and a unary (recursive) relationship. Give an example of each. *(Section 7)*
> [!NOTE]
> ***Your Answer***
>
> Binary Relationship: A relationship that connects two different entity types.

Example: Customer places Order (Customer and Order are two separate entities).

Unary (Recursive) Relationship: A relationship where an entity type relates to itself (connecting instances of the same entity category).

Example: Employee manages Employee (an employee can manage other employees, but both managers and staff belong to the same Employee entity)
>




9. What is the difference between an identifying relationship and a non-identifying relationship? How does this affect the child table's primary key? *(Section 7)*
> [!NOTE]
> ***Your Answer***
>
> Identifying Relationship: A relationship where the child entity is a weak entity that depends on the parent entity for its complete identity.

Effect on Primary Key: The parent's primary key is included as part of the child table's primary key (forming a composite primary key). Example: order_id is both a foreign key and part of the primary key in the OrderItem table.

Non-Identifying Relationship: A relationship where the child entity is strong and can exist independently of the parent.

Effect on Primary Key: The parent's primary key is included in the child table as a regular foreign key, but it is not part of the child's primary key. Example: category_id is a foreign key in the Product table, but the product's own product_id remains the sole primary key.
>




10. In crow's foot notation, what does the following endpoint mean: a circle followed by a crow's foot (fork)? *(Section 9)*
A circle (O) followed by a crow's foot (<), written as ──O<──, represents a zero or many (optional many) cardinality and participation constraint.
The circle (O) closest to the entity means a minimum of zero (participation is optional).

The crow's foot (<) on the outside means a maximum of many (unlimited instances).

11. Why can't a many-to-many (M:N) relationship be directly implemented in a relational database? What is the solution? *(Section 10)*

> [!NOTE]
> ***Your Answer***
>
> A relational table cell can only hold a single value, meaning you cannot store a list of multiple records inside a single row. For example, you can't put a list of many products inside an orders table row, nor can you put a list of many orders inside a products table row.
>The Solution: Use a junction table (also called an associative, bridge, or linking table) placed between the two entities. It contains foreign keys pointing back to both original tables, breaking the many-to-many relationship down into two manageable one-to-many (1:N) relationships (e.g., Order → OrderItem ← Product).
>
>
>

12. A business rule states: "Every employee must belong to exactly one department, and every department must have at least one employee." Express this using min-max notation for both sides. *(Section 8)*

> [!NOTE]
> ***Your Answer***
>
> Employee side: (1, 1) — Every employee must belong to a department (minimum 1) and can belong to at most one department (maximum 1).

Department side: (1, N) — Every department must have at least one employee (minimum 1) and can have many employees (maximum N).
>
>
>
>

---

## Exercise 3: ER Diagram Reading Exercise

### Diagram A: Library System

Study the following ER description and answer the questions below.

```
┌──────────┐                        ┌──────────┐
│  AUTHOR  │──||──────O<────────────│   BOOK   │
└──────────┘                        └─────┬────┘
                                          │
                                    ||    │
                                          │
                                    O<    │
                                          │
                                   ┌──────┴─────┐
                                   │    LOAN     │
                                   └──────┬──────┘
                                          │
                                    ||    │
                                          │
                                    O<    │
                                          │
                                   ┌──────┴──────┐
                                   │   MEMBER    │
                                   └─────────────┘
```

Relationships (in crow's foot):
- Author `──||──────O<──` Book
- Book `──||──────O<──` Loan
- Member `──||──────O<──` Loan

**Questions:**

a) Can an author exist without having written any books? Explain using the notation.
> [!NOTE]
> ***Your Answer***
>
> Yes, an author can exist without having written any books.
>Explanation using the notation:Looking at the relationship line between AUTHOR and BOOK, the symbols closest to the BOOK end are 0< (a circle 0 combined with a crow's foot <).   The 0 (circle) represents a minimum cardinality of zero, meaning participation is optional.   This indicates that an author can relate to zero or many books, meaning an author can exist in the system even if they have not written any books yet.
>
>
>

b) Can a book exist without being loaned? Explain using the notation.
> [!NOTE]
> ***Your Answer***
>
> Yes, a book can exist without being loaned.
>Explanation using the notation:Looking at the relationship line between BOOK and LOAN, the symbols located near the LOAN end are 0< (a circle followed by a crow's foot).   The circle (0) represents a minimum cardinality of zero, meaning a book can be associated with zero or many loans.   Therefore, a book can exist in the system even if it has never been checked out or loaned out.
>
>
>

c) What type of entity is Loan in this diagram? Is it a junction/associative entity? Why?


> [!NOTE]
> ***Your Answer***
>
>Type of Entity: LOAN is a weak entity (or a junction/associative entity).
>Yes, it is a junction/associative entity because it sits directly between the BOOK and MEMBER entities to resolve what is fundamentally a many-to-many relationship (since a member can borrow many books over time, and a book can be loaned to many members over time). It acts as a bridge containing the association between a specific book and a specific member.
>
>
>

d) What is the cardinality of the Author-Book relationship? Is this realistic? What might be a more accurate model?


> [!NOTE]
> ***Your Answer***
>
> The diagram shows a 1:N (one-to-many) relationship. Specifically, an author can write zero or many books (0< symbol near BOOK), but each book is written by exactly one author (|| symbol near AUTHOR).
>No, this is not realistic for a real-world library or publishing system. Many books are co-authored by two or more writers (such as academic textbooks or collaborative novels).
>A realistic model should treat the relationship between authors and books as a many-to-many (M:N) relationship, since an author can write many books and a book can have multiple authors. To implement this properly in a database, you would resolve it by introducing a junction/associative table (e.g., BookAuthor) between AUTHOR and BOOK.
>
>

e) What attributes would you add to the Loan entity?


> [!NOTE]
> ***Your Answer***
>
> Primary/Foreign Keys:

loan_id (PK) — A unique identifier for each specific loan transaction (or a composite key using book_id and member_id along with a timestamp, since the same book can be loaned multiple times).

book_id (FK) — References the specific book being borrowed.

member_id (FK) — References the member who is borrowing the book.

Transaction Details:

loan_date (or checkout_date) — The date and time when the book was checked out.

due_date — The deadline by which the book must be returned.

return_date — The actual date the book was returned (which can remain empty/null while the book is still checked out).

fine_amount — Any late fees incurred if the book is returned past the due date.
>
>
>
>

### Diagram B: School System

```
STUDENT ──O|──────O<── ENROLLMENT ──>|──||── COURSE
                                        │
                                    ||  │
                                        │
                                    O<  │
                                        │
                                   TEACHER
```

Relationships:
- Student `──O|──────O<──` Enrollment (a student may have zero or many enrollments)
- Enrollment `──||──────||──` Course (each enrollment is for exactly one course)
- Teacher `──||──────O<──` Course (each course has zero or many sections, each taught by exactly one teacher)

**Questions:**

a) Can a student exist without being enrolled in any course?


> [!NOTE]
> ***Your Answer***
>
> Yes, a student can exist without being enrolled in any course.
>Explanation using the notation:Looking at the relationship line between STUDENT and ENROLLMENT, the symbol closest to the student side is -0|, where the 0 represents a minimum cardinality of zero.
> As explicitly stated in the diagram's relationship notes, a "student may have zero or many enrollments".
> Therefore, a student record can be created and exist in the system even if they are not currently enrolled in any courses.
>
>
>

b) Can a course exist without having any enrolled students?


> [!NOTE]
> ***Your Answer***
>
>  No, a course cannot exist without having at least one enrolled student.
>Looking at the relationship line between ENROLLMENT and COURSE, the symbol near the ENROLLMENT end is >| (a crow's foot combined with a vertical bar |).
>The vertical bar | represents a minimum cardinality of one (mandatory participation), unlike the circle 0 which would represent optional participation.
>This indicates that every course must have one or many enrollments, meaning a course cannot exist in the system without at least one student enrolled.
>

c) What is the cardinality between Student and Course (through Enrollment)?


> [!NOTE]
> ***Your Answer***
>
> Cardinality: The overall relationship between Student and Course (through the Enrollment junction table) is Many-to-Many (M:N).   Breakdown:Student side: A student can have zero or many enrollments (meaning a student can take multiple courses).
>  Course side: Each individual enrollment links to exactly one course, but a course can be associated with many student enrollments.
>Since a single student can take many courses, and a single course can have many students, the ENROLLMENT table acts as the bridge that successfully breaks this M:N relationship down into manageable 1:N relationships.
>
>
>

d) Can a teacher exist without teaching any courses?


> [!NOTE]
> ***Your Answer***
>
> Yes, a teacher can exist without teaching any courses.
>  Explanation using the notation:Looking at the relationship line between TEACHER and COURSE, the symbols located near the TEACHER end are 0< (a circle followed by a crow's foot).
>  The circle (0) represents a minimum cardinality of zero, meaning participation is optional.   This indicates that a teacher can be associated with zero or many courses, allowing a teacher to exist in the system even if they are not currently assigned to teach any classes.
>
>
>
>

e) Is the Teacher-Course relationship 1:1 or 1:N? What does this imply about team teaching?


> [!NOTE]
> ***Your Answer***
>
>Relationship Type: The Teacher-Course relationship is a 1:N (one-to-many) relationship (from the perspective of a teacher). According to the diagram and notes, each course/section is taught by exactly one teacher (||), but a single teacher can teach zero or many courses (0<).
> Implication for Team Teaching: This model does not support team teaching. Because the constraint strictly limits each course or section to one teacher (||), you cannot assign multiple teachers to co-teach the same course or section in this database design.   
>
>
>
>

---

## Exercise 4: ER Diagram Creation — Gym/Fitness Center

### Scenario

FitZone is a local gym and fitness center. They need a database to manage their operations. Here are the business rules:

1. The gym has **members**. Each member has an ID, first name, last name, email, phone, date of birth, and membership start date.

2. The gym offers **membership plans** (e.g., "Basic", "Premium", "Student"). Each plan has a plan ID, name, monthly price, and description. Each member subscribes to exactly one plan. A plan can have many members.

3. The gym has **trainers** (employees who lead classes). Each trainer has an ID, first name, last name, specialization (e.g., "Yoga", "CrossFit"), and hire date.

4. The gym offers **classes** (e.g., "Morning Yoga", "HIIT Blast"). Each class has an ID, name, day of the week, start time, end time, and maximum capacity. Each class is led by exactly one trainer, but a trainer can lead many classes.

5. Members can **register** for classes. A member can register for many classes, and a class can have many registered members. The registration records the registration date.

6. The gym has **equipment** (treadmills, dumbbells, etc.). Each piece of equipment has an ID, name, type, purchase date, and status ("working", "maintenance", "retired").

7. When equipment breaks, a **maintenance request** is created. Each request has an ID, request date, description of the problem, status ("open", "in progress", "closed"), and resolution date. Each request is for exactly one piece of equipment. One piece of equipment can have many maintenance requests over time.

### Task

1. Identify all entities and their attributes (including key attributes).

> [!NOTE]
> ***Your Answer***
>
>MEMBER

member_id (PK)

first_name

last_name

email

phone

date_of_birth

membership_start_date

MEMBERSHIP_PLAN

plan_id (PK)

name

monthly_price

description

TRAINER

trainer_id (PK)

first_name

last_name

specialization

hire_date

CLASS

class_id (PK)

name

day_of_week

start_time

end_time

max_capacity

REGISTRATION (Junction Entity between Member and Class)

member_id (PK/FK)

class_id (PK/FK)

registration_date

EQUIPMENT

equipment_id (PK)

name

type

purchase_date

status

MAINTENANCE_REQUEST

request_id (PK)

request_date

description

status

resolution_date
>
>
>
>

2. Identify all relationships with their cardinality and participation constraints.

> [!NOTE]
> ***Your Answer***
>Membership Plan to Member (1:N Relationship)

Cardinality: One-to-Many. A membership plan can be associated with multiple members, but each member belongs to only one plan.

Participation Constraints:

Member side: Mandatory (each member must subscribe to exactly one plan, min 1, max 1).

Plan side: Optional/Many (a plan can have zero or many members, min 0, max N).

2. Trainer to Class (1:N Relationship)

Cardinality: One-to-Many. A trainer can lead multiple classes, but each class is taught by a single trainer.

Participation Constraints:

Class side: Mandatory (each class is led by exactly one trainer, min 1, max 1).

Trainer side: Optional/Many (a trainer can lead zero or many classes, min 0, max N).

3. Member to Class via REGISTRATION (M:N Relationship)

Cardinality: Many-to-Many. Resolved through the REGISTRATION junction table. Members can sign up for multiple classes, and classes can hold multiple members.

Participation Constraints:

Member side: Optional/Many (a member can register for zero or many classes).

Class side: Optional/Many (a class can have zero or many registered members).

4. Equipment to Maintenance Request (1:N Relationship)

Cardinality: One-to-Many. A single piece of equipment can accumulate multiple maintenance requests over time, but a specific request applies to only one item.

Participation Constraints:

Maintenance Request side: Mandatory (each request is for exactly one piece of equipment, min 1, max 1).

Equipment side: Optional/Many (a piece of equipment can have zero or many maintenance requests, min 0, max N).
> 
>
>
>
>

3. Draw a complete ER diagram using crow's foot notation.

> [!NOTE]
> ***Your Answer***
>
> *(

https://imgur.com/a/yegYq7w
)*
>
>
>
>

4. Identify any entity that might be considered a weak entity or a junction/associative entity. Justify your answer.

> [!NOTE]
> ***Your Answer***
>
> 1. Junction / Associative Entity: REGISTRATION
Entity: REGISTRATION

Justification: It acts as the bridge that resolves the Many-to-Many (M:N) relationship between MEMBER and CLASS. Because a database cannot directly implement an M:N relationship, the junction entity breaks it down into two 1:N relationships. It stores the composite primary key made of member_id and class_id, along with relationship-specific attributes like registration_date.

2. Weak Entity Considerations: MAINTENANCE_REQUEST
Entity: MAINTENANCE_REQUEST

Justification: While MAINTENANCE_REQUEST has an existence dependency on EQUIPMENT (meaning a request cannot exist without a piece of equipment to attach to), it is technically classified as a strong entity with a mandatory foreign key rather than a true weak entity. This is because it possesses its own unique primary key (request_id) rather than relying on a composite key that includes the parent entity's primary key for its identification.
>
>
>
>

5. Are there any M:N relationships? If so, what junction entity resolves them?

> [!NOTE]
> ***Your Answer***
>Yes, there is a Many-to-Many (M:N) relationship in the FitZone model.

The M:N Relationship: It exists between MEMBER and CLASS, since a member can register for many classes and a single class can have many registered members.

The Resolving Junction Entity: This relationship is resolved by the REGISTRATION junction entity, which breaks the M:N relationship down into two manageable 1:N relationships (MEMBER to REGISTRATION and CLASS to REGISTRATION).
> 
>
>
>
>
---

## Exercise 5: Find and Correct the Errors

The following ER diagram description contains **four errors**. Find each error, explain why it's wrong, and provide the correction.

### Scenario: Online Bookstore

**Entities and attributes:**

1. **Books**
   - book_id (PK)
   - title
   - author_name
   - price
   - genres (stores "Fiction, Mystery, Thriller" as a comma-separated string)

2. **Customer**
   - customer_id (PK)
   - full_name
   - address

3. **Purchase**
   - purchase_id (PK)
   - purchase_date
   - total_amount

**Relationships:**
- Books to Customer: M:N (implemented directly — no junction table)
- Customer to Purchase: 1:N (one customer, many purchases)
- Books to Purchase: no relationship defined

### Your Task

Find the four errors in this design and for each one:

a) State what the error is
> [!NOTE]
> ***Your Answer***
>
> Error 1: The genres attribute in the Books entity is a multivalued attribute storing multiple values as a single comma-separated string ("Fiction, Mystery, Thriller").

Error 2: The entity is named using the plural form (Books) instead of the required singular noun convention (BOOK).

Error 3: The relationship between Books and Customer is modeled as a direct Many-to-Many (M:N) relationship without a junction/associative table.

Error 4: There is no relationship defined between Books and Purchase.
>
>
>
>

b) Explain why it's a problem (reference the relevant theory section)

> [!NOTE]
> ***Your Answer***
>
>Error 1: Multivalued Attribute (genres)
Why it's a problem: This violates First Normal Form (1NF). According to normalization theory, 1NF requires that all attributes contain atomic (indivisible) values and that each cell in a table holds a single value. Storing multiple values as a comma-separated string destroys data atomicity, making it extremely inefficient to search, filter, index, or query books by a specific genre.

Error 2: Plural Entity Naming (Books)
Why it's a problem: This violates Database Naming Conventions and Conceptual Design Standards. In relational modeling, entity names should always be singular nouns (e.g., BOOK) because each individual row in the corresponding table represents a single, distinct instance of that entity. Plural names can lead to confusion and inconsistencies when mapping relationships and columns.

Error 3: Direct M:N Relationship Without a Junction Table
Why it's a problem: This violates Relational Database Implementation Rules. Relational Database Management Systems (RDBMS) cannot physically implement a direct Many-to-Many relationship between two tables. Trying to force an M:N relationship without an intermediate bridge table leads to structural failure or massive data redundancy.

Error 4: Missing Relationship Between Books and Purchase
Why it's a problem: This violates Business Rule Completeness and Referential Integrity. In an e-commerce context, a purchase transaction is fundamentally meaningless without knowing what items were bought. Without a relationship connecting Books to Purchase, the database tracks that a customer spent a total_amount, but it completely loses the line-item detail of which specific books were purchased.
>
>
>
>

c) Describe how to fix it

> [!NOTE]
> ***Your Answer***
>
> Error 1: Multivalued Attribute (genres)
How to fix it: Remove the genres attribute from the BOOK entity. Create a separate GENRE entity (with genre_id and genre_name) and introduce a junction table named BOOK_GENRE (containing book_id and genre_id). This normalizes the data to First Normal Form (1NF) by ensuring atomic values and allows a book to have multiple genres properly.

Error 2: Plural Entity Naming (Books)
How to fix it: Rename the entity from the plural form (Books) to the standard singular noun convention: BOOK. Every table name should represent a single instance of that entity.

Error 3: Direct M:N Relationship Without a Junction Table
How to fix it: Remove the direct Many-to-Many link between BOOK and CUSTOMER. Instead, use the transaction/purchase structure (or a dedicated junction table) to bridge them correctly into One-to-Many (1:N) relationships, ensuring the RDBMS can physically manage the association.

Error 4: Missing Relationship Between Books and Purchase
How to fix it: Establish an explicit relationship between BOOK and PURCHASE by creating a junction table (commonly called PURCHASE_ITEM or ORDER_LINE). This table should include foreign keys for book_id and purchase_id, along with transaction-specific attributes like quantity and price_at_purchase, successfully capturing what was bought in each order.
>
>
>
>

**Hints:** Think about multivalued attributes, M:N relationships, entity naming conventions, and missing relationships.

---

## Submission Checklist

- [x ] Exercise 1: ER diagram + design decision paragraph
- [x ] Exercise 2: All 12 theory review answers
- [x ] Exercise 3: All questions answered for both Diagram A and Diagram B
- [x ] Exercise 4: Entity list, relationship list, ER diagram, and justifications
- [x ] Exercise 5: Four errors identified with explanations and corrections
