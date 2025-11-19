# SQL Tutorial - Intermediate Level 📊

Welcome to the intermediate SQL tutorial! Here you'll learn about JOINs, aggregate functions, and data grouping.

## Table of Contents
- [Introduction](#introduction)
- [Aggregate Functions](#aggregate-functions)
- [GROUP BY Clause](#group-by-clause)
- [HAVING Clause](#having-clause)
- [JOINS](#joins)
- [DISTINCT Keyword](#distinct-keyword)
- [Practice Exercises](#practice-exercises)

---

## Introduction

This tutorial builds on beginner concepts and introduces more powerful SQL features for data analysis and working with multiple tables.

## Sample Database Setup

We'll use an expanded school database with multiple related tables:

```sql
-- Create database
CREATE DATABASE school;
USE school;

-- Students table
CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    age INT,
    grade VARCHAR(2),
    class_id INT
);

-- Classes table
CREATE TABLE classes (
    class_id INT PRIMARY KEY AUTO_INCREMENT,
    class_name VARCHAR(50),
    teacher_name VARCHAR(50),
    room_number VARCHAR(10)
);

-- Enrollments table
CREATE TABLE enrollments (
    enrollment_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT,
    course_name VARCHAR(50),
    score INT
);

-- Insert sample data
INSERT INTO classes (class_name, teacher_name, room_number) VALUES
('Math 101', 'Dr. Smith', 'A101'),
('Science 201', 'Prof. Johnson', 'B205'),
('English 101', 'Ms. Williams', 'C102'),
('History 301', 'Mr. Brown', 'A205');

INSERT INTO students (first_name, last_name, age, grade, class_id) VALUES
('John', 'Doe', 18, 'A', 1),
('Jane', 'Smith', 17, 'B', 1),
('Mike', 'Johnson', 19, 'A', 2),
('Sarah', 'Williams', 18, 'C', 2),
('Tom', 'Brown', 17, 'B', 3),
('Lisa', 'Davis', 19, 'A', 3),
('David', 'Miller', 18, 'B', 1),
('Emma', 'Wilson', 17, 'A', 4);

INSERT INTO enrollments (student_id, course_name, score) VALUES
(1, 'Math', 85),
(1, 'Science', 90),
(1, 'English', 88),
(2, 'Math', 78),
(2, 'Science', 82),
(3, 'Math', 92),
(3, 'Science', 95),
(3, 'History', 88),
(4, 'English', 75),
(4, 'History', 80),
(5, 'Math', 88),
(5, 'English', 85),
(6, 'Science', 94),
(6, 'History', 90),
(7, 'Math', 80),
(8, 'English', 92);
```

---

## Aggregate Functions

Aggregate functions perform calculations on a set of values and return a single value.

### Common Aggregate Functions:
- `COUNT()` - Counts rows
- `SUM()` - Adds up values
- `AVG()` - Calculates average
- `MIN()` - Finds minimum value
- `MAX()` - Finds maximum value

### Example 1: COUNT - Count total students
```sql
SELECT COUNT(*) AS total_students 
FROM students;
```

**Result:**
```
+----------------+
| total_students |
+----------------+
|              8 |
+----------------+
```

### Example 2: AVG - Average age
```sql
SELECT AVG(age) AS average_age 
FROM students;
```

### Example 3: MIN and MAX
```sql
SELECT 
    MIN(age) AS youngest,
    MAX(age) AS oldest
FROM students;
```

### Example 4: SUM - Total of all scores
```sql
SELECT SUM(score) AS total_score 
FROM enrollments;
```

### Example 5: COUNT with condition
```sql
SELECT COUNT(*) AS students_with_A 
FROM students 
WHERE grade = 'A';
```

---

## GROUP BY Clause

`GROUP BY` groups rows that have the same values in specified columns.

### Syntax:
```sql
SELECT column1, aggregate_function(column2)
FROM table_name
GROUP BY column1;
```

### Example 1: Count students by grade
```sql
SELECT grade, COUNT(*) AS student_count
FROM students
GROUP BY grade
ORDER BY grade;
```

**Result:**
```
+-------+---------------+
| grade | student_count |
+-------+---------------+
| A     |             4 |
| B     |             3 |
| C     |             1 |
+-------+---------------+
```

### Example 2: Average age by class
```sql
SELECT class_id, AVG(age) AS avg_age
FROM students
GROUP BY class_id;
```

### Example 3: Count enrollments per course
```sql
SELECT course_name, COUNT(*) AS enrollment_count
FROM enrollments
GROUP BY course_name
ORDER BY enrollment_count DESC;
```

### Example 4: Average score per student
```sql
SELECT 
    student_id,
    COUNT(*) AS courses_taken,
    AVG(score) AS average_score,
    MIN(score) AS lowest_score,
    MAX(score) AS highest_score
FROM enrollments
GROUP BY student_id;
```

**Result:**
```
+------------+---------------+---------------+--------------+---------------+
| student_id | courses_taken | average_score | lowest_score | highest_score |
+------------+---------------+---------------+--------------+---------------+
|          1 |             3 |       87.6667 |           85 |            90 |
|          2 |             2 |       80.0000 |           78 |            82 |
|          3 |             3 |       91.6667 |           88 |            95 |
...
```

### Example 5: Multiple columns in GROUP BY
```sql
SELECT grade, class_id, COUNT(*) AS student_count
FROM students
GROUP BY grade, class_id
ORDER BY grade, class_id;
```

---

## HAVING Clause

`HAVING` filters grouped results (like `WHERE` but for groups).

### Syntax:
```sql
SELECT column1, aggregate_function(column2)
FROM table_name
GROUP BY column1
HAVING condition;
```

### Example 1: Grades with more than 1 student
```sql
SELECT grade, COUNT(*) AS student_count
FROM students
GROUP BY grade
HAVING COUNT(*) > 1;
```

**Result:**
```
+-------+---------------+
| grade | student_count |
+-------+---------------+
| A     |             4 |
| B     |             3 |
+-------+---------------+
```

### Example 2: Students with average score above 85
```sql
SELECT 
    student_id,
    AVG(score) AS average_score
FROM enrollments
GROUP BY student_id
HAVING AVG(score) > 85
ORDER BY average_score DESC;
```

### Example 3: Courses with at least 3 students
```sql
SELECT course_name, COUNT(*) AS enrollment_count
FROM enrollments
GROUP BY course_name
HAVING COUNT(*) >= 3
ORDER BY enrollment_count DESC;
```

### Example 4: Combining WHERE and HAVING
```sql
-- Find courses where students scored above 80 on average,
-- but only count scores above 75
SELECT 
    course_name,
    COUNT(*) AS student_count,
    AVG(score) AS avg_score
FROM enrollments
WHERE score > 75
GROUP BY course_name
HAVING AVG(score) > 85;
```

**Note:** `WHERE` filters rows before grouping, `HAVING` filters after grouping.

---

## JOINS

JOINs combine rows from two or more tables based on a related column.

### Types of JOINs:
1. **INNER JOIN** - Returns matching rows from both tables
2. **LEFT JOIN** - Returns all rows from left table, matching from right
3. **RIGHT JOIN** - Returns all rows from right table, matching from left
4. **CROSS JOIN** - Returns cartesian product of both tables

### INNER JOIN

### Example 1: Students with their class information
```sql
SELECT 
    students.first_name,
    students.last_name,
    classes.class_name,
    classes.teacher_name
FROM students
INNER JOIN classes ON students.class_id = classes.class_id;
```

**Result:**
```
+------------+-----------+-------------+--------------+
| first_name | last_name | class_name  | teacher_name |
+------------+-----------+-------------+--------------+
| John       | Doe       | Math 101    | Dr. Smith    |
| Jane       | Smith     | Math 101    | Dr. Smith    |
| Mike       | Johnson   | Science 201 | Prof. Johnson|
...
```

### Example 2: Students with their enrollment scores
```sql
SELECT 
    s.first_name,
    s.last_name,
    e.course_name,
    e.score
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id
ORDER BY s.last_name, e.course_name;
```

**Note:** `s` and `e` are table aliases for shorter syntax.

### Example 3: Triple JOIN
```sql
SELECT 
    s.first_name,
    s.last_name,
    c.class_name,
    e.course_name,
    e.score
FROM students s
INNER JOIN classes c ON s.class_id = c.class_id
INNER JOIN enrollments e ON s.student_id = e.student_id
WHERE e.score >= 90
ORDER BY e.score DESC;
```

### LEFT JOIN

Returns all records from the left table, and matching records from the right table.

### Example 4: All students and their enrollments (including those with no enrollments)
```sql
SELECT 
    s.first_name,
    s.last_name,
    e.course_name,
    e.score
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
ORDER BY s.student_id;
```

### Example 5: Find students with no enrollments
```sql
SELECT 
    s.first_name,
    s.last_name
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
WHERE e.enrollment_id IS NULL;
```

### RIGHT JOIN

### Example 6: Similar to LEFT JOIN but starts from right table
```sql
SELECT 
    s.first_name,
    s.last_name,
    e.course_name,
    e.score
FROM enrollments e
RIGHT JOIN students s ON e.student_id = s.student_id;
```

---

## DISTINCT Keyword

`DISTINCT` removes duplicate values from results.

### Example 1: List unique grades
```sql
SELECT DISTINCT grade 
FROM students 
ORDER BY grade;
```

**Result:**
```
+-------+
| grade |
+-------+
| A     |
| B     |
| C     |
+-------+
```

### Example 2: Unique courses offered
```sql
SELECT DISTINCT course_name 
FROM enrollments
ORDER BY course_name;
```

### Example 3: Count distinct values
```sql
SELECT COUNT(DISTINCT course_name) AS unique_courses
FROM enrollments;
```

---

## Practice Exercises

### Exercise 1
Find the average score for each course, showing only courses with an average above 85.

<details>
<summary>Solution</summary>

```sql
SELECT course_name, AVG(score) AS avg_score
FROM enrollments
GROUP BY course_name
HAVING AVG(score) > 85
ORDER BY avg_score DESC;
```
</details>

### Exercise 2
List all students with their class names and teacher names, ordered by teacher name.

<details>
<summary>Solution</summary>

```sql
SELECT 
    s.first_name,
    s.last_name,
    c.class_name,
    c.teacher_name
FROM students s
INNER JOIN classes c ON s.class_id = c.class_id
ORDER BY c.teacher_name;
```
</details>

### Exercise 3
Find how many students are in each class, showing class name and count.

<details>
<summary>Solution</summary>

```sql
SELECT 
    c.class_name,
    COUNT(s.student_id) AS student_count
FROM classes c
LEFT JOIN students s ON c.class_id = s.class_id
GROUP BY c.class_name
ORDER BY student_count DESC;
```
</details>

### Exercise 4
Find students who have taken more than 2 courses and show their average score.

<details>
<summary>Solution</summary>

```sql
SELECT 
    s.first_name,
    s.last_name,
    COUNT(e.enrollment_id) AS courses_taken,
    AVG(e.score) AS avg_score
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id
GROUP BY s.student_id, s.first_name, s.last_name
HAVING COUNT(e.enrollment_id) > 2
ORDER BY avg_score DESC;
```
</details>

### Exercise 5
List all courses and the number of students scoring above 85 in each course.

<details>
<summary>Solution</summary>

```sql
SELECT 
    course_name,
    COUNT(*) AS high_scorers
FROM enrollments
WHERE score > 85
GROUP BY course_name
ORDER BY high_scorers DESC;
```
</details>

---

## Summary

In this tutorial, you learned:
- ✅ Aggregate functions: COUNT, SUM, AVG, MIN, MAX
- ✅ Grouping data with GROUP BY
- ✅ Filtering groups with HAVING
- ✅ Joining tables with INNER JOIN and LEFT JOIN
- ✅ Removing duplicates with DISTINCT

**Next Steps:** Continue to the [Advanced SQL Tutorial](03_Advanced_SQL.md) to learn about subqueries, CTEs, and window functions!

---

**Keep Learning! 🎓**
