## 🔷 What is a Relational Database?

A **Relational Database (RDB)** is a type of database that stores data in **tables (relations)**, where each table is made up of **rows and columns**. The relational model was first introduced by **Edgar F. Codd** in 1970. It is based on the concept of mathematical **relations**, hence the name.

---

## 🔷 Core Concepts

### 1. **Tables (Relations)**
- Each table represents one **entity** (e.g., customers, products).
- Each **row (record/tuple)** in a table is a unique instance of that entity.
- Each **column (attribute)** represents a property of the entity.

---

## 🔑 **Types of Keys in a Relational Database**

| Key Type             | Description |
|----------------------|-------------|
|  **Super Key**      | A set of one or more columns that can uniquely identify a record. Super keys can have extra information that’s not needed to identify records. Not all super keys can be candidate keys, but the reverse is true. In relation, a number of super keys is more than a number of candidate keys. |
|  **Candidate Key**  | A minimal set of one or more attributes that can uniquely identify each row. It's a key that can potentially be chosen as the primary key |
| **Primary Key**    | Uniquely identifies each record in a table. A table can have only one primary key, and this primary key can consist of a single or multiple columns |
| **Alternate Key**  | Candidate keys that are not chosen as the primary key |
| **Foreign Key**    | A key used to link two tables |
| **Composite Key**  | A key made up of two or more columns |
| **Unique Key**     | Ensures uniqueness of values but allows one null |
| **Surrogate Key**  | A system-generated key (usually numeric) |

Let’s go through each with examples.

---

### 🔹 1. **Primary Key**
A column (or group of columns) that uniquely identifies each row in a table.

✅ **Must be unique and not null**

```sql
CREATE TABLE Students (
    student_id INT PRIMARY KEY,
    name VARCHAR(50),
    email VARCHAR(100)
);
```

- `student_id` is the **primary key** here. No two students can have the same ID.

---

### 🔹 2. **Candidate Key**
Any column or set of columns that can **uniquely identify** a row. There can be **multiple candidate keys**.

Example:

```sql
CREATE TABLE Employees (
    emp_id INT,
    ssn VARCHAR(11),
    email VARCHAR(100),
    PRIMARY KEY (emp_id)
);
```

Here:
- `emp_id`, `ssn`, and `email` can all uniquely identify an employee.
- These are **candidate keys**, but only one is chosen as the **primary key**.

---

### 🔹 3. **Alternate Key**
A **candidate key** that is **not chosen** as the primary key.

In the above example:
- If `emp_id` is selected as the primary key, then `ssn` and `email` are **alternate keys**.

You can enforce uniqueness on alternate keys:
```sql
ALTER TABLE Employees ADD CONSTRAINT unique_ssn UNIQUE (ssn);
```

---

### 🔹 4. **Foreign Key**
Used to create a **relationship** between two tables. It references the primary key of another table.

```sql
CREATE TABLE Departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(100)
);

CREATE TABLE Employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(100),
    dept_id INT,
    FOREIGN KEY (dept_id) REFERENCES Departments(dept_id)
);
```

- `dept_id` in `Employees` is a **foreign key** that refers to `Departments`.

---

### 🔹 5. **Composite Key**
A **primary key made of two or more columns**.

```sql
CREATE TABLE Course_Registrations (
    student_id INT,
    course_id INT,
    registration_date DATE,
    PRIMARY KEY (student_id, course_id)
);
```

- The combination of `student_id` and `course_id` ensures uniqueness. This is a **composite key**.

---

### 🔹 6. **Super Key**
Any set of columns that **uniquely identifies** a row. All **candidate keys** and **primary keys** are **super keys**, but a super key can contain **extra unnecessary attributes**.

Example:

In this table:

```sql
CREATE TABLE Books (
    isbn VARCHAR(20),
    title VARCHAR(100),
    author VARCHAR(100)
);
```

Assume `isbn` is unique:
- `isbn` is a **candidate key** and a **super key**.
- `(isbn, title)`, `(isbn, author)` are **super keys**, but not candidate keys (since they contain extra data).

---

### 🔹 7. **Unique Key**
Similar to a primary key but:
- Allows **one NULL** value for SQL server. But MySQL allows multiple NULL values in a UNIQUE column
- Ensures all values are **unique**.

```sql
CREATE TABLE Customers (
    customer_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE
);
```

- `email` must be unique for every customer but can be null once.

---

### 🔹 8. **Surrogate Key**
A system-generated unique identifier, often used as a **substitute** for a natural key. Typically auto-incremented.

```sql
CREATE TABLE Orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    order_date DATE,
    customer_name VARCHAR(100)
);
```

- `order_id` is a **surrogate key**, not derived from business data.

---

## 🔄 Summary Table

| Key Type        | Uniqueness | Null Allowed | Purpose |
|-----------------|------------|--------------|---------|
| Primary Key     | Yes        | ❌ No         | Uniquely identify each row |
| Candidate Key   | Yes        | ❌ No         | Potential primary key |
| Alternate Key   | Yes        | ❌ No         | Not selected as primary key |
| Foreign Key     | No         | ✅ Yes        | Links to another table |
| Composite Key   | Yes        | ❌ No         | Combines columns to form primary key |
| Super Key       | Yes        | Depends      | Any set of columns that uniquely identifies a row |
| Unique Key      | Yes        | ✅ One null   | Enforces uniqueness with some flexibility |
| Surrogate Key   | Yes        | ❌ No         | System-generated identifier |

---

---

## 🔍 What is Indexing?

**Indexing** in a database is like an index in a book — it helps you **find data quickly** without scanning the entire table.

Instead of checking every row, the database uses an **index structure (like B-trees or hash)** to jump directly to the relevant rows.

---

## 🔍 What is Indexing?

**Indexing** is the process of creating a data structure (usually a B-Tree or Hash) to **quickly locate and access data** in a table, without scanning every row.

## 🔐 What is a Key Index?

A **key index** is an index that is **automatically created** when you define a **primary key**, **unique key**, or **foreign key** on a column.

---

## 🔢 Types of Indexing

Here are the most common types of indexing:

### 1. **Primary Index (Clustered Index)**
- Automatically created when a **primary key** is defined.
- The data in the table is **physically ordered** according to this index.
- Only **one** clustered index is allowed per table.

#### 📌 Example:
```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100)
);
```
- `student_id` will have a **clustered index**.

---

### 2. **Secondary Index (Non-Clustered Index)**
- Created manually on columns to improve search performance.
- Points to the location of the actual row.
- Data is **not stored in order** of this index.

#### 📌 Example:
```sql
CREATE INDEX idx_name ON students(name);
```

- Searching for names becomes faster using this **secondary index**.

---

### 3. **Unique Index**
- Ensures that all values in the indexed column are **unique**.
- Automatically created on `UNIQUE` constraints.

#### 📌 Example:
```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE
);
```

- `email` gets a **unique index**, allowing only one entry per unique value.

---

### 4. **Composite Index (Multi-column Index)**
- An index on **two or more columns**.
- Great for queries that **filter or sort by multiple columns**.

#### 📌 Example:
```sql
CREATE INDEX idx_name_age ON employees(name, age);
```

- Optimizes queries like:
```sql
SELECT * FROM employees WHERE name = 'John' AND age = 30;
```

> ⚠️ Order matters: the index helps when filtering by `name` or both `name` and `age`, but **not just `age`**.

---

### 5. **Full-text Index**
- Used for **text search** in large text fields (e.g., `VARCHAR`, `TEXT`).
- Supports `MATCH ... AGAINST` queries in MySQL.

#### 📌 Example:
```sql
CREATE FULLTEXT INDEX idx_description ON products(description);
```

- Optimizes:
```sql
SELECT * FROM products WHERE MATCH(description) AGAINST ('laptop');
```

---

### 6. **Spatial Index**
- Used for **geospatial data** (e.g., latitude and longitude).
- Only supported on spatial column types like `POINT`, `GEOMETRY`.

#### 📌 Example:
```sql
CREATE SPATIAL INDEX idx_location ON places(location);
```

---

### 7. **Hash Index** (used in some engines like Memory)
- Uses a **hash table** for quick lookups.
- Very fast for **exact matches**, but not useful for **range queries**.

---

## 🛠️ MySQL Index Types Summary

| Index Type        | Use Case                                 | Supports Range Queries | Engine Support     |
|-------------------|-------------------------------------------|------------------------|--------------------|
| Primary Index     | Primary key column                        | ✅ Yes                 | InnoDB (Clustered) |
| Secondary Index   | For improving lookup on other columns     | ✅ Yes                 | All engines        |
| Unique Index      | Enforcing uniqueness                     | ✅ Yes                 | All engines        |
| Composite Index   | Multi-column search or sorting            | ✅ Yes                 | All engines        |
| Full-text Index   | Searching long text columns               | ❌ No (only boolean/natural lang) | MyISAM, InnoDB (MySQL 5.6+) |
| Spatial Index     | Geospatial data (maps, GIS, etc.)         | ❌ No                 | MyISAM, InnoDB     |
| Hash Index        | Very fast exact match                     | ❌ No                 | MEMORY engine      |

---

## ⚡ Use `EXPLAIN` to See Index Usage
You can use `EXPLAIN` before a query to see if and which indexes are being used:

```sql
EXPLAIN SELECT * FROM students WHERE name = 'Alice';
```

---

## ✅ Best Practices

- Create indexes on **columns used in WHERE, JOIN, ORDER BY, or GROUP BY**.
- Avoid indexing **columns with many duplicate values**.
- Don't over-index – each index slows down **INSERT, UPDATE, DELETE** operations.
- Use **composite indexes** when filtering on **multiple columns together**.

---

## ⚙️ How Indexes Work Internally

Most relational databases use a **B-tree (balanced tree)** structure to store indexes. This keeps data **sorted** and allows:

- **Logarithmic time complexity** for searches
- Efficient **range queries**, **ORDER BY**, **GROUP BY**

In **MySQL InnoDB**, the **primary key index** is stored as a **clustered index**, meaning:
- Table data is physically organized by the primary key.
- All other indexes (secondary indexes) point to the primary key.

---

## ⚡ Benefits of Key Indexing

✅ Faster `SELECT` queries  
✅ Efficient `JOIN`s between tables  
✅ Speed up `WHERE`, `ORDER BY`, and `GROUP BY`  
✅ Enforce **data integrity** (uniqueness, referential)

---

## 🚫 Drawbacks of Too Many Indexes

⚠️ **Slower `INSERT`, `UPDATE`, `DELETE`** operations  
⚠️ Consumes **more disk space**  
⚠️ May need to be **re-built** after massive data changes

So: **Index what you query often, not everything!**

---

## 🔍 View Indexes in MySQL (via phpMyAdmin or SQL)

```sql
SHOW INDEX FROM table_name;
```

Or from **phpMyAdmin**:
- Open a table → Click on the **Structure** tab → Look for **Indexes** at the bottom.

---

## 🔑 Summary Table

| Key Type      | Uniqueness | Auto Indexed? | NULL Allowed? | Best For                      |
|---------------|------------|---------------|---------------|-------------------------------|
| Primary Key   | Yes        | ✅ Yes         | ❌ No         | Unique row identification     |
| Unique Key    | Yes        | ✅ Yes         | ✅ Yes (MySQL) | Enforcing business constraints|
| Foreign Key   | No         | ❌ No (Manual) | ✅ Yes        | Relationships / JOINs         |

---

## 🔗 What is a Relationship Between Tables?

In relational databases, **tables are connected** using **relationships** that define **how data in one table relates to data in another**. These relationships are created using **keys** — primarily **primary keys** and **foreign keys**.

---

## 🔰 Types of Relationships

There are **three main types** of relationships:

### 1. **One-to-One (1:1)**
Each row in **Table A** relates to **one and only one** row in **Table B**.

#### 🔄 Example: Person and Passport
- A person can have only **one passport**.
- A passport is issued to only **one person**.

```sql
CREATE TABLE Person (
    person_id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE Passport (
    passport_id INT PRIMARY KEY,
    person_id INT UNIQUE,
    issue_date DATE,
    FOREIGN KEY (person_id) REFERENCES Person(person_id)
);
```

---

### 2. **One-to-Many (1:N)**
Each row in **Table A** can be related to **many rows** in **Table B**, but each row in **Table B** relates to **only one** row in **Table A**.

#### 🛒 Example: Customer and Orders
- One customer can place **many orders**.
- Each order is placed by **one customer**.

```sql
CREATE TABLE Customer (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES Customer(customer_id)
);
```

---

### 3. **Many-to-Many (M:N)**
Each row in **Table A** can relate to **many rows** in **Table B**, and vice versa.  
This requires a **junction (bridge) table**.

#### 📚 Example: Students and Courses
- A student can enroll in **many courses**.
- A course can have **many students**.

```sql
CREATE TABLE Student (
    student_id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE Course (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(100)
);

-- Bridge table (Junction)
CREATE TABLE Enrollment (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES Student(student_id),
    FOREIGN KEY (course_id) REFERENCES Course(course_id)
);
```

---

## 🏥 Real-Life Relationship Examples by Domain:

### 🎓 **University System**
- 1:1 → Student and Student ID Card
- 1:N → Teacher and Classes
- M:N → Students and Clubs

### 🛒 **E-Commerce**
- 1:N → User and Orders
- 1:N → Order and OrderItems
- M:N → Products and Categories (via ProductCategory table)

### 🏥 **Hospital**
- 1:N → Doctor and Patients
- M:N → Patients and Medicines (via Prescription table)
- 1:1 → Patient and Bed (in some cases)

---

## 🔐 Keys Used in Relationships

| Key Type        | Description |
|------------------|-------------|
| **Primary Key (PK)** | Uniquely identifies each record in a table |
| **Foreign Key (FK)** | Points to a PK in another table to form a relationship |
| **Composite Key** | When two columns together form a primary or foreign key |

---

## ✅ Summary

| Relationship Type | Example | Implementation |
|-------------------|---------|----------------|
| One-to-One        | Person ↔ Passport | Foreign Key + Unique constraint |
| One-to-Many       | Customer → Orders | Foreign Key in "many" table |
| Many-to-Many      | Students ↔ Courses | Bridge table with two FKs |

---

Let me know if you’d like an **ER diagram**, **Laravel example**, or **SQL practice questions** based on relationships!

---

## 🔷 Normalization

**Normalization** is the process of organizing data to reduce redundancy and improve data integrity.

### Common Normal Forms:
| Normal Form | Description |
|-------------|-------------|
| **1NF** | Eliminate duplicate columns. |
| **2NF** | Eliminate partial dependency (non-key columns depend on the whole primary key). |
| **3NF** | Eliminate transitive dependency (non-key columns depend only on the primary key). |
| **BCNF** | A stricter version of 3NF. |

---

## 🔷 Structured Query Language (SQL)

SQL is used to **interact with relational databases**.

### Main Types of SQL Statements:
- **DDL (Data Definition Language):**
  - `CREATE`, `ALTER`, `DROP`
- **DML (Data Manipulation Language):**
  - `SELECT`, `INSERT`, `UPDATE`, `DELETE`
- **DCL (Data Control Language):**
  - `GRANT`, `REVOKE`
- **TCL (Transaction Control Language):**
  - `COMMIT`, `ROLLBACK`, `SAVEPOINT`

### Example SQL:
```sql
-- Create a table
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    Name VARCHAR(100),
    Email VARCHAR(100)
);

-- Insert data
INSERT INTO Customers (CustomerID, Name, Email)
VALUES (1, 'John Doe', 'john@example.com');

-- Select data
SELECT * FROM Customers;
```

---

## 🔷 Transactions

A **transaction** is a sequence of operations performed as a single logical unit of work.

### ACID Properties:
- **Atomicity**: All or nothing.
- **Consistency**: Data must be consistent before and after the transaction.
- **Isolation**: Concurrent transactions do not interfere.
- **Durability**: Once committed, changes persist even after a failure.

---

## 🔷 Constraints

Relational databases support several constraints to maintain data integrity:
- **NOT NULL** – Ensures column can’t have null values.
- **UNIQUE** – Ensures all values in a column are different.
- **CHECK** – Validates values based on condition.
- **DEFAULT** – Assigns a default value if none is provided.

---

## 🔷 Advantages

| Advantage                     | Explanation |
|------------------------------|-------------|
| **Data Integrity**           | Maintains accuracy and consistency. |
| **Redundancy Elimination**  | Reduces duplicated data through normalization. |
| **Ease of Use**              | SQL provides a simple interface for querying data. |
| **Security**                 | Provides roles, permissions, and encryption. |
| **Data Relationships**       | Easily define and maintain relationships between data. |
| **Scalability**              | Suitable for growing datasets (especially vertical scaling). |

---

## 🔷 Disadvantages

| Disadvantage                | Explanation |
|----------------------------|-------------|
| **Complex Joins**          | May become difficult in large databases with many relationships. |
| **Performance Overhead**   | Normalized structure may slow down queries. |
| **Scalability Limits**     | Not as horizontally scalable as NoSQL databases. |
| **Fixed Schema**           | Harder to evolve compared to NoSQL's flexible schema. |

---

## 🔷 Popular Relational Database Systems (RDBMS)

| RDBMS             | Notes |
|-------------------|-------|
| **MySQL**         | Open source, widely used in web apps. |
| **PostgreSQL**    | Open source, known for reliability and standards compliance. |
| **Oracle Database** | Enterprise-level, highly robust. |
| **Microsoft SQL Server** | Common in enterprise and .NET environments. |
| **SQLite**        | Lightweight, embedded database used in mobile apps. |

---

## 🔷 Real-World Example: E-Commerce

Imagine an e-commerce application with the following tables:

- `Users`: `user_id`, `name`, `email`
- `Products`: `product_id`, `name`, `price`
- `Orders`: `order_id`, `user_id`, `order_date`
- `OrderItems`: `order_item_id`, `order_id`, `product_id`, `quantity`

This setup allows efficient storage and querying of users, products, orders, and their relationships.

---

## 🔷 Summary

| Feature            | Description |
|-------------------|-------------|
| **Model**          | Tabular (rows & columns) |
| **Key Concepts**   | Tables, Keys, Relationships |
| **Integrity**      | Maintained with constraints |
| **Query Language** | SQL |
| **Use Cases**      | Business systems, websites, ERP, inventory, CRM |

---

## 🔗 What is a JOIN?

A **JOIN** is used to **combine rows from two or more tables** based on a related column between them (usually a foreign key relationship).

---

## 🧩 Types of JOINs in SQL

| Type            | Description                                                             |
|------------------|-------------------------------------------------------------------------|
| `INNER JOIN`     | Returns rows with **matching values** in both tables                    |
| `LEFT JOIN`      | Returns **all rows from the left** table + matching rows from the right |
| `RIGHT JOIN`     | Returns **all rows from the right** table + matching rows from the left |
| `FULL OUTER JOIN`| Returns all rows when there's a match in one of the tables              |
| `CROSS JOIN`     | Returns the **Cartesian product** (all combinations)                    |
| `SELF JOIN`      | A table joined with **itself**                                          |

---

## 🎓 Example Scenario

Let’s say we have two tables:

### 🔹 `students` table:

| student_id | name     |
|------------|----------|
| 1          | Alice    |
| 2          | Bob      |
| 3          | Charlie  |

### 🔹 `courses` table:

| course_id | student_id | course_name    |
|-----------|------------|----------------|
| 101       | 1          | Math           |
| 102       | 2          | Science        |
| 103       | 1          | Physics        |

---

### ✅ 1. **INNER JOIN**

> Returns only the rows that have matching `student_id` in both tables.

```sql
SELECT students.name, courses.course_name
FROM students
INNER JOIN courses ON students.student_id = courses.student_id;
```

#### 🧾 Result:

| name   | course_name |
|--------|-------------|
| Alice  | Math        |
| Alice  | Physics     |
| Bob    | Science     |

---

### ✅ 2. **LEFT JOIN** (or LEFT OUTER JOIN)

> Returns **all students**, even if they have no courses.

```sql
SELECT students.name, courses.course_name
FROM students
LEFT JOIN courses ON students.student_id = courses.student_id;
```

#### 🧾 Result:

| name    | course_name |
|---------|-------------|
| Alice   | Math        |
| Alice   | Physics     |
| Bob     | Science     |
| Charlie | NULL        |

---

### ✅ 3. **RIGHT JOIN** (or RIGHT OUTER JOIN)

> Returns **all courses**, even if they have no matching student (rare but possible).

```sql
SELECT students.name, courses.course_name
FROM students
RIGHT JOIN courses ON students.student_id = courses.student_id;
```

#### 🧾 Result:

| name   | course_name |
|--------|-------------|
| Alice  | Math        |
| Alice  | Physics     |
| Bob    | Science     |

(If a course had no `student_id`, it would show `NULL` for name.)

---

### ✅ 4. **FULL OUTER JOIN** *(not supported in MySQL natively)*

> Returns all students and all courses, matching where possible.  
> In MySQL, use `UNION`:

```sql
SELECT students.name, courses.course_name
FROM students
LEFT JOIN courses ON students.student_id = courses.student_id

UNION

SELECT students.name, courses.course_name
FROM students
RIGHT JOIN courses ON students.student_id = courses.student_id;
```

---

### ✅ 5. **CROSS JOIN**

> Returns **every combination** of rows from both tables.

```sql
SELECT students.name, courses.course_name
FROM students
CROSS JOIN courses;
```

If 3 students × 3 courses = **9 rows** total.

---

### ✅ 6. **SELF JOIN**

> Used to compare rows within the **same table**.

### Example: Employee table

| emp_id | emp_name | manager_id |
|--------|----------|------------|
| 1      | Alice    | NULL       |
| 2      | Bob      | 1          |
| 3      | Charlie  | 1          |

```sql
SELECT e.emp_name AS Employee, m.emp_name AS Manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

---

## 🧠 Quick Summary Table

| Join Type        | Includes Non-Matching Rows | From Which Table?     |
|------------------|----------------------------|------------------------|
| INNER JOIN       | ❌ No                      | Both tables            |
| LEFT JOIN        | ✅ Yes                     | Left table             |
| RIGHT JOIN       | ✅ Yes                     | Right table            |
| FULL OUTER JOIN  | ✅ Yes                     | Both tables            |
| CROSS JOIN       | ❌ No (All combinations)   | Both tables            |
| SELF JOIN        | ❌/✅ (depends)             | Same table             |

---

## 📘 What is NoSQL?

**NoSQL (Not Only SQL)** is a category of databases designed to handle a wide variety of data models that aren't easily managed by traditional **relational databases (SQL)**. NoSQL databases are **non-relational**, **schema-less**, and optimized for **horizontal scaling** and **high performance** with **big data** and **real-time web apps**.

---

## 📂 Types of NoSQL Databases

| Type             | Description                                                  | Example Databases               | Use Case Example                    |
|------------------|--------------------------------------------------------------|----------------------------------|-------------------------------------|
| **Document**     | Stores data as JSON, BSON, or XML documents                  | MongoDB, CouchDB, ArangoDB       | User profiles, blog posts           |
| **Key-Value**    | Simple key-value pairs                                       | Redis, DynamoDB, Riak            | Caching, session management         |
| **Column-based** | Stores data in columns (like spreadsheets)                   | Cassandra, HBase, ScyllaDB       | Time-series data, logs              |
| **Graph**        | Data is stored as nodes and edges for relationships          | Neo4j, Amazon Neptune            | Social networks, fraud detection    |

---

## ✅ Strengths of NoSQL

| Feature                        | Benefit                                                                 |
|-------------------------------|-------------------------------------------------------------------------|
| **Scalability**               | Easily scales horizontally across many servers                         |
| **Schema-less**              | Allows flexible data models — no need to predefine schema               |
| **High Performance**         | Optimized for high read/write throughput, especially with big data      |
| **Supports Modern Data**     | Works well with JSON, XML, key-value pairs, graphs                      |
| **Agility in Development**   | Ideal for agile, iterative development processes                        |
| **Cloud Native**             | Fits well with distributed, cloud-based architectures                   |

---

## ❌ Weaknesses of NoSQL

| Weakness                     | Description                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| **Lack of ACID Transactions** | Not always strongly consistent — may sacrifice consistency for performance |
| **Less Mature Tooling**     | Compared to SQL, fewer standard tools, ORMs, and ecosystem components       |
| **Learning Curve**          | Developers used to SQL may need time to adapt to querying styles            |
| **Eventual Consistency**    | Some systems provide eventual rather than strong consistency                |
| **Query Limitations**       | Queries may be less powerful or harder to write than SQL joins/aggregates   |

---

## 📌 When to Use NoSQL

✅ Use NoSQL when:

- You need **flexible schema** (e.g., user profiles, product catalogs).
- Your application is **scaling rapidly** and needs **horizontal scalability**.
- You're handling **huge volumes of data**, e.g., logs, IoT, clickstreams.
- Your data is **semi-structured** or **unstructured** (e.g., JSON, text).
- You're building **real-time apps** (e.g., messaging apps, dashboards).
- You need a **high-speed cache** (use Redis, for example).

❌ Avoid NoSQL if:

- You need **complex joins**, **multi-table transactions**, or **strong consistency**.
- Your data model is well-defined and **doesn’t change frequently**.
- You need **ACID compliance** (Atomicity, Consistency, Isolation, Durability).

---

## 🌐 Real-world Examples

| Company     | Use Case                                     | NoSQL Used     |
|-------------|----------------------------------------------|----------------|
| Facebook    | Social graph, messages                        | Cassandra      |
| Amazon      | Shopping cart, recommendation engine         | DynamoDB       |
| Netflix     | Real-time analytics, user history             | Cassandra, Redis|
| Uber        | Geospatial data, trip history                 | MongoDB        |
| Airbnb      | Search and listings metadata                  | Couchbase      |

---

## 🔍 Sample NoSQL Query (MongoDB Example)

```js
db.users.find({ "age": { "$gt": 21 } });
```

This returns all users older than 21 — flexible and JSON-like!

---

## 🧠 Summary

| Feature       | NoSQL                        | SQL (Relational)               |
|---------------|------------------------------|-------------------------------|
| Schema        | Flexible                     | Fixed                         |
| Scalability   | Horizontal                   | Vertical                      |
| ACID Support  | Limited (BASE)               | Full ACID                     |
| Query Language| Varies (Mongo, CQL, etc.)    | SQL                           |
| Data Model    | Document, Key-Value, etc.    | Tables and Rows               |

---

## 🏁 Final Thoughts

NoSQL is **not a replacement** for SQL but rather a complement. Use **what fits your project**:

- Combine MongoDB + PostgreSQL if needed.
- Use **NoSQL for speed and scale**, and **SQL for integrity and structure**.

---

## 🔐 What are ACID Properties?

**ACID** stands for:

- **A** – Atomicity  
- **C** – Consistency  
- **I** – Isolation  
- **D** – Durability  

These properties ensure that **database transactions** are processed reliably. A database transaction is a sequence of one or more operations performed on a database that are treated as a single unit of work

---

### 1. ✅ **Atomicity** — "All or Nothing"

> A transaction must be **fully completed** or **fully rolled back**.  
> Partial transactions are **not allowed**.

#### 📌 Example:
Imagine you are transferring money from Account A to Account B.

```sql
-- Deduct $100 from A
UPDATE accounts SET balance = balance - 100 WHERE id = 'A';

-- Add $100 to B
UPDATE accounts SET balance = balance + 100 WHERE id = 'B';
```

If the first query succeeds but the second fails, atomicity ensures the **whole transaction is rolled back**, and no money is deducted.

---

### 2. ✅ **Consistency** — "Valid State Before & After"

> A transaction brings the database from **one valid state to another**.  
> All rules (constraints, triggers, cascades) must be followed.

#### 📌 Example:
A `balance` column must never be negative. If a transaction causes a balance to go negative, the database will **reject** the transaction, ensuring consistency.

---

### 3. ✅ **Isolation** — "No Interference"

> Concurrent transactions should not interfere with each other.  
> The result should be **as if transactions ran one after another**.

#### 📌 Example:
Two users try to book the **last ticket** at the same time.

- Without isolation: both may see the ticket available and book it.
- With isolation: only one will succeed, the other will wait or fail.

MySQL supports **isolation levels** like:
- `READ UNCOMMITTED`
- `READ COMMITTED`
- `REPEATABLE READ` (default in InnoDB)
- `SERIALIZABLE`

---

### 4. ✅ **Durability** — "Permanence"

> Once a transaction is committed, its changes are **permanent**,  
> even if there’s a system crash.

#### 📌 Example:
After transferring money and getting a success message, the change is saved in the database. Even if the server goes down seconds later, the transaction will **not be lost**.

Durability is achieved using **logs**, **backups**, and **write-ahead logging (WAL)**.

---

## 🔄 Summary Table

| Property   | Description                              | Real-life Example                       |
|------------|------------------------------------------|------------------------------------------|
| Atomicity  | All steps succeed or all fail            | Bank transfer both debit and credit      |
| Consistency| DB always stays in a valid state         | Never go below 0 balance                 |
| Isolation  | Transactions don't affect each other     | Two people booking same seat             |
| Durability | Changes stay even after power loss       | Payment confirmation survives a crash    |

---

## 🔄 Transaction Isolation Levels

Understanding isolation levels is crucial for concurrent database operations.

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read | Performance |
|-----------------|------------|---------------------|--------------|-------------|
| **READ UNCOMMITTED** | ✅ Possible | ✅ Possible | ✅ Possible | Fastest |
| **READ COMMITTED** | ❌ No | ✅ Possible | ✅ Possible | Fast |
| **REPEATABLE READ** | ❌ No | ❌ No | ✅ Possible | Medium |
| **SERIALIZABLE** | ❌ No | ❌ No | ❌ No | Slowest |

### Isolation Problems Explained

#### 1. **Dirty Read**
Reading uncommitted changes from another transaction.

```sql
-- Transaction A
BEGIN;
UPDATE products SET price = 100 WHERE id = 1;
-- Not committed yet

-- Transaction B reads the uncommitted change
SELECT price FROM products WHERE id = 1; -- Sees 100

-- Transaction A rolls back
ROLLBACK;

-- Transaction B read "dirty" data that never existed
```

#### 2. **Non-Repeatable Read**
Same query returns different results within a transaction.

```sql
-- Transaction A
BEGIN;
SELECT price FROM products WHERE id = 1; -- Returns 100

-- Transaction B updates and commits
UPDATE products SET price = 150 WHERE id = 1;
COMMIT;

-- Transaction A reads again
SELECT price FROM products WHERE id = 1; -- Now returns 150 (different!)
```

#### 3. **Phantom Read**
New rows appear in subsequent reads.

```sql
-- Transaction A
BEGIN;
SELECT COUNT(*) FROM orders WHERE user_id = 5; -- Returns 3

-- Transaction B inserts and commits
INSERT INTO orders (user_id) VALUES (5);
COMMIT;

-- Transaction A counts again
SELECT COUNT(*) FROM orders WHERE user_id = 5; -- Now returns 4 (phantom row!)
```

---

## 🔐 Database Constraints

Constraints ensure data integrity and validity.

### Types of Constraints

#### 1. **NOT NULL**
```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(100) NOT NULL,
    name VARCHAR(50) NOT NULL
);
```

#### 2. **UNIQUE**
```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    username VARCHAR(50) UNIQUE
);
```

#### 3. **CHECK**
```sql
CREATE TABLE products (
    id INT PRIMARY KEY,
    price DECIMAL(10,2) CHECK (price > 0),
    stock INT CHECK (stock >= 0),
    discount INT CHECK (discount BETWEEN 0 AND 100)
);
```

#### 4. **DEFAULT**
```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    status VARCHAR(20) DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### 5. **FOREIGN KEY with Actions**
```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    FOREIGN KEY (user_id) REFERENCES users(id)
        ON DELETE CASCADE           -- Delete orders when user is deleted
        ON UPDATE CASCADE           -- Update orders when user id changes
);

-- Other options:
-- ON DELETE SET NULL
-- ON DELETE SET DEFAULT
-- ON DELETE RESTRICT (prevent deletion)
-- ON DELETE NO ACTION
```

---

## 🎯 Advanced Indexing Strategies

### Types of Indexes

#### 1. **Single Column Index**
```sql
CREATE INDEX idx_email ON users(email);
```

#### 2. **Composite Index**
```sql
CREATE INDEX idx_name_age ON users(last_name, first_name, age);

-- Query optimization follows "leftmost prefix" rule
SELECT * FROM users WHERE last_name = 'Smith'; -- Uses index ✅
SELECT * FROM users WHERE first_name = 'John'; -- Doesn't use index ❌
SELECT * FROM users WHERE last_name = 'Smith' AND first_name = 'John'; -- Uses index ✅
```

#### 3. **Unique Index**
```sql
CREATE UNIQUE INDEX idx_unique_email ON users(email);
```

#### 4. **Full-Text Index**
```sql
-- For text search
CREATE FULLTEXT INDEX idx_description ON products(description);

SELECT * FROM products WHERE MATCH(description) AGAINST('laptop computer');
```

#### 5. **Partial Index (PostgreSQL)**
```sql
-- Index only active users
CREATE INDEX idx_active_users ON users(email) WHERE active = true;
```

#### 6. **Covering Index**
```sql
-- Index includes all columns needed for query
CREATE INDEX idx_user_details ON users(id, name, email);

-- This query uses only the index (doesn't touch table)
SELECT id, name, email FROM users WHERE id = 123;
```

### Index Best Practices

✅ **Do:**
- Index foreign keys
- Index columns in WHERE, JOIN, ORDER BY clauses
- Index columns with high cardinality (many unique values)
- Monitor slow queries and add indexes

❌ **Don't:**
- Over-index (slows down INSERT/UPDATE/DELETE)
- Index small tables
- Index columns with low cardinality (e.g., boolean, gender)
- Index frequently updated columns unnecessarily

---

## 🔍 Query Optimization Techniques

### 1. **Use EXPLAIN**

```sql
EXPLAIN SELECT * FROM orders 
WHERE user_id = 123 AND status = 'pending';

-- Shows:
-- - Which indexes are used
-- - Number of rows scanned
-- - Query execution plan
```

### 2. **Avoid SELECT ***

❌ **Bad:**
```sql
SELECT * FROM users WHERE id = 123;
```

✅ **Good:**
```sql
SELECT id, name, email FROM users WHERE id = 123;
```

### 3. **Use Proper JOINs**

❌ **Bad (Implicit Join):**
```sql
SELECT * FROM orders, users 
WHERE orders.user_id = users.id;
```

✅ **Good (Explicit Join):**
```sql
SELECT o.*, u.name 
FROM orders o
INNER JOIN users u ON o.user_id = u.id;
```

### 4. **Use Indexes in WHERE**

```sql
-- Create index
CREATE INDEX idx_created_at ON orders(created_at);

-- Use indexed column
SELECT * FROM orders 
WHERE created_at >= '2024-01-01';
```

### 5. **Avoid Functions on Indexed Columns**

❌ **Bad (Prevents index usage):**
```sql
SELECT * FROM users WHERE YEAR(created_at) = 2024;
```

✅ **Good (Uses index):**
```sql
SELECT * FROM users 
WHERE created_at >= '2024-01-01' 
  AND created_at < '2025-01-01';
```

### 6. **Use LIMIT for Pagination**

```sql
-- Efficient pagination
SELECT * FROM products 
ORDER BY id 
LIMIT 20 OFFSET 40;

-- Even better with cursor-based pagination
SELECT * FROM products 
WHERE id > 1000 
ORDER BY id 
LIMIT 20;
```

### 7. **Batch Operations**

❌ **Bad (Many queries):**
```sql
INSERT INTO users (name) VALUES ('John');
INSERT INTO users (name) VALUES ('Jane');
INSERT INTO users (name) VALUES ('Bob');
```

✅ **Good (Single query):**
```sql
INSERT INTO users (name) VALUES 
    ('John'),
    ('Jane'),
    ('Bob');
```

---

## 🗂️ Database Normalization Deep Dive

### Normal Forms Summary

| Normal Form | Rule | Example Violation |
|-------------|------|-------------------|
| **1NF** | Atomic values, no repeating groups | Storing multiple phone numbers in one field |
| **2NF** | 1NF + No partial dependencies | Non-key attributes depend on part of composite key |
| **3NF** | 2NF + No transitive dependencies | Storing city and country (country depends on city) |
| **BCNF** | 3NF + Every determinant is a candidate key | Special case of 3NF |

### Example: Normalization Process

#### Unnormalized Data
```
Orders Table:
order_id | customer_name | items                  | prices
1        | John          | Laptop, Mouse, Keyboard | 1000, 20, 50
```

#### 1NF (First Normal Form)
```
Orders Table:
order_id | customer_name | item     | price
1        | John          | Laptop   | 1000
1        | John          | Mouse    | 20
1        | John          | Keyboard | 50
```

#### 2NF & 3NF (Normalized)
```
Customers:
customer_id | name
1           | John

Orders:
order_id | customer_id | order_date
1        | 1           | 2024-01-15

Order_Items:
order_id | product_id | quantity | price
1        | 101        | 1        | 1000
1        | 102        | 1        | 20

Products:
product_id | name     | price
101        | Laptop   | 1000
102        | Mouse    | 20
```

### When to Denormalize

**Denormalization** is intentionally introducing redundancy for performance.

**When to consider:**
- Read-heavy applications
- Reporting/analytics
- Caching frequently accessed data
- Reducing complex joins

**Example:**
```sql
-- Denormalized for faster queries
CREATE TABLE order_summary (
    order_id INT,
    customer_name VARCHAR(100),  -- Redundant
    total_amount DECIMAL(10,2),  -- Calculated
    item_count INT               -- Calculated
);
```

---

## 🔄 Database Replication & Scaling

### Replication Types

#### 1. **Master-Slave (Primary-Replica)**

```
Master (Write) ──┐
                 ├──> Slave 1 (Read)
                 ├──> Slave 2 (Read)
                 └──> Slave 3 (Read)
```

**Use case:** Read-heavy applications

#### 2. **Master-Master**

```
Master 1 ←──→ Master 2
```

**Use case:** High availability, multi-region

#### 3. **Multi-Master with Conflict Resolution**

Handle write conflicts:
- Last Write Wins (LWW)
- Application-level resolution
- CRDT (Conflict-free Replicated Data Types)

---

### Scaling Strategies

#### Vertical Scaling (Scale Up)
- Add more CPU, RAM, SSD
- **Pros:** Simple
- **Cons:** Hardware limits, expensive, single point of failure

#### Horizontal Scaling (Scale Out)
- Add more database servers
- **Pros:** Unlimited scaling, better fault tolerance
- **Cons:** Complex, consistency challenges

#### Sharding (Horizontal Partitioning)

Split data across multiple databases.

```sql
-- Users with ID 1-1000 → Shard 1
-- Users with ID 1001-2000 → Shard 2
-- Users with ID 2001-3000 → Shard 3
```

**Sharding Strategies:**
1. **Range-based:** By ID, date
2. **Hash-based:** Hash(user_id) % num_shards
3. **Geographic:** By location
4. **Directory-based:** Lookup table

---

## 📚 Resources

### Official Documentation
- [MySQL Documentation](https://dev.mysql.com/doc/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [Redis Documentation](https://redis.io/documentation)

### Books
- **"Database Internals"** by Alex Petrov
- **"Designing Data-Intensive Applications"** by Martin Kleppmann
- **"SQL Performance Explained"** by Markus Winand
- **"High Performance MySQL"** by Baron Schwartz

### Online Courses
- [Stanford Database Course (CS145)](https://cs145-fa19.github.io/)
- [CMU Database Systems (15-445)](https://15445.courses.cs.cmu.edu/)
- [SQL Tutorial - W3Schools](https://www.w3schools.com/sql/)
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/)

### Practice Platforms
- [LeetCode Database](https://leetcode.com/problemset/database/)
- [HackerRank SQL](https://www.hackerrank.com/domains/sql)
- [SQLZoo](https://sqlzoo.net/)
- [Mode SQL Tutorial](https://mode.com/sql-tutorial/)

---

## ✅ Database Interview Checklist

### Fundamentals
- [ ] Understand ACID properties
- [ ] Know different types of keys
- [ ] Master JOIN operations
- [ ] Understand normalization (1NF, 2NF, 3NF)
- [ ] Grasp indexing concepts

### Intermediate
- [ ] Transaction isolation levels
- [ ] Query optimization techniques
- [ ] Index types and when to use them
- [ ] Constraints and their purposes
- [ ] Stored procedures and triggers

### Advanced
- [ ] Replication strategies
- [ ] Sharding and partitioning
- [ ] CAP theorem
- [ ] Database scaling patterns
- [ ] NoSQL vs SQL trade-offs

---

**Remember:** Database design is about trade-offs. There's no one-size-fits-all solution. Understand your requirements and choose accordingly!

---

## 🎯 Database Interview Questions — Critical Scenarios

---

### 🔴 High Traffic & Scaling

**Q1. Your MySQL database is the bottleneck under high traffic. What is your step-by-step approach?**

**Answer:**

**Step 1 — Diagnose:**
```sql
-- Find slow queries (enable slow query log)
SHOW VARIABLES LIKE 'slow_query_log';
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;  -- Log queries > 1 second

-- Check running queries
SHOW PROCESSLIST;

-- Analyze query
EXPLAIN SELECT * FROM orders WHERE user_id = 5 AND status = 'pending';
```

**Step 2 — Optimize queries:**
- Add missing indexes based on EXPLAIN output
- Rewrite N+1 causing queries with JOINs
- Avoid `SELECT *` — fetch only needed columns
- Use covering indexes

**Step 3 — Read/Write splitting:**
```
Primary DB (writes) → Replica DB 1, 2, 3 (reads)
```
Route 80% of traffic (reads) to replicas. Application connects to replica for SELECTs.

**Step 4 — Caching layer:**
- Cache frequent, rarely-changing reads in Redis
- Use query result caching for expensive aggregates

**Step 5 — Vertical then horizontal scaling:**
- Vertical: More RAM (InnoDB buffer pool), faster disk (SSD/NVMe)
- Horizontal: Sharding by user_id ranges or consistent hashing

---

**Q2. What is database sharding and what are the trade-offs?**

**Answer:**
**Sharding** splits data horizontally across multiple database instances (shards). Each shard holds a subset of rows.

**Strategies:**
| Strategy | How | Pros | Cons |
|---------|-----|------|------|
| **Range-based** | Shard by user_id 1-1M → DB1, 1M-2M → DB2 | Simple, range queries efficient | Hotspots if one range is more active |
| **Hash-based** | `shard = hash(user_id) % N` | Even distribution | Range queries cross all shards |
| **Directory-based** | Lookup table maps ID → shard | Flexible | Lookup table is single point of failure |
| **Geographic** | Users in EU → EU shard | Data residency compliance | Cross-region queries expensive |

**Advantages:**
- Horizontal scalability — add shards as data grows
- Each shard handles less load

**Disadvantages:**
- Cross-shard JOINs are expensive or impossible
- Transactions across shards require distributed 2PC
- Application complexity — must know which shard to query
- Re-sharding is painful

**When to shard:** After you've exhausted read replicas, caching, and vertical scaling. Sharding is a last resort due to complexity.

---

**Q3. Explain database replication and its types.**

**Answer:**
Replication copies data from a primary (master) to one or more replicas (slaves).

**Types:**

| Type | How | Use Case |
|------|-----|---------|
| **Async Replication** | Primary doesn't wait for replica acknowledgment | Faster writes, potential data loss on failover |
| **Semi-sync Replication** | Primary waits for at least one replica to acknowledge | Balance of performance and safety |
| **Sync Replication** | Primary waits for ALL replicas | Zero data loss, slower writes |

**MySQL replication (binlog-based):**
```sql
-- On Primary
SHOW MASTER STATUS;  -- Get binlog position

-- On Replica
CHANGE MASTER TO
  MASTER_HOST='primary-ip',
  MASTER_LOG_FILE='mysql-bin.000001',
  MASTER_LOG_POS=154;
START SLAVE;
SHOW SLAVE STATUS\G
```

**Replication lag:** The replica may be seconds/minutes behind the primary. Critical for apps that write then immediately read (use primary for post-write reads).

---

**Q4. What is connection pooling and why is it critical for high-traffic databases?**

**Answer:**
Each database connection is expensive (TCP handshake, auth, memory allocation). Without pooling, high traffic causes connection storms.

**Without pooling:**
- 500 concurrent PHP-FPM workers × each opens a connection = 500 DB connections
- MySQL max_connections typically 150–300 → connections fail

**With pooling (PgBouncer for PostgreSQL, ProxySQL for MySQL):**
```
PHP-FPM workers (500) → ProxySQL pool (20 connections) → MySQL
```

The pool maintains a small set of persistent connections and queues requests.

**Configuration (MySQL via ProxySQL):**
```ini
[proxysql]
mysql_max_connections=100
mysql_default_query_timeout=10000
```

**PHP PDO persistent connections:**
```php
$pdo = new PDO($dsn, $user, $pass, [
    PDO::ATTR_PERSISTENT => true  // Reuse connection per PHP-FPM worker
]);
```

---

### ⚖️ Transactions & Isolation

**Q5. Explain the four ACID properties with real-world examples.**

**Answer:**

| Property | Definition | Example |
|---------|-----------|---------|
| **Atomicity** | All operations succeed or none do | Bank transfer: debit + credit — if credit fails, debit is rolled back |
| **Consistency** | Data moves from one valid state to another | Can't have negative account balance after transfer |
| **Isolation** | Concurrent transactions don't interfere | Two users booking the last seat: only one succeeds |
| **Durability** | Committed data survives system failure | After `COMMIT`, data persists even if server crashes |

```sql
-- Atomic transfer
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
-- Check constraints, then:
COMMIT;  -- or ROLLBACK if any step failed
```

---

**Q6. What are transaction isolation levels and what problems does each prevent?**

**Answer:**

**Problems:**
- **Dirty Read:** Reading uncommitted data from another transaction
- **Non-repeatable Read:** Same row returns different values within a transaction
- **Phantom Read:** Same query returns different rows (new rows inserted)

| Isolation Level | Dirty Read | Non-repeatable Read | Phantom Read | Performance |
|----------------|-----------|--------------------|--------------|-----------| 
| `READ UNCOMMITTED` | ✅ Possible | ✅ Possible | ✅ Possible | Fastest |
| `READ COMMITTED` | ❌ Prevented | ✅ Possible | ✅ Possible | Fast |
| `REPEATABLE READ` | ❌ Prevented | ❌ Prevented | ✅ Possible (❌ in MySQL InnoDB) | Moderate |
| `SERIALIZABLE` | ❌ Prevented | ❌ Prevented | ❌ Prevented | Slowest |

**MySQL InnoDB default:** `REPEATABLE READ` (with gap locks that also prevent phantom reads).

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
START TRANSACTION;
SELECT balance FROM accounts WHERE id = 1;  -- Consistent snapshot
-- ... other operations ...
COMMIT;
```

**Practical advice:** Use `REPEATABLE READ` (default) for most apps. Drop to `READ COMMITTED` for high-concurrency reporting queries where phantom reads are acceptable.

---

**Q7. What is a deadlock and how do you prevent it?**

**Answer:**
A deadlock occurs when two transactions each hold a lock the other needs.

```
Transaction A: LOCK row 1 → waits for row 2
Transaction B: LOCK row 2 → waits for row 1
→ Deadlock
```

**Prevention strategies:**
1. **Consistent lock ordering** — always lock resources in the same order
   ```sql
   -- Always lock lower ID first
   -- Transaction A and B both: lock account 1 first, then account 2
   ```

2. **Keep transactions short** — minimize time locks are held

3. **Use `SELECT ... FOR UPDATE` explicitly** — acquire locks early
   ```sql
   START TRANSACTION;
   SELECT * FROM orders WHERE id = 5 FOR UPDATE;  -- Lock now
   UPDATE orders SET status = 'processing' WHERE id = 5;
   COMMIT;
   ```

4. **Retry on deadlock** — InnoDB detects deadlocks and rolls back one transaction
   ```php
   // Application-level retry
   for ($attempt = 0; $attempt < 3; $attempt++) {
       try {
           DB::transaction(fn() => processOrder($orderId));
           break;
       } catch (QueryException $e) {
           if ($e->getCode() !== '40001') throw $e; // 40001 = deadlock
           usleep(100000 * $attempt); // Exponential backoff
       }
   }
   ```

---

### 🗂️ Indexing Deep Dive

**Q8. A query is slow even though the column is indexed. What are the possible causes?**

**Answer:**

1. **Function on indexed column** — optimizer can't use the index:
   ```sql
   -- ❌ Slow — YEAR() prevents index use
   SELECT * FROM orders WHERE YEAR(created_at) = 2024;

   -- ✅ Fast — range query uses index
   SELECT * FROM orders WHERE created_at BETWEEN '2024-01-01' AND '2024-12-31';
   ```

2. **Type mismatch** — implicit casting disables index:
   ```sql
   -- ❌ Slow — user_id is INT but '5' is VARCHAR
   SELECT * FROM orders WHERE user_id = '5';
   ```

3. **Leading wildcard in LIKE**:
   ```sql
   -- ❌ Full table scan
   SELECT * FROM products WHERE name LIKE '%phone%';

   -- ✅ Uses index (prefix search)
   SELECT * FROM products WHERE name LIKE 'phone%';
   ```

4. **Low selectivity** — column has few distinct values (e.g., `status` with 3 values). Optimizer may prefer full scan.

5. **Wrong composite index order:**
   ```sql
   -- Index: (status, created_at)
   -- ❌ Can't use index efficiently — skipping leading column
   SELECT * FROM orders WHERE created_at > '2024-01-01';

   -- ✅ Uses index — leading column is present
   SELECT * FROM orders WHERE status = 'pending' AND created_at > '2024-01-01';
   ```

6. **Table statistics stale** — run `ANALYZE TABLE orders;`

---

**Q9. What is a covering index and why is it powerful?**

**Answer:**
A **covering index** includes ALL columns a query needs — the database never has to access the actual table rows (no "table heap" lookup).

```sql
-- Query
SELECT name, email FROM users WHERE company_id = 5 ORDER BY name;

-- Regular index on company_id: 
--   1. Search index for company_id=5 (gets primary key IDs)
--   2. For each ID, fetch row from table heap (to get name, email)

-- Covering index: (company_id, name, email)
CREATE INDEX idx_covering ON users(company_id, name, email);
-- ✅ All data found in index — zero table heap access
```

EXPLAIN will show `Using index` (not `Using index; Using temporary`) for a covering index.

**When to use:** High-frequency queries on large tables where the index + data columns are narrow.  
**Trade-off:** Wider indexes = more storage and slower writes.

---

### 🔵 NoSQL vs SQL

**Q10. SQL vs NoSQL — when do you choose each?**

**Answer:**

| Criteria | SQL (Relational) | NoSQL |
|---------|-----------------|-------|
| **Data structure** | Fixed schema, structured | Flexible schema, semi-structured |
| **Relationships** | Foreign keys, JOINs | Denormalized, embedded documents |
| **ACID** | Native | Varies (MongoDB 4+ supports multi-doc ACID) |
| **Scaling** | Vertical first, horizontal complex | Horizontal by design |
| **Query complexity** | Rich SQL, complex JOINs | Limited or application-side JOINs |
| **Consistency** | Strong | Eventual (usually configurable) |

**NoSQL types and use cases:**

| Type | Examples | Best For |
|------|---------|---------|
| **Document** | MongoDB, CouchDB | Content management, user profiles, catalogs |
| **Key-Value** | Redis, DynamoDB | Caching, sessions, leaderboards, rate limiting |
| **Column-family** | Cassandra, HBase | Time-series data, analytics at massive scale |
| **Graph** | Neo4j, Amazon Neptune | Social networks, recommendation engines, fraud detection |

**Choose SQL when:** Data has clear relationships, you need complex queries, transactions are critical (banking, inventory).  
**Choose NoSQL when:** Schema is evolving, massive write throughput needed, data is naturally document-shaped, or you need geo-distribution.

---

**Q11. What is the CAP theorem and how does it apply to database selection?**

**Answer:**
CAP theorem states that a distributed system can only guarantee **2 of 3** properties simultaneously:

- **C**onsistency — every read gets the most recent write
- **A**vailability — every request gets a response (may be stale)
- **P**artition Tolerance — system continues operating if network partitions occur

Since network partitions are unavoidable in distributed systems, you're always choosing between **CP** or **AP**.

| System | Type | Trade-off |
|--------|------|-----------|
| MySQL (single node) | CA | Not partition tolerant — not distributed |
| MySQL + sync replication | CP | Blocks writes during partition |
| Cassandra | AP | Returns stale data during partition |
| MongoDB (default) | CP | Primary elections during partition |
| DynamoDB | AP | Eventual consistency by default |
| Redis Cluster | AP | Reads may be stale during partition |

**PACELC** (extended CAP): Also considers Latency vs Consistency tradeoff when there's no partition.

---

**Q12. What is the difference between optimistic and pessimistic locking?**

**Answer:**

**Pessimistic Locking** — assumes conflict will happen, locks immediately:
```sql
-- Lock row for the duration of the transaction
START TRANSACTION;
SELECT * FROM inventory WHERE product_id = 1 FOR UPDATE;  -- Other transactions blocked
UPDATE inventory SET quantity = quantity - 1 WHERE product_id = 1;
COMMIT;
```
**Use when:** High contention (popular item inventory), must prevent any data race.

**Optimistic Locking** — assumes conflict is rare, check at save time:
```sql
-- Read with version
SELECT quantity, version FROM inventory WHERE product_id = 1;
-- (version = 5, quantity = 10)

-- Update only if version hasn't changed
UPDATE inventory 
SET quantity = 9, version = 6 
WHERE product_id = 1 AND version = 5;
-- If 0 rows affected → someone else modified it → retry
```

**In Eloquent (via `updated_at` timestamp):**
```php
// Optimistic locking with version column
$inventory = Inventory::find(1);
$inventory->quantity -= 1;
$affected = Inventory::where('id', 1)
    ->where('version', $inventory->version)
    ->update(['quantity' => $inventory->quantity, 'version' => $inventory->version + 1]);

if ($affected === 0) {
    throw new StaleDataException('Record was modified concurrently');
}
```

| Approach | Best When | Downside |
|---------|----------|---------|
| Pessimistic | High contention, financial data | Performance (locks held longer) |
| Optimistic | Low contention, read-heavy | Must handle retry logic |

---

### 🧮 Query Optimization

**Q13. What does EXPLAIN output tell you and what are the key fields to watch?**

**Answer:**

```sql
EXPLAIN SELECT u.name, COUNT(o.id) 
FROM users u 
LEFT JOIN orders o ON o.user_id = u.id 
WHERE u.active = 1 
GROUP BY u.id;
```

| Field | What to Watch For |
|-------|------------------|
| **type** | `ALL` = full scan (bad), `index` = index scan, `ref` = index lookup, `eq_ref` = primary key lookup (best), `const` = single row |
| **key** | Which index was used. `NULL` = no index used |
| **rows** | Estimated rows examined. Multiply across JOINs for total cost |
| **Extra** | `Using filesort` = expensive sort, `Using temporary` = temp table, `Using index` = covering index (good) |
| **filtered** | % of rows remaining after filtering. Low % = effective index |

**Red flags:** `type = ALL` on large tables, `Extra = Using filesort` or `Using temporary` on high-frequency queries.

---

**Q14. When should you use a stored procedure vs application-level code?**

**Answer:**

| Criteria | Stored Procedure | Application Code |
|---------|-----------------|-----------------|
| **Performance** | Faster for complex, data-intensive operations (less network round trips) | Network overhead for multiple queries |
| **Portability** | DB-specific syntax | Language-agnostic |
| **Version control** | Harder to track | Git-friendly |
| **Testing** | Difficult to unit test | Easy with mocks |
| **Debugging** | Limited tooling | Full debugger support |
| **Business logic** | Should generally NOT live here | Preferred location |

**Stored procedures make sense for:**
- Batch operations on millions of rows (ETL jobs)
- Audit triggers that must fire regardless of application
- Calculations that need to join many tables in a single DB call

**Modern recommendation:** Keep business logic in application code. Use stored procedures sparingly for performance-critical, data-intensive operations that don't fit in ORM.

---

**Q15. What is database partitioning and how does it differ from sharding?**

**Answer:**

| Aspect | Partitioning | Sharding |
|--------|-------------|---------|
| **Location** | Same server, different physical segments | Different servers |
| **Transparency** | Transparent to application (single table name) | Application must route to correct shard |
| **Complexity** | Low | High |
| **Scale** | Single server limits still apply | True horizontal scale |
| **Use case** | Large tables on powerful single server | Distributed systems |

**MySQL Partitioning types:**
```sql
-- Range partitioning by year
CREATE TABLE orders (
    id INT,
    created_at DATE,
    amount DECIMAL(10,2)
)
PARTITION BY RANGE (YEAR(created_at)) (
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);
```

**Benefits:** Partition pruning — `WHERE created_at > '2024-01-01'` only scans the 2024 partition.

---

### 🔬 Advanced Concepts

**Q16. What is the difference between `DELETE`, `TRUNCATE`, and `DROP`?**

**Answer:**

| Command | What it does | Rollback? | Triggers? | Speed |
|---------|-------------|-----------|----------|-------|
| `DELETE` | Removes rows with WHERE filter | ✅ Yes (in transaction) | ✅ Yes | Slow (row-by-row) |
| `TRUNCATE` | Removes ALL rows, resets auto-increment | ❌ No (DDL in most DBs) | ❌ No | Fast (deallocates pages) |
| `DROP` | Removes entire table structure + data | ❌ No | ❌ No | Instant |

```sql
DELETE FROM users WHERE active = 0;          -- Selective, logged, triggers fire
TRUNCATE TABLE sessions;                     -- Fast wipe, no triggers
DROP TABLE temp_import;                      -- Destroy table entirely
```

**Interview trap:** TRUNCATE cannot be rolled back in MySQL (DDL auto-commits). PostgreSQL allows rolling back TRUNCATE.

---

**Q17. What is the difference between a view and a materialized view?**

**Answer:**

| Feature | View | Materialized View |
|---------|------|------------------|
| **Data storage** | No — executes query on access | Yes — stores query result physically |
| **Performance** | Same as underlying query | Fast reads (pre-computed) |
| **Freshness** | Always up-to-date | Stale until refreshed |
| **Refresh** | N/A | Manual or scheduled `REFRESH MATERIALIZED VIEW` |
| **MySQL support** | ✅ Yes | ❌ No (use summary tables instead) |
| **PostgreSQL support** | ✅ Yes | ✅ Yes |

```sql
-- PostgreSQL Materialized View
CREATE MATERIALIZED VIEW monthly_revenue AS
SELECT DATE_TRUNC('month', created_at) as month,
       SUM(amount) as total
FROM orders
GROUP BY 1;

-- Refresh (blocking)
REFRESH MATERIALIZED VIEW monthly_revenue;

-- Non-blocking refresh (PostgreSQL 9.4+)
REFRESH MATERIALIZED VIEW CONCURRENTLY monthly_revenue;
```

**Use when:** Complex aggregations that are expensive to compute on every read but can tolerate slight staleness (reporting dashboards, analytics).

---

**Q18. What is the difference between `HAVING` and `WHERE`?**

**Answer:**

```sql
-- WHERE filters BEFORE grouping (works on individual rows)
-- HAVING filters AFTER grouping (works on aggregate results)

-- ❌ Wrong: Can't use aggregate in WHERE
SELECT department, AVG(salary) as avg_salary
FROM employees
WHERE AVG(salary) > 50000  -- Error!
GROUP BY department;

-- ✅ Correct: Use HAVING for aggregate conditions
SELECT department, AVG(salary) as avg_salary
FROM employees
WHERE active = 1          -- Pre-aggregation filter (reduces rows before grouping)
GROUP BY department
HAVING AVG(salary) > 50000;  -- Post-aggregation filter
```

**Performance tip:** Always filter with `WHERE` as much as possible before `GROUP BY` — reduces the number of rows being aggregated.

---

**Q19. Explain window functions and when to use them over GROUP BY.**

**Answer:**
Window functions compute values across a set of rows related to the current row **without collapsing rows** (unlike GROUP BY).

```sql
-- GROUP BY collapses to one row per group
SELECT department, AVG(salary) FROM employees GROUP BY department;

-- Window function — keeps all rows, adds aggregated column
SELECT 
    name,
    department,
    salary,
    AVG(salary) OVER (PARTITION BY department) as dept_avg,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) as salary_rank,
    ROW_NUMBER() OVER (ORDER BY hire_date) as hire_sequence,
    LAG(salary, 1) OVER (PARTITION BY department ORDER BY hire_date) as prev_salary
FROM employees;
```

**Common window functions:**

| Function | Use Case |
|---------|---------|
| `ROW_NUMBER()` | Unique sequential number per partition |
| `RANK()` | Ranking with gaps on ties |
| `DENSE_RANK()` | Ranking without gaps on ties |
| `LAG(col, n)` | Access previous row's value |
| `LEAD(col, n)` | Access next row's value |
| `SUM() OVER` | Running total |
| `FIRST_VALUE()` | First value in window |
| `NTILE(n)` | Divide rows into n buckets |

**Use over GROUP BY when:** You need both individual row data AND aggregated data in the same query result.

---

**Q20. What is the difference between `UNION` and `UNION ALL`? What about `INTERSECT` and `EXCEPT`?**

**Answer:**

```sql
-- UNION: Combines results, removes duplicates (sorts/dedupes — slower)
SELECT email FROM users UNION SELECT email FROM admins;

-- UNION ALL: Combines all results, keeps duplicates (faster — no dedup)
SELECT email FROM users UNION ALL SELECT email FROM admins;

-- INTERSECT: Returns rows that exist in BOTH result sets
SELECT email FROM users INTERSECT SELECT email FROM newsletter_subscribers;

-- EXCEPT (MINUS in Oracle): Rows in first result but NOT in second
SELECT email FROM users EXCEPT SELECT email FROM unsubscribed_users;
```

**Performance:** Always use `UNION ALL` unless you need deduplication — `UNION` forces a sort/hash to remove duplicates.

| Operator | Returns | Deduplication |
|---------|---------|--------------|
| `UNION` | A ∪ B | ✅ Yes (slower) |
| `UNION ALL` | A + B (all rows) | ❌ No (faster) |
| `INTERSECT` | A ∩ B | ✅ Yes |
| `EXCEPT`/`MINUS` | A − B | ✅ Yes |

**Note:** MySQL does not natively support `INTERSECT` or `EXCEPT` before version 8.0.31. Use JOINs or subqueries instead.

