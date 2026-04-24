# MySQL Complete Notes — Beginner to Advanced

## Table of Contents

1. [Introduction to Databases](#1-introduction-to-databases)
2. [MySQL Installation & Setup](#2-mysql-installation--setup)
3. [MySQL Basics](#3-mysql-basics)
4. [Database Operations](#4-database-operations)
5. [Table Operations](#5-table-operations)
6. [Constraints](#6-constraints)
7. [CRUD Operations](#7-crud-operations)
8. [Data Querying (SELECT Deep Dive)](#8-data-querying-select-deep-dive)
9. [Filtering Data](#9-filtering-data)
10. [Sorting & Grouping](#10-sorting--grouping)
11. [Aggregate Functions](#11-aggregate-functions)
12. [Joins](#12-joins)
13. [Subqueries](#13-subqueries)
14. [Views](#14-views)
15. [Indexes](#15-indexes)
16. [Keys](#16-keys)
17. [Functions](#17-functions)
18. [Stored Procedures](#18-stored-procedures)
19. [Triggers](#19-triggers)
20. [Transactions](#20-transactions)
21. [Normalization](#21-normalization)
22. [Relationships](#22-relationships)
23. [MySQL Security](#23-mysql-security)
24. [Backup & Restore](#24-backup--restore)
25. [Performance Optimization](#25-performance-optimization)
26. [Advanced MySQL Concepts](#26-advanced-mysql-concepts)
27. [MySQL Architecture](#27-mysql-architecture)
28. [JSON Support in MySQL](#28-json-support-in-mysql)
29. [MySQL with Applications](#29-mysql-with-applications)
30. [Real-world Practices](#30-real-world-practices)

---

## 1. Introduction to Databases

### What is a Database?

A **database** is an organized collection of data stored electronically so it can be easily accessed, managed, and updated. Think of it like a digital filing cabinet where you store information in a structured way.

**Example:** A school stores student names, grades, and subjects in a database.

---

### Types of Databases

#### Relational Databases (SQL)
- Store data in **tables** (rows and columns)
- Tables are related to each other
- Use SQL (Structured Query Language)
- Examples: MySQL, PostgreSQL, SQLite, Oracle

#### Non-Relational Databases (NoSQL)
- Store data in **documents, key-value pairs, graphs, or columns**
- More flexible structure
- Examples: MongoDB, Redis, Cassandra, Firebase

| Feature | Relational (SQL) | Non-Relational (NoSQL) |
|---|---|---|
| Structure | Tables | Documents / Key-Value / Graph |
| Schema | Fixed | Flexible |
| Best For | Structured data | Unstructured / large-scale data |
| Examples | MySQL, PostgreSQL | MongoDB, Redis |

---

### What is DBMS?

**DBMS (Database Management System)** is software that helps you create, manage, and interact with a database.

- **Examples:** MySQL, SQLite, Microsoft Access
- It acts as a **middleman** between users and the database
- Handles storing, retrieving, updating, and deleting data

---

### What is RDBMS?

**RDBMS (Relational Database Management System)** is a type of DBMS where data is stored in **tables** and tables can be **related** to each other using keys.

- All relational databases (MySQL, PostgreSQL, Oracle) are RDBMS
- Uses SQL for all operations
- Enforces data integrity through constraints

---

### Features of MySQL

- **Open-source** — free to use
- **Fast and reliable** — handles millions of records
- **Cross-platform** — works on Windows, Linux, Mac
- **Secure** — supports user roles and passwords
- **Scalable** — can grow with your application
- **Supports transactions** — ACID compliant
- **Wide community support**

---

### Use Cases of MySQL

- **Web applications** — WordPress, Facebook (originally), Twitter
- **E-commerce** — storing products, orders, users
- **Banking systems** — transactions, accounts
- **Healthcare** — patient records
- **Django / Flask backends** — most Python web apps

---

### Tips & Tricks

- MySQL is case-insensitive for SQL keywords (`SELECT` = `select`)
- Table and column names are case-sensitive on Linux but not on Windows

---

### Django REST Framework Integration

```python
# settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydb',
        'USER': 'root',
        'PASSWORD': 'yourpassword',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

Install MySQL client:
```bash
pip install mysqlclient
```

---

## 2. MySQL Installation & Setup

### Installing MySQL

#### Windows
1. Download MySQL Installer from https://dev.mysql.com/downloads/installer/
2. Run the installer → Choose "Developer Default"
3. Set root password during setup
4. Complete the installation

#### Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
```

#### Mac
```bash
brew install mysql
brew services start mysql
mysql_secure_installation
```

---

### MySQL Server vs MySQL Client

| | MySQL Server | MySQL Client |
|---|---|---|
| What it is | The actual database engine that runs in background | A tool to connect and send queries to the server |
| Examples | `mysqld` (MySQL Daemon) | `mysql` CLI, MySQL Workbench |
| Role | Stores and processes data | Sends SQL commands to the server |

---

### MySQL Workbench Setup

1. Download from https://dev.mysql.com/downloads/workbench/
2. Open Workbench → Click `+` to add connection
3. Enter: hostname (`localhost`), port (`3306`), username (`root`), password
4. Click "Test Connection" → Connect

---

### Connecting to MySQL via CLI

```bash
mysql -u root -p
# Enter your password when prompted
```

---

### Basic Configuration

```bash
# Check MySQL status
sudo systemctl status mysql

# Start MySQL
sudo systemctl start mysql

# Stop MySQL
sudo systemctl stop mysql

# Restart MySQL
sudo systemctl restart mysql
```

---

### Django REST Framework Integration

```bash
pip install mysqlclient djangorestframework
```

```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydb',
        'USER': 'root',
        'PASSWORD': 'yourpassword',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

---

## 3. MySQL Basics

### SQL Overview

**SQL (Structured Query Language)** is the language used to talk to relational databases. You use SQL to create tables, insert data, update data, and retrieve data.

---

### SQL Categories

#### DDL — Data Definition Language
Defines the **structure** of the database (tables, columns).

| Command | Purpose |
|---|---|
| `CREATE` | Create database or table |
| `ALTER` | Modify existing table |
| `DROP` | Delete table or database |
| `TRUNCATE` | Remove all rows from a table |
| `RENAME` | Rename a table |

```sql
CREATE TABLE students (id INT, name VARCHAR(50));
ALTER TABLE students ADD age INT;
DROP TABLE students;
```

---

#### DML — Data Manipulation Language
Manipulates the **data** inside tables.

| Command | Purpose |
|---|---|
| `INSERT` | Add new rows |
| `UPDATE` | Modify existing rows |
| `DELETE` | Remove rows |

```sql
INSERT INTO students VALUES (1, 'Ali', 20);
UPDATE students SET age = 21 WHERE id = 1;
DELETE FROM students WHERE id = 1;
```

---

#### DQL — Data Query Language
Used to **retrieve** data.

| Command | Purpose |
|---|---|
| `SELECT` | Fetch data from table |

```sql
SELECT * FROM students;
SELECT name, age FROM students WHERE age > 18;
```

---

#### DCL — Data Control Language
Controls **permissions** on data.

| Command | Purpose |
|---|---|
| `GRANT` | Give permission |
| `REVOKE` | Remove permission |

```sql
GRANT SELECT ON mydb.* TO 'user1'@'localhost';
REVOKE SELECT ON mydb.* FROM 'user1'@'localhost';
```

---

#### TCL — Transaction Control Language
Manages **transactions** (groups of operations).

| Command | Purpose |
|---|---|
| `COMMIT` | Save all changes |
| `ROLLBACK` | Undo changes |
| `SAVEPOINT` | Set a point to rollback to |

```sql
START TRANSACTION;
UPDATE accounts SET balance = balance - 500 WHERE id = 1;
COMMIT;
```

---

### Best Practices

- Always use DDL in migrations, not raw SQL in production
- Use DML inside transactions for safety
- Never grant unnecessary permissions (DCL)

---

## 4. Database Operations

### Creating a Database

```sql
CREATE DATABASE school;

-- With character set (recommended)
CREATE DATABASE school
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;
```

---

### Using a Database

```sql
USE school;
```

---

### Dropping a Database

```sql
DROP DATABASE school;

-- Safe drop (won't error if not exists)
DROP DATABASE IF EXISTS school;
```

---

### Renaming a Database

MySQL does **not** support `RENAME DATABASE` directly. Workaround:
```bash
# In terminal: dump old, create new, import
mysqldump old_db | mysql new_db
```

---

### Showing Databases

```sql
SHOW DATABASES;

-- Show current database
SELECT DATABASE();
```

---

### Tips & Tricks

- Always use `utf8mb4` charset to support emojis and all Unicode characters
- Use `IF EXISTS` / `IF NOT EXISTS` to avoid errors in scripts

---

### Django Integration

Django automatically creates a database if it doesn't exist when you run:
```bash
python manage.py migrate
```

---

## 5. Table Operations

### Creating Tables

```sql
CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INT,
    email VARCHAR(150) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

### Data Types

#### Numeric Types

| Type | Description | Example |
|---|---|---|
| `TINYINT` | Very small integer (-128 to 127) | Age |
| `INT` | Standard integer | ID, count |
| `BIGINT` | Very large integer | Population |
| `FLOAT` | Decimal (approximate) | Temperature |
| `DOUBLE` | Large decimal (approximate) | Scientific data |
| `DECIMAL(p,s)` | Exact decimal | Price (10,2) |

```sql
price DECIMAL(10, 2)   -- 10 digits total, 2 after decimal
```

---

#### String Types

| Type | Description | Max Size |
|---|---|---|
| `CHAR(n)` | Fixed length string | 255 chars |
| `VARCHAR(n)` | Variable length string | 65535 chars |
| `TEXT` | Long text | 65535 bytes |
| `MEDIUMTEXT` | Medium long text | 16 MB |
| `LONGTEXT` | Very long text | 4 GB |
| `ENUM` | One of a list | Custom |

```sql
gender ENUM('Male', 'Female', 'Other')
```

---

#### Date & Time Types

| Type | Format | Example |
|---|---|---|
| `DATE` | YYYY-MM-DD | 2024-01-15 |
| `TIME` | HH:MM:SS | 14:30:00 |
| `DATETIME` | YYYY-MM-DD HH:MM:SS | 2024-01-15 14:30:00 |
| `TIMESTAMP` | YYYY-MM-DD HH:MM:SS (UTC) | Auto-updates |
| `YEAR` | YYYY | 2024 |

```sql
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
```

---

#### Boolean Type

MySQL doesn't have a true BOOLEAN type — it uses `TINYINT(1)`:
- `0` = FALSE
- `1` = TRUE

```sql
is_active BOOLEAN DEFAULT TRUE
-- Same as: is_active TINYINT(1) DEFAULT 1
```

---

### Altering Tables

```sql
-- Add column
ALTER TABLE students ADD COLUMN phone VARCHAR(15);

-- Modify column
ALTER TABLE students MODIFY COLUMN phone VARCHAR(20);

-- Drop column
ALTER TABLE students DROP COLUMN phone;

-- Rename column
ALTER TABLE students RENAME COLUMN name TO full_name;
```

---

### Dropping Tables

```sql
DROP TABLE students;
DROP TABLE IF EXISTS students;
```

---

### Renaming Tables

```sql
RENAME TABLE students TO learners;
ALTER TABLE learners RENAME TO students;
```

---

### TRUNCATE vs DELETE

| Feature | TRUNCATE | DELETE |
|---|---|---|
| Removes all rows | Yes | Yes (if no WHERE) |
| Can use WHERE | No | Yes |
| Resets AUTO_INCREMENT | Yes | No |
| Can be rolled back | No (DDL) | Yes (DML) |
| Speed | Faster | Slower |

```sql
TRUNCATE TABLE students;         -- Remove all rows, reset ID
DELETE FROM students;            -- Remove all rows, keep ID sequence
DELETE FROM students WHERE id=1; -- Remove specific row
```

---

### Best Practices

- Always define a `PRIMARY KEY` in every table
- Use `VARCHAR` instead of `CHAR` unless you need fixed length
- Use `DECIMAL` for money, not `FLOAT`
- Always include `created_at` and `updated_at` timestamps

---

### Django Integration

```python
# models.py
from django.db import models

class Student(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()
    email = models.EmailField(unique=True)
    is_active = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        db_table = 'students'
```

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 6. Constraints

Constraints are **rules** you apply to columns to make sure only valid data is stored.

---

### NOT NULL

Column cannot be empty.

```sql
name VARCHAR(100) NOT NULL
```

---

### UNIQUE

All values in the column must be different.

```sql
email VARCHAR(150) UNIQUE
```

---

### PRIMARY KEY

Uniquely identifies each row. Combination of NOT NULL + UNIQUE. Only one per table.

```sql
id INT PRIMARY KEY
-- or
id INT AUTO_INCREMENT PRIMARY KEY
```

---

### FOREIGN KEY

Links a column in one table to the PRIMARY KEY of another table. Enforces **referential integrity**.

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    student_id INT,
    FOREIGN KEY (student_id) REFERENCES students(id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```

**ON DELETE / ON UPDATE options:**
- `CASCADE` — automatically delete/update related rows
- `SET NULL` — set to NULL
- `RESTRICT` — prevent deletion/update if reference exists
- `NO ACTION` — same as RESTRICT

---

### DEFAULT

Sets a default value if none is provided.

```sql
is_active BOOLEAN DEFAULT TRUE,
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
```

---

### CHECK

Ensures value meets a condition.

```sql
age INT CHECK (age >= 0 AND age <= 120),
gender ENUM('Male', 'Female', 'Other')
```

---

### AUTO_INCREMENT

Automatically increases value by 1 for each new row.

```sql
id INT AUTO_INCREMENT PRIMARY KEY
```

Start from a custom value:
```sql
AUTO_INCREMENT = 1000
```

---

### Composite Keys

A primary key made of **two or more columns** together.

```sql
CREATE TABLE enrollment (
    student_id INT,
    course_id INT,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(id),
    FOREIGN KEY (course_id) REFERENCES courses(id)
);
```

---

### Tips & Tricks

- Use `FOREIGN KEY ... ON DELETE CASCADE` to auto-delete related records
- Composite keys are useful for many-to-many relationships
- `CHECK` constraints were fully supported only from MySQL 8.0+

---

### Best Practices

- Every table should have a PRIMARY KEY
- Use FOREIGN KEYs to maintain data integrity
- Use NOT NULL wherever data is required
- Don't over-index — only add constraints that are needed

---

### Django Integration

```python
class Order(models.Model):
    student = models.ForeignKey(
        Student,
        on_delete=models.CASCADE,
        related_name='orders'
    )
    amount = models.DecimalField(max_digits=10, decimal_places=2)
```

---

## 7. CRUD Operations

**CRUD** = Create, Read, Update, Delete — the four basic operations on data.

---

### INSERT

```sql
-- Insert one row
INSERT INTO students (name, age, email)
VALUES ('Ali', 20, 'ali@example.com');

-- Insert multiple rows
INSERT INTO students (name, age, email) VALUES
('Sara', 22, 'sara@example.com'),
('John', 25, 'john@example.com');
```

---

### SELECT

```sql
-- All columns, all rows
SELECT * FROM students;

-- Specific columns
SELECT name, age FROM students;

-- With condition
SELECT * FROM students WHERE age > 18;
```

---

### UPDATE

```sql
-- Update one column
UPDATE students SET age = 21 WHERE id = 1;

-- Update multiple columns
UPDATE students
SET age = 21, email = 'new@example.com'
WHERE id = 1;
```

> **Warning:** Always use `WHERE` with UPDATE. Without it, all rows get updated!

---

### DELETE

```sql
-- Delete specific row
DELETE FROM students WHERE id = 1;

-- Delete all rows
DELETE FROM students;
```

> **Warning:** Always use `WHERE` with DELETE. Without it, all rows are deleted!

---

### Tips & Tricks

- Always use WHERE in UPDATE and DELETE
- Use transactions when doing multiple DML operations together
- `INSERT IGNORE` skips rows that would cause duplicate errors
- `INSERT ... ON DUPLICATE KEY UPDATE` updates if record exists

```sql
INSERT INTO students (id, name) VALUES (1, 'Ali')
ON DUPLICATE KEY UPDATE name = 'Ali Updated';
```

---

### Django REST Framework Integration

```python
# serializers.py
from rest_framework import serializers
from .models import Student

class StudentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        fields = '__all__'

# views.py
from rest_framework import viewsets
from .models import Student
from .serializers import StudentSerializer

class StudentViewSet(viewsets.ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentSerializer

# urls.py
from rest_framework.routers import DefaultRouter
from .views import StudentViewSet

router = DefaultRouter()
router.register(r'students', StudentViewSet)
urlpatterns = router.urls
```

API endpoints generated:
- `GET /students/` — list all
- `POST /students/` — create
- `GET /students/1/` — retrieve
- `PUT /students/1/` — update
- `DELETE /students/1/` — delete

---

## 8. Data Querying (SELECT Deep Dive)

### Selecting Specific Columns

```sql
SELECT name, age FROM students;
```

---

### Aliases

Give a column or table a temporary name using `AS`.

```sql
SELECT name AS student_name, age AS student_age FROM students;
SELECT s.name FROM students AS s;
```

---

### DISTINCT

Remove duplicate values.

```sql
SELECT DISTINCT city FROM students;
```

---

### LIMIT

Fetch only a specific number of rows.

```sql
SELECT * FROM students LIMIT 5;
```

---

### OFFSET

Skip a certain number of rows (used for pagination).

```sql
-- Skip first 10 rows, get next 5
SELECT * FROM students LIMIT 5 OFFSET 10;

-- Short form
SELECT * FROM students LIMIT 10, 5;
```

---

### Pagination Example

```sql
-- Page 1: rows 1-10
SELECT * FROM students LIMIT 10 OFFSET 0;

-- Page 2: rows 11-20
SELECT * FROM students LIMIT 10 OFFSET 10;
```

---

### Tips & Tricks

- Always use `LIMIT` when testing queries on large tables
- Use `DISTINCT` carefully — it can be slow on large datasets
- Pagination: `OFFSET = (page_number - 1) * page_size`

---

### Django Integration

```python
# Pagination in DRF
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10
}

# In views
from rest_framework.pagination import PageNumberPagination

class StudentList(generics.ListAPIView):
    queryset = Student.objects.values('name', 'age').distinct()
    serializer_class = StudentSerializer
    pagination_class = PageNumberPagination
```

---

## 9. Filtering Data

### WHERE Clause

Filters rows based on a condition.

```sql
SELECT * FROM students WHERE age = 20;
SELECT * FROM students WHERE city = 'Delhi';
```

---

### Comparison Operators

| Operator | Meaning |
|---|---|
| `=` | Equal |
| `!=` or `<>` | Not equal |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal |
| `<=` | Less than or equal |

```sql
SELECT * FROM students WHERE age >= 18;
SELECT * FROM students WHERE age != 20;
```

---

### Logical Operators (AND, OR, NOT)

```sql
-- AND: both conditions must be true
SELECT * FROM students WHERE age > 18 AND city = 'Delhi';

-- OR: at least one condition must be true
SELECT * FROM students WHERE city = 'Delhi' OR city = 'Mumbai';

-- NOT: negate the condition
SELECT * FROM students WHERE NOT city = 'Delhi';
```

---

### BETWEEN

Filter rows within a range (inclusive).

```sql
SELECT * FROM students WHERE age BETWEEN 18 AND 25;
SELECT * FROM orders WHERE created_at BETWEEN '2024-01-01' AND '2024-12-31';
```

---

### IN

Match against a list of values.

```sql
SELECT * FROM students WHERE city IN ('Delhi', 'Mumbai', 'Pune');
SELECT * FROM students WHERE city NOT IN ('Delhi', 'Mumbai');
```

---

### LIKE

Pattern matching for strings.

| Pattern | Meaning |
|---|---|
| `%` | Any number of characters |
| `_` | Exactly one character |

```sql
-- Starts with 'A'
SELECT * FROM students WHERE name LIKE 'A%';

-- Ends with 'i'
SELECT * FROM students WHERE name LIKE '%i';

-- Contains 'ali'
SELECT * FROM students WHERE name LIKE '%ali%';

-- Second character is 'a'
SELECT * FROM students WHERE name LIKE '_a%';
```

---

### IS NULL

Check for NULL values.

```sql
SELECT * FROM students WHERE phone IS NULL;
SELECT * FROM students WHERE phone IS NOT NULL;
```

---

### Tips & Tricks

- `LIKE '%text%'` is slow — avoid on large tables; use FULLTEXT index instead
- `NULL` cannot be compared with `=`. Always use `IS NULL`
- Use `IN` instead of multiple `OR` conditions — it's cleaner

---

### Django Integration

```python
# Filtering in DRF
from django_filters.rest_framework import DjangoFilterBackend

class StudentViewSet(viewsets.ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentSerializer
    filter_backends = [DjangoFilterBackend]
    filterset_fields = ['city', 'age']

# ORM equivalents:
Student.objects.filter(age__gte=18)         # WHERE age >= 18
Student.objects.filter(city__in=['Delhi'])   # WHERE city IN ('Delhi')
Student.objects.filter(name__icontains='ali') # WHERE name LIKE '%ali%'
Student.objects.filter(phone__isnull=True)   # WHERE phone IS NULL
```

---

## 10. Sorting & Grouping

### ORDER BY

Sort results in ascending or descending order.

```sql
-- Ascending (default)
SELECT * FROM students ORDER BY name ASC;

-- Descending
SELECT * FROM students ORDER BY age DESC;

-- Sort by multiple columns
SELECT * FROM students ORDER BY city ASC, age DESC;
```

---

### GROUP BY

Group rows that have the same value in a column. Used with aggregate functions.

```sql
-- Count students per city
SELECT city, COUNT(*) AS total
FROM students
GROUP BY city;

-- Average age per city
SELECT city, AVG(age) AS avg_age
FROM students
GROUP BY city;
```

---

### HAVING

Filter groups (like WHERE, but for groups). Used after GROUP BY.

```sql
-- Cities with more than 5 students
SELECT city, COUNT(*) AS total
FROM students
GROUP BY city
HAVING total > 5;

-- Cities where average age > 20
SELECT city, AVG(age) AS avg_age
FROM students
GROUP BY city
HAVING avg_age > 20;
```

---

### WHERE vs HAVING

| | WHERE | HAVING |
|---|---|---|
| Filters | Rows | Groups |
| Used with | Regular columns | Aggregate functions |
| Runs | Before GROUP BY | After GROUP BY |

```sql
-- WHERE filters rows first, then GROUP BY groups, then HAVING filters groups
SELECT city, COUNT(*) AS total
FROM students
WHERE age > 18          -- filter rows first
GROUP BY city
HAVING total > 3;       -- then filter groups
```

---

### Tips & Tricks

- Always use ORDER BY with LIMIT for predictable pagination
- HAVING cannot use column aliases defined outside aggregate functions in some cases — use full expression if needed
- GROUP BY requires all non-aggregated SELECT columns to be in GROUP BY

---

### Django Integration

```python
from django.db.models import Count, Avg

# GROUP BY city with COUNT
Student.objects.values('city').annotate(total=Count('id'))

# HAVING: cities with more than 5 students
Student.objects.values('city').annotate(total=Count('id')).filter(total__gt=5)

# ORDER BY
Student.objects.order_by('-age')  # Descending
Student.objects.order_by('city', '-age')  # Multiple
```

---

## 11. Aggregate Functions

Aggregate functions perform calculations on **multiple rows** and return a **single value**.

---

### COUNT

Count the number of rows.

```sql
SELECT COUNT(*) FROM students;                    -- All rows
SELECT COUNT(phone) FROM students;                -- Non-NULL phone values
SELECT COUNT(DISTINCT city) FROM students;        -- Distinct cities
```

---

### SUM

Add up all values in a column.

```sql
SELECT SUM(salary) FROM employees;
SELECT department, SUM(salary) FROM employees GROUP BY department;
```

---

### AVG

Calculate the average value.

```sql
SELECT AVG(age) FROM students;
SELECT department, AVG(salary) FROM employees GROUP BY department;
```

---

### MIN

Find the minimum value.

```sql
SELECT MIN(age) FROM students;
SELECT MIN(salary) FROM employees;
```

---

### MAX

Find the maximum value.

```sql
SELECT MAX(age) FROM students;
SELECT MAX(salary) FROM employees;
```

---

### Combined Example

```sql
SELECT
    department,
    COUNT(*) AS total_employees,
    SUM(salary) AS total_salary,
    AVG(salary) AS avg_salary,
    MIN(salary) AS min_salary,
    MAX(salary) AS max_salary
FROM employees
GROUP BY department;
```

---

### Tips & Tricks

- `COUNT(*)` counts all rows including NULLs; `COUNT(column)` skips NULLs
- `AVG` ignores NULL values
- Use `ROUND(AVG(salary), 2)` to round results

---

### Django Integration

```python
from django.db.models import Count, Sum, Avg, Min, Max

Student.objects.aggregate(total=Count('id'))
Student.objects.aggregate(avg_age=Avg('age'))
Employee.objects.values('department').annotate(
    total=Count('id'),
    total_salary=Sum('salary'),
    avg_salary=Avg('salary')
)
```

---

## 12. Joins

**Joins** combine rows from two or more tables based on a related column.

---

### Setup for Examples

```sql
-- students table: id, name, city_id
-- cities table: id, city_name
```

---

### INNER JOIN

Returns only rows where there is a **match in both tables**.

```sql
SELECT students.name, cities.city_name
FROM students
INNER JOIN cities ON students.city_id = cities.id;
```

---

### LEFT JOIN

Returns **all rows from the left table** + matching rows from the right. If no match, right side is NULL.

```sql
SELECT students.name, cities.city_name
FROM students
LEFT JOIN cities ON students.city_id = cities.id;
-- All students shown; city_name = NULL if no matching city
```

---

### RIGHT JOIN

Returns **all rows from the right table** + matching rows from the left. If no match, left side is NULL.

```sql
SELECT students.name, cities.city_name
FROM students
RIGHT JOIN cities ON students.city_id = cities.id;
-- All cities shown; name = NULL if no student from that city
```

---

### FULL JOIN (Workaround)

MySQL doesn't have FULL JOIN. Use UNION of LEFT + RIGHT JOIN.

```sql
SELECT students.name, cities.city_name
FROM students LEFT JOIN cities ON students.city_id = cities.id
UNION
SELECT students.name, cities.city_name
FROM students RIGHT JOIN cities ON students.city_id = cities.id;
```

---

### CROSS JOIN

Returns **all combinations** of rows from both tables (Cartesian product).

```sql
SELECT s.name, c.city_name
FROM students s
CROSS JOIN cities c;
-- If 5 students and 4 cities: returns 5*4 = 20 rows
```

---

### SELF JOIN

Join a table **with itself**. Useful for hierarchical data (e.g., employees and their managers).

```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
INNER JOIN employees m ON e.manager_id = m.id;
```

---

### Join Summary

| Join Type | Returns |
|---|---|
| INNER JOIN | Only matching rows from both tables |
| LEFT JOIN | All left rows + matching right rows |
| RIGHT JOIN | All right rows + matching left rows |
| FULL JOIN | All rows from both tables |
| CROSS JOIN | All combinations |
| SELF JOIN | Table joined with itself |

---

### Tips & Tricks

- Always use table aliases for readability in joins
- Use `INNER JOIN` when you only want matched data
- Use `LEFT JOIN` when you want all records from the main table
- Avoid `CROSS JOIN` on large tables — result can be huge

---

### Django Integration

```python
# Django ORM handles joins automatically via ForeignKey
# select_related = INNER/LEFT JOIN for ForeignKey
students = Student.objects.select_related('city').all()

# prefetch_related = separate query for ManyToMany
students = Student.objects.prefetch_related('courses').all()

# Raw SQL join
from django.db import connection
with connection.cursor() as cursor:
    cursor.execute("""
        SELECT s.name, c.city_name
        FROM students s
        INNER JOIN cities c ON s.city_id = c.id
    """)
    rows = cursor.fetchall()
```

---

## 13. Subqueries

A **subquery** is a query inside another query.

---

### Single Row Subquery

Returns one row, one column. Use with `=`, `>`, `<` etc.

```sql
-- Find student with the highest age
SELECT name FROM students
WHERE age = (SELECT MAX(age) FROM students);
```

---

### Multiple Row Subquery

Returns multiple rows. Use with `IN`, `ANY`, `ALL`.

```sql
-- Students who live in top 3 cities
SELECT name FROM students
WHERE city_id IN (SELECT id FROM cities LIMIT 3);

-- Students older than ANY manager
SELECT name FROM employees
WHERE salary > ANY (SELECT salary FROM employees WHERE role = 'Manager');
```

---

### Correlated Subquery

The inner query **uses values from the outer query**. Runs once per row.

```sql
-- Students whose age is above the average age of their city
SELECT name, age, city_id
FROM students s
WHERE age > (
    SELECT AVG(age)
    FROM students
    WHERE city_id = s.city_id
);
```

---

### Nested Subqueries

Subquery inside a subquery.

```sql
SELECT name FROM students
WHERE city_id IN (
    SELECT id FROM cities
    WHERE country_id = (
        SELECT id FROM countries WHERE name = 'India'
    )
);
```

---

### Subquery in FROM (Derived Table)

```sql
SELECT avg_data.city_id, avg_data.avg_age
FROM (
    SELECT city_id, AVG(age) AS avg_age
    FROM students
    GROUP BY city_id
) AS avg_data
WHERE avg_data.avg_age > 20;
```

---

### Tips & Tricks

- Use JOINs instead of subqueries when possible — JOINs are usually faster
- Correlated subqueries are slow on large tables — consider JOIN or CTE
- Use `EXISTS` instead of `IN` for large subquery results

```sql
-- EXISTS is often faster than IN for large datasets
SELECT name FROM students s
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.student_id = s.id
);
```

---

### Django Integration

```python
from django.db.models import Subquery, OuterRef

# Subquery with OuterRef (correlated subquery)
from django.db.models import Subquery, OuterRef, Avg

city_avg = Student.objects.filter(
    city_id=OuterRef('city_id')
).values('city_id').annotate(avg=Avg('age')).values('avg')

students = Student.objects.annotate(city_avg_age=Subquery(city_avg))
```

---

## 14. Views

A **View** is a **virtual table** based on a SQL query. It doesn't store data itself — it shows data from one or more tables.

### Why Use Views?

- Simplify complex queries
- Reuse query logic
- Hide sensitive columns from users
- Provide a layer of security

---

### Creating Views

```sql
CREATE VIEW student_summary AS
SELECT s.name, s.age, c.city_name
FROM students s
INNER JOIN cities c ON s.city_id = c.id;

-- Use it like a table
SELECT * FROM student_summary;
SELECT * FROM student_summary WHERE age > 20;
```

---

### Updating Views

```sql
CREATE OR REPLACE VIEW student_summary AS
SELECT s.name, s.age, c.city_name, s.email
FROM students s
INNER JOIN cities c ON s.city_id = c.id;
```

---

### Dropping Views

```sql
DROP VIEW student_summary;
DROP VIEW IF EXISTS student_summary;
```

---

### Updatable Views

You can INSERT/UPDATE/DELETE through a view if it's based on a **single table** with no aggregations.

```sql
CREATE VIEW adult_students AS
SELECT id, name, age FROM students WHERE age >= 18;

UPDATE adult_students SET age = 22 WHERE id = 1;
```

---

### Tips & Tricks

- Views don't improve performance — the query still runs each time
- Use `WITH CHECK OPTION` to prevent inserting rows that wouldn't appear in the view
- For better performance, use **Materialized Views** (not natively supported in MySQL; use a summary table instead)

---

### Best Practices

- Use views for reporting queries
- Use views to restrict user access to sensitive columns
- Don't use views inside views (bad performance)

---

## 15. Indexes

An **Index** is like a book's index — it helps MySQL find data faster without scanning every row.

### Why Use Indexes?

- Speed up SELECT queries
- Speed up WHERE, ORDER BY, JOIN operations
- Without index: **Full table scan** (slow)
- With index: **Index seek** (fast)

---

### Types of Indexes

#### Primary Index

Automatically created when you define a PRIMARY KEY.

```sql
id INT AUTO_INCREMENT PRIMARY KEY
-- Primary index is created automatically
```

---

#### Unique Index

Ensures all values are unique. Created with UNIQUE constraint.

```sql
CREATE UNIQUE INDEX idx_email ON students(email);
-- or
email VARCHAR(150) UNIQUE
```

---

#### Composite Index

Index on two or more columns.

```sql
CREATE INDEX idx_name_city ON students(name, city_id);
-- Most effective when querying by name first, then city
```

---

#### Full-Text Index

For fast text searching.

```sql
CREATE FULLTEXT INDEX idx_bio ON students(bio);

-- Use with MATCH...AGAINST
SELECT * FROM students
WHERE MATCH(bio) AGAINST('developer python' IN BOOLEAN MODE);
```

---

### Creating and Dropping Indexes

```sql
-- Create index
CREATE INDEX idx_age ON students(age);

-- Show indexes
SHOW INDEX FROM students;

-- Drop index
DROP INDEX idx_age ON students;
ALTER TABLE students DROP INDEX idx_age;
```

---

### Index Optimization Tips

- Index columns used in WHERE, JOIN, ORDER BY
- Don't index every column — indexes slow down INSERT/UPDATE
- Composite index: put the most selective column first
- Use `EXPLAIN` to check if indexes are being used

```sql
EXPLAIN SELECT * FROM students WHERE age = 20;
```

---

### Advantages & Disadvantages

| Advantages | Disadvantages |
|---|---|
| Speeds up read queries | Slows down INSERT/UPDATE/DELETE |
| Improves JOIN performance | Takes extra disk space |
| Helps ORDER BY | Too many indexes = overhead |

---

### Django Integration

```python
class Student(models.Model):
    name = models.CharField(max_length=100, db_index=True)
    email = models.EmailField(unique=True)
    age = models.IntegerField()

    class Meta:
        indexes = [
            models.Index(fields=['name', 'city']),  # Composite
        ]
```

---

## 16. Keys

### Primary Key

- Uniquely identifies each row
- Cannot be NULL
- Only one per table
- Usually an auto-increment integer

```sql
id INT AUTO_INCREMENT PRIMARY KEY
```

---

### Foreign Key

- Links to PRIMARY KEY of another table
- Maintains referential integrity
- Can be NULL (optional relationship)

```sql
FOREIGN KEY (student_id) REFERENCES students(id)
```

---

### Candidate Key

- A column (or set of columns) that **could** be the primary key
- Uniquely identifies a row
- A table can have multiple candidate keys
- **Example:** `email` and `phone` can both be candidate keys; only one becomes the primary key

---

### Super Key

- Any combination of columns that can uniquely identify a row
- Includes primary key, candidate keys, and any superset of them
- **Example:** `(id)`, `(id, name)`, `(email)` are all super keys

---

### Key Hierarchy

```
Super Key → includes all unique identifier combinations
  └── Candidate Key → minimal super key (no extra columns)
        └── Primary Key → chosen candidate key (the actual PK)
```

---

### Django Integration

```python
class Course(models.Model):
    code = models.CharField(max_length=10, unique=True)  # Candidate key
    name = models.CharField(max_length=100)

class Enrollment(models.Model):
    student = models.ForeignKey(Student, on_delete=models.CASCADE)  # FK
    course = models.ForeignKey(Course, on_delete=models.CASCADE)    # FK

    class Meta:
        unique_together = ('student', 'course')  # Composite PK alternative
```

---

## 17. Functions

### Built-in String Functions

```sql
SELECT UPPER('hello');              -- HELLO
SELECT LOWER('HELLO');              -- hello
SELECT LENGTH('hello');             -- 5
SELECT CHAR_LENGTH('hello');        -- 5 (better for Unicode)
SELECT CONCAT('Ali', ' ', 'Khan');  -- Ali Khan
SELECT SUBSTRING('Hello', 2, 3);   -- ell (start, length)
SELECT TRIM('  hello  ');           -- hello
SELECT LTRIM('  hello');            -- hello
SELECT RTRIM('hello  ');            -- hello
SELECT REPLACE('Hello World', 'World', 'MySQL'); -- Hello MySQL
SELECT REVERSE('hello');            -- olleh
SELECT LEFT('hello', 3);            -- hel
SELECT RIGHT('hello', 3);           -- llo
SELECT LPAD('5', 3, '0');           -- 005
SELECT RPAD('5', 3, '0');           -- 500
SELECT INSTR('hello world', 'world'); -- 7 (position)
```

---

### Built-in Numeric Functions

```sql
SELECT ROUND(3.14159, 2);   -- 3.14
SELECT CEIL(3.2);            -- 4
SELECT FLOOR(3.9);           -- 3
SELECT ABS(-10);             -- 10
SELECT MOD(10, 3);           -- 1
SELECT POWER(2, 8);          -- 256
SELECT SQRT(16);             -- 4
SELECT RAND();               -- Random 0-1
SELECT TRUNCATE(3.14159, 2); -- 3.14 (no rounding)
```

---

### Built-in Date Functions

```sql
SELECT NOW();                            -- 2024-01-15 14:30:00
SELECT CURDATE();                        -- 2024-01-15
SELECT CURTIME();                        -- 14:30:00
SELECT DATE('2024-01-15 14:30:00');      -- 2024-01-15
SELECT YEAR(NOW());                      -- 2024
SELECT MONTH(NOW());                     -- 1
SELECT DAY(NOW());                       -- 15
SELECT DAYNAME(NOW());                   -- Monday
SELECT MONTHNAME(NOW());                 -- January
SELECT DATEDIFF('2024-12-31', '2024-01-01'); -- 365
SELECT DATE_ADD(NOW(), INTERVAL 7 DAY);
SELECT DATE_FORMAT(NOW(), '%d/%m/%Y');   -- 15/01/2024
```

---

### User-Defined Functions (UDF)

```sql
DELIMITER //

CREATE FUNCTION full_name(fname VARCHAR(50), lname VARCHAR(50))
RETURNS VARCHAR(100)
DETERMINISTIC
BEGIN
    RETURN CONCAT(fname, ' ', lname);
END //

DELIMITER ;

-- Usage
SELECT full_name('Ali', 'Khan');  -- Ali Khan
```

---

### Tips & Tricks

- Use `CHAR_LENGTH()` instead of `LENGTH()` for UTF-8 strings (emojis, Arabic, Hindi)
- `DATE_FORMAT()` is very useful for reports
- User-defined functions must be `DETERMINISTIC` (same input → same output) or `NOT DETERMINISTIC`

---

### Django Integration

```python
from django.db.models.functions import Upper, Length, Concat, Now
from django.db.models import F, Value, CharField

Student.objects.annotate(
    upper_name=Upper('name'),
    name_length=Length('name'),
    full_label=Concat('name', Value(' - '), 'city', output_field=CharField())
)
```

---

## 18. Stored Procedures

A **Stored Procedure** is a saved block of SQL code that you can **call by name**. Like a function but for SQL operations.

### Why Use Stored Procedures?

- Reuse complex queries
- Reduce network traffic (logic runs on DB server)
- Better security (grant EXECUTE instead of table access)
- Faster (pre-compiled)

---

### Creating Procedures

```sql
DELIMITER //

CREATE PROCEDURE get_all_students()
BEGIN
    SELECT * FROM students;
END //

DELIMITER ;
```

---

### Calling Procedures

```sql
CALL get_all_students();
```

---

### Parameters: IN, OUT, INOUT

#### IN — Input parameter

```sql
DELIMITER //
CREATE PROCEDURE get_student_by_age(IN min_age INT)
BEGIN
    SELECT * FROM students WHERE age >= min_age;
END //
DELIMITER ;

CALL get_student_by_age(18);
```

#### OUT — Output parameter

```sql
DELIMITER //
CREATE PROCEDURE count_students(OUT total INT)
BEGIN
    SELECT COUNT(*) INTO total FROM students;
END //
DELIMITER ;

CALL count_students(@total);
SELECT @total;
```

#### INOUT — Both input and output

```sql
DELIMITER //
CREATE PROCEDURE double_value(INOUT val INT)
BEGIN
    SET val = val * 2;
END //
DELIMITER ;

SET @num = 5;
CALL double_value(@num);
SELECT @num;  -- 10
```

---

### Drop a Procedure

```sql
DROP PROCEDURE IF EXISTS get_all_students;
```

---

### Tips & Tricks

- Use `DELIMITER //` to change the statement delimiter temporarily
- Store business logic in procedures for better security
- Debug with `SELECT` statements inside the procedure

---

### Django Integration

```python
from django.db import connection

def get_students_by_age(min_age):
    with connection.cursor() as cursor:
        cursor.callproc('get_student_by_age', [min_age])
        rows = cursor.fetchall()
    return rows
```

---

## 19. Triggers

A **Trigger** is a SQL block that **automatically runs** when a specific event happens on a table (INSERT, UPDATE, DELETE).

### Trigger Events

| Trigger | When It Fires |
|---|---|
| `BEFORE INSERT` | Before a new row is inserted |
| `AFTER INSERT` | After a new row is inserted |
| `BEFORE UPDATE` | Before a row is updated |
| `AFTER UPDATE` | After a row is updated |
| `BEFORE DELETE` | Before a row is deleted |
| `AFTER DELETE` | After a row is deleted |

---

### BEFORE INSERT

```sql
DELIMITER //
CREATE TRIGGER before_student_insert
BEFORE INSERT ON students
FOR EACH ROW
BEGIN
    SET NEW.name = UPPER(NEW.name);  -- Force name to uppercase
END //
DELIMITER ;
```

---

### AFTER INSERT

```sql
DELIMITER //
CREATE TRIGGER after_student_insert
AFTER INSERT ON students
FOR EACH ROW
BEGIN
    INSERT INTO audit_log (action, table_name, record_id, created_at)
    VALUES ('INSERT', 'students', NEW.id, NOW());
END //
DELIMITER ;
```

---

### BEFORE UPDATE

```sql
DELIMITER //
CREATE TRIGGER before_student_update
BEFORE UPDATE ON students
FOR EACH ROW
BEGIN
    SET NEW.updated_at = NOW();
END //
DELIMITER ;
```

---

### AFTER DELETE

```sql
DELIMITER //
CREATE TRIGGER after_student_delete
AFTER DELETE ON students
FOR EACH ROW
BEGIN
    INSERT INTO deleted_records (original_id, name, deleted_at)
    VALUES (OLD.id, OLD.name, NOW());
END //
DELIMITER ;
```

---

### OLD vs NEW

| Keyword | Available In | Meaning |
|---|---|---|
| `NEW` | INSERT, UPDATE | New values being inserted/updated |
| `OLD` | UPDATE, DELETE | Old values before change/deletion |

---

### Show and Drop Triggers

```sql
SHOW TRIGGERS;
DROP TRIGGER IF EXISTS after_student_insert;
```

---

### Tips & Tricks

- Use triggers for audit logging
- Don't use triggers for complex business logic — they're hard to debug
- Triggers run automatically — be careful with recursive triggers

---

## 20. Transactions

A **Transaction** is a group of SQL operations that run together. Either **all succeed** or **all fail** — no partial updates.

### ACID Properties

| Property | Meaning |
|---|---|
| **A**tomicity | All or nothing — either all operations succeed or all fail |
| **C**onsistency | Database stays in a valid state before and after transaction |
| **I**solation | Transactions don't interfere with each other |
| **D**urability | Once committed, changes are permanent (even if system crashes) |

---

### COMMIT

Save all changes permanently.

```sql
START TRANSACTION;
UPDATE accounts SET balance = balance - 500 WHERE id = 1;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;
COMMIT;  -- Both changes saved
```

---

### ROLLBACK

Undo all changes since the transaction started.

```sql
START TRANSACTION;
UPDATE accounts SET balance = balance - 500 WHERE id = 1;
-- Something went wrong!
ROLLBACK;  -- Both changes undone
```

---

### SAVEPOINT

Create a checkpoint inside a transaction to partially rollback.

```sql
START TRANSACTION;
INSERT INTO orders VALUES (1, 'Product A', 100);
SAVEPOINT sp1;

INSERT INTO orders VALUES (2, 'Product B', 200);
-- Product B has an issue, rollback to savepoint
ROLLBACK TO SAVEPOINT sp1;

-- Product A is still pending
COMMIT;  -- Only Product A is saved
```

---

### Autocommit

By default, MySQL auto-commits every statement.

```sql
-- Disable autocommit
SET autocommit = 0;

-- Enable autocommit
SET autocommit = 1;
```

---

### Tips & Tricks

- Always use transactions for financial operations
- Keep transactions short — long transactions lock tables
- InnoDB supports transactions; MyISAM does NOT

---

### Django Integration

```python
from django.db import transaction

# Method 1: decorator
@transaction.atomic
def transfer_money(from_id, to_id, amount):
    Account.objects.filter(id=from_id).update(balance=F('balance') - amount)
    Account.objects.filter(id=to_id).update(balance=F('balance') + amount)

# Method 2: context manager
with transaction.atomic():
    Account.objects.filter(id=from_id).update(balance=F('balance') - amount)
    Account.objects.filter(id=to_id).update(balance=F('balance') + amount)

# With savepoint
with transaction.atomic():
    order = Order.objects.create(...)
    sid = transaction.savepoint()
    try:
        Payment.objects.create(...)
    except Exception:
        transaction.savepoint_rollback(sid)
```

---

## 21. Normalization

**Normalization** is the process of organizing a database to **reduce data redundancy** and improve data integrity.

---

### 1NF — First Normal Form

**Rule:** Each column must contain **atomic (indivisible) values**. No repeating groups.

**Bad (Not 1NF):**
| student_id | name | subjects |
|---|---|---|
| 1 | Ali | Math, Physics, English |

**Good (1NF):**
| student_id | name | subject |
|---|---|---|
| 1 | Ali | Math |
| 1 | Ali | Physics |
| 1 | Ali | English |

---

### 2NF — Second Normal Form

**Rule:** Must be in 1NF + **no partial dependency** (non-key columns must depend on the WHOLE primary key, not part of it).

Applicable only when there's a **composite primary key**.

**Bad (Not 2NF):**
| student_id | course_id | student_name | course_name |
|---|---|---|---|
- `student_name` depends only on `student_id` (partial dependency)
- `course_name` depends only on `course_id` (partial dependency)

**Good (2NF):**
- Students table: `(student_id, student_name)`
- Courses table: `(course_id, course_name)`
- Enrollment table: `(student_id, course_id)`

---

### 3NF — Third Normal Form

**Rule:** Must be in 2NF + **no transitive dependency** (non-key column must not depend on another non-key column).

**Bad (Not 3NF):**
| student_id | name | zip_code | city |
|---|---|---|---|
- `city` depends on `zip_code`, not on `student_id` (transitive dependency)

**Good (3NF):**
- Students: `(student_id, name, zip_code)`
- ZipCodes: `(zip_code, city)`

---

### BCNF — Boyce-Codd Normal Form

**Rule:** Stricter version of 3NF. For every functional dependency `A → B`, `A` must be a superkey.

Fixes edge cases in 3NF where a non-primary candidate key has a dependency.

---

### Denormalization

Sometimes we **intentionally break normalization rules** to improve performance.

- Add redundant columns to avoid expensive JOINs
- Useful for read-heavy applications (analytics, dashboards)

```sql
-- Instead of joining to get city name every time,
-- store city_name directly in students table
```

**When to denormalize:**
- Read performance is critical
- Data doesn't change often
- Complex reporting queries

---

### Tips & Tricks

- Normalize first, then denormalize only if you have a performance problem
- Most web applications use 3NF
- BCNF is mainly academic; 3NF is sufficient for most projects

---

## 22. Relationships

### One-to-One

Each record in Table A relates to exactly **one** record in Table B.

**Example:** One user has one profile.

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50)
);

CREATE TABLE profiles (
    id INT PRIMARY KEY,
    user_id INT UNIQUE,           -- UNIQUE enforces 1-to-1
    bio TEXT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Django:**
```python
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='profile')
    bio = models.TextField()
```

---

### One-to-Many

One record in Table A relates to **many** records in Table B.

**Example:** One teacher has many students.

```sql
CREATE TABLE teachers (id INT PRIMARY KEY, name VARCHAR(100));

CREATE TABLE students (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    teacher_id INT,
    FOREIGN KEY (teacher_id) REFERENCES teachers(id)
);
```

**Django:**
```python
class Student(models.Model):
    teacher = models.ForeignKey(Teacher, on_delete=models.CASCADE, related_name='students')
```

---

### Many-to-Many

Many records in Table A relate to **many** records in Table B. Requires a **junction/bridge table**.

**Example:** Students enroll in many courses; courses have many students.

```sql
CREATE TABLE students (id INT PRIMARY KEY, name VARCHAR(100));
CREATE TABLE courses (id INT PRIMARY KEY, title VARCHAR(100));

CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    enrolled_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(id),
    FOREIGN KEY (course_id) REFERENCES courses(id)
);
```

**Django:**
```python
class Student(models.Model):
    courses = models.ManyToManyField('Course', through='Enrollment', related_name='students')

class Enrollment(models.Model):
    student = models.ForeignKey(Student, on_delete=models.CASCADE)
    course = models.ForeignKey(Course, on_delete=models.CASCADE)
    enrolled_at = models.DateTimeField(auto_now_add=True)
```

---

## 23. MySQL Security

### Users and Roles

```sql
-- Create a new user
CREATE USER 'appuser'@'localhost' IDENTIFIED BY 'strongpassword123';

-- Create user for any host
CREATE USER 'appuser'@'%' IDENTIFIED BY 'strongpassword123';

-- View all users
SELECT user, host FROM mysql.user;
```

---

### GRANT

Give permissions to a user.

```sql
-- Grant all privileges on one database
GRANT ALL PRIVILEGES ON mydb.* TO 'appuser'@'localhost';

-- Grant specific privileges
GRANT SELECT, INSERT, UPDATE ON mydb.students TO 'appuser'@'localhost';

-- Grant only SELECT (read-only user)
GRANT SELECT ON mydb.* TO 'readonly'@'localhost';

-- Apply changes
FLUSH PRIVILEGES;
```

---

### REVOKE

Remove permissions.

```sql
REVOKE INSERT ON mydb.students FROM 'appuser'@'localhost';
REVOKE ALL PRIVILEGES ON mydb.* FROM 'appuser'@'localhost';
FLUSH PRIVILEGES;
```

---

### Password Management

```sql
-- Change a user's password
ALTER USER 'appuser'@'localhost' IDENTIFIED BY 'newpassword123';

-- Drop a user
DROP USER 'appuser'@'localhost';
```

---

### Show Grants

```sql
SHOW GRANTS FOR 'appuser'@'localhost';
```

---

### Best Practices

- Never use `root` for application connections
- Create a separate user with minimum required privileges
- Use strong passwords
- Restrict hosts — use `'user'@'localhost'` instead of `'user'@'%'` when possible
- Regularly audit user permissions

---

### Django Integration

```python
# Use a dedicated DB user in settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydb',
        'USER': 'appuser',        # NOT root
        'PASSWORD': 'strongpassword',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

---

## 24. Backup & Restore

### mysqldump

`mysqldump` is the most common tool to export MySQL databases.

```bash
# Backup one database
mysqldump -u root -p mydb > mydb_backup.sql

# Backup specific table
mysqldump -u root -p mydb students > students_backup.sql

# Backup all databases
mysqldump -u root -p --all-databases > all_backup.sql

# Backup with compression
mysqldump -u root -p mydb | gzip > mydb_backup.sql.gz
```

---

### Exporting Database

```bash
# Export with structure + data (default)
mysqldump -u root -p mydb > backup.sql

# Export structure only
mysqldump -u root -p --no-data mydb > structure.sql

# Export data only
mysqldump -u root -p --no-create-info mydb > data.sql
```

---

### Importing Database

```bash
# Import into existing database
mysql -u root -p mydb < backup.sql

# Create database first, then import
mysql -u root -p -e "CREATE DATABASE mydb"
mysql -u root -p mydb < backup.sql

# Import compressed backup
gunzip < mydb_backup.sql.gz | mysql -u root -p mydb
```

---

### Tips & Tricks

- Schedule daily backups with cron jobs
- Always test your backups by restoring them
- Store backups off-server (S3, Google Drive)
- Use `--single-transaction` for InnoDB to avoid locking

```bash
mysqldump -u root -p --single-transaction mydb > backup.sql
```

---

### Automated Backup Script

```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
mysqldump -u root -p'password' mydb > /backups/mydb_$DATE.sql
# Keep only last 7 backups
find /backups -name "*.sql" -mtime +7 -delete
```

---

## 25. Performance Optimization

### Query Optimization

```sql
-- Bad: SELECT *
SELECT * FROM students;

-- Good: Select only needed columns
SELECT id, name, age FROM students;

-- Bad: Function on indexed column (index not used)
SELECT * FROM students WHERE YEAR(created_at) = 2024;

-- Good: Range query (index used)
SELECT * FROM students WHERE created_at BETWEEN '2024-01-01' AND '2024-12-31';
```

---

### Index Usage

```sql
-- Add index to columns used in WHERE, JOIN, ORDER BY
CREATE INDEX idx_age ON students(age);
CREATE INDEX idx_city_age ON students(city_id, age);

-- Verify index is used
EXPLAIN SELECT * FROM students WHERE age = 20;
```

---

### EXPLAIN Keyword

`EXPLAIN` shows how MySQL executes a query.

```sql
EXPLAIN SELECT * FROM students WHERE age = 20;
```

**Key columns in output:**
| Column | Meaning |
|---|---|
| `type` | Join type (ALL = full scan = BAD, ref/eq_ref = good) |
| `key` | Which index is being used |
| `rows` | Estimated rows scanned |
| `Extra` | Additional info (Using index, Using filesort) |

---

### Avoiding Slow Queries

```sql
-- Enable slow query log
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;  -- Log queries taking > 1 second

-- Bad: Wildcard at start (can't use index)
SELECT * FROM students WHERE name LIKE '%Ali';

-- Good: Wildcard only at end (can use index)
SELECT * FROM students WHERE name LIKE 'Ali%';

-- Bad: OR on different columns
SELECT * FROM students WHERE name = 'Ali' OR age = 20;

-- Good: Use UNION
SELECT * FROM students WHERE name = 'Ali'
UNION
SELECT * FROM students WHERE age = 20;
```

---

### Other Optimization Tips

- Use `LIMIT` to reduce result set size
- Avoid `SELECT DISTINCT` unless necessary
- Use `JOIN` instead of subqueries where possible
- Normalize data to reduce redundancy
- Use connection pooling in production
- Cache frequent queries at application level

---

### Django Integration

```python
# Check generated SQL
qs = Student.objects.filter(age__gte=18)
print(qs.query)  # Shows raw SQL

# Use select_related to avoid N+1 queries
students = Student.objects.select_related('city').all()

# Use only() to select specific columns
students = Student.objects.only('id', 'name', 'age')

# Use defer() to exclude heavy columns
students = Student.objects.defer('bio', 'profile_image')
```

---

## 26. Advanced MySQL Concepts

### Window Functions

Window functions perform calculations **across a set of rows** related to the current row, without collapsing them like GROUP BY.

```sql
-- ROW_NUMBER: assign row number per group
SELECT
    name,
    city,
    salary,
    ROW_NUMBER() OVER (PARTITION BY city ORDER BY salary DESC) AS rank_in_city
FROM employees;

-- RANK: same rank for ties, gaps after
SELECT name, salary, RANK() OVER (ORDER BY salary DESC) AS salary_rank FROM employees;

-- DENSE_RANK: same rank for ties, no gaps
SELECT name, salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank FROM employees;

-- Running total (SUM)
SELECT name, salary,
    SUM(salary) OVER (ORDER BY id) AS running_total
FROM employees;

-- LAG: get previous row value
SELECT name, salary,
    LAG(salary, 1) OVER (ORDER BY id) AS prev_salary
FROM employees;
```

---

### Common Table Expressions (CTE)

CTE is a **named temporary result set** you can reference in the same query. Makes complex queries readable.

```sql
WITH city_averages AS (
    SELECT city_id, AVG(age) AS avg_age
    FROM students
    GROUP BY city_id
)
SELECT s.name, s.age, ca.avg_age
FROM students s
JOIN city_averages ca ON s.city_id = ca.city_id
WHERE s.age > ca.avg_age;
```

Multiple CTEs:
```sql
WITH
active_students AS (
    SELECT * FROM students WHERE is_active = 1
),
adult_students AS (
    SELECT * FROM active_students WHERE age >= 18
)
SELECT * FROM adult_students;
```

---

### Recursive Queries

Recursive CTEs are used for **hierarchical data** (org charts, categories, menus).

```sql
WITH RECURSIVE employee_hierarchy AS (
    -- Base case: top-level managers (no manager)
    SELECT id, name, manager_id, 0 AS level
    FROM employees WHERE manager_id IS NULL

    UNION ALL

    -- Recursive case: find employees of each manager
    SELECT e.id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.id
)
SELECT * FROM employee_hierarchy ORDER BY level;
```

---

### Partitioning

Divide a large table into smaller physical parts for better performance.

```sql
CREATE TABLE orders (
    id INT,
    order_date DATE,
    amount DECIMAL(10,2)
)
PARTITION BY RANGE (YEAR(order_date)) (
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION future VALUES LESS THAN MAXVALUE
);
```

---

### Replication Basics

- **Master-Slave (Primary-Replica):** All writes go to master; slaves replicate and handle reads
- **Use case:** Scale read operations, backup
- Binary log on master → relay log on slave → applied by slave SQL thread

---

### Sharding Basics

- **Horizontal sharding:** Split rows across multiple databases based on a key (e.g., user_id % 4)
- **Vertical sharding:** Split tables across databases by feature
- Used when a single server can't handle the load
- MySQL doesn't do sharding natively; use Vitess or application-level sharding

---

### Django Integration

```python
# CTEs with Django (using raw SQL)
from django.db import connection

with connection.cursor() as cursor:
    cursor.execute("""
        WITH city_avg AS (
            SELECT city_id, AVG(age) as avg_age FROM students GROUP BY city_id
        )
        SELECT s.name, s.age, ca.avg_age
        FROM students s JOIN city_avg ca ON s.city_id = ca.city_id
    """)
    results = cursor.fetchall()

# Window functions with Django ORM (Django 2.0+)
from django.db.models import Window, F
from django.db.models.functions import Rank

Employee.objects.annotate(
    salary_rank=Window(
        expression=Rank(),
        order_by=F('salary').desc()
    )
)
```

---

## 27. MySQL Architecture

### Storage Engines

MySQL supports pluggable storage engines. The engine handles how data is stored on disk.

| Feature | InnoDB | MyISAM |
|---|---|---|
| Transactions | Yes (ACID) | No |
| Foreign Keys | Yes | No |
| Row-level Locking | Yes | No (table-level) |
| Full-text Search | Yes (5.6+) | Yes |
| Crash Recovery | Yes | Limited |
| Default in MySQL | Yes (5.5+) | Older versions |
| Best For | General use | Read-only/legacy |

```sql
-- Specify engine
CREATE TABLE students (...) ENGINE=InnoDB;

-- Check engine
SHOW TABLE STATUS FROM mydb LIKE 'students';
```

---

### Buffer Pool (InnoDB)

The **Buffer Pool** is InnoDB's memory cache. It keeps frequently accessed data pages in RAM to avoid disk reads.

- Default: 128MB; set to 70-80% of RAM for dedicated DB servers
- More buffer pool = fewer disk reads = faster queries

```ini
# my.cnf
innodb_buffer_pool_size = 1G
```

---

### Query Cache

*(Removed in MySQL 8.0)*

In older versions, MySQL could cache SELECT query results. Deprecated because it caused contention.

---

### MySQL Logs

| Log | Purpose |
|---|---|
| **Error Log** | MySQL startup, shutdown, and errors |
| **Binary Log (binlog)** | All data-changing events (used for replication and recovery) |
| **General Query Log** | All SQL statements received (for debugging, disabled by default) |
| **Slow Query Log** | Queries slower than `long_query_time` |

```ini
# my.cnf
log_error = /var/log/mysql/error.log
general_log = ON
general_log_file = /var/log/mysql/general.log
slow_query_log = ON
long_query_time = 1
log_bin = /var/log/mysql/mysql-bin.log
```

---

## 28. JSON Support in MySQL

### JSON Data Type

MySQL 5.7+ supports a native JSON type with validation.

```sql
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    attributes JSON
);
```

---

### Storing JSON

```sql
INSERT INTO products (name, attributes) VALUES (
    'Laptop',
    '{"brand": "Dell", "ram": 16, "storage": 512, "colors": ["black", "silver"]}'
);
```

---

### Querying JSON

```sql
-- Extract value with ->
SELECT attributes->'$.brand' FROM products;        -- Returns "Dell"

-- Extract without quotes with ->>
SELECT attributes->>'$.brand' FROM products;       -- Returns Dell

-- In WHERE clause
SELECT * FROM products WHERE attributes->>'$.brand' = 'Dell';
SELECT * FROM products WHERE attributes->'$.ram' > 8;
```

---

### JSON Functions

```sql
-- Extract
SELECT JSON_EXTRACT(attributes, '$.brand') FROM products;

-- Set value
UPDATE products SET attributes = JSON_SET(attributes, '$.price', 999.99) WHERE id = 1;

-- Remove key
UPDATE products SET attributes = JSON_REMOVE(attributes, '$.colors') WHERE id = 1;

-- Check key exists
SELECT JSON_CONTAINS_PATH(attributes, 'one', '$.brand') FROM products;

-- Array length
SELECT JSON_LENGTH(attributes->'$.colors') FROM products;

-- All keys
SELECT JSON_KEYS(attributes) FROM products;
```

---

### Tips & Tricks

- Don't store everything in JSON — use proper columns for frequently queried fields
- Create virtual (generated) columns on JSON fields for better indexing

```sql
ALTER TABLE products
ADD COLUMN brand VARCHAR(50) GENERATED ALWAYS AS (attributes->>'$.brand') STORED,
ADD INDEX idx_brand (brand);
```

---

### Django Integration

```python
# Django 3.1+ has JSONField for MySQL/PostgreSQL
class Product(models.Model):
    name = models.CharField(max_length=100)
    attributes = models.JSONField(default=dict)

# Query JSON fields
Product.objects.filter(attributes__brand='Dell')
Product.objects.filter(attributes__ram__gte=16)
```

---

## 29. MySQL with Applications

### Connecting MySQL with Python

```bash
pip install mysql-connector-python
# or
pip install pymysql
```

```python
import mysql.connector

conn = mysql.connector.connect(
    host='localhost',
    user='root',
    password='yourpassword',
    database='mydb'
)

cursor = conn.cursor()

# Execute query
cursor.execute("SELECT * FROM students")
rows = cursor.fetchall()

for row in rows:
    print(row)

# Insert data
cursor.execute("INSERT INTO students (name, age) VALUES (%s, %s)", ('Ali', 20))
conn.commit()

cursor.close()
conn.close()
```

---

### Connecting MySQL with Django

```bash
pip install mysqlclient
```

```python
# settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydb',
        'USER': 'appuser',
        'PASSWORD': 'yourpassword',
        'HOST': 'localhost',
        'PORT': '3306',
        'OPTIONS': {
            'charset': 'utf8mb4',
            'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
        }
    }
}
```

---

### ORM vs Raw SQL

| | ORM (Django) | Raw SQL |
|---|---|---|
| What it is | Python objects map to DB tables | Direct SQL queries |
| Readability | Python-friendly | SQL expertise needed |
| Safety | Auto-escaped (safe from SQL injection) | Must manually escape |
| Flexibility | Limited for complex queries | Full SQL power |
| Performance | Slightly slower | Fastest |
| Portability | Database-agnostic | DB-specific |

```python
# ORM
students = Student.objects.filter(age__gte=18).order_by('-age')

# Raw SQL
from django.db import connection
with connection.cursor() as cursor:
    cursor.execute("SELECT * FROM students WHERE age >= %s ORDER BY age DESC", [18])
    rows = cursor.fetchall()

# Raw queryset (still returns model instances)
students = Student.objects.raw("SELECT * FROM students WHERE age >= %s", [18])
```

---

### Full DRF Example

```python
# models.py
class Student(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()
    email = models.EmailField(unique=True)
    created_at = models.DateTimeField(auto_now_add=True)

# serializers.py
from rest_framework import serializers
class StudentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        fields = ['id', 'name', 'age', 'email', 'created_at']

# views.py
from rest_framework import generics, filters
class StudentListCreateView(generics.ListCreateAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentSerializer
    filter_backends = [filters.SearchFilter, filters.OrderingFilter]
    search_fields = ['name', 'email']
    ordering_fields = ['age', 'created_at']

class StudentDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentSerializer

# urls.py
from django.urls import path
urlpatterns = [
    path('students/', StudentListCreateView.as_view()),
    path('students/<int:pk>/', StudentDetailView.as_view()),
]
```

---

## 30. Real-world Practices

### Designing Schemas

- Start with an **ER diagram** (Entity-Relationship)
- Identify entities, attributes, and relationships
- Normalize to at least 3NF
- Add proper indexes from the start
- Always include `created_at`, `updated_at`, `is_active` columns
- Use `BIGINT` for IDs in high-scale apps

```sql
-- Good schema example
CREATE TABLE users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(150) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

---

### Writing Efficient Queries

```sql
-- Bad: All columns, no limit
SELECT * FROM orders;

-- Good: Specific columns, limit
SELECT id, user_id, total_amount, created_at FROM orders LIMIT 100;

-- Bad: Function on indexed column
SELECT * FROM orders WHERE MONTH(created_at) = 1;

-- Good: Range on indexed column
SELECT * FROM orders WHERE created_at >= '2024-01-01' AND created_at < '2024-02-01';

-- Bad: N+1 (one query per user)
-- (Avoid in application code)

-- Good: Single JOIN query
SELECT u.name, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON o.user_id = u.id
GROUP BY u.id;
```

---

### Handling Large Data

- Use **pagination** — never load all rows at once
- Use **indexes** on all filter/sort columns
- **Partition** very large tables by date or region
- Use **archiving** — move old data to archive tables
- Use **caching** (Redis/Memcached) for frequent queries
- Use **READ REPLICAS** for scaling reads

```sql
-- Efficient pagination with keyset (no OFFSET)
-- Better than OFFSET for large tables
SELECT * FROM orders
WHERE id > 10000    -- last seen id
ORDER BY id
LIMIT 20;
```

---

### Debugging SQL Errors

**Common errors and solutions:**

| Error | Cause | Solution |
|---|---|---|
| `ERROR 1045: Access denied` | Wrong username/password | Check credentials |
| `ERROR 1049: Unknown database` | DB doesn't exist | CREATE DATABASE first |
| `ERROR 1062: Duplicate entry` | Unique constraint violation | Check for existing record |
| `ERROR 1064: SQL syntax error` | Wrong SQL syntax | Check query syntax carefully |
| `ERROR 1215: Cannot add foreign key` | Data type mismatch | Match parent/child column types exactly |
| `ERROR 1366: Incorrect integer value` | Wrong data type | Check data being inserted |

```sql
-- Check last error
SHOW ERRORS;

-- Check warnings
SHOW WARNINGS;

-- Debug with EXPLAIN
EXPLAIN SELECT * FROM students WHERE age = 20;
```

---

### Production Checklist

- [ ] Use InnoDB engine for all tables
- [ ] Create proper indexes on query columns
- [ ] Use a dedicated DB user (not root) for applications
- [ ] Enable and monitor slow query log
- [ ] Set up automated daily backups
- [ ] Use transactions for multi-step operations
- [ ] Set `utf8mb4` as default charset
- [ ] Enable binary logging for point-in-time recovery
- [ ] Set up a read replica for scaling
- [ ] Use connection pooling (e.g., ProxySQL, pgBouncer equivalent)
- [ ] Monitor with `SHOW PROCESSLIST` and `INFORMATION_SCHEMA`

```sql
-- Monitor running queries
SHOW FULL PROCESSLIST;

-- Check table sizes
SELECT
    table_name,
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS size_mb
FROM information_schema.TABLES
WHERE table_schema = 'mydb'
ORDER BY size_mb DESC;
```

---

### Django Production Settings

```python
# settings.py for production
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': os.environ.get('DB_NAME'),
        'USER': os.environ.get('DB_USER'),
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': os.environ.get('DB_HOST', 'localhost'),
        'PORT': os.environ.get('DB_PORT', '3306'),
        'CONN_MAX_AGE': 60,  # Connection pooling
        'OPTIONS': {
            'charset': 'utf8mb4',
            'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
            'connect_timeout': 10,
        }
    }
}
```

---

*End of MySQL Complete Notes*
