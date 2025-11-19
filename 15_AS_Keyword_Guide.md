# 🏷️ מדריך AS ב-SQL - כל השימושים

## 📌 מה זה AS?

**AS** היא מילת מפתח ב-SQL שמשמשת **לכינויים (Aliases)** - לתת שם חלופי לעמודות, טבלאות, או תוצאות.

**למה צריך את זה?**
- 📝 להפוך שמות לקריאים יותר
- 🎯 לפשט שאילתות מורכבות
- ✨ ליצור עמודות מחושבות עם שמות ברורים
- 🔗 לקצר שמות טבלאות ב-JOINs

---

## 🎯 סוגי השימושים ב-AS

### 1️⃣ AS לעמודות (Column Alias)
### 2️⃣ AS לטבלאות (Table Alias)
### 3️⃣ AS לתוצאות מחושבות
### 4️⃣ AS לתת-שאילתות (Subqueries)
### 5️⃣ AS ל-CTEs (Common Table Expressions)

בואו נעבור על כל אחד! 🚀

---

## 1️⃣ AS לעמודות - Column Alias

### למה משתמשים?

לתת לעמודה **שם יותר קריא** או **תיאורי** בתוצאות.

### תחביר

```sql
SELECT column_name AS alias_name
FROM table_name;
```

---

### דוגמה בסיסית

```sql
SELECT 
    first_name AS שם_פרטי,
    last_name AS שם_משפחה,
    email AS דואר_אלקטרוני
FROM customers;
```

**תוצאה:**
```
שם_פרטי | שם_משפחה | דואר_אלקטרוני
---------|-----------|------------------
אבי      | כהן      | avi@example.com
בני      | לוי      | benny@example.com
```

**במקום:** `first_name`, `last_name`, `email`  
**קיבלנו:** שמות בעברית וברורים! ✅

---

### AS אופציונלי!

```sql
-- עם AS (מומלץ) ✅
SELECT name AS customer_name FROM customers;

-- בלי AS (גם עובד) ✅
SELECT name customer_name FROM customers;
```

**שניהם עובדים, אבל עם AS יותר ברור!** 📝

---

### דוגמאות נוספות

```sql
-- שמות תיאוריים
SELECT 
    product_id AS מספר_מוצר,
    product_name AS שם_המוצר,
    price AS מחיר_ליחידה,
    stock_quantity AS כמות_במלאי
FROM products;

-- באנגלית
SELECT 
    order_id AS order_number,
    order_date AS purchase_date,
    customer_id AS buyer_id
FROM orders;

-- קיצורים
SELECT 
    customer_name AS cust_name,
    total_purchases AS total_purch,
    registration_date AS reg_date
FROM customers;
```

---

### AS עם רווחים - צריך מרכאות!

```sql
-- עם רווח - צריך מרכאות! ✅
SELECT 
    name AS "שם הלקוח",
    city AS "עיר מגורים",
    age AS "גיל"
FROM customers;

-- בלי מרכאות - לא יעבוד! ❌
SELECT name AS שם הלקוח FROM customers;  -- שגיאה!
```

**כלל:** אם יש רווח או תווים מיוחדים - השתמשו במרכאות! 📌

---

## 2️⃣ AS לטבלאות - Table Alias

### למה משתמשים?

- ✂️ **לקצר שמות ארוכים** של טבלאות
- 🔗 **ל-JOINs** - חובה כמעט!
- 📖 **לקריאות** של שאילתות מורכבות

### תחביר

```sql
SELECT column_name
FROM table_name AS alias;
```

---

### דוגמה בסיסית

```sql
-- בלי AS - ארוך ומסורבל ❌
SELECT 
    customer_information.customer_name,
    customer_information.email,
    customer_information.phone
FROM customer_information
WHERE customer_information.status = 'active';

-- עם AS - קצר ונקי! ✅
SELECT 
    c.customer_name,
    c.email,
    c.phone
FROM customer_information AS c
WHERE c.status = 'active';
```

**הרבה יותר קל לקרוא!** 😊

---

### AS חובה ב-JOINs!

```sql
SELECT 
    c.name AS customer_name,
    o.order_id,
    o.amount,
    p.product_name
FROM customers AS c
INNER JOIN orders AS o ON c.customer_id = o.customer_id
INNER JOIN products AS p ON o.product_id = p.product_id
WHERE c.city = 'תל אביב';
```

**ללא AS:**
- לא נדע מאיזו טבלה כל עמודה 🤔
- שאילתה ארוכה ומסורבלת 😵

**עם AS:**
- `c` = customers
- `o` = orders
- `p` = products
- ברור ונקי! ✨

---

### AS לטבלה - גם אופציונלי

```sql
-- עם AS (מומלץ) ✅
FROM customers AS c

-- בלי AS (גם עובד) ✅
FROM customers c
```

---

### כינויים מקובלים לטבלאות

```sql
-- כינויים נפוצים
customers → c או cust
orders → o או ord
products → p או prod
users → u
employees → e או emp
departments → d או dept
sales → s
items → i
categories → cat

-- דוגמה:
SELECT 
    c.name,
    o.order_id,
    p.product_name,
    cat.category_name
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN products p ON o.product_id = p.product_id
JOIN categories cat ON p.category_id = cat.category_id;
```

---

## 3️⃣ AS לתוצאות מחושבות

### למה משתמשים?

לתת **שם ברור** לחישובים, פונקציות, וביטויים.

---

### חישובים מתמטיים

```sql
SELECT 
    product_name,
    price,
    quantity,
    price * quantity AS total_price,
    price * quantity * 0.17 AS vat_amount,
    price * quantity * 1.17 AS price_with_vat
FROM order_items;
```

**תוצאה:**
```
product_name | price | quantity | total_price | vat_amount | price_with_vat
-------------|-------|----------|-------------|------------|----------------
מחשב נייד    | 3000  | 2        | 6000        | 1020       | 7020
עכבר         | 50    | 5        | 250         | 42.5       | 292.5
```

**ברור מאוד מה כל עמודה מחושבת!** 📊

---

### פונקציות צבירה

```sql
SELECT 
    customer_id,
    COUNT(*) AS total_orders,
    SUM(amount) AS total_spent,
    AVG(amount) AS average_order,
    MIN(amount) AS smallest_order,
    MAX(amount) AS largest_order
FROM orders
GROUP BY customer_id;
```

**תוצאה:**
```
customer_id | total_orders | total_spent | average_order | smallest_order | largest_order
------------|--------------|-------------|---------------|----------------|---------------
1           | 15           | 4500        | 300           | 50             | 800
2           | 8            | 2400        | 300           | 100            | 600
```

**שמות תיאוריים במקום COUNT(*), SUM(amount)!** 🎯

---

### פונקציות מחרוזות

```sql
SELECT 
    first_name,
    last_name,
    CONCAT(first_name, ' ', last_name) AS full_name,
    UPPER(email) AS email_uppercase,
    LOWER(city) AS city_lowercase,
    LENGTH(phone) AS phone_length
FROM customers;
```

**תוצאה:**
```
first_name | last_name | full_name  | email_uppercase    | city_lowercase | phone_length
-----------|-----------|------------|--------------------|--------------  |-------------
אבי        | כהן      | אבי כהן    | AVI@EXAMPLE.COM    | tel aviv       | 10
```

---

### פונקציות תאריך

```sql
SELECT 
    order_id,
    order_date,
    YEAR(order_date) AS order_year,
    MONTH(order_date) AS order_month,
    DAY(order_date) AS order_day,
    DAYNAME(order_date) AS day_of_week,
    DATEDIFF(CURRENT_DATE, order_date) AS days_since_order
FROM orders;
```

**תוצאה:**
```
order_id | order_date | order_year | order_month | order_day | day_of_week | days_since_order
---------|------------|------------|-------------|-----------|-------------|------------------
101      | 2025-11-15 | 2025       | 11          | 15        | Friday      | 4
```

---

### CASE עם AS

```sql
SELECT 
    product_name,
    price,
    stock_quantity,
    CASE 
        WHEN stock_quantity = 0 THEN 'אזל מהמלאי'
        WHEN stock_quantity < 10 THEN 'מלאי נמוך'
        WHEN stock_quantity < 50 THEN 'מלאי בינוני'
        ELSE 'במלאי'
    END AS stock_status,
    CASE 
        WHEN price < 100 THEN 'זול'
        WHEN price < 500 THEN 'בינוני'
        ELSE 'יקר'
    END AS price_category
FROM products;
```

**תוצאה:**
```
product_name | price | stock_quantity | stock_status   | price_category
-------------|-------|----------------|----------------|----------------
מקלדת        | 150   | 5              | מלאי נמוך      | בינוני
מחשב נייד    | 3500  | 25             | מלאי בינוני    | יקר
עט           | 5     | 0              | אזל מהמלאי     | זול
```

**CASE ארוך? תנו לו שם קצר וברור!** ✨

---

## 4️⃣ AS לתת-שאילתות (Subqueries)

### למה משתמשים?

תת-שאילתה ב-FROM **חייבת** לקבל שם (Alias)!

---

### דוגמה בסיסית

```sql
SELECT 
    customer_totals.customer_name,
    customer_totals.total_spent
FROM (
    SELECT 
        c.name AS customer_name,
        SUM(o.amount) AS total_spent
    FROM customers c
    JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.name
) AS customer_totals
WHERE customer_totals.total_spent > 1000;
```

**ללא AS customer_totals - שגיאה!** ❌

---

### תת-שאילתה מורכבת

```sql
SELECT 
    monthly_sales.year,
    monthly_sales.month,
    monthly_sales.total,
    monthly_sales.total - monthly_sales.prev_month_total AS growth
FROM (
    SELECT 
        YEAR(order_date) AS year,
        MONTH(order_date) AS month,
        SUM(amount) AS total,
        LAG(SUM(amount)) OVER (ORDER BY YEAR(order_date), MONTH(order_date)) AS prev_month_total
    FROM orders
    GROUP BY YEAR(order_date), MONTH(order_date)
) AS monthly_sales
WHERE monthly_sales.total > 10000;
```

**AS monthly_sales הופך את התת-שאילתה לטבלה שאפשר לעבוד איתה!** 🎯

---

### JOIN עם תת-שאילתה

```sql
SELECT 
    c.name,
    c.email,
    top_customers.order_count,
    top_customers.total_spent
FROM customers c
INNER JOIN (
    SELECT 
        customer_id,
        COUNT(*) AS order_count,
        SUM(amount) AS total_spent
    FROM orders
    GROUP BY customer_id
    HAVING SUM(amount) > 5000
) AS top_customers ON c.customer_id = top_customers.customer_id
ORDER BY top_customers.total_spent DESC;
```

**AS top_customers חובה כאן!** 📌

---

## 5️⃣ AS ל-CTEs (Common Table Expressions)

### מה זה CTE?

**CTE** = תת-שאילתה עם שם שמוגדרת בתחילת השאילתה עם `WITH`.

### תחביר

```sql
WITH alias_name AS (
    SELECT ...
)
SELECT ... FROM alias_name;
```

---

### דוגמה בסיסית

```sql
WITH customer_totals AS (
    SELECT 
        customer_id,
        SUM(amount) AS total_spent,
        COUNT(*) AS order_count
    FROM orders
    GROUP BY customer_id
)
SELECT 
    c.name,
    ct.total_spent,
    ct.order_count
FROM customers c
JOIN customer_totals ct ON c.customer_id = ct.customer_id
WHERE ct.total_spent > 1000;
```

**AS customer_totals נותן שם ל-CTE!** 🏷️

---

### מספר CTEs

```sql
WITH 
    customer_totals AS (
        SELECT 
            customer_id,
            SUM(amount) AS total_spent
        FROM orders
        GROUP BY customer_id
    ),
    top_products AS (
        SELECT 
            product_id,
            COUNT(*) AS times_ordered
        FROM order_items
        GROUP BY product_id
    ),
    monthly_revenue AS (
        SELECT 
            YEAR(order_date) AS year,
            MONTH(order_date) AS month,
            SUM(amount) AS revenue
        FROM orders
        GROUP BY YEAR(order_date), MONTH(order_date)
    )
SELECT 
    ct.customer_id,
    ct.total_spent,
    tp.times_ordered,
    mr.revenue
FROM customer_totals ct
CROSS JOIN top_products tp
CROSS JOIN monthly_revenue mr;
```

**כל CTE מקבל AS עם השם שלו!** ✨

---

## 🎨 דוגמאות מעשיות מלאות

### דוגמה 1: דוח מכירות מפורט

```sql
SELECT 
    c.name AS customer_name,
    c.city AS customer_city,
    o.order_id AS order_number,
    o.order_date AS purchase_date,
    p.product_name AS item_name,
    oi.quantity AS qty,
    oi.price AS unit_price,
    oi.quantity * oi.price AS subtotal,
    (oi.quantity * oi.price) * 0.17 AS vat,
    (oi.quantity * oi.price) * 1.17 AS total_with_vat,
    CASE 
        WHEN o.status = 'delivered' THEN 'נמסר ✅'
        WHEN o.status = 'shipped' THEN 'בדרך 🚚'
        WHEN o.status = 'processing' THEN 'בטיפול ⏳'
        ELSE 'ממתין 📋'
    END AS order_status_hebrew
FROM customers AS c
INNER JOIN orders AS o ON c.customer_id = o.customer_id
INNER JOIN order_items AS oi ON o.order_id = oi.order_id
INNER JOIN products AS p ON oi.product_id = p.product_id
WHERE o.order_date >= '2025-01-01'
ORDER BY o.order_date DESC;
```

**כל עמודה עם שם ברור ותיאורי!** 📊

---

### דוגמה 2: ניתוח ביצועים

```sql
WITH 
    sales_by_rep AS (
        SELECT 
            sr.rep_id,
            sr.rep_name,
            COUNT(DISTINCT s.sale_id) AS total_sales,
            SUM(s.amount) AS revenue,
            AVG(s.amount) AS avg_sale_value
        FROM sales_reps AS sr
        LEFT JOIN sales AS s ON sr.rep_id = s.rep_id
        WHERE s.sale_date >= '2025-01-01'
        GROUP BY sr.rep_id, sr.rep_name
    ),
    rep_ranking AS (
        SELECT 
            *,
            RANK() OVER (ORDER BY revenue DESC) AS revenue_rank,
            RANK() OVER (ORDER BY total_sales DESC) AS sales_rank
        FROM sales_by_rep
    )
SELECT 
    rr.rep_name AS sales_rep,
    rr.total_sales AS deals_closed,
    rr.revenue AS total_revenue,
    rr.avg_sale_value AS avg_deal_size,
    rr.revenue_rank AS rank_by_revenue,
    rr.sales_rank AS rank_by_volume,
    CASE 
        WHEN rr.revenue_rank <= 3 THEN '🥇 Top Performer'
        WHEN rr.revenue_rank <= 10 THEN '⭐ Above Average'
        ELSE '📊 Meeting Expectations'
    END AS performance_tier
FROM rep_ranking AS rr
ORDER BY rr.revenue DESC;
```

**CTE + Column Alias + CASE = דוח מושלם!** 🏆

---

### דוגמה 3: מערכת ניהול מלאי

```sql
SELECT 
    p.product_id AS sku,
    p.product_name AS product,
    c.category_name AS category,
    s.supplier_name AS supplier,
    p.stock_quantity AS current_stock,
    p.reorder_level AS reorder_point,
    p.stock_quantity - p.reorder_level AS stock_above_reorder,
    COALESCE(recent_sales.units_sold_last_30d, 0) AS sold_last_month,
    CASE 
        WHEN p.stock_quantity = 0 THEN 0
        ELSE ROUND(p.stock_quantity / NULLIF(recent_sales.units_sold_last_30d, 0) * 30, 1)
    END AS days_of_stock_remaining,
    CASE 
        WHEN p.stock_quantity = 0 THEN 'Out of Stock 🔴'
        WHEN p.stock_quantity < p.reorder_level THEN 'Reorder Now ⚠️'
        WHEN p.stock_quantity < p.reorder_level * 1.5 THEN 'Low Stock 🟡'
        ELSE 'In Stock ✅'
    END AS stock_alert
FROM products AS p
INNER JOIN categories AS c ON p.category_id = c.category_id
INNER JOIN suppliers AS s ON p.supplier_id = s.supplier_id
LEFT JOIN (
    SELECT 
        product_id,
        SUM(quantity) AS units_sold_last_30d
    FROM order_items oi
    JOIN orders o ON oi.order_id = o.order_id
    WHERE o.order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
    GROUP BY product_id
) AS recent_sales ON p.product_id = recent_sales.product_id
ORDER BY 
    CASE 
        WHEN p.stock_quantity = 0 THEN 1
        WHEN p.stock_quantity < p.reorder_level THEN 2
        ELSE 3
    END,
    p.product_name;
```

**AS בכל מקום - עמודות, טבלאות, תת-שאילתות!** 🎯

---

## 🚨 טעויות נפוצות

### ❌ טעות 1: שכחת AS לתת-שאילתה

```sql
-- לא יעבוד! ❌
SELECT *
FROM (
    SELECT customer_id, SUM(amount) AS total
    FROM orders
    GROUP BY customer_id
);  -- חסר AS alias_name!
```

✅ **פתרון:**
```sql
SELECT *
FROM (
    SELECT customer_id, SUM(amount) AS total
    FROM orders
    GROUP BY customer_id
) AS customer_totals;  -- חובה!
```

---

### ❌ טעות 2: שימוש ב-Alias לפני שהוא הוגדר

```sql
-- לא יעבוד! ❌
SELECT 
    price * quantity AS total,
    total * 0.17 AS vat  -- total עדיין לא קיים כאן!
FROM order_items;
```

✅ **פתרון 1: חישוב מחדש**
```sql
SELECT 
    price * quantity AS total,
    (price * quantity) * 0.17 AS vat
FROM order_items;
```

✅ **פתרון 2: תת-שאילתה**
```sql
SELECT 
    total,
    total * 0.17 AS vat
FROM (
    SELECT price * quantity AS total
    FROM order_items
) AS calculated;
```

---

### ❌ טעות 3: Alias עם תווים מיוחדים ללא מרכאות

```sql
-- לא יעבוד! ❌
SELECT name AS שם הלקוח FROM customers;
```

✅ **פתרון:**
```sql
SELECT name AS "שם הלקוח" FROM customers;
-- או
SELECT name AS `שם הלקוח` FROM customers;  -- MySQL
-- או
SELECT name AS [שם הלקוח] FROM customers;  -- SQL Server
```

---

### ❌ טעות 4: Alias זהה לשם עמודה קיים

```sql
-- מבלבל! ⚠️
SELECT 
    customer_id AS customer_id,  -- למה בכלל?
    name AS customer_id  -- בלבול! שני customer_id
FROM customers;
```

✅ **פתרון: שמות ייחודיים וברורים**
```sql
SELECT 
    customer_id AS cust_id,
    name AS customer_name
FROM customers;
```

---

## 💡 טיפים מקצועיים

### 1️⃣ קונבנציות שמות

```sql
-- טוב ✅
SELECT 
    COUNT(*) AS total_count,
    SUM(amount) AS total_amount,
    AVG(rating) AS avg_rating

-- לא טוב ❌
SELECT 
    COUNT(*) AS c,
    SUM(amount) AS s,
    AVG(rating) AS a
```

**שמות תיאוריים > קיצורים לא ברורים!** 📝

---

### 2️⃣ עקביות בשמות

```sql
-- עקבי ✅
SELECT 
    customer_name,
    customer_city,
    customer_age

-- לא עקבי ❌
SELECT 
    customer_name,
    city_customer,
    age
```

---

### 3️⃣ Snake_case vs camelCase

```sql
-- Snake_case (מומלץ ב-SQL) ✅
SELECT 
    customer_name,
    order_date,
    total_amount

-- camelCase ✅
SELECT 
    customerName,
    orderDate,
    totalAmount

-- PascalCase ❌ (פחות נפוץ ב-SQL)
SELECT 
    CustomerName,
    OrderDate,
    TotalAmount
```

**בחרו סגנון ותשארו איתו!** 🎨

---

### 4️⃣ AS עם Window Functions

```sql
SELECT 
    product_name,
    category,
    price,
    ROW_NUMBER() OVER (PARTITION BY category ORDER BY price DESC) AS price_rank_in_category,
    RANK() OVER (ORDER BY price DESC) AS overall_price_rank,
    DENSE_RANK() OVER (ORDER BY sales DESC) AS sales_rank,
    LAG(price) OVER (ORDER BY price) AS prev_price,
    price - LAG(price) OVER (ORDER BY price) AS price_diff
FROM products;
```

**Window Functions תמיד צריכות AS!** 🪟

---

### 5️⃣ AS קצר לטבלאות, ארוך לעמודות

```sql
-- טוב ✅
SELECT 
    c.customer_id,
    c.name AS customer_full_name,
    o.order_id,
    o.amount AS order_total_amount
FROM customers AS c
JOIN orders AS o ON c.customer_id = o.customer_id;

-- פחות טוב ❌
SELECT 
    customers.customer_id,
    customers.name AS n,
    orders.order_id,
    orders.amount AS a
FROM customers
JOIN orders ON customers.customer_id = orders.customer_id;
```

---

## 🎓 תרגילים להתנסות

### תרגיל 1: שמות ברורים
כתבו שאילתה שמחשבת מחיר כולל (כמות × מחיר) ותנו לכל עמודה שם תיאורי.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    product_name AS item_name,
    quantity AS qty_ordered,
    price AS unit_price,
    quantity * price AS subtotal,
    (quantity * price) * 0.17 AS tax_amount,
    (quantity * price) * 1.17 AS total_with_tax
FROM order_items;
```
</details>

---

### תרגיל 2: JOIN עם Aliases
חברו 3 טבלאות (customers, orders, products) עם aliases קצרים ותנו לכל עמודה שם ברור.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    c.name AS customer_name,
    c.city AS customer_location,
    o.order_id AS order_number,
    o.order_date AS purchase_date,
    p.product_name AS item_purchased,
    o.amount AS order_value
FROM customers AS c
INNER JOIN orders AS o ON c.customer_id = o.customer_id
INNER JOIN products AS p ON o.product_id = p.product_id;
```
</details>

---

### תרגיל 3: CTE עם Alias
צרו CTE שמסכם הזמנות לפי לקוח, ואז בחרו רק לקוחות שהוציאו מעל 1000 ₪.

<details>
<summary>💡 פתרון</summary>

```sql
WITH customer_spending AS (
    SELECT 
        customer_id,
        COUNT(*) AS order_count,
        SUM(amount) AS total_spent,
        AVG(amount) AS avg_order_value
    FROM orders
    GROUP BY customer_id
)
SELECT 
    c.name AS customer_name,
    cs.order_count AS num_orders,
    cs.total_spent AS total_spending,
    cs.avg_order_value AS avg_per_order
FROM customers AS c
INNER JOIN customer_spending AS cs ON c.customer_id = cs.customer_id
WHERE cs.total_spent > 1000
ORDER BY cs.total_spent DESC;
```
</details>

---

## 📊 טבלת סיכום - כל שימושי AS

| שימוש | תחביר | דוגמה | חובה? |
|-------|-------|--------|-------|
| **Column Alias** | `SELECT col AS alias` | `SELECT name AS customer_name` | לא (מומלץ) |
| **Table Alias** | `FROM table AS alias` | `FROM customers AS c` | לא (מומלץ) |
| **Calculated Column** | `SELECT expr AS alias` | `SELECT price * qty AS total` | לא (מומלץ) |
| **Subquery Alias** | `FROM (SELECT...) AS alias` | `FROM (...) AS totals` | **כן!** |
| **CTE Alias** | `WITH alias AS (SELECT...)` | `WITH totals AS (...)` | **כן!** |

---

## ❓ שאלות נפוצות

**Q: חובה להשתמש ב-AS?**
A: לא בעמודות וטבלאות, אבל **כן** בתת-שאילתות ו-CTEs. מומלץ תמיד לקריאות!

**Q: מה ההבדל בין AS ובלי AS?**
A: אין הבדל בפונקציונליות - רק בקריאות. `SELECT name customer_name` זהה ל-`SELECT name AS customer_name`.

**Q: איזה תווים מותרים ב-Alias?**
A: אותיות, מספרים, קו תחתון. אם רוצים רווחים או תווים מיוחדים - צריך מרכאות.

**Q: אפשר להשתמש ב-Alias ב-WHERE?**
A: לא! WHERE רץ לפני SELECT. השתמשו ב-HAVING או תת-שאילתה.

**Q: מה עדיף - Alias קצר או ארוך?**
A: 
- לטבלאות: קצר (c, o, p)
- לעמודות: תיאורי (customer_name, total_amount)

---

## 🎉 סיימתם!

עכשיו אתם יודעים:
✅ כל 5 שימושי AS ב-SQL  
✅ מתי AS חובה ומתי אופציונלי  
✅ איך לתת שמות ברורים ותיאוריים  
✅ קונבנציות ומוסכמות לשמות  
✅ טעויות נפוצות ואיך להימנע מהן  
✅ טיפים מקצועיים לקוד נקי וקריא  

**AS הוא כלי פשוט אבל חזק - תשתמשו בו כדי להפוך את הקוד שלכם לברור וקריא!** 💪

---

**חזרה למדריך הקודם:** [מדריך JOINs מרובים](14_Multiple_JOINs_Guide.md)
