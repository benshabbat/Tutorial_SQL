# SQL Tutorial - Beginner Level 🎯

Welcome to the beginner SQL tutorial! This guide will teach you the fundamentals of SQL using MySQL.

## Table of Contents
- [Introduction](#introduction)
- [What is SQL?](#what-is-sql)
- [Setting Up](#setting-up)
- [Basic SELECT Statement](#basic-select-statement)
- [Filtering with WHERE](#filtering-with-where)
- [Sorting with ORDER BY](#sorting-with-order-by)
- [Limiting Results](#limiting-results)
- [Practice Exercises](#practice-exercises)

---

## Introduction

SQL (Structured Query Language) is the standard language for managing and manipulating databases. This tutorial focuses on MySQL, one of the most popular database systems.

## What is SQL?

SQL allows you to:
- Retrieve data from databases
- Insert new data
- Update existing data
- Delete data
- Create new databases and tables

## Setting Up

Before we start, make sure you have MySQL installed. You can use:
- MySQL Workbench (GUI tool)
- Command line MySQL client
- Online platforms like SQLFiddle or DB Fiddle

For this tutorial, we'll use a sample database called `school`:

```sql
-- Create database
CREATE DATABASE school;

-- Use the database
USE school;

-- Create a sample students table
CREATE TABLE students (
    id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    age INT,
    grade VARCHAR(2),
    city VARCHAR(50)
);

-- Insert sample data
INSERT INTO students (first_name, last_name, age, grade, city) VALUES
('John', 'Smith', 18, 'A', 'New York'),
('Emma', 'Johnson', 17, 'B', 'Los Angeles'),
('Michael', 'Brown', 19, 'A', 'Chicago'),
('Sarah', 'Davis', 18, 'C', 'New York'),
('David', 'Wilson', 17, 'B', 'Houston'),
('Lisa', 'Anderson', 19, 'A', 'Chicago'),
('James', 'Taylor', 18, 'B', 'New York'),
('Emily', 'Thomas', 17, 'A', 'Miami');
```

---

## Basic SELECT Statement

The `SELECT` statement is used to retrieve data from a database.

### Syntax:
```sql
SELECT column1, column2, ... FROM table_name;
```

### Example 1: Select all columns
```sql
SELECT * FROM students;
```

**Result:** Shows all students with all their information.

### Example 2: Select specific columns
```sql
SELECT first_name, last_name FROM students;
```

**Result:**
```
+-----------+----------+
| first_name | last_name |
+-----------+----------+
| John      | Smith    |
| Emma      | Johnson  |
| Michael   | Brown    |
...
```

### Example 3: Select with calculated columns
```sql
SELECT first_name, last_name, age, age + 10 AS age_in_10_years 
FROM students;
```

---

## Filtering with WHERE

The `WHERE` clause filters records based on specific conditions.

### Syntax:
```sql
SELECT column1, column2 FROM table_name WHERE condition;
```

### Example 1: Filter by exact value
```sql
SELECT * FROM students WHERE city = 'New York';
```

**Result:** Shows only students from New York.

### Example 2: Filter by numeric comparison
```sql
SELECT first_name, last_name, age 
FROM students 
WHERE age >= 18;
```

**Result:** Shows students who are 18 or older.

### Example 3: Multiple conditions with AND
```sql
SELECT first_name, last_name, grade, city 
FROM students 
WHERE grade = 'A' AND city = 'Chicago';
```

**Result:** Shows students with grade A from Chicago.

### Example 4: Multiple conditions with OR
```sql
SELECT first_name, last_name, city 
FROM students 
WHERE city = 'New York' OR city = 'Chicago';
```

### Example 5: Using IN for multiple values
```sql
SELECT first_name, last_name, city 
FROM students 
WHERE city IN ('New York', 'Chicago', 'Miami');
```

### Example 6: Using BETWEEN
```sql
SELECT first_name, last_name, age 
FROM students 
WHERE age BETWEEN 17 AND 18;
```

### Example 7: Using LIKE for pattern matching
```sql
-- Find students whose first name starts with 'J'
SELECT first_name, last_name 
FROM students 
WHERE first_name LIKE 'J%';

-- Find students whose last name contains 'son'
SELECT first_name, last_name 
FROM students 
WHERE last_name LIKE '%son%';
```

**Wildcards:**
- `%` - Matches any number of characters
- `_` - Matches exactly one character

---

## Sorting with ORDER BY

The `ORDER BY` clause sorts the result set.

### Syntax:
```sql
SELECT column1, column2 FROM table_name ORDER BY column1 [ASC|DESC];
```

### Example 1: Sort ascending (default)
```sql
SELECT first_name, last_name, age 
FROM students 
ORDER BY age;
```

**Result:** Students sorted from youngest to oldest.

### Example 2: Sort descending
```sql
SELECT first_name, last_name, age 
FROM students 
ORDER BY age DESC;
```

**Result:** Students sorted from oldest to youngest.

### Example 3: Sort by multiple columns
```sql
SELECT first_name, last_name, grade, age 
FROM students 
ORDER BY grade ASC, age DESC;
```

**Result:** Sorted by grade first, then by age within each grade.

### Example 4: Sort alphabetically
```sql
SELECT first_name, last_name 
FROM students 
ORDER BY last_name, first_name;
```

---

## Limiting Results

The `LIMIT` clause restricts the number of rows returned.

### Syntax:
```sql
SELECT column1, column2 FROM table_name LIMIT number;
```

### Example 1: Get first 5 students
```sql
SELECT * FROM students LIMIT 5;
```

### Example 2: Get top 3 oldest students
```sql
SELECT first_name, last_name, age 
FROM students 
ORDER BY age DESC 
LIMIT 3;
```

### Example 3: Pagination with OFFSET
```sql
-- Get rows 4-6 (skip first 3)
SELECT first_name, last_name 
FROM students 
LIMIT 3 OFFSET 3;
```

---

## Practice Exercises

Try these exercises to test your knowledge:

### Exercise 1
Find all students who are 17 years old.

<details>
<summary>Solution</summary>

```sql
SELECT * FROM students WHERE age = 17;
```
</details>

### Exercise 2
List all students from New York, sorted by their last name.

<details>
<summary>Solution</summary>

```sql
SELECT * FROM students 
WHERE city = 'New York' 
ORDER BY last_name;
```
</details>

### Exercise 3
Find the first 3 students with grade 'A', sorted by age (oldest first).

<details>
<summary>Solution</summary>

```sql
SELECT * FROM students 
WHERE grade = 'A' 
ORDER BY age DESC 
LIMIT 3;
```
</details>

### Exercise 4
Find all students whose first name ends with 'a'.

<details>
<summary>Solution</summary>

```sql
SELECT * FROM students 
WHERE first_name LIKE '%a';
```
</details>

### Exercise 5
List students who are either 18 years old OR from Chicago.

<details>
<summary>Solution</summary>

```sql
SELECT * FROM students 
WHERE age = 18 OR city = 'Chicago';
```
</details>

---

## Summary

In this tutorial, you learned:
- ✅ How to use `SELECT` to retrieve data
- ✅ How to filter data with `WHERE`
- ✅ How to sort results with `ORDER BY`
- ✅ How to limit results with `LIMIT`
- ✅ Basic comparison operators and wildcards

**Next Steps:** Continue to the [Intermediate SQL Tutorial](02_Intermediate_SQL.md) to learn about JOINs, GROUP BY, and aggregate functions!

---

**Happy Learning! 🚀**
