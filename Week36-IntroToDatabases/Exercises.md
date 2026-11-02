# Week 36 — Exercises & Project Task

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

These exercises accompany the Week 36 Theory material. Complete all sections.

---

## Part 1: TrailShop Project Task

### Task 1: Set Up PostgreSQL

Goal: install a working PostgreSQL server on your computer and confirm you can start `psql` (or an equivalent client).

---

### Local installation

**Step 1 — Download the installer**

1. Open [https://www.postgresql.org/download/windows/](https://www.postgresql.org/download/windows/).
2. Click **Download the installer** (EDB installer is fine).
3. Choose a recent stable version (e.g. 16 or 17) for **Windows x86-64**.
4. Save the `.exe` file and run it.

_(macOS / Linux: start from [https://www.postgresql.org/download/](https://www.postgresql.org/download/) and follow the OS-specific guide.)_

**Step 2 — Run the setup wizard**

Work through the wizard. Suggested choices for this course:

1. **Installation directory** — leave the default.
2. **Select components** — keep at least **PostgreSQL Server**, **pgAdmin 4**, **Command Line Tools**, and **Stack Builder** (Stack Builder can be skipped later).
3. **Data directory** — leave the default.
4. **Password** — set a password for the superuser account `postgres`.
   **Write it down.** You will need it every time you connect.
5. **Port** — leave **5432** (default) unless that port is already in use.
6. **Locale** — leave the default.
7. Finish the install. You can decline launching Stack Builder if prompted.

**Step 3 — Confirm the service is running (Windows)**

1. Press `Win`, type **Services**, open **Services**.
2. Find a service named like `postgresql-x64-16` (version number may differ).
3. Status should be **Running**. If not, right-click → **Start**.

**Step 4 — Open a terminal where** `psql` **is available**

On Windows, the easiest reliable options are:

- **Option 4a:** Start Menu → **SQL Shell (psql)** (installed with PostgreSQL), **or**
- **Option 4b:** Open **PowerShell** or **Command Prompt**.

If `psql` is not found in PowerShell, either use **SQL Shell (psql)** or add the PostgreSQL `bin` folder to your PATH, for example:

```
C:\Program Files\PostgreSQL\16\bin
```

(Adjust `16` to match your installed version.)

**Step 5 — Verify the installation**

In PowerShell / Command Prompt, run:

```
psql --version
```

You should see something like `psql (PostgreSQL) 16.x`.

If you used **SQL Shell (psql)** instead, you can skip `--version` and go straight to connecting in Task 2 — successful connection also proves the install works.

**Step 6 — Capture evidence for submission**

Take a screenshot of `psql --version` output (or of a successful `psql` connection prompt).

---

### Task 2: Create the TrailShop Database

Goal: create an empty database named `trailshop` and confirm you are connected to it.

Complete these steps after Task 1 succeeds.

**Step 1 — Connect to the PostgreSQL server as** `postgres`

**If you use SQL Shell (psql) on Windows**, press Enter to accept defaults for Server, Database, Port, and Username (`postgres`), then type the password you set during install when prompted.

**If you use PowerShell / Command Prompt**, run:

```
psql -U postgres -h localhost -p 5432
```

Enter the `postgres` password when asked.

You should see a prompt similar to:

```
postgres=#
```

That means you are connected to the default `postgres` maintenance database — which is normal before creating `trailshop`.

**Step 2 — List existing databases (optional but useful)**

At the `postgres=#` prompt, run:

```
\l
```

Scan the list. If `trailshop` already exists from an earlier attempt, skip Step 3 and go to Step 4.

**Step 3 — Create the database**

Still at the `postgres=#` prompt, run exactly:

```sql
CREATE DATABASE trailshop;
```

Expected result:

```
CREATE DATABASE
```

If you see `ERROR: database "trailshop" already exists`, the database is already there — continue to Step 4.

**Step 4 — Connect to** `trailshop`

In `psql`, run:

```
\c trailshop
```

Expected result (wording may vary slightly):

```
You are now connected to database "trailshop" as user "postgres".
```

**Step 5 — Confirm the prompt and that the database is empty**

1. Check that your prompt shows `trailshop`, for example:

```
trailshop=#
```

1. List tables:

```
\dt
```

You should see **no tables** (or a message that no relations were found). That is expected in Week 36 — tables come later.

1. Confirm the current database name with SQL:

```sql
SELECT current_database();
```

Expected: one row with `trailshop`.

**Step 6 — Quit psql (when finished)**

```
\q
```

**Step 7 — Capture evidence for submission**

Take a screenshot showing:

- successful `\c trailshop` (or the `trailshop=#` prompt), **and**
- `\dt` with an empty result / “Did not find any relations”

---

### Troubleshooting (Tasks 1–2)

| Problem                                | What to try                                                                                                                  |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `psql` is not recognized               | Use **SQL Shell (psql)**, or add PostgreSQL’s `bin` folder to PATH and open a **new** terminal                               |
| Password authentication failed         | Use the password set for user `postgres` during install; check Caps Lock                                                     |
| Connection refused / could not connect | Confirm the PostgreSQL Windows service is **Running**; confirm port **5432**                                                 |
| Port already in use                    | Either stop the other service using 5432, or reinstall/reconfigure PostgreSQL to another port and always pass `-p` that port |
| Permission denied to create database   | Connect as `postgres` (superuser), not as a limited role                                                                     |

---

### Task 3: Reflection Worksheet

Answer the following in your own words (write 2–3 sentences per point):

1. List **3 specific problems** TrailShop would face if they kept using spreadsheets as their product catalog grows to 5,000+ items with 10 staff members.

> [!NOTE]
>Data becomes inconsistent across multiple files  
With many staff updating different spreadsheets, product prices, stock levels, and descriptions will quickly become mismatched. One file may show a product in stock while another shows it sold out, causing wrong orders and customer complaints.

Frequent overwriting and lost changes  
Spreadsheets don’t handle multiple editors well. If two staff open the same file and save their changes, one person’s work can overwrite the other’s. As the catalog grows, this becomes a daily problem and leads to missing or corrupted data.

Slow, manual, and error‑prone reporting  
Pulling reports from several large spreadsheets becomes extremely time‑consuming. Staff must copy, merge, and clean data manually, which increases mistakes. Simple tasks like checking low‑stock items or total inventory value take far longer than they should.
>
>Switching TrailShop to a database gives three major benefits that directly solve their spreadsheet problems. First, a database provides one centralized place for all product information, so every staff member sees the same prices, stock levels, and descriptions, completely removing the inconsistency that happens when multiple spreadsheets exist. Second, databases handle many users at the same time through proper locking and transactions, which prevents the overwriting and lost changes that occur when several people edit the same spreadsheet. Third, reporting becomes fast and reliable because SQL can instantly filter, sort, and join large amounts of data, eliminating the slow, error‑prone manual merging that spreadsheets require when the catalog grows to thousands of items.

2. List **3 benefits** of switching to a database system, explaining how each one solves a problem from your list above.

> [!NOTE]
> 
> 1. Reduced data redundancy
A database stores data in a structured, centralized way, so the same data is not duplicated in multiple files.

2. Better data consistency and integrity
Rules like primary keys, foreign keys, and constraints ensure the data stays accurate and reliable.

3. Improved data sharing and concurrency
Multiple users can safely access and update the data at the same time without conflicts.

3. Explain the three-schema architecture in your own words. Why is the separation into three levels useful?

> [!NOTE]
> ***Your Answer***
>
>The three‑schema architecture is a way of organizing a database into three separate levels, each with a different purpose:

1. External Level (User View)
This is what individual users or applications see.
Each user can have a different view of the same database — for example, a doctor sees patient details, while an accountant sees billing information.

2. Conceptual Level (Logical Structure)
This level describes the overall logical design of the database:
tables, relationships, constraints, keys, and rules.
It is the unified view of the entire database.

3. Internal Level (Physical Storage)
This level describes how data is actually stored on disk:
indexes, file organization, data blocks, and physical structures.
The three‑schema architecture is useful because it gives the database data independence. Changes in one level do not force changes in the others. You can modify the physical storage (internal level) to improve performance without affecting the logical design, and you can change the logical design without breaking user views. This separation makes databases easier to maintain, safer to update, and more flexible as applications grow.
---

## Part 2: Theory Review Questions

Answer each question in 2–4 sentences. Reference the Theory material sections as needed.

### Short-Answer Questions

**Q1.** What is the difference between data and information? Give a concrete example using TrailShop data.
_(See Section 1 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
>Data is raw, unprocessed facts.
Information is data that has been organized or interpreted so it becomes meaningful.

For TrailShop:
“5000, Hiking Boots, 89.90, 12” is data — just numbers and labels.
But “Hiking Boots are low stock with only 12 units left” is information — it tells TrailShop something useful for decision‑making.

**Q2.** List and explain three disadvantages of file-based data management systems. For each, describe how it would affect TrailShop specifically.
_(See Section 2 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> File‑based data management causes serious problems because each file becomes its own isolated copy of the truth. The first disadvantage is data redundancy and inconsistency: when TrailShop keeps product details in multiple spreadsheets, staff inevitably update different files at different times. As the catalog grows, prices, stock levels, and product descriptions drift apart, so customers see outdated prices online while the warehouse sees different stock numbers. A second disadvantage is poor concurrency control. Spreadsheets cannot safely handle many users editing at once, so with ten staff members, TrailShop would constantly face overwritten changes, corrupted files, and lost updates whenever two people open and save the same sheet. The third disadvantage is data isolation, meaning information is scattered across separate files with different formats. For TrailShop, answering simple questions like “Which products are low stock?” or “What sold last month?” requires manually merging spreadsheets, writing ad‑hoc scripts, and dealing with mismatched product IDs, a slow, error‑prone process that becomes unmanageable as the business grows.

**Q3.** What is a DBMS? List four of its core functions.
_(See Sections 3 and 4 of this week's Theory material.)_

> [!NOTE]
> A DBMS (Database Management System) is software that stores, organizes, and manages data in a database. It lets users create, read, update, and delete data easily while keeping the data safe, consistent, and accessible.

- A DBMS first provides data definition, which means it allows you to create and define the structure of the database — tables, columns, data types, constraints, and relationships. This is done using DDL commands like CREATE and ALTER.

Next, it performs data manipulation, which means inserting, updating, deleting, and retrieving data stored in the database. These actions are done using DML commands such as INSERT, UPDATE, DELETE, and SELECT.

A DBMS also maintains a data dictionary, which is a special set of tables that stores metadata about the database itself. It keeps information about table names, column names, data types, constraints, indexes, and user permissions. This makes the database self‑describing.

Finally, a DBMS handles concurrency control, ensuring that multiple users can access and modify data at the same time without causing conflicts or losing updates. It uses techniques like locking and MVCC to keep data consistent even when many people are working simultaneously.

>
> _(Write your answer here.)_

**Q4.** Explain program-data independence with a concrete example. Why is it important?
_(See Section 5.2 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
> Program–data independence means that changes to how data is stored do not require changes to the application programs that use that data.
> TrailShop has a products table. Suppose the DBA adds an index, moves the table to a new disk, or changes the physical file format. Even after these changes, TrailShop’s application can still run.
> Program–data independence is important because it makes databases easy to maintain and safe to evolve. DBAs can optimize storage, reorganize files, or improve performance without breaking applications. Developers don’t need to rewrite code every time the database structure changes. This reduces bugs, lowers maintenance cost, and keeps systems flexible as TrailShop grows.

**Q5.** What is metadata? Give two examples of metadata for a `products` table.
_(See Section 8 of this week's Theory material.)_

> [!NOTE]
>Metadata is data about data, it describes the structure, rules, and properties of the actual data stored in a table.

Two examples for a products table
The data type of each column, such as price DECIMAL(8,2) or name VARCHAR(100).

The constraints, such as PRIMARY KEY (product_id) or NOT NULL on name.
> _(Write your answer here.)_

**Q6.** What is the three-schema architecture? Name and briefly describe each level.
_(See Section 3.3 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
>1. External Level (User View)
This is what individual users or applications see.
Each user can have a different view of the same database — for example, a doctor sees patient details, while an accountant sees billing information.

2. Conceptual Level (Logical Structure)
This level describes the overall logical design of the database:
tables, relationships, constraints, keys, and rules.
It is the unified view of the entire database.

3. Internal Level (Physical Storage)
This level describes how data is actually stored on disk:
indexes, file organization, data blocks, and physical structures.

**Q7.** Explain the difference between logical data independence and physical data independence.
_(See Section 3.4 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
>Logical data independence means you can change the logical structure of the database without affecting the application programs. Logical structure refers to tables, columns, relationships, and constraints. For example, if you add a new column to a table, existing queries that do not use that column continue to work normally. This independence makes the database easier to modify and evolve over time.

Physical data independence means you can change the physical storage of the database without affecting the logical structure or the application programs. Physical storage includes file organization, indexes, data blocks, and disk placement. For example, adding an index or moving data to a faster disk does not require any changes to the SQL queries. This independence allows DBAs to optimize performance without disturbing developers.

**Q8.** What is a transaction? Why is atomicity important? Give a TrailShop example.
_(See Section 5.5 of this week's Theory material.)_

> [!NOTE]
> ***Your Answer***
>
>A transaction is a group of one or more database operations that must be treated as a single unit of work. Atomicity is important because it guarantees that either all the steps succeed or none of them do, preventing partial updates that could corrupt data.

For TrailShop, imagine a customer places an order: the system must insert the order, insert the order_items, and reduce the stock. If atomicity didn’t exist and the stock update failed after the order was created, TrailShop would end up with an order in the system but no stock reduction, causing inaccurate inventory and possible overselling. Atomicity ensures the whole operation is rolled back, keeping the data correct.
### True/False

For each statement, write **True** or **False** and correct any false statements.

1. A DBMS stores only data, not information about the data's structure.
2. In a file-based system, changing the format of a data file requires updating every program that reads it.
3. Data redundancy means the same data is stored in multiple places.
4. PostgreSQL is a commercial, closed-source database system.
5. The conceptual level of the three-schema architecture describes how data is physically stored on disk.

> [!NOTE]
> 1. F
> 2. T
> 3. T
> 4. F
> 5. F
>
> _(Write True/False and corrections for all five statements above.)_

### Matching Exercise

Match each term (1–10) with its definition (A–J).

| #   | Term              |
| --- | ----------------- |
| 1   | Data dictionary   |
| 2   | RDBMS             |
| 3   | Concurrency       |
| 4   | View              |
| 5   | Schema            |
| 6   | Data isolation    |
| 7   | Transaction       |
| 8   | SQL               |
| 9   | Data independence |
| 10  | ACID              |

| Letter | Definition                                                                          |
| ------ | ----------------------------------------------------------------------------------- |
| A      | A virtual table defined by a query, showing a subset of data                        |
| B      | Multiple users accessing data at the same time                                      |
| C      | The formal definition of a database's structure (tables, columns, types)            |
| D      | A logical unit of work that must complete fully or not at all                       |
| E      | The standard language for querying and managing relational databases                |
| F      | The system catalog storing metadata about the database                              |
| G      | Data trapped in separate files/formats that are hard to combine                     |
| H      | A DBMS based on the relational model, using tables and SQL                          |
| I      | The ability to change storage or structure without affecting applications           |
| J      | Atomicity, Consistency, Isolation, Durability — properties of reliable transactions |

> [!NOTE]
> ***Your Answers***
>
> | #   | Your Match |
> | --- | ---------- |
> | 1   |  F          |
> | 2   | H      |
> | 3   | B           |
> | 4   | A           |
> | 5   | C           |
> | 6   | G           |
> | 7   |   D         |
> | 8   |   E         |
> | 9   |   I         |
> | 10  |     J       |

---

## Part 3: Practical Exercises

These exercises require a working PostgreSQL installation. See Part 1, Task 1 if you haven't set it up yet.

### Exercise 3.1: Explore psql

Connect to PostgreSQL using psql and complete the following. Write down the command you used and the output (or a summary of it).

1. List all databases on your server.
2. Connect to the `trailshop` database.
3. List all tables in the `trailshop` database.
4. Use `\?` to display the list of psql meta-commands. Find and write down the commands for:

- Describing a specific table's structure
- Listing all users/roles
- Showing help for a specific SQL command

5. Quit psql.

> [!NOTE]
> ***Your Answer***
>
> _(Document the commands you used and summarize the output for each step.)_

### Exercise 3.2: Explore the System Catalog

While connected to `trailshop`, run the following queries and write down what they return:

```sql
SELECT current_database();
```

```sql
SELECT version();
```

```sql
SELECT table_name FROM information_schema.tables
WHERE table_schema = 'public';
```

Why does the last query return no rows? What would you expect to see after creating tables in future weeks?

> [!NOTE]
> ***Your Answer***
>
> _(Write your answer here.)_

### Exercise 3.3: Create and Drop a Test Database

Practice database creation and deletion:

```sql
-- Create a test database
CREATE DATABASE test_playground;

-- List databases to confirm it exists
\l

-- Drop (delete) the test database
DROP DATABASE test_playground;

-- List databases again to confirm it's gone
\l
```

**Warning:** `DROP DATABASE` permanently deletes a database and all its data. Always double-check the database name before running this command.

---

## Submission Checklist

- [ ] PostgreSQL installed and working (screenshot of `psql --version` or equivalent)
- [ ] `trailshop` database created (screenshot of `\c trailshop` showing successful connection)
- [ ] Reflection Worksheet answers (Part 1, Task 3)
- [ ] Theory Review Questions answered (Part 2)
- [ ] Practical Exercise outputs documented (Part 3)
