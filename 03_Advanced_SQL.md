# SQL Tutorial - Advanced Level 🚀

Welcome to the advanced SQL tutorial! Master complex queries with subqueries, CTEs, window functions, and transactions.

## Table of Contents
- [Introduction](#introduction)
- [Subqueries](#subqueries)
- [Common Table Expressions (CTEs)](#common-table-expressions-ctes)
- [Window Functions](#window-functions)
- [Transactions](#transactions)
- [Views](#views)
- [Stored Procedures](#stored-procedures)
- [Practice Exercises](#practice-exercises)

---

## Introduction

This tutorial covers advanced SQL techniques for complex data analysis and database management. These features will help you write more efficient and maintainable queries.

## Sample Database Setup

```sql
CREATE DATABASE company;
USE company;

-- Departments table
CREATE TABLE departments (
    dept_id INT PRIMARY KEY AUTO_INCREMENT,
    dept_name VARCHAR(50),
    location VARCHAR(50)
);

-- Employees table
CREATE TABLE employees (
    emp_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    dept_id INT,
    salary DECIMAL(10, 2),
    hire_date DATE,
    manager_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);

-- Sales table
CREATE TABLE sales (
    sale_id INT PRIMARY KEY AUTO_INCREMENT,
    emp_id INT,
    sale_date DATE,
    amount DECIMAL(10, 2),
    FOREIGN KEY (emp_id) REFERENCES employees(emp_id)
);

-- Insert sample data
INSERT INTO departments (dept_name, location) VALUES
('Sales', 'New York'),
('Engineering', 'San Francisco'),
('Marketing', 'Chicago'),
('HR', 'New York');

INSERT INTO employees (first_name, last_name, dept_id, salary, hire_date, manager_id) VALUES
('John', 'Smith', 1, 75000, '2020-01-15', NULL),
('Sarah', 'Johnson', 1, 65000, '2020-03-20', 1),
('Mike', 'Williams', 1, 60000, '2021-02-10', 1),
('Emily', 'Brown', 2, 95000, '2019-06-01', NULL),
('David', 'Jones', 2, 85000, '2020-08-15', 4),
('Lisa', 'Garcia', 2, 80000, '2021-01-20', 4),
('Tom', 'Martinez', 3, 70000, '2020-05-10', NULL),
('Anna', 'Rodriguez', 3, 65000, '2021-03-15', 7),
('James', 'Wilson', 4, 60000, '2020-09-01', NULL);

INSERT INTO sales (emp_id, sale_date, amount) VALUES
(1, '2024-01-05', 15000),
(1, '2024-01-15', 22000),
(2, '2024-01-08', 18000),
(2, '2024-01-20', 25000),
(3, '2024-01-12', 12000),
(3, '2024-01-25', 20000),
(1, '2024-02-03', 30000),
(2, '2024-02-10', 28000),
(3, '2024-02-15', 15000),
(1, '2024-02-20', 35000);
```

---

## Subqueries

A subquery is a query nested inside another query. It can be used in SELECT, FROM, WHERE, or HAVING clauses.

### Types of Subqueries:
1. **Scalar subquery** - Returns a single value
2. **Row subquery** - Returns a single row
3. **Table subquery** - Returns a table
4. **Correlated subquery** - References outer query

### Example 1: Scalar Subquery in WHERE
```sql
-- Find employees earning more than the average salary
SELECT first_name, last_name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees)
ORDER BY salary DESC;
```

**Result:**
```
+------------+-----------+----------+
| first_name | last_name | salary   |
+------------+-----------+----------+
| Emily      | Brown     | 95000.00 |
| David      | Jones     | 85000.00 |
| Lisa       | Garcia    | 80000.00 |
| John       | Smith     | 75000.00 |
| Tom        | Martinez  | 70000.00 |
+------------+-----------+----------+
```

### Example 2: Subquery in SELECT clause
```sql
-- Show each employee with their department's average salary
SELECT 
    first_name,
    last_name,
    salary,
    (SELECT AVG(salary) 
     FROM employees e2 
     WHERE e2.dept_id = e1.dept_id) AS dept_avg_salary
FROM employees e1;
```

### Example 3: Subquery with IN
```sql
-- Find employees in departments located in New York
SELECT first_name, last_name, dept_id
FROM employees
WHERE dept_id IN (
    SELECT dept_id 
    FROM departments 
    WHERE location = 'New York'
);
```

### Example 4: Subquery with EXISTS
```sql
-- Find employees who have made sales
SELECT first_name, last_name
FROM employees e
WHERE EXISTS (
    SELECT 1 
    FROM sales s 
    WHERE s.emp_id = e.emp_id
);
```

### Example 5: Correlated Subquery
```sql
-- Find employees earning more than their department average
SELECT 
    e1.first_name,
    e1.last_name,
    e1.salary,
    e1.dept_id
FROM employees e1
WHERE salary > (
    SELECT AVG(salary)
    FROM employees e2
    WHERE e2.dept_id = e1.dept_id
)
ORDER BY dept_id, salary DESC;
```

### Example 6: Subquery in FROM clause (Derived Table)
```sql
-- Find departments with average salary above 70000
SELECT dept_id, avg_salary
FROM (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY dept_id
) AS dept_averages
WHERE avg_salary > 70000
ORDER BY avg_salary DESC;
```

---

## Common Table Expressions (CTEs)

CTEs provide a way to write more readable queries by defining temporary result sets.

### Syntax:
```sql
WITH cte_name AS (
    SELECT ...
)
SELECT ... FROM cte_name;
```

### Example 1: Simple CTE
```sql
-- Same query as above but with CTE (more readable)
WITH dept_averages AS (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY dept_id
)
SELECT 
    d.dept_name,
    da.avg_salary
FROM dept_averages da
JOIN departments d ON da.dept_id = d.dept_id
WHERE da.avg_salary > 70000
ORDER BY da.avg_salary DESC;
```

### Example 2: Multiple CTEs
```sql
-- Compare department sales and employee counts
WITH dept_sales AS (
    SELECT 
        e.dept_id,
        SUM(s.amount) AS total_sales
    FROM sales s
    JOIN employees e ON s.emp_id = e.emp_id
    GROUP BY e.dept_id
),
dept_counts AS (
    SELECT 
        dept_id,
        COUNT(*) AS employee_count
    FROM employees
    GROUP BY dept_id
)
SELECT 
    d.dept_name,
    dc.employee_count,
    COALESCE(ds.total_sales, 0) AS total_sales
FROM departments d
LEFT JOIN dept_counts dc ON d.dept_id = dc.dept_id
LEFT JOIN dept_sales ds ON d.dept_id = ds.dept_id
ORDER BY total_sales DESC;
```

### Example 3: Recursive CTE (Employee Hierarchy)
```sql
-- Build employee hierarchy (manager-subordinate relationships)
WITH RECURSIVE employee_hierarchy AS (
    -- Base case: top-level managers (no manager)
    SELECT 
        emp_id,
        first_name,
        last_name,
        manager_id,
        1 AS level,
        CAST(first_name AS CHAR(200)) AS hierarchy_path
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case: employees with managers
    SELECT 
        e.emp_id,
        e.first_name,
        e.last_name,
        e.manager_id,
        eh.level + 1,
        CONCAT(eh.hierarchy_path, ' > ', e.first_name)
    FROM employees e
    INNER JOIN employee_hierarchy eh ON e.manager_id = eh.emp_id
)
SELECT 
    emp_id,
    CONCAT(REPEAT('  ', level - 1), first_name, ' ', last_name) AS employee,
    level,
    hierarchy_path
FROM employee_hierarchy
ORDER BY hierarchy_path;
```

---

## Window Functions

Window functions perform calculations across a set of rows related to the current row, without grouping.

### Common Window Functions:
- `ROW_NUMBER()` - Assigns unique row numbers
- `RANK()` - Assigns rank with gaps
- `DENSE_RANK()` - Assigns rank without gaps
- `NTILE()` - Divides rows into buckets
- `LAG()` - Access previous row
- `LEAD()` - Access next row
- Aggregate functions with OVER()

### Syntax:
```sql
function_name() OVER (
    [PARTITION BY column]
    [ORDER BY column]
    [ROWS or RANGE clause]
)
```

### Example 1: ROW_NUMBER - Number employees by salary
```sql
SELECT 
    first_name,
    last_name,
    salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

**Result:**
```
+------------+-----------+----------+-------------+
| first_name | last_name | salary   | salary_rank |
+------------+-----------+----------+-------------+
| Emily      | Brown     | 95000.00 |           1 |
| David      | Jones     | 85000.00 |           2 |
| Lisa       | Garcia    | 80000.00 |           3 |
| John       | Smith     | 75000.00 |           4 |
...
```

### Example 2: RANK vs DENSE_RANK
```sql
SELECT 
    first_name,
    last_name,
    salary,
    RANK() OVER (ORDER BY salary DESC) AS rank_with_gaps,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS rank_no_gaps
FROM employees;
```

### Example 3: PARTITION BY - Rank within each department
```sql
SELECT 
    d.dept_name,
    e.first_name,
    e.last_name,
    e.salary,
    RANK() OVER (PARTITION BY e.dept_id ORDER BY e.salary DESC) AS dept_rank
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
ORDER BY d.dept_name, dept_rank;
```

**Result:**
```
+-------------+------------+-----------+----------+-----------+
| dept_name   | first_name | last_name | salary   | dept_rank |
+-------------+------------+-----------+----------+-----------+
| Engineering | Emily      | Brown     | 95000.00 |         1 |
| Engineering | David      | Jones     | 85000.00 |         2 |
| Engineering | Lisa       | Garcia    | 80000.00 |         3 |
| HR          | James      | Wilson    | 60000.00 |         1 |
| Marketing   | Tom        | Martinez  | 70000.00 |         1 |
| Marketing   | Anna       | Rodriguez | 65000.00 |         2 |
| Sales       | John       | Smith     | 75000.00 |         1 |
| Sales       | Sarah      | Johnson   | 65000.00 |         2 |
| Sales       | Mike       | Williams  | 60000.00 |         3 |
+-------------+------------+-----------+----------+-----------+
```

### Example 4: Running Total with SUM()
```sql
SELECT 
    sale_id,
    emp_id,
    sale_date,
    amount,
    SUM(amount) OVER (
        PARTITION BY emp_id 
        ORDER BY sale_date
    ) AS running_total
FROM sales
ORDER BY emp_id, sale_date;
```

### Example 5: LAG and LEAD - Compare with previous/next
```sql
SELECT 
    sale_date,
    amount,
    LAG(amount) OVER (ORDER BY sale_date) AS previous_sale,
    LEAD(amount) OVER (ORDER BY sale_date) AS next_sale,
    amount - LAG(amount) OVER (ORDER BY sale_date) AS change_from_previous
FROM sales
WHERE emp_id = 1
ORDER BY sale_date;
```

### Example 6: Moving Average
```sql
SELECT 
    sale_date,
    amount,
    AVG(amount) OVER (
        ORDER BY sale_date 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS moving_avg_3_days
FROM sales
WHERE emp_id = 1
ORDER BY sale_date;
```

### Example 7: NTILE - Divide into quartiles
```sql
SELECT 
    first_name,
    last_name,
    salary,
    NTILE(4) OVER (ORDER BY salary) AS salary_quartile
FROM employees
ORDER BY salary DESC;
```

---

## Transactions

Transactions ensure data integrity by grouping SQL statements into a single unit of work.

### ACID Properties:
- **Atomicity** - All or nothing
- **Consistency** - Valid state before and after
- **Isolation** - Transactions don't interfere
- **Durability** - Changes persist after commit

### Syntax:
```sql
START TRANSACTION;
-- SQL statements
COMMIT;  -- or ROLLBACK;
```

### Example 1: Basic Transaction
```sql
START TRANSACTION;

UPDATE employees
SET salary = salary * 1.10
WHERE dept_id = 1;

-- Check if update is correct
SELECT first_name, salary FROM employees WHERE dept_id = 1;

-- If everything looks good
COMMIT;

-- If something is wrong
-- ROLLBACK;
```

### Example 2: Transaction with Error Handling
```sql
START TRANSACTION;

-- Transfer money between accounts (conceptual example)
UPDATE employees SET salary = salary - 5000 WHERE emp_id = 1;
UPDATE employees SET salary = salary + 5000 WHERE emp_id = 2;

-- Verify both updates
SELECT emp_id, salary FROM employees WHERE emp_id IN (1, 2);

-- Commit if both succeeded
COMMIT;
```

### Example 3: Savepoints
```sql
START TRANSACTION;

INSERT INTO departments (dept_name, location) VALUES ('IT', 'Boston');
SAVEPOINT after_insert;

UPDATE departments SET location = 'Seattle' WHERE dept_name = 'IT';
SAVEPOINT after_update;

-- Oops, we want to undo the update but keep the insert
ROLLBACK TO SAVEPOINT after_insert;

COMMIT;
```

### Example 4: Transaction Isolation Levels
```sql
-- Set isolation level
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

START TRANSACTION;
-- Your queries here
COMMIT;
```

**Isolation Levels:**
- `READ UNCOMMITTED` - Lowest isolation, fastest
- `READ COMMITTED` - Default in many databases
- `REPEATABLE READ` - MySQL default
- `SERIALIZABLE` - Highest isolation, slowest

---

## Views

Views are virtual tables based on queries. They simplify complex queries and provide security.

### Syntax:
```sql
CREATE VIEW view_name AS
SELECT ...;
```

### Example 1: Simple View
```sql
CREATE VIEW employee_details AS
SELECT 
    e.emp_id,
    e.first_name,
    e.last_name,
    e.salary,
    d.dept_name,
    d.location
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;

-- Use the view
SELECT * FROM employee_details WHERE location = 'New York';
```

### Example 2: View with Aggregation
```sql
CREATE VIEW department_summary AS
SELECT 
    d.dept_id,
    d.dept_name,
    COUNT(e.emp_id) AS employee_count,
    AVG(e.salary) AS avg_salary,
    MAX(e.salary) AS max_salary,
    MIN(e.salary) AS min_salary
FROM departments d
LEFT JOIN employees e ON d.dept_id = e.dept_id
GROUP BY d.dept_id, d.dept_name;

-- Query the view
SELECT * FROM department_summary
WHERE employee_count > 2
ORDER BY avg_salary DESC;
```

### Example 3: Update a View
```sql
-- Some views can be updated
UPDATE employee_details
SET salary = salary * 1.05
WHERE dept_name = 'Sales';
```

### Example 4: Drop a View
```sql
DROP VIEW IF EXISTS employee_details;
```

---

## Stored Procedures

Stored procedures are reusable SQL code blocks stored in the database.

### Syntax:
```sql
DELIMITER //
CREATE PROCEDURE procedure_name(parameters)
BEGIN
    -- SQL statements
END //
DELIMITER ;
```

### Example 1: Simple Procedure
```sql
DELIMITER //
CREATE PROCEDURE GetEmployeeCount()
BEGIN
    SELECT COUNT(*) AS total_employees FROM employees;
END //
DELIMITER ;

-- Call the procedure
CALL GetEmployeeCount();
```

### Example 2: Procedure with Parameters
```sql
DELIMITER //
CREATE PROCEDURE GetEmployeesByDept(IN dept_name VARCHAR(50))
BEGIN
    SELECT e.first_name, e.last_name, e.salary
    FROM employees e
    JOIN departments d ON e.dept_id = d.dept_id
    WHERE d.dept_name = dept_name
    ORDER BY e.salary DESC;
END //
DELIMITER ;

-- Call with parameter
CALL GetEmployeesByDept('Engineering');
```

### Example 3: Procedure with OUT Parameter
```sql
DELIMITER //
CREATE PROCEDURE GetDeptAvgSalary(
    IN dept_name VARCHAR(50),
    OUT avg_salary DECIMAL(10,2)
)
BEGIN
    SELECT AVG(e.salary) INTO avg_salary
    FROM employees e
    JOIN departments d ON e.dept_id = d.dept_id
    WHERE d.dept_name = dept_name;
END //
DELIMITER ;

-- Call and get output
CALL GetDeptAvgSalary('Engineering', @avg_sal);
SELECT @avg_sal AS average_salary;
```

### Example 4: Procedure with IF Statement
```sql
DELIMITER //
CREATE PROCEDURE GiveRaise(
    IN employee_id INT,
    IN raise_percent DECIMAL(5,2)
)
BEGIN
    DECLARE current_salary DECIMAL(10,2);
    
    SELECT salary INTO current_salary
    FROM employees
    WHERE emp_id = employee_id;
    
    IF current_salary < 70000 THEN
        UPDATE employees
        SET salary = salary * (1 + raise_percent / 100)
        WHERE emp_id = employee_id;
        SELECT 'Raise applied' AS message;
    ELSE
        SELECT 'Salary already above threshold' AS message;
    END IF;
END //
DELIMITER ;

-- Call the procedure
CALL GiveRaise(3, 10);
```

---

## Practice Exercises

### Exercise 1
Find employees who earn more than the average salary in their department using a subquery.

<details>
<summary>Solution</summary>

```sql
SELECT 
    e1.first_name,
    e1.last_name,
    e1.salary,
    d.dept_name
FROM employees e1
JOIN departments d ON e1.dept_id = d.dept_id
WHERE e1.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.dept_id = e1.dept_id
)
ORDER BY d.dept_name, e1.salary DESC;
```
</details>

### Exercise 2
Create a CTE to find the top 2 highest-paid employees in each department.

<details>
<summary>Solution</summary>

```sql
WITH ranked_employees AS (
    SELECT 
        e.first_name,
        e.last_name,
        e.salary,
        d.dept_name,
        RANK() OVER (PARTITION BY e.dept_id ORDER BY e.salary DESC) AS salary_rank
    FROM employees e
    JOIN departments d ON e.dept_id = d.dept_id
)
SELECT first_name, last_name, salary, dept_name
FROM ranked_employees
WHERE salary_rank <= 2
ORDER BY dept_name, salary_rank;
```
</details>

### Exercise 3
Calculate the cumulative sales for each employee by date.

<details>
<summary>Solution</summary>

```sql
SELECT 
    e.first_name,
    e.last_name,
    s.sale_date,
    s.amount,
    SUM(s.amount) OVER (
        PARTITION BY s.emp_id 
        ORDER BY s.sale_date
    ) AS cumulative_sales
FROM sales s
JOIN employees e ON s.emp_id = e.emp_id
ORDER BY e.last_name, s.sale_date;
```
</details>

### Exercise 4
Create a view showing each department's total sales and employee count.

<details>
<summary>Solution</summary>

```sql
CREATE VIEW department_performance AS
SELECT 
    d.dept_id,
    d.dept_name,
    COUNT(DISTINCT e.emp_id) AS employee_count,
    COUNT(s.sale_id) AS total_sales_count,
    COALESCE(SUM(s.amount), 0) AS total_sales_amount
FROM departments d
LEFT JOIN employees e ON d.dept_id = e.dept_id
LEFT JOIN sales s ON e.emp_id = s.emp_id
GROUP BY d.dept_id, d.dept_name;

-- Query the view
SELECT * FROM department_performance
ORDER BY total_sales_amount DESC;
```
</details>

### Exercise 5
Find the difference between each sale and the previous sale for each employee.

<details>
<summary>Solution</summary>

```sql
SELECT 
    e.first_name,
    e.last_name,
    s.sale_date,
    s.amount,
    LAG(s.amount) OVER (
        PARTITION BY s.emp_id 
        ORDER BY s.sale_date
    ) AS previous_sale,
    s.amount - LAG(s.amount) OVER (
        PARTITION BY s.emp_id 
        ORDER BY s.sale_date
    ) AS difference
FROM sales s
JOIN employees e ON s.emp_id = e.emp_id
ORDER BY e.last_name, s.sale_date;
```
</details>

---

## Summary

In this tutorial, you learned:
- ✅ Subqueries for complex filtering and calculations
- ✅ CTEs for readable and maintainable queries
- ✅ Window functions for advanced analytics
- ✅ Transactions for data integrity
- ✅ Views for query simplification
- ✅ Stored procedures for reusable code

**Next Steps:** Continue to the [Database Design Tutorial](04_Database_Design.md) to learn about creating efficient database structures!

---

**Master SQL! 💪**
