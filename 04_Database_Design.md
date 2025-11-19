# SQL Tutorial - Database Design 🏗️

Welcome to the database design tutorial! Learn how to create well-structured, efficient databases from scratch.

## Table of Contents
- [Introduction](#introduction)
- [Database Design Principles](#database-design-principles)
- [Creating Tables](#creating-tables)
- [Data Types](#data-types)
- [Constraints](#constraints)
- [Indexes](#indexes)
- [Relationships](#relationships)
- [Normalization](#normalization)
- [Best Practices](#best-practices)
- [Practice Project](#practice-project)

---

## Introduction

Database design is the process of organizing data according to a database model. Good design ensures data integrity, efficiency, and scalability.

## Database Design Principles

### Key Principles:
1. **Data Integrity** - Ensure accuracy and consistency
2. **Minimal Redundancy** - Avoid duplicate data
3. **Performance** - Optimize for speed
4. **Scalability** - Handle growth
5. **Security** - Protect sensitive data
6. **Maintainability** - Easy to update and modify

### Design Process:
1. Requirements Analysis
2. Conceptual Design (ER Diagram)
3. Logical Design (Tables and Relationships)
4. Physical Design (Implementation)
5. Optimization

---

## Creating Tables

### Basic Syntax:
```sql
CREATE TABLE table_name (
    column1 datatype constraints,
    column2 datatype constraints,
    ...
    table_constraints
);
```

### Example 1: Simple Table
```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Example 2: Table with Multiple Constraints
```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(100) NOT NULL,
    sku VARCHAR(50) UNIQUE NOT NULL,
    price DECIMAL(10, 2) NOT NULL CHECK (price >= 0),
    stock_quantity INT DEFAULT 0 CHECK (stock_quantity >= 0),
    category_id INT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(category_id)
);
```

### Modifying Tables:

#### Add Column
```sql
ALTER TABLE users
ADD COLUMN phone VARCHAR(20);
```

#### Modify Column
```sql
ALTER TABLE users
MODIFY COLUMN email VARCHAR(150) NOT NULL;
```

#### Drop Column
```sql
ALTER TABLE users
DROP COLUMN phone;
```

#### Rename Table
```sql
RENAME TABLE users TO customers;
```

#### Drop Table
```sql
DROP TABLE IF EXISTS old_table;
```

---

## Data Types

### Numeric Types:

```sql
CREATE TABLE numeric_examples (
    -- Integer types
    tiny_int TINYINT,           -- -128 to 127
    small_int SMALLINT,         -- -32,768 to 32,767
    medium_int MEDIUMINT,       -- -8,388,608 to 8,388,607
    regular_int INT,            -- -2,147,483,648 to 2,147,483,647
    big_int BIGINT,             -- Very large numbers
    
    -- Unsigned (positive only)
    positive_int INT UNSIGNED,  -- 0 to 4,294,967,295
    
    -- Decimal types
    exact_price DECIMAL(10, 2), -- Exact decimal (10 digits, 2 after decimal)
    float_num FLOAT,            -- Approximate decimal
    double_num DOUBLE           -- Larger approximate decimal
);
```

### String Types:

```sql
CREATE TABLE string_examples (
    -- Fixed length (faster for known lengths)
    country_code CHAR(2),       -- Always 2 characters
    
    -- Variable length (more flexible)
    username VARCHAR(50),       -- Up to 50 characters
    email VARCHAR(255),         -- Up to 255 characters
    
    -- Text types
    short_text TINYTEXT,        -- Up to 255 characters
    description TEXT,           -- Up to 65,535 characters
    long_content MEDIUMTEXT,    -- Up to 16,777,215 characters
    huge_content LONGTEXT,      -- Up to 4,294,967,295 characters
    
    -- Binary data
    file_data BLOB              -- Binary data
);
```

### Date and Time Types:

```sql
CREATE TABLE datetime_examples (
    -- Date only
    birth_date DATE,            -- YYYY-MM-DD
    
    -- Time only
    meeting_time TIME,          -- HH:MM:SS
    
    -- Date and time
    created_at DATETIME,        -- YYYY-MM-DD HH:MM:SS
    
    -- Timestamp (auto-updates, timezone-aware)
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    -- Year only
    year_founded YEAR           -- YYYY
);
```

### Other Types:

```sql
CREATE TABLE other_types (
    -- Boolean
    is_active BOOLEAN,          -- TRUE or FALSE (stored as TINYINT)
    
    -- Enum (predefined list)
    status ENUM('pending', 'approved', 'rejected'),
    
    -- Set (multiple values from list)
    permissions SET('read', 'write', 'delete'),
    
    -- JSON (MySQL 5.7+)
    metadata JSON
);
```

---

## Constraints

Constraints enforce rules on data in tables.

### PRIMARY KEY

Uniquely identifies each record.

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100)
);

-- Composite primary key
CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id)
);
```

### FOREIGN KEY

Links tables together and maintains referential integrity.

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```

**ON DELETE options:**
- `CASCADE` - Delete related records
- `SET NULL` - Set foreign key to NULL
- `RESTRICT` - Prevent deletion
- `NO ACTION` - Same as RESTRICT

### UNIQUE

Ensures all values in a column are different.

```sql
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(100) UNIQUE NOT NULL,
    username VARCHAR(50) UNIQUE NOT NULL
);

-- Composite unique constraint
CREATE TABLE user_profiles (
    user_id INT,
    profile_name VARCHAR(50),
    UNIQUE (user_id, profile_name)
);
```

### NOT NULL

Ensures a column cannot have NULL values.

```sql
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    phone VARCHAR(20)  -- Can be NULL
);
```

### CHECK

Validates data against a condition.

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100),
    price DECIMAL(10, 2) CHECK (price >= 0),
    stock INT CHECK (stock >= 0),
    discount_percent INT CHECK (discount_percent BETWEEN 0 AND 100)
);

-- Named check constraint
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    age INT,
    salary DECIMAL(10, 2),
    CONSTRAINT chk_age CHECK (age >= 18 AND age <= 65),
    CONSTRAINT chk_salary CHECK (salary > 0)
);
```

### DEFAULT

Sets a default value for a column.

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    order_date DATE DEFAULT (CURRENT_DATE),
    status VARCHAR(20) DEFAULT 'pending',
    quantity INT DEFAULT 1,
    is_shipped BOOLEAN DEFAULT FALSE
);
```

### Adding Constraints Later:

```sql
-- Add primary key
ALTER TABLE students
ADD PRIMARY KEY (student_id);

-- Add foreign key
ALTER TABLE orders
ADD FOREIGN KEY (customer_id) REFERENCES customers(customer_id);

-- Add unique constraint
ALTER TABLE users
ADD UNIQUE (email);

-- Add check constraint
ALTER TABLE products
ADD CONSTRAINT chk_price CHECK (price >= 0);

-- Drop constraint
ALTER TABLE products
DROP CONSTRAINT chk_price;
```

---

## Indexes

Indexes speed up data retrieval but slow down inserts/updates.

### Types of Indexes:
1. **Primary Key Index** - Automatically created
2. **Unique Index** - For unique values
3. **Regular Index** - For faster searches
4. **Full-Text Index** - For text searches
5. **Composite Index** - Multiple columns

### Creating Indexes:

```sql
-- Simple index
CREATE INDEX idx_email ON users(email);

-- Unique index
CREATE UNIQUE INDEX idx_username ON users(username);

-- Composite index
CREATE INDEX idx_name ON employees(last_name, first_name);

-- Full-text index
CREATE FULLTEXT INDEX idx_description ON products(description);

-- Index on expression
CREATE INDEX idx_upper_email ON users((UPPER(email)));
```

### Using Indexes:

```sql
-- This query will use the index
SELECT * FROM users WHERE email = 'john@example.com';

-- This will also use composite index
SELECT * FROM employees WHERE last_name = 'Smith';

-- Full-text search
SELECT * FROM products 
WHERE MATCH(description) AGAINST('laptop computer');
```

### Managing Indexes:

```sql
-- Show indexes
SHOW INDEX FROM users;

-- Drop index
DROP INDEX idx_email ON users;

-- Analyze index usage
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
```

### Index Best Practices:

```sql
-- Good: Index frequently queried columns
CREATE INDEX idx_status ON orders(status);

-- Good: Index foreign keys
CREATE INDEX idx_customer_id ON orders(customer_id);

-- Good: Composite index for common multi-column queries
CREATE INDEX idx_search ON products(category_id, price);

-- Avoid: Too many indexes (slows down writes)
-- Avoid: Indexing small tables
-- Avoid: Indexing columns with low cardinality (few unique values)
```

---

## Relationships

### One-to-One Relationship

One record in Table A relates to one record in Table B.

```sql
-- Example: User and UserProfile
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE user_profiles (
    profile_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT UNIQUE NOT NULL,  -- UNIQUE makes it one-to-one
    bio TEXT,
    avatar_url VARCHAR(255),
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);
```

### One-to-Many Relationship

One record in Table A relates to many records in Table B.

```sql
-- Example: Customer and Orders
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date DATE,
    total_amount DECIMAL(10, 2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- One customer can have many orders
```

### Many-to-Many Relationship

Many records in Table A relate to many records in Table B.

```sql
-- Example: Students and Courses
CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    student_name VARCHAR(100) NOT NULL
);

CREATE TABLE courses (
    course_id INT PRIMARY KEY AUTO_INCREMENT,
    course_name VARCHAR(100) NOT NULL
);

-- Junction/Bridge table
CREATE TABLE student_courses (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    grade VARCHAR(2),
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);

-- Many students can enroll in many courses
```

---

## Normalization

Normalization organizes data to reduce redundancy and improve integrity.

### First Normal Form (1NF)

- No repeating groups
- Each cell contains a single value
- Each record is unique

**Before (Not 1NF):**
```sql
CREATE TABLE students (
    student_id INT,
    name VARCHAR(100),
    courses VARCHAR(255)  -- "Math, Science, English" - Multiple values!
);
```

**After (1NF):**
```sql
CREATE TABLE students (
    student_id INT,
    name VARCHAR(100)
);

CREATE TABLE student_courses (
    student_id INT,
    course_name VARCHAR(50)
);
```

### Second Normal Form (2NF)

- Must be in 1NF
- All non-key attributes depend on the entire primary key

**Before (Not 2NF):**
```sql
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    product_name VARCHAR(100),  -- Depends only on product_id, not the composite key
    quantity INT,
    PRIMARY KEY (order_id, product_id)
);
```

**After (2NF):**
```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100)
);

CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

### Third Normal Form (3NF)

- Must be in 2NF
- No transitive dependencies (non-key attributes depend only on primary key)

**Before (Not 3NF):**
```sql
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(100),
    dept_id INT,
    dept_name VARCHAR(50),  -- Depends on dept_id, not emp_id (transitive)
    dept_location VARCHAR(50)  -- Depends on dept_id
);
```

**After (3NF):**
```sql
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50),
    dept_location VARCHAR(50)
);

CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(100),
    dept_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);
```

### When to Denormalize:

Sometimes you intentionally break normalization for performance:

```sql
-- Denormalized for faster queries (avoids JOIN)
CREATE TABLE order_summary (
    order_id INT PRIMARY KEY,
    customer_name VARCHAR(100),  -- Duplicated from customers table
    total_amount DECIMAL(10, 2),
    order_date DATE
);

-- Use when:
-- - Read-heavy applications
-- - Reports and analytics
-- - Performance is critical
```

---

## Best Practices

### Naming Conventions:

```sql
-- Tables: plural nouns
CREATE TABLE customers (...);
CREATE TABLE products (...);

-- Columns: singular, descriptive
CREATE TABLE users (
    user_id INT,           -- Not just 'id'
    first_name VARCHAR(50), -- Snake_case or camelCase
    email_address VARCHAR(100)
);

-- Indexes: prefix with 'idx_'
CREATE INDEX idx_email ON users(email);

-- Foreign keys: reference the related table
CREATE TABLE orders (
    order_id INT,
    customer_id INT  -- References customers table
);
```

### Data Type Selection:

```sql
-- Use appropriate sizes
CREATE TABLE users (
    age TINYINT UNSIGNED,     -- 0-255 is enough for age
    country_code CHAR(2),      -- Fixed 2 characters
    email VARCHAR(255),        -- Variable length
    bio TEXT                   -- Can be long
);

-- Use DECIMAL for money
CREATE TABLE products (
    price DECIMAL(10, 2)  -- Not FLOAT or DOUBLE
);
```

### Default Values and NULL:

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE DEFAULT (CURRENT_DATE),  -- Default value
    status VARCHAR(20) DEFAULT 'pending',    -- Default value
    notes TEXT                                -- Can be NULL
);
```

### Documentation:

```sql
-- Add comments to complex tables
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT COMMENT 'Unique product identifier',
    sku VARCHAR(50) UNIQUE COMMENT 'Stock Keeping Unit',
    price DECIMAL(10, 2) COMMENT 'Price in USD'
) COMMENT='Product catalog table';
```

---

## Practice Project

### E-Commerce Database Design

Let's design a complete e-commerce database:

```sql
-- Create database
CREATE DATABASE ecommerce;
USE ecommerce;

-- 1. Categories
CREATE TABLE categories (
    category_id INT PRIMARY KEY AUTO_INCREMENT,
    category_name VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    parent_category_id INT,
    FOREIGN KEY (parent_category_id) REFERENCES categories(category_id)
);

-- 2. Products
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(200) NOT NULL,
    sku VARCHAR(50) UNIQUE NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL CHECK (price >= 0),
    cost DECIMAL(10, 2) CHECK (cost >= 0),
    stock_quantity INT DEFAULT 0 CHECK (stock_quantity >= 0),
    category_id INT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(category_id),
    INDEX idx_category (category_id),
    INDEX idx_sku (sku)
);

-- 3. Customers
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(20),
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login TIMESTAMP,
    INDEX idx_email (email)
);

-- 4. Addresses
CREATE TABLE addresses (
    address_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    address_type ENUM('billing', 'shipping') NOT NULL,
    street_address VARCHAR(255) NOT NULL,
    city VARCHAR(100) NOT NULL,
    state VARCHAR(50),
    postal_code VARCHAR(20) NOT NULL,
    country VARCHAR(50) NOT NULL,
    is_default BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE CASCADE
);

-- 5. Orders
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status ENUM('pending', 'processing', 'shipped', 'delivered', 'cancelled') DEFAULT 'pending',
    subtotal DECIMAL(10, 2) NOT NULL,
    tax DECIMAL(10, 2) DEFAULT 0,
    shipping_cost DECIMAL(10, 2) DEFAULT 0,
    total_amount DECIMAL(10, 2) NOT NULL,
    shipping_address_id INT,
    billing_address_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (shipping_address_id) REFERENCES addresses(address_id),
    FOREIGN KEY (billing_address_id) REFERENCES addresses(address_id),
    INDEX idx_customer (customer_id),
    INDEX idx_status (status),
    INDEX idx_order_date (order_date)
);

-- 6. Order Items
CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    unit_price DECIMAL(10, 2) NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id),
    INDEX idx_order (order_id),
    INDEX idx_product (product_id)
);

-- 7. Reviews
CREATE TABLE reviews (
    review_id INT PRIMARY KEY AUTO_INCREMENT,
    product_id INT NOT NULL,
    customer_id INT NOT NULL,
    rating INT NOT NULL CHECK (rating BETWEEN 1 AND 5),
    title VARCHAR(200),
    comment TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE CASCADE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    UNIQUE (product_id, customer_id),  -- One review per customer per product
    INDEX idx_product (product_id),
    INDEX idx_rating (rating)
);

-- 8. Shopping Cart
CREATE TABLE cart_items (
    cart_item_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    added_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id),
    UNIQUE (customer_id, product_id),
    INDEX idx_customer (customer_id)
);

-- Example queries:

-- Get product details with average rating
SELECT 
    p.product_name,
    p.price,
    p.stock_quantity,
    c.category_name,
    AVG(r.rating) AS avg_rating,
    COUNT(r.review_id) AS review_count
FROM products p
LEFT JOIN categories c ON p.category_id = c.category_id
LEFT JOIN reviews r ON p.product_id = r.product_id
WHERE p.is_active = TRUE
GROUP BY p.product_id
ORDER BY avg_rating DESC;

-- Get customer order history
SELECT 
    o.order_id,
    o.order_date,
    o.status,
    o.total_amount,
    COUNT(oi.order_item_id) AS items_count
FROM orders o
LEFT JOIN order_items oi ON o.order_id = oi.order_id
WHERE o.customer_id = 1
GROUP BY o.order_id
ORDER BY o.order_date DESC;
```

---

## Summary

In this tutorial, you learned:
- ✅ Database design principles
- ✅ Creating and modifying tables
- ✅ Choosing appropriate data types
- ✅ Using constraints for data integrity
- ✅ Creating indexes for performance
- ✅ Designing table relationships
- ✅ Normalization concepts
- ✅ Best practices for database design

**Congratulations!** You've completed all SQL tutorials. You now have the knowledge to design and query databases effectively!

---

**Build Amazing Databases! 🎉**
