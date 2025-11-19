# 📦 מדריך מקיף על WITH (CTE) ב-SQL

## 📌 מה זה WITH ולמה זה מהפכני?

**WITH** (נקרא גם **CTE - Common Table Expression**) הוא כלי שמאפשר לנו ליצור "טבלאות זמניות" בתוך שאילתה.

**במילים פשוטות:**
במקום לכתוב שאילתות ענקיות ומסובכות, אנחנו שוברים אותן לחלקים קטנים ומובנים! 🎯

### דוגמה - לפני ואחרי

#### ❌ לפני (מסובך!)

```sql
SELECT 
    c.customer_id,
    c.name,
    o.total_orders,
    p.total_payments
FROM customers c
JOIN (
    SELECT customer_id, COUNT(*) as total_orders
    FROM orders
    GROUP BY customer_id
) o ON c.customer_id = o.customer_id
JOIN (
    SELECT customer_id, SUM(amount) as total_payments
    FROM payments
    GROUP BY customer_id
) p ON c.customer_id = p.customer_id;
```

**בלגן! קשה לקרוא וקשה לתחזק!** 😵

---

#### ✅ אחרי (ברור!)

```sql
WITH customer_orders AS (
    SELECT customer_id, COUNT(*) as total_orders
    FROM orders
    GROUP BY customer_id
),
customer_payments AS (
    SELECT customer_id, SUM(amount) as total_payments
    FROM payments
    GROUP BY customer_id
)
SELECT 
    c.customer_id,
    c.name,
    co.total_orders,
    cp.total_payments
FROM customers c
JOIN customer_orders co ON c.customer_id = co.customer_id
JOIN customer_payments cp ON c.customer_id = cp.customer_id;
```

**ברור, קריא, ומאורגן!** ✨

---

## 🎯 מתי נשתמש ב-WITH?

✅ **כדאי להשתמש כש:**
- יש subqueries מסובכים או חוזרים
- רוצים לשבור שאילתה גדולה לחלקים קטנים
- צריכים לחשב ביניים (intermediate results)
- רוצים קוד קריא ומתוחזק
- עובדים עם שאילתות רקורסיביות (Recursive)

❌ **לא כדאי כש:**
- השאילתה פשוטה ומובנת כמו שהיא
- צריכים ביצועים קריטיים (לפעמים Subquery יותר מהיר)
- משתמשים ב-MySQL ישן (לפני 8.0)

---

## 🔤 תחביר בסיסי

### מבנה כללי

```sql
WITH cte_name AS (
    -- שאילתה
    SELECT ...
)
SELECT * FROM cte_name;
```

### כמה CTEs ביחד

```sql
WITH 
    first_cte AS (
        SELECT ...
    ),
    second_cte AS (
        SELECT ...
    ),
    third_cte AS (
        SELECT ... FROM first_cte  -- אפשר להשתמש ב-CTE קודם!
    )
SELECT * FROM third_cte;
```

---

## 📚 סוגי WITH - 4 שימושים עיקריים

### 1️⃣ CTE פשוט (Simple CTE)
### 2️⃣ CTE מרובה (Multiple CTEs)
### 3️⃣ CTE רקורסיבי (Recursive CTE)
### 4️⃣ CTE עם DML (INSERT/UPDATE/DELETE)

בואו נעבור על כל אחד! 🚀

---

## 1️⃣ CTE פשוט - Simple CTE

### דוגמה בסיסית

```sql
-- חישוב סטטיסטיקות לקוחות
WITH customer_stats AS (
    SELECT 
        customer_id,
        COUNT(*) as total_orders,
        SUM(amount) as total_spent,
        AVG(amount) as avg_order
    FROM orders
    GROUP BY customer_id
)
SELECT 
    c.name,
    cs.total_orders AS 'סה"כ הזמנות',
    cs.total_spent AS 'סה"כ הוצאה',
    cs.avg_order AS 'ממוצע הזמנה'
FROM customers c
JOIN customer_stats cs ON c.customer_id = cs.customer_id
WHERE cs.total_spent > 10000
ORDER BY cs.total_spent DESC;
```

**מה קורה פה?**
1. ✅ יצרנו CTE בשם `customer_stats`
2. ✅ חישבנו סטטיסטיקות לכל לקוח
3. ✅ השתמשנו ב-CTE כמו טבלה רגילה

---

### דוגמה - סינון מוצרים פופולריים

```sql
WITH popular_products AS (
    SELECT 
        product_id,
        product_name,
        COUNT(*) as times_ordered
    FROM order_items oi
    JOIN products p ON oi.product_id = p.product_id
    GROUP BY product_id, product_name
    HAVING COUNT(*) > 50
)
SELECT 
    pp.product_name AS 'שם המוצר',
    pp.times_ordered AS 'נמכר פעמים',
    p.price AS 'מחיר',
    p.stock AS 'במלאי'
FROM popular_products pp
JOIN products p ON pp.product_id = p.product_id
ORDER BY pp.times_ordered DESC;
```

---

### דוגמה - מציאת ממוצעים

```sql
-- מוצרים מעל המחיר הממוצע
WITH avg_price AS (
    SELECT AVG(price) as average
    FROM products
)
SELECT 
    product_name,
    price,
    (price - ap.average) as diff_from_avg
FROM products, avg_price ap
WHERE price > ap.average
ORDER BY price DESC;
```

---

## 2️⃣ CTE מרובה - Multiple CTEs

### שימוש במספר CTEs

```sql
WITH 
    -- CTE 1: הזמנות לפי לקוח
    customer_orders AS (
        SELECT 
            customer_id,
            COUNT(*) as order_count,
            SUM(total_amount) as total_spent
        FROM orders
        WHERE order_date >= '2025-01-01'
        GROUP BY customer_id
    ),
    -- CTE 2: לקוחות VIP
    vip_customers AS (
        SELECT customer_id
        FROM customer_orders
        WHERE total_spent > 10000
    ),
    -- CTE 3: מוצרים שנקנו על ידי VIP
    vip_products AS (
        SELECT DISTINCT p.product_id, p.product_name
        FROM order_items oi
        JOIN orders o ON oi.order_id = o.order_id
        JOIN products p ON oi.product_id = p.product_id
        WHERE o.customer_id IN (SELECT customer_id FROM vip_customers)
    )
SELECT 
    vp.product_name AS 'מוצר VIP',
    COUNT(DISTINCT oi.order_id) AS 'מספר הזמנות',
    SUM(oi.quantity) AS 'כמות נמכרה'
FROM vip_products vp
JOIN order_items oi ON vp.product_id = oi.product_id
GROUP BY vp.product_id, vp.product_name
ORDER BY COUNT(DISTINCT oi.order_id) DESC;
```

**מה עשינו?**
1. 📊 חישבנו הזמנות לפי לקוח
2. 👑 זיהינו לקוחות VIP
3. 🛍️ מצאנו אילו מוצרים VIP קונים
4. 📈 חישבנו סטטיסטיקות

---

### דוגמה - דוח מכירות מורכב

```sql
WITH 
    -- מכירות חודשיות
    monthly_sales AS (
        SELECT 
            DATE_FORMAT(order_date, '%Y-%m') as month,
            SUM(total_amount) as total_sales,
            COUNT(*) as order_count
        FROM orders
        GROUP BY DATE_FORMAT(order_date, '%Y-%m')
    ),
    -- ממוצע חודשי
    avg_monthly AS (
        SELECT AVG(total_sales) as avg_sales
        FROM monthly_sales
    ),
    -- חודשים מעל הממוצע
    above_avg_months AS (
        SELECT ms.*
        FROM monthly_sales ms, avg_monthly am
        WHERE ms.total_sales > am.avg_sales
    )
SELECT 
    month AS 'חודש',
    total_sales AS 'מכירות',
    order_count AS 'מספר הזמנות',
    (SELECT avg_sales FROM avg_monthly) AS 'ממוצע',
    total_sales - (SELECT avg_sales FROM avg_monthly) AS 'הפרש מממוצע'
FROM above_avg_months
ORDER BY month;
```

---

### דוגמה - שרשרת של CTEs

```sql
WITH 
    -- שלב 1: הזמנות השנה
    this_year_orders AS (
        SELECT *
        FROM orders
        WHERE YEAR(order_date) = 2025
    ),
    -- שלב 2: לקוחות פעילים
    active_customers AS (
        SELECT DISTINCT customer_id
        FROM this_year_orders
    ),
    -- שלב 3: מוצרים שנקנו
    purchased_products AS (
        SELECT DISTINCT oi.product_id
        FROM order_items oi
        JOIN this_year_orders tyo ON oi.order_id = tyo.order_id
    ),
    -- שלב 4: ספקים רלוונטיים
    relevant_suppliers AS (
        SELECT DISTINCT s.supplier_id, s.supplier_name
        FROM suppliers s
        JOIN products p ON s.supplier_id = p.supplier_id
        WHERE p.product_id IN (SELECT product_id FROM purchased_products)
    )
SELECT 
    rs.supplier_name AS 'ספק',
    COUNT(DISTINCT pp.product_id) AS 'מוצרים פעילים',
    (SELECT COUNT(*) FROM active_customers) AS 'לקוחות פעילים',
    (SELECT COUNT(*) FROM this_year_orders) AS 'הזמנות השנה'
FROM relevant_suppliers rs
JOIN products p ON rs.supplier_id = p.supplier_id
JOIN purchased_products pp ON p.product_id = pp.product_id
GROUP BY rs.supplier_id, rs.supplier_name
ORDER BY COUNT(DISTINCT pp.product_id) DESC;
```

---

## 3️⃣ CTE רקורסיבי - Recursive CTE

### 🔁 מה זה רקורסיה?

פונקציה או שאילתה שקוראת לעצמה! שימושי למבני עץ, היררכיות, וגרפים.

### תחביר רקורסיבי

```sql
WITH RECURSIVE cte_name AS (
    -- חלק 1: Anchor (בסיס)
    SELECT ...
    
    UNION ALL
    
    -- חלק 2: Recursive (רקורסיה)
    SELECT ...
    FROM cte_name  -- קורא לעצמו!
    WHERE <תנאי עצירה>
)
SELECT * FROM cte_name;
```

---

### דוגמה 1: מספרים מ-1 עד 10

```sql
WITH RECURSIVE numbers AS (
    -- בסיס: מתחילים מ-1
    SELECT 1 AS n
    
    UNION ALL
    
    -- רקורסיה: מוסיפים 1 בכל פעם
    SELECT n + 1
    FROM numbers
    WHERE n < 10  -- תנאי עצירה!
)
SELECT * FROM numbers;
```

**תוצאה:**
```
n
---
1
2
3
4
5
6
7
8
9
10
```

---

### דוגמה 2: היררכיית עובדים (מנהלים)

```sql
-- טבלת עובדים
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    manager_id INT,
    position VARCHAR(50)
);

-- נתונים לדוגמה
INSERT INTO employees VALUES
    (1, 'שרה (CEO)', NULL, 'מנכ"ל'),
    (2, 'דני', 1, 'סמנכ"ל'),
    (3, 'מיכל', 1, 'סמנכ"ל'),
    (4, 'אבי', 2, 'מנהל צוות'),
    (5, 'רחל', 2, 'מנהל צוות'),
    (6, 'יוסי', 4, 'מפתח'),
    (7, 'תמר', 4, 'מפתח'),
    (8, 'עומר', 5, 'מפתח');

-- מציאת כל ההיררכיה תחת דני (2)
WITH RECURSIVE employee_hierarchy AS (
    -- בסיס: התחל מדני
    SELECT 
        employee_id,
        name,
        manager_id,
        position,
        1 AS level,
        CAST(name AS CHAR(200)) AS path
    FROM employees
    WHERE employee_id = 2
    
    UNION ALL
    
    -- רקורסיה: מצא את כל העובדים מתחת
    SELECT 
        e.employee_id,
        e.name,
        e.manager_id,
        e.position,
        eh.level + 1,
        CONCAT(eh.path, ' -> ', e.name)
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT 
    REPEAT('  ', level - 1) AS indent,
    name AS 'עובד',
    position AS 'תפקיד',
    level AS 'רמה',
    path AS 'נתיב'
FROM employee_hierarchy
ORDER BY level, name;
```

**תוצאה:**
```
indent | עובד  | תפקיד      | רמה | נתיב
-------|-------|------------|-----|---------------------------
       | דני   | סמנכ"ל     | 1   | דני
  	   | אבי   | מנהל צוות  | 2   | דני -> אבי
  	   | רחל   | מנהל צוות  | 2   | דני -> רחל
    	 | יוסי  | מפתח       | 3   | דני -> אבי -> יוסי
    	 | תמר   | מפתח       | 3   | דני -> אבי -> תמר
    	 | עומר  | מפתח       | 3   | דני -> רחל -> עומר
```

---

### דוגמה 3: קטגוריות מוצרים (עץ)

```sql
-- טבלת קטגוריות
CREATE TABLE categories (
    category_id INT PRIMARY KEY,
    category_name VARCHAR(100),
    parent_id INT
);

INSERT INTO categories VALUES
    (1, 'אלקטרוניקה', NULL),
    (2, 'מחשבים', 1),
    (3, 'טלפונים', 1),
    (4, 'מחשבים ניידים', 2),
    (5, 'מחשבים שולחניים', 2),
    (6, 'סמארטפונים', 3),
    (7, 'טאבלטים', 3);

-- כל הקטגוריות תחת "אלקטרוניקה"
WITH RECURSIVE category_tree AS (
    -- בסיס
    SELECT 
        category_id,
        category_name,
        parent_id,
        0 AS depth,
        CAST(category_name AS CHAR(200)) AS full_path
    FROM categories
    WHERE parent_id IS NULL
    
    UNION ALL
    
    -- רקורסיה
    SELECT 
        c.category_id,
        c.category_name,
        c.parent_id,
        ct.depth + 1,
        CONCAT(ct.full_path, ' > ', c.category_name)
    FROM categories c
    JOIN category_tree ct ON c.parent_id = ct.category_id
)
SELECT 
    REPEAT('--', depth) AS tree,
    category_name AS 'קטגוריה',
    full_path AS 'נתיב מלא'
FROM category_tree
ORDER BY full_path;
```

**תוצאה:**
```
tree      | קטגוריה           | נתיב מלא
----------|-------------------|------------------------------------------
          | אלקטרוניקה        | אלקטרוניקה
--        | מחשבים            | אלקטרוניקה > מחשבים
----      | מחשבים ניידים    | אלקטרוניקה > מחשבים > מחשבים ניידים
----      | מחשבים שולחניים  | אלקטרוניקה > מחשבים > מחשבים שולחניים
--        | טלפונים           | אלקטרוניקה > טלפונים
----      | סמארטפונים        | אלקטרוניקה > טלפונים > סמארטפונים
----      | טאבלטים           | אלקטרוניקה > טלפונים > טאבלטים
```

---

### דוגמה 4: סדרת פיבונאצ'י

```sql
WITH RECURSIVE fibonacci AS (
    -- בסיס: שני המספרים הראשונים
    SELECT 1 AS n, 0 AS fib, 1 AS next_fib
    
    UNION ALL
    
    -- רקורסיה: חישוב המספר הבא
    SELECT 
        n + 1,
        next_fib,
        fib + next_fib
    FROM fibonacci
    WHERE n < 10
)
SELECT 
    n AS 'מיקום',
    fib AS 'מספר פיבונאצ\'י'
FROM fibonacci;
```

**תוצאה:**
```
מיקום | מספר פיבונאצ'י
------|---------------
1     | 0
2     | 1
3     | 1
4     | 2
5     | 3
6     | 5
7     | 8
8     | 13
9     | 21
10    | 34
```

---

### דוגמה 5: מבנה תיקיות (Path Finding)

```sql
-- תיקיות
CREATE TABLE folders (
    folder_id INT PRIMARY KEY,
    folder_name VARCHAR(100),
    parent_folder_id INT
);

INSERT INTO folders VALUES
    (1, 'Root', NULL),
    (2, 'Documents', 1),
    (3, 'Pictures', 1),
    (4, 'Work', 2),
    (5, 'Personal', 2),
    (6, 'Projects', 4),
    (7, 'Reports', 4);

-- מציאת הנתיב המלא לתיקייה
WITH RECURSIVE folder_path AS (
    SELECT 
        folder_id,
        folder_name,
        parent_folder_id,
        CAST(folder_name AS CHAR(500)) AS path
    FROM folders
    WHERE folder_id = 6  -- Projects
    
    UNION ALL
    
    SELECT 
        f.folder_id,
        f.folder_name,
        f.parent_folder_id,
        CONCAT(f.folder_name, '/', fp.path)
    FROM folders f
    JOIN folder_path fp ON f.folder_id = fp.parent_folder_id
)
SELECT path AS 'נתיב מלא'
FROM folder_path
WHERE parent_folder_id IS NULL;
```

**תוצאה:**
```
נתיב מלא
--------------------------
Root/Documents/Work/Projects
```

---

## 4️⃣ CTE עם DML (Data Manipulation)

### שימוש ב-WITH עם INSERT

```sql
WITH new_customers AS (
    SELECT 
        name,
        email,
        city
    FROM temp_customer_import
    WHERE email NOT IN (SELECT email FROM customers)
)
INSERT INTO customers (name, email, city)
SELECT name, email, city
FROM new_customers;
```

---

### שימוש ב-WITH עם UPDATE

```sql
WITH customer_totals AS (
    SELECT 
        customer_id,
        SUM(total_amount) as lifetime_value
    FROM orders
    GROUP BY customer_id
)
UPDATE customers c
JOIN customer_totals ct ON c.customer_id = ct.customer_id
SET c.vip_status = CASE 
    WHEN ct.lifetime_value > 50000 THEN 'Diamond'
    WHEN ct.lifetime_value > 20000 THEN 'Gold'
    WHEN ct.lifetime_value > 10000 THEN 'Silver'
    ELSE 'Regular'
END;
```

---

### שימוש ב-WITH עם DELETE

```sql
WITH inactive_customers AS (
    SELECT customer_id
    FROM customers
    WHERE customer_id NOT IN (
        SELECT DISTINCT customer_id 
        FROM orders 
        WHERE order_date > DATE_SUB(NOW(), INTERVAL 2 YEAR)
    )
)
DELETE FROM customers
WHERE customer_id IN (SELECT customer_id FROM inactive_customers);
```

---

### RETURNING Clause (PostgreSQL)

```sql
WITH deleted_orders AS (
    DELETE FROM orders
    WHERE order_date < '2020-01-01'
    RETURNING order_id, customer_id, total_amount
)
SELECT 
    customer_id,
    COUNT(*) as deleted_count,
    SUM(total_amount) as deleted_value
FROM deleted_orders
GROUP BY customer_id;
```

---

## 🔥 דוגמאות מעשיות מציאות

### תרחיש 1: דוח מכירות יומי מפורט

```sql
WITH 
    -- מכירות יומיות
    daily_sales AS (
        SELECT 
            DATE(order_date) as sale_date,
            SUM(total_amount) as daily_total,
            COUNT(*) as order_count,
            COUNT(DISTINCT customer_id) as unique_customers
        FROM orders
        WHERE order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
        GROUP BY DATE(order_date)
    ),
    -- ממוצעים
    averages AS (
        SELECT 
            AVG(daily_total) as avg_daily_sales,
            AVG(order_count) as avg_daily_orders
        FROM daily_sales
    ),
    -- ימים חריגים
    exceptional_days AS (
        SELECT 
            ds.*,
            CASE 
                WHEN ds.daily_total > av.avg_daily_sales * 1.5 THEN 'יום מצוין'
                WHEN ds.daily_total < av.avg_daily_sales * 0.5 THEN 'יום חלש'
                ELSE 'יום רגיל'
            END as day_type
        FROM daily_sales ds, averages av
    )
SELECT 
    sale_date AS 'תאריך',
    daily_total AS 'מכירות',
    order_count AS 'הזמנות',
    unique_customers AS 'לקוחות',
    day_type AS 'סוג יום',
    (SELECT avg_daily_sales FROM averages) AS 'ממוצע'
FROM exceptional_days
ORDER BY sale_date DESC;
```

---

### תרחיש 2: ניתוח לקוחות - RFM Analysis

```sql
WITH 
    -- Recency: מתי קנה לאחרונה
    recency AS (
        SELECT 
            customer_id,
            DATEDIFF(CURRENT_DATE, MAX(order_date)) as days_since_last_order
        FROM orders
        GROUP BY customer_id
    ),
    -- Frequency: כמה פעמים קנה
    frequency AS (
        SELECT 
            customer_id,
            COUNT(*) as order_count
        FROM orders
        GROUP BY customer_id
    ),
    -- Monetary: כמה הוציא
    monetary AS (
        SELECT 
            customer_id,
            SUM(total_amount) as total_spent
        FROM orders
        GROUP BY customer_id
    ),
    -- שילוב RFM
    rfm_combined AS (
        SELECT 
            c.customer_id,
            c.name,
            r.days_since_last_order,
            f.order_count,
            m.total_spent,
            CASE 
                WHEN r.days_since_last_order <= 30 THEN 5
                WHEN r.days_since_last_order <= 90 THEN 4
                WHEN r.days_since_last_order <= 180 THEN 3
                WHEN r.days_since_last_order <= 365 THEN 2
                ELSE 1
            END as r_score,
            CASE 
                WHEN f.order_count >= 20 THEN 5
                WHEN f.order_count >= 10 THEN 4
                WHEN f.order_count >= 5 THEN 3
                WHEN f.order_count >= 2 THEN 2
                ELSE 1
            END as f_score,
            CASE 
                WHEN m.total_spent >= 50000 THEN 5
                WHEN m.total_spent >= 20000 THEN 4
                WHEN m.total_spent >= 10000 THEN 3
                WHEN m.total_spent >= 5000 THEN 2
                ELSE 1
            END as m_score
        FROM customers c
        LEFT JOIN recency r ON c.customer_id = r.customer_id
        LEFT JOIN frequency f ON c.customer_id = f.customer_id
        LEFT JOIN monetary m ON c.customer_id = m.customer_id
    )
SELECT 
    customer_id,
    name AS 'שם לקוח',
    days_since_last_order AS 'ימים מרכישה אחרונה',
    order_count AS 'מספר הזמנות',
    total_spent AS 'סה"כ הוצאה',
    CONCAT(r_score, f_score, m_score) AS 'RFM Score',
    CASE 
        WHEN (r_score + f_score + m_score) >= 13 THEN 'Champion'
        WHEN (r_score + f_score + m_score) >= 10 THEN 'Loyal'
        WHEN (r_score + f_score + m_score) >= 7 THEN 'Potential'
        ELSE 'At Risk'
    END as segment
FROM rfm_combined
ORDER BY (r_score + f_score + m_score) DESC;
```

---

### תרחיש 3: ניתוח מלאי - מוצרים שנגמרים

```sql
WITH 
    -- מהירות מכירה (ממוצע פריטים ליום)
    sales_velocity AS (
        SELECT 
            p.product_id,
            p.product_name,
            p.stock,
            COUNT(*) / 30.0 as items_per_day  -- 30 ימים אחרונים
        FROM products p
        JOIN order_items oi ON p.product_id = oi.product_id
        JOIN orders o ON oi.order_id = o.order_id
        WHERE o.order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
        GROUP BY p.product_id, p.product_name, p.stock
    ),
    -- ימים עד גמר המלאי
    stock_forecast AS (
        SELECT 
            product_id,
            product_name,
            stock,
            items_per_day,
            CASE 
                WHEN items_per_day > 0 THEN stock / items_per_day
                ELSE 999
            END as days_until_out
        FROM sales_velocity
    ),
    -- דחיפות הזמנה
    reorder_priority AS (
        SELECT 
            *,
            CASE 
                WHEN days_until_out <= 7 THEN 'דחוף מאוד'
                WHEN days_until_out <= 14 THEN 'דחוף'
                WHEN days_until_out <= 30 THEN 'להזמין בקרוב'
                ELSE 'תקין'
            END as priority
        FROM stock_forecast
    )
SELECT 
    product_name AS 'מוצר',
    stock AS 'במלאי',
    ROUND(items_per_day, 2) AS 'נמכר ליום',
    ROUND(days_until_out, 0) AS 'ימים עד גמר',
    priority AS 'דחיפות'
FROM reorder_priority
WHERE days_until_out <= 30
ORDER BY days_until_out;
```

---

### תרחיש 4: השוואת ביצועים - שנה עם שנה

```sql
WITH 
    -- מכירות שנה נוכחית
    current_year AS (
        SELECT 
            DATE_FORMAT(order_date, '%Y-%m') as month,
            SUM(total_amount) as sales
        FROM orders
        WHERE YEAR(order_date) = YEAR(CURRENT_DATE)
        GROUP BY DATE_FORMAT(order_date, '%Y-%m')
    ),
    -- מכירות שנה קודמת
    previous_year AS (
        SELECT 
            DATE_FORMAT(order_date, '%Y-%m') as month,
            SUM(total_amount) as sales
        FROM orders
        WHERE YEAR(order_date) = YEAR(CURRENT_DATE) - 1
        GROUP BY DATE_FORMAT(order_date, '%Y-%m')
    ),
    -- שילוב עם חישוב צמיחה
    comparison AS (
        SELECT 
            cy.month,
            cy.sales as current_sales,
            py.sales as previous_sales,
            cy.sales - py.sales as absolute_change,
            ROUND(((cy.sales - py.sales) / py.sales) * 100, 2) as percent_change
        FROM current_year cy
        LEFT JOIN previous_year py ON 
            MONTH(cy.month) = MONTH(py.month)
    )
SELECT 
    month AS 'חודש',
    current_sales AS 'מכירות השנה',
    previous_sales AS 'מכירות אשתקד',
    absolute_change AS 'הפרש',
    CONCAT(percent_change, '%') AS 'שינוי באחוזים',
    CASE 
        WHEN percent_change > 20 THEN '🚀 צמיחה מעולה'
        WHEN percent_change > 0 THEN '✅ צמיחה'
        WHEN percent_change > -10 THEN '⚠️ ירידה קלה'
        ELSE '❌ ירידה חדה'
    END as status
FROM comparison
ORDER BY month;
```

---

### תרחיש 5: זיהוי דפוסי הונאה

```sql
WITH 
    -- פעילות חריגה לפי לקוח
    suspicious_activity AS (
        SELECT 
            customer_id,
            COUNT(*) as order_count_today,
            SUM(total_amount) as total_spent_today,
            MAX(total_amount) as largest_order
        FROM orders
        WHERE DATE(order_date) = CURRENT_DATE
        GROUP BY customer_id
    ),
    -- ממוצע היסטורי
    customer_average AS (
        SELECT 
            customer_id,
            AVG(order_count) as avg_daily_orders,
            AVG(total_spent) as avg_daily_spent
        FROM (
            SELECT 
                customer_id,
                DATE(order_date) as order_day,
                COUNT(*) as order_count,
                SUM(total_amount) as total_spent
            FROM orders
            WHERE order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 90 DAY)
              AND DATE(order_date) < CURRENT_DATE
            GROUP BY customer_id, DATE(order_date)
        ) daily_stats
        GROUP BY customer_id
    ),
    -- זיהוי חריגות
    fraud_detection AS (
        SELECT 
            c.customer_id,
            c.name,
            c.email,
            sa.order_count_today,
            sa.total_spent_today,
            ca.avg_daily_orders,
            ca.avg_daily_spent,
            CASE 
                WHEN sa.order_count_today > ca.avg_daily_orders * 5 THEN 'הזמנות חריגות'
                WHEN sa.total_spent_today > ca.avg_daily_spent * 5 THEN 'סכום חריג'
                WHEN sa.largest_order > ca.avg_daily_spent * 10 THEN 'הזמנה ענקית'
                ELSE 'תקין'
            END as alert_type
        FROM customers c
        JOIN suspicious_activity sa ON c.customer_id = sa.customer_id
        LEFT JOIN customer_average ca ON c.customer_id = ca.customer_id
        WHERE sa.order_count_today > COALESCE(ca.avg_daily_orders * 3, 5)
           OR sa.total_spent_today > COALESCE(ca.avg_daily_spent * 3, 10000)
    )
SELECT 
    customer_id,
    name AS 'לקוח',
    email AS 'אימייל',
    order_count_today AS 'הזמנות היום',
    total_spent_today AS 'הוצאה היום',
    ROUND(avg_daily_orders, 1) AS 'ממוצע הזמנות',
    ROUND(avg_daily_spent, 2) AS 'ממוצע הוצאה',
    alert_type AS '⚠️ התראה'
FROM fraud_detection
ORDER BY total_spent_today DESC;
```

---

## ⚡ ביצועים ואופטימיזציה

### ✅ יתרונות ביצועים

```sql
-- ✅ CTE מחושב פעם אחת
WITH expensive_calc AS (
    SELECT 
        customer_id,
        SUM(complex_calculation()) as result
    FROM huge_table
    GROUP BY customer_id
)
SELECT * FROM expensive_calc WHERE result > 100
UNION ALL
SELECT * FROM expensive_calc WHERE result <= 100;
-- expensive_calc מחושב רק פעם אחת!
```

---

### ❌ מלכודות ביצועים

```sql
-- ❌ CTE מחושב בכל פעם (לפעמים)
WITH all_orders AS (
    SELECT * FROM orders  -- 10 מליון שורות!
)
SELECT * FROM all_orders WHERE customer_id = 123;
-- עדיף: SELECT * FROM orders WHERE customer_id = 123;
```

---

### 💡 טיפים לביצועים

#### 1. סנן מוקדם

```sql
-- ❌ לא טוב
WITH all_data AS (
    SELECT * FROM huge_table  -- הכל!
)
SELECT * FROM all_data WHERE date > '2025-01-01';

-- ✅ טוב
WITH filtered_data AS (
    SELECT * FROM huge_table WHERE date > '2025-01-01'  -- סינון מוקדם!
)
SELECT * FROM filtered_data;
```

---

#### 2. השתמש באינדקסים

```sql
-- ודא שיש אינדקסים על שדות ב-WHERE/JOIN של ה-CTE
CREATE INDEX idx_order_date ON orders(order_date);
CREATE INDEX idx_customer_id ON orders(customer_id);

WITH recent_orders AS (
    SELECT * FROM orders 
    WHERE order_date > '2025-01-01'  -- משתמש באינדקס!
)
SELECT * FROM recent_orders;
```

---

#### 3. הימנע מ-CTEs מיותרים

```sql
-- ❌ מיותר
WITH simple_filter AS (
    SELECT * FROM customers WHERE city = 'תל אביב'
)
SELECT * FROM simple_filter;

-- ✅ פשוט יותר
SELECT * FROM customers WHERE city = 'תל אביב';
```

---

#### 4. Materialized CTEs (Oracle, PostgreSQL)

```sql
-- PostgreSQL: כפה materialize
WITH customer_stats AS MATERIALIZED (
    SELECT customer_id, COUNT(*) as order_count
    FROM orders
    GROUP BY customer_id
)
SELECT * FROM customer_stats WHERE order_count > 10;
```

---

## 🎓 תרגילים

### תרגיל 1: חישוב Top 5 לקוחות
צרו CTE שמחשב את 5 הלקוחות עם ההוצאה הגבוהה ביותר, ואז הציגו את כל ההזמנות שלהם.

<details>
<summary>💡 פתרון</summary>

```sql
WITH top_customers AS (
    SELECT 
        customer_id,
        SUM(total_amount) as total_spent
    FROM orders
    GROUP BY customer_id
    ORDER BY SUM(total_amount) DESC
    LIMIT 5
)
SELECT 
    c.name AS 'שם לקוח',
    o.order_id AS 'מספר הזמנה',
    o.order_date AS 'תאריך',
    o.total_amount AS 'סכום',
    tc.total_spent AS 'סה"כ כל ההזמנות'
FROM top_customers tc
JOIN customers c ON tc.customer_id = c.customer_id
JOIN orders o ON tc.customer_id = o.customer_id
ORDER BY tc.total_spent DESC, o.order_date DESC;
```
</details>

---

### תרגיל 2: מספרים זוגיים בין 1 ל-20
השתמשו ב-CTE רקורסיבי ליצירת רשימה של כל המספרים הזוגיים בין 1 ל-20.

<details>
<summary>💡 פתרון</summary>

```sql
WITH RECURSIVE even_numbers AS (
    -- בסיס: מתחיל מ-2
    SELECT 2 AS num
    
    UNION ALL
    
    -- רקורסיה: הוסף 2 בכל פעם
    SELECT num + 2
    FROM even_numbers
    WHERE num < 20
)
SELECT num AS 'מספר זוגי'
FROM even_numbers;
```
</details>

---

### תרגיל 3: מוצרים שלא נמכרו בחודש האחרון
מצאו מוצרים שלא הופיעו באף הזמנה ב-30 הימים האחרונים.

<details>
<summary>💡 פתרון</summary>

```sql
WITH 
    -- מוצרים שנמכרו בחודש האחרון
    sold_products AS (
        SELECT DISTINCT oi.product_id
        FROM order_items oi
        JOIN orders o ON oi.order_id = o.order_id
        WHERE o.order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
    ),
    -- מוצרים שלא נמכרו
    unsold_products AS (
        SELECT p.product_id, p.product_name, p.stock, p.price
        FROM products p
        WHERE p.product_id NOT IN (SELECT product_id FROM sold_products)
    )
SELECT 
    product_name AS 'מוצר',
    stock AS 'במלאי',
    price AS 'מחיר',
    stock * price AS 'ערך מלאי'
FROM unsold_products
ORDER BY stock * price DESC;
```
</details>

---

### תרגיל 4: היררכיית תגובות בפורום
יש טבלת `comments` עם `comment_id`, `parent_comment_id`, `content`.
הציגו את כל ההיררכיה של תגובה מסוימת.

<details>
<summary>💡 פתרון</summary>

```sql
WITH RECURSIVE comment_thread AS (
    -- בסיס: התגובה הראשית
    SELECT 
        comment_id,
        parent_comment_id,
        content,
        1 AS level,
        CAST(comment_id AS CHAR(200)) AS path
    FROM comments
    WHERE comment_id = 1  -- התגובה שרוצים לראות
    
    UNION ALL
    
    -- רקורסיה: כל התגובות מתחת
    SELECT 
        c.comment_id,
        c.parent_comment_id,
        c.content,
        ct.level + 1,
        CONCAT(ct.path, ' -> ', c.comment_id)
    FROM comments c
    JOIN comment_thread ct ON c.parent_comment_id = ct.comment_id
)
SELECT 
    REPEAT('  ', level - 1) AS indent,
    comment_id,
    content AS 'תוכן',
    level AS 'רמה',
    path AS 'נתיב'
FROM comment_thread
ORDER BY path;
```
</details>

---

## ❌ טעויות נפוצות

### 1️⃣ CTE לא מוגדר לפני שימוש

```sql
-- ❌ שגיאה
WITH second AS (
    SELECT * FROM first  -- first עוד לא הוגדר!
),
first AS (
    SELECT * FROM customers
)
SELECT * FROM second;

-- ✅ נכון - סדר חשוב!
WITH first AS (
    SELECT * FROM customers
),
second AS (
    SELECT * FROM first
)
SELECT * FROM second;
```

---

### 2️⃣ שכחת RECURSIVE

```sql
-- ❌ שגיאה
WITH numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 10
)
SELECT * FROM numbers;
-- Error: recursive query without RECURSIVE

-- ✅ נכון
WITH RECURSIVE numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 10
)
SELECT * FROM numbers;
```

---

### 3️⃣ CTE ללא שאילתה אחריו

```sql
-- ❌ שגיאה - CTE חייב להיות בשימוש
WITH customer_stats AS (
    SELECT customer_id, COUNT(*) as total
    FROM orders
    GROUP BY customer_id
);
-- Error: WITH clause must be followed by SELECT

-- ✅ נכון
WITH customer_stats AS (
    SELECT customer_id, COUNT(*) as total
    FROM orders
    GROUP BY customer_id
)
SELECT * FROM customer_stats;  -- שאילתה שמשתמשת ב-CTE
```

---

### 4️⃣ רקורסיה אינסופית

```sql
-- ❌ סכנה! לא יעצור!
WITH RECURSIVE infinite AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM infinite  -- אין תנאי עצירה!
)
SELECT * FROM infinite;

-- ✅ נכון - יש תנאי עצירה
WITH RECURSIVE safe AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM safe WHERE n < 100  -- תנאי עצירה!
)
SELECT * FROM safe;
```

---

### 5️⃣ שימוש חוזר ב-CTE (לא אפשרי)

```sql
-- ❌ לא אפשרי - CTE חד פעמי
WITH temp AS (SELECT * FROM customers)
SELECT * FROM temp;

SELECT * FROM temp;  -- Error: temp לא קיים יותר!

-- ✅ פתרון - הגדר מחדש
WITH temp AS (SELECT * FROM customers)
SELECT * FROM temp;

WITH temp AS (SELECT * FROM customers)  -- הגדרה מחדש
SELECT * FROM temp;
```

---

## 💡 טיפים מקצועיים

### ✅ טיפ 1: שמות מובנים ל-CTEs

```sql
-- ❌ לא ברור
WITH t1 AS (...), t2 AS (...), t3 AS (...)
SELECT * FROM t3;

-- ✅ ברור
WITH 
    customer_orders AS (...),
    customer_payments AS (...),
    customer_summary AS (...)
SELECT * FROM customer_summary;
```

---

### ✅ טיפ 2: הערות לכל CTE

```sql
WITH 
    -- מחשב את סה"כ ההזמנות לכל לקוח בשנה האחרונה
    yearly_orders AS (
        SELECT customer_id, COUNT(*) as order_count
        FROM orders
        WHERE order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 1 YEAR)
        GROUP BY customer_id
    ),
    -- מוצא לקוחות VIP (מעל 10 הזמנות)
    vip_customers AS (
        SELECT customer_id
        FROM yearly_orders
        WHERE order_count > 10
    )
SELECT * FROM vip_customers;
```

---

### ✅ טיפ 3: פירוק שאילתות מורכבות

```sql
-- במקום שאילתה ענקית אחת, פרקו לשלבים:
WITH 
    step1_raw_data AS (...),
    step2_filtered AS (...),
    step3_calculated AS (...),
    step4_aggregated AS (...),
    step5_final AS (...)
SELECT * FROM step5_final;
```

---

### ✅ טיפ 4: שימוש ב-CTEs לבדיקות

```sql
WITH 
    -- נתוני בדיקה
    test_data AS (
        SELECT 1 as id, 'Test 1' as name, 100 as value
        UNION ALL
        SELECT 2, 'Test 2', 200
        UNION ALL
        SELECT 3, 'Test 3', 300
    )
SELECT * FROM test_data WHERE value > 150;
-- שימושי לבדיקות מהירות!
```

---

### ✅ טיפ 5: EXPLAIN לאופטימיזציה

```sql
-- בדקו את ביצועי ה-CTE
EXPLAIN 
WITH customer_stats AS (
    SELECT customer_id, COUNT(*) as total
    FROM orders
    GROUP BY customer_id
)
SELECT * FROM customer_stats WHERE total > 10;
```

---

## 📋 טבלת סיכום

| סוג CTE | שימוש | דוגמה |
|---------|-------|--------|
| **Simple CTE** | שאילתות בודדות ופשוטות | `WITH x AS (SELECT...) SELECT * FROM x` |
| **Multiple CTEs** | שברו שאילתות מורכבות לחלקים | `WITH a AS (...), b AS (...) SELECT...` |
| **Recursive CTE** | היררכיות, עצים, גרפים | `WITH RECURSIVE x AS (... UNION ALL ...) SELECT...` |
| **CTE with DML** | INSERT/UPDATE/DELETE מתקדם | `WITH x AS (...) INSERT INTO ... SELECT...` |

---

## 🎯 מתי להשתמש במה?

| צורך | פתרון |
|------|--------|
| שאילתה פשוטה | ⚡ אל תשתמש ב-CTE |
| Subquery חוזר | ✅ CTE פשוט |
| שאילתה מורכבת | ✅ Multiple CTEs |
| מבנה עץ/היררכיה | ✅ Recursive CTE |
| חישובים מורכבים | ✅ Multiple CTEs |
| קריאות קוד | ✅ CTE (תמיד!) |

---

## ❓ שאלות נפוצות

**Q: מה ההבדל בין CTE ל-Subquery?**
A: CTE קריא יותר, אפשר לעשות recursive, ואפשר להגדיר פעם אחת ולהשתמש כמה פעמים.

**Q: מה ההבדל בין CTE ל-Temp Table?**
A: CTE קיים רק במשך השאילתה. Temp Table נשאר עד סיום ה-session.

**Q: האם CTE מהיר יותר מ-Subquery?**
A: לא בהכרח. זה תלוי במקרה. לפעמים זהה, לפעמים CTE מהיר יותר.

**Q: האם אפשר CTE בתוך CTE?**
A: לא ישירות, אבל אפשר להגדיר CTE שמשתמש ב-CTE אחר.

**Q: מה זה MATERIALIZED CTE?**
A: CTE שמחושב פעם אחת ונשמר בזיכרון (PostgreSQL, Oracle).

---

## 🎉 סיכום

עכשיו אתם יודעים:
✅ מה זה WITH (CTE) ולמה זה מדהים  
✅ 4 סוגי CTEs: Simple, Multiple, Recursive, DML  
✅ איך לשבור שאילתות מורכבות לחלקים קטנים  
✅ Recursive CTE להיררכיות ועצים  
✅ דוגמאות מעשיות מציאות (RFM, מלאי, הונאה)  
✅ אופטימיזציה וביצועים  
✅ טעויות נפוצות ופתרונות  

**WITH הוא אחד הכלים החזקים ב-SQL - תשתמשו בו בחוכמה!** 📦✨

---

**חזרה למדריך הקודם:** [מדריך LIKE](17_LIKE_Patterns_Guide.md)
