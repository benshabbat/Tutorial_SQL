# 🔝 מדריך מקיף על TOP/LIMIT ב-SQL

## 📌 מה זה TOP/LIMIT ולמה זה חשוב?

**TOP** (SQL Server) ו-**LIMIT** (MySQL, PostgreSQL) הם פקודות שמגבילות את **מספר השורות** שחוזרות משאילתה.

**במילים פשוטות:**
במקום לקבל מיליון שורות, אנחנו אומרים "תן לי רק את ה-10 הראשונות!" 🎯

### דוגמה בסיסית

```sql
-- MySQL/PostgreSQL - LIMIT
SELECT * FROM customers
ORDER BY total_spent DESC
LIMIT 5;

-- SQL Server - TOP
SELECT TOP 5 * FROM customers
ORDER BY total_spent DESC;

-- תוצאה: רק 5 הלקוחות עם ההוצאה הגבוהה ביותר!
```

---

## 🎯 מתי נשתמש ב-TOP/LIMIT?

✅ **כדאי להשתמש כש:**
- רוצים לראות דוגמאות (למשל, 10 שורות ראשונות)
- צריכים את ה-Top N (למשל, 5 המוצרים הנמכרים ביותר)
- בודקים נתונים (Preview)
- עושים Pagination (עימוד)
- משפרים ביצועים (פחות שורות = מהיר יותר)

❌ **לא כדאי כש:**
- צריכים את כל הנתונים
- לא ברור איזה N לבחור
- יש סינון טוב יותר עם WHERE

---

## 🌍 הבדלים בין מערכות

| מערכת | תחביר | דוגמה |
|-------|-------|--------|
| **MySQL** | `LIMIT n` או `LIMIT n OFFSET m` | `SELECT * FROM t LIMIT 10;` |
| **PostgreSQL** | `LIMIT n` או `LIMIT n OFFSET m` | `SELECT * FROM t LIMIT 10;` |
| **SQL Server** | `TOP n` או `OFFSET-FETCH` | `SELECT TOP 10 * FROM t;` |
| **Oracle** | `FETCH FIRST n ROWS` או `ROWNUM` | `SELECT * FROM t FETCH FIRST 10 ROWS ONLY;` |
| **SQLite** | `LIMIT n` | `SELECT * FROM t LIMIT 10;` |

---

## 📚 סוגי השימושים - 6 דרכים עיקריות

### 1️⃣ LIMIT/TOP פשוט - N שורות ראשונות
### 2️⃣ LIMIT/TOP עם ORDER BY - Top N מסודרים
### 3️⃣ LIMIT עם OFFSET - דילוג על שורות (Pagination)
### 4️⃣ TOP PERCENT - אחוזים (SQL Server)
### 5️⃣ TOP WITH TIES - כולל שוויון (SQL Server)
### 6️⃣ FETCH FIRST - תקן SQL מודרני

בואו נעבור על כל אחד! 🚀

---

## 1️⃣ LIMIT/TOP פשוט

### MySQL/PostgreSQL - LIMIT

```sql
-- 10 שורות ראשונות
SELECT * FROM customers LIMIT 10;

-- שורה אחת
SELECT * FROM customers LIMIT 1;

-- 100 שורות
SELECT * FROM customers LIMIT 100;
```

---

### SQL Server - TOP

```sql
-- 10 שורות ראשונות
SELECT TOP 10 * FROM customers;

-- שורה אחת
SELECT TOP 1 * FROM customers;

-- 100 שורות
SELECT TOP 100 * FROM customers;
```

---

### דוגמה מעשית - בדיקת נתונים

```sql
-- MySQL: הצצה מהירה לטבלה
SELECT * FROM orders LIMIT 5;

-- תוצאה:
-- order_id | customer_id | order_date  | total_amount
-- ---------|-------------|-------------|-------------
-- 1        | 101         | 2025-01-15  | 250.00
-- 2        | 102         | 2025-01-16  | 180.50
-- 3        | 101         | 2025-01-17  | 420.00
-- 4        | 103         | 2025-01-18  | 95.75
-- 5        | 104         | 2025-01-19  | 310.20
```

---

## 2️⃣ LIMIT/TOP עם ORDER BY - Top N

### ⚠️ חשוב מאוד!
**בלי ORDER BY - התוצאה אקראית!**
**עם ORDER BY - מקבלים את ה-Top N לפי קריטריון ברור!**

---

### דוגמה 1: Top 5 לקוחות לפי הוצאה

```sql
-- MySQL
SELECT 
    customer_id,
    name,
    total_spent
FROM customers
ORDER BY total_spent DESC
LIMIT 5;

-- SQL Server
SELECT TOP 5
    customer_id,
    name,
    total_spent
FROM customers
ORDER BY total_spent DESC;
```

**תוצאה:**
```
customer_id | name         | total_spent
------------|--------------|------------
105         | אבי כהן      | 85,000
112         | דני לוי      | 72,500
98          | מיכל משה     | 68,300
134         | רחל ברק     | 61,200
87          | יוסי אלון    | 58,900
```

---

### דוגמה 2: 10 המוצרים הזולים ביותר

```sql
-- MySQL
SELECT 
    product_id,
    product_name,
    price
FROM products
ORDER BY price ASC
LIMIT 10;

-- SQL Server
SELECT TOP 10
    product_id,
    product_name,
    price
FROM customers
ORDER BY price ASC;
```

---

### דוגמה 3: 3 ההזמנות האחרונות

```sql
-- MySQL
SELECT 
    order_id,
    customer_name,
    order_date,
    total_amount
FROM orders
ORDER BY order_date DESC
LIMIT 3;

-- תוצאה: 3 ההזמנות האחרונות
```

---

### דוגמה 4: המוצר הנמכר ביותר

```sql
-- MySQL
SELECT 
    p.product_name,
    COUNT(*) as times_sold
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
GROUP BY p.product_id, p.product_name
ORDER BY COUNT(*) DESC
LIMIT 1;

-- תוצאה: המוצר מס' 1!
```

---

### דוגמה 5: Top 10 ערים עם הכי הרבה לקוחות

```sql
-- MySQL
SELECT 
    city,
    COUNT(*) as customer_count
FROM customers
GROUP BY city
ORDER BY COUNT(*) DESC
LIMIT 10;
```

---

## 3️⃣ LIMIT עם OFFSET - Pagination (עימוד)

### 📄 מה זה Pagination?

חלוקת תוצאות לעמודים - כמו בגוגל (עמוד 1, 2, 3...)

### תחביר MySQL/PostgreSQL

```sql
SELECT * FROM table
ORDER BY column
LIMIT [number_of_rows] OFFSET [rows_to_skip];
```

---

### דוגמה - עמודים של 10 שורות

```sql
-- עמוד 1 (שורות 1-10)
SELECT * FROM customers
ORDER BY customer_id
LIMIT 10 OFFSET 0;

-- עמוד 2 (שורות 11-20)
SELECT * FROM customers
ORDER BY customer_id
LIMIT 10 OFFSET 10;

-- עמוד 3 (שורות 21-30)
SELECT * FROM customers
ORDER BY customer_id
LIMIT 10 OFFSET 20;

-- עמוד 4 (שורות 31-40)
SELECT * FROM customers
ORDER BY customer_id
LIMIT 10 OFFSET 30;
```

---

### נוסחה כללית לעימוד

```sql
-- עמוד N עם M שורות בעמוד
LIMIT M OFFSET (N-1) * M

-- דוגמה: עמוד 5 עם 20 שורות
LIMIT 20 OFFSET (5-1) * 20  -- OFFSET 80
```

---

### דוגמה מעשית - רשימת מוצרים עם עימוד

```sql
-- פרמטרים
SET @page = 3;           -- עמוד 3
SET @page_size = 15;     -- 15 מוצרים בעמוד

-- שאילתה
SELECT 
    product_id,
    product_name,
    price,
    category
FROM products
ORDER BY product_name
LIMIT @page_size OFFSET (@page - 1) * @page_size;

-- מחזיר מוצרים 31-45
```

---

### SQL Server - OFFSET FETCH

```sql
-- עמוד 1 (שורות 1-10)
SELECT * FROM customers
ORDER BY customer_id
OFFSET 0 ROWS FETCH NEXT 10 ROWS ONLY;

-- עמוד 2 (שורות 11-20)
SELECT * FROM customers
ORDER BY customer_id
OFFSET 10 ROWS FETCH NEXT 10 ROWS ONLY;

-- עמוד 3 (שורות 21-30)
SELECT * FROM customers
ORDER BY customer_id
OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY;
```

---

### דוגמה - מערכת חיפוש עם עימוד

```sql
-- MySQL
-- פרמטרים: חיפוש "מחשב", עמוד 2, 20 תוצאות לעמוד
SET @search_term = '%מחשב%';
SET @page = 2;
SET @page_size = 20;

SELECT 
    product_id,
    product_name,
    price,
    description
FROM products
WHERE product_name LIKE @search_term
   OR description LIKE @search_term
ORDER BY product_name
LIMIT @page_size OFFSET (@page - 1) * @page_size;
```

---

### חישוב מספר עמודים

```sql
-- MySQL - סה"כ עמודים
SELECT 
    CEIL(COUNT(*) / 20) as total_pages,
    COUNT(*) as total_results
FROM products
WHERE product_name LIKE '%מחשב%';

-- תוצאה: total_pages = 5, total_results = 87
```

---

## 4️⃣ TOP PERCENT - אחוזים (SQL Server)

### מה זה TOP PERCENT?

במקום מספר קבוע, לוקחים אחוז מהשורות!

```sql
-- 10% מהלקוחות
SELECT TOP 10 PERCENT *
FROM customers
ORDER BY total_spent DESC;

-- אם יש 1000 לקוחות -> מחזיר 100
-- אם יש 500 לקוחות -> מחזיר 50
```

---

### דוגמאות מעשיות

```sql
-- 1% המוצרים היקרים ביותר
SELECT TOP 1 PERCENT
    product_name,
    price
FROM products
ORDER BY price DESC;

-- 5% הלקוחות הכי פעילים
SELECT TOP 5 PERCENT
    customer_id,
    name,
    order_count
FROM customers
ORDER BY order_count DESC;

-- 25% ההזמנות הגדולות ביותר
SELECT TOP 25 PERCENT
    order_id,
    total_amount
FROM orders
ORDER BY total_amount DESC;
```

---

### חישוב ידני של אחוזים (MySQL)

```sql
-- MySQL לא תומך TOP PERCENT, אבל אפשר לחשב ידנית
SET @total = (SELECT COUNT(*) FROM customers);
SET @top_percent = 10;
SET @limit = FLOOR(@total * @top_percent / 100);

SELECT *
FROM customers
ORDER BY total_spent DESC
LIMIT @limit;
```

---

## 5️⃣ TOP WITH TIES - כולל שוויון (SQL Server)

### 🤝 מה זה WITH TIES?

אם יש כמה שורות עם **אותו הערך**, תכלול אותן גם!

---

### דוגמה - בלי WITH TIES (רגיל)

```sql
-- SQL Server
SELECT TOP 3
    product_name,
    price
FROM products
ORDER BY price DESC;

-- תוצאה:
-- product_name       | price
-- -------------------|-------
-- מחשב נייד Pro      | 5000
-- טלפון Pro Max      | 4500
-- טאבלט Ultra        | 4500  -- יש עוד אחד ב-4500!
```

**בעיה:** יש עוד מוצר במחיר 4500 שלא נכלל!

---

### דוגמה - עם WITH TIES (כולל שוויון)

```sql
-- SQL Server
SELECT TOP 3 WITH TIES
    product_name,
    price
FROM products
ORDER BY price DESC;

-- תוצאה:
-- product_name       | price
-- -------------------|-------
-- מחשב נייד Pro      | 5000
-- טלפון Pro Max      | 4500
-- טאבלט Ultra        | 4500
-- אוזניות Premium    | 4500  -- נכלל בגלל WITH TIES!
```

**פתרון:** כל המוצרים במחיר 4500 נכללים!

---

### דוגמה מעשית - Top 5 מוכרים

```sql
-- SQL Server
SELECT TOP 5 WITH TIES
    salesperson_name,
    total_sales
FROM salespeople
ORDER BY total_sales DESC;

-- אם יש 3 אנשים עם אותה מכירה במקום ה-5
-- כולם יוצגו!
```

---

### MySQL - חלופה ל-WITH TIES

```sql
-- MySQL: שימוש ב-CTE
WITH top_price AS (
    SELECT price
    FROM products
    ORDER BY price DESC
    LIMIT 3
)
SELECT p.product_name, p.price
FROM products p
WHERE p.price >= (SELECT MIN(price) FROM top_price)
ORDER BY p.price DESC;
```

---

## 6️⃣ FETCH FIRST - תקן SQL מודרני

### 📜 התקן החדש (SQL:2008)

תחביר אחיד לכל המערכות! נתמך ב-PostgreSQL, Oracle, DB2, SQL Server 2012+.

```sql
SELECT * FROM customers
ORDER BY total_spent DESC
FETCH FIRST 10 ROWS ONLY;
```

---

### תחביר מלא

```sql
-- רק N שורות
SELECT * FROM table
ORDER BY column
FETCH FIRST n ROWS ONLY;

-- עם OFFSET
SELECT * FROM table
ORDER BY column
OFFSET m ROWS
FETCH NEXT n ROWS ONLY;

-- עם TIES
SELECT * FROM table
ORDER BY column
FETCH FIRST n ROWS WITH TIES;

-- אחוזים (Oracle)
SELECT * FROM table
ORDER BY column
FETCH FIRST 10 PERCENT ROWS ONLY;
```

---

### דוגמאות

```sql
-- PostgreSQL
-- 5 הלקוחות הכי טובים
SELECT customer_id, name, total_spent
FROM customers
ORDER BY total_spent DESC
FETCH FIRST 5 ROWS ONLY;

-- עימוד: עמוד 3 עם 20 שורות
SELECT * FROM products
ORDER BY product_name
OFFSET 40 ROWS
FETCH NEXT 20 ROWS ONLY;

-- כולל שוויון
SELECT * FROM scores
ORDER BY score DESC
FETCH FIRST 10 ROWS WITH TIES;
```

---

## 🔥 דוגמאות מעשיות מציאות

### תרחיש 1: דף הבית - מוצרים פופולריים

```sql
-- MySQL
SELECT 
    p.product_id,
    p.product_name,
    p.price,
    p.image_url,
    COUNT(oi.order_id) as times_ordered,
    AVG(r.rating) as avg_rating
FROM products p
JOIN order_items oi ON p.product_id = oi.product_id
LEFT JOIN reviews r ON p.product_id = r.product_id
WHERE p.in_stock = 1
GROUP BY p.product_id, p.product_name, p.price, p.image_url
ORDER BY COUNT(oi.order_id) DESC, AVG(r.rating) DESC
LIMIT 12;  -- 12 מוצרים לדף הבית
```

---

### תרחיש 2: לוח מובילים (Leaderboard)

```sql
-- Top 10 שחקנים
SELECT 
    player_id,
    player_name,
    total_score,
    level,
    RANK() OVER (ORDER BY total_score DESC) as rank
FROM players
ORDER BY total_score DESC
LIMIT 10;

-- עם מיקום של שחקן ספציפי
WITH ranked_players AS (
    SELECT 
        player_id,
        player_name,
        total_score,
        ROW_NUMBER() OVER (ORDER BY total_score DESC) as position
    FROM players
)
SELECT * FROM ranked_players
WHERE position <= 10
   OR player_id = 12345  -- השחקן שלנו
ORDER BY position;
```

---

### תרחיש 3: דוח Top 20 לקוחות VIP

```sql
-- MySQL
WITH customer_stats AS (
    SELECT 
        c.customer_id,
        c.name,
        c.email,
        COUNT(o.order_id) as total_orders,
        SUM(o.total_amount) as lifetime_value,
        MAX(o.order_date) as last_order_date,
        DATEDIFF(CURRENT_DATE, MAX(o.order_date)) as days_since_last_order
    FROM customers c
    LEFT JOIN orders o ON c.customer_id = o.customer_id
    GROUP BY c.customer_id, c.name, c.email
)
SELECT 
    customer_id AS 'מספר לקוח',
    name AS 'שם',
    email AS 'אימייל',
    total_orders AS 'הזמנות',
    lifetime_value AS 'סה"כ הוצאה',
    last_order_date AS 'הזמנה אחרונה',
    days_since_last_order AS 'ימים מאז',
    CASE 
        WHEN lifetime_value >= 100000 THEN '💎 Diamond'
        WHEN lifetime_value >= 50000 THEN '🥇 Gold'
        WHEN lifetime_value >= 20000 THEN '🥈 Silver'
        ELSE '🥉 Bronze'
    END as vip_tier
FROM customer_stats
WHERE total_orders > 0
ORDER BY lifetime_value DESC
LIMIT 20;
```

---

### תרחיש 4: מוצרים שצריך להזמין (מלאי נמוך)

```sql
-- MySQL
-- Top 15 מוצרים שהמלאי שלהם נמוך ביחס למכירות
WITH sales_velocity AS (
    SELECT 
        p.product_id,
        p.product_name,
        p.stock,
        p.reorder_point,
        COUNT(*) as sold_last_30_days,
        COUNT(*) / 30.0 as daily_velocity
    FROM products p
    JOIN order_items oi ON p.product_id = oi.product_id
    JOIN orders o ON oi.order_id = o.order_id
    WHERE o.order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
    GROUP BY p.product_id, p.product_name, p.stock, p.reorder_point
)
SELECT 
    product_name AS 'מוצר',
    stock AS 'במלאי',
    reorder_point AS 'נקודת הזמנה',
    sold_last_30_days AS 'נמכר ב-30 יום',
    ROUND(daily_velocity, 2) AS 'ממוצע יומי',
    ROUND(stock / daily_velocity, 0) AS 'ימים עד גמר',
    CASE 
        WHEN stock < reorder_point THEN '🔴 להזמין עכשיו!'
        WHEN stock / daily_velocity < 14 THEN '🟡 להזמין בקרוב'
        ELSE '🟢 תקין'
    END as status
FROM sales_velocity
WHERE stock / daily_velocity < 21  -- פחות מ-3 שבועות
ORDER BY stock / daily_velocity ASC
LIMIT 15;
```

---

### תרחיש 5: חדשות/בלוג - 10 פוסטים אחרונים

```sql
-- MySQL
SELECT 
    post_id,
    title,
    author_name,
    published_date,
    category,
    view_count,
    comment_count,
    featured_image
FROM blog_posts
WHERE status = 'published'
  AND published_date <= CURRENT_TIMESTAMP
ORDER BY published_date DESC
LIMIT 10;
```

---

### תרחיש 6: מערכת המלצות - "מוצרים שאהבת"

```sql
-- MySQL
-- מוצרים דומים למה שהלקוח קנה
WITH customer_categories AS (
    SELECT DISTINCT p.category
    FROM order_items oi
    JOIN orders o ON oi.order_id = o.order_id
    JOIN products p ON oi.product_id = p.product_id
    WHERE o.customer_id = 123
),
popular_in_categories AS (
    SELECT 
        p.product_id,
        p.product_name,
        p.price,
        p.category,
        COUNT(oi.order_id) as popularity
    FROM products p
    JOIN order_items oi ON p.product_id = oi.product_id
    WHERE p.category IN (SELECT category FROM customer_categories)
      AND p.product_id NOT IN (
          SELECT product_id FROM order_items oi2
          JOIN orders o2 ON oi2.order_id = o2.order_id
          WHERE o2.customer_id = 123
      )
    GROUP BY p.product_id, p.product_name, p.price, p.category
)
SELECT 
    product_name AS 'מוצר מומלץ',
    price AS 'מחיר',
    category AS 'קטגוריה',
    popularity AS 'פופולריות'
FROM popular_in_categories
ORDER BY popularity DESC
LIMIT 6;  -- 6 המלצות
```

---

### תרחיש 7: דוח ניהולי - Top & Bottom 5

```sql
-- MySQL
-- 5 המוצרים הכי מצליחים
(SELECT 
    'Top' as category,
    product_name,
    total_revenue,
    total_units_sold
FROM (
    SELECT 
        p.product_name,
        SUM(oi.quantity * oi.unit_price) as total_revenue,
        SUM(oi.quantity) as total_units_sold
    FROM products p
    JOIN order_items oi ON p.product_id = oi.product_id
    GROUP BY p.product_id, p.product_name
    ORDER BY SUM(oi.quantity * oi.unit_price) DESC
    LIMIT 5
) top_products)

UNION ALL

-- 5 המוצרים הכי פחות מצליחים
(SELECT 
    'Bottom' as category,
    product_name,
    total_revenue,
    total_units_sold
FROM (
    SELECT 
        p.product_name,
        SUM(oi.quantity * oi.unit_price) as total_revenue,
        SUM(oi.quantity) as total_units_sold
    FROM products p
    JOIN order_items oi ON p.product_id = oi.product_id
    GROUP BY p.product_id, p.product_name
    ORDER BY SUM(oi.quantity * oi.unit_price) ASC
    LIMIT 5
) bottom_products)

ORDER BY 
    CASE WHEN category = 'Top' THEN 1 ELSE 2 END,
    total_revenue DESC;
```

---

## ⚡ ביצועים ואופטימיזציה

### ✅ LIMIT משפר ביצועים!

```sql
-- ❌ איטי - סורק את כל הטבלה
SELECT * FROM huge_table
ORDER BY created_date DESC;
-- סורק 10,000,000 שורות!

-- ✅ מהיר - עוצר אחרי N שורות
SELECT * FROM huge_table
ORDER BY created_date DESC
LIMIT 10;
-- עוצר אחרי 10 שורות (אם יש אינדקס)
```

---

### 🚀 טיפים לביצועים

#### 1. השתמש באינדקס על עמודת ORDER BY

```sql
-- יצירת אינדקס
CREATE INDEX idx_created_date ON orders(created_date DESC);

-- עכשיו זה מהיר מאוד!
SELECT * FROM orders
ORDER BY created_date DESC
LIMIT 10;
```

---

#### 2. LIMIT עם WHERE

```sql
-- ✅ מהיר - WHERE מסנן קודם
SELECT * FROM orders
WHERE customer_id = 123
ORDER BY order_date DESC
LIMIT 5;

-- אינדקס מומלץ
CREATE INDEX idx_customer_date ON orders(customer_id, order_date DESC);
```

---

#### 3. הימנע מ-OFFSET גדול

```sql
-- ❌ איטי! צריך לדלג על 1,000,000 שורות
SELECT * FROM huge_table
ORDER BY id
LIMIT 10 OFFSET 1000000;

-- ✅ מהיר יותר - Keyset Pagination
SELECT * FROM huge_table
WHERE id > 1000000
ORDER BY id
LIMIT 10;
```

---

### 🔑 Keyset Pagination (Cursor-based)

במקום OFFSET, שמור את המפתח האחרון!

```sql
-- עמוד 1
SELECT * FROM products
ORDER BY product_id
LIMIT 10;
-- תוצאה: product_id = 1 עד 10
-- שמור: last_id = 10

-- עמוד 2 (במקום OFFSET 10)
SELECT * FROM products
WHERE product_id > 10  -- המפתח האחרון!
ORDER BY product_id
LIMIT 10;
-- הרבה יותר מהיר!
```

---

### ⚠️ בעיות ביצועים עם TOP PERCENT

```sql
-- ❌ חייב לספור את כל השורות!
SELECT TOP 10 PERCENT *
FROM huge_table
ORDER BY created_date DESC;
-- סורק הכל כדי לדעת כמה זה 10%

-- ✅ עדיף מספר קבוע
SELECT TOP 1000 *
FROM huge_table
ORDER BY created_date DESC;
```

---

## 🎓 תרגילים

### תרגיל 1: Top 3 ערים
מצאו את 3 הערים עם הכי הרבה לקוחות.

<details>
<summary>💡 פתרון</summary>

```sql
-- MySQL
SELECT 
    city AS 'עיר',
    COUNT(*) as 'מספר לקוחות'
FROM customers
GROUP BY city
ORDER BY COUNT(*) DESC
LIMIT 3;

-- SQL Server
SELECT TOP 3
    city AS 'עיר',
    COUNT(*) as 'מספר לקוחות'
FROM customers
GROUP BY city
ORDER BY COUNT(*) DESC;
```
</details>

---

### תרגיל 2: עימוד מוצרים
יש 237 מוצרים. צרו שאילתה לעמוד 5 עם 20 מוצרים בעמוד.

<details>
<summary>💡 פתרון</summary>

```sql
-- MySQL
SELECT 
    product_id,
    product_name,
    price
FROM products
ORDER BY product_name
LIMIT 20 OFFSET 80;  -- (5-1) * 20 = 80

-- SQL Server
SELECT 
    product_id,
    product_name,
    price
FROM products
ORDER BY product_name
OFFSET 80 ROWS
FETCH NEXT 20 ROWS ONLY;

-- סה"כ עמודים: CEIL(237 / 20) = 12 עמודים
```
</details>

---

### תרגיל 3: 5% הלקוחות הכי טובים
מצאו את 5% הלקוחות עם ההוצאה הגבוהה ביותר (יש 1000 לקוחות).

<details>
<summary>💡 פתרון</summary>

```sql
-- SQL Server
SELECT TOP 5 PERCENT
    customer_id,
    name,
    total_spent
FROM customers
ORDER BY total_spent DESC;
-- מחזיר 50 לקוחות (5% מ-1000)

-- MySQL - חישוב ידני
SET @top_5_percent = FLOOR(1000 * 0.05);

SELECT 
    customer_id,
    name,
    total_spent
FROM customers
ORDER BY total_spent DESC
LIMIT 50;  -- 5% מ-1000
```
</details>

---

### תרגיל 4: המוצר השני הכי יקר
מצאו את המוצר השני הכי יקר (לא הראשון!).

<details>
<summary>💡 פתרון</summary>

```sql
-- MySQL - דרך 1
SELECT product_name, price
FROM products
ORDER BY price DESC
LIMIT 1 OFFSET 1;  -- דלג על הראשון

-- MySQL - דרך 2
SELECT product_name, price
FROM products
WHERE price < (SELECT MAX(price) FROM products)
ORDER BY price DESC
LIMIT 1;

-- SQL Server
SELECT product_name, price
FROM products
ORDER BY price DESC
OFFSET 1 ROWS
FETCH NEXT 1 ROW ONLY;
```
</details>

---

### תרגיל 5: Top 10 עם קשרים
מצאו את 10 הציונים הגבוהים ביותר, כולל כל מי שיש לו אותו ציון.

<details>
<summary>💡 פתרון</summary>

```sql
-- SQL Server
SELECT TOP 10 WITH TIES
    student_name,
    score
FROM exam_results
ORDER BY score DESC;

-- MySQL - חלופה
WITH top_scores AS (
    SELECT DISTINCT score
    FROM exam_results
    ORDER BY score DESC
    LIMIT 10
)
SELECT 
    student_name,
    score
FROM exam_results
WHERE score >= (SELECT MIN(score) FROM top_scores)
ORDER BY score DESC;
```
</details>

---

## ❌ טעויות נפוצות

### 1️⃣ LIMIT/TOP בלי ORDER BY

```sql
-- ❌ תוצאה אקראית!
SELECT * FROM customers LIMIT 10;
-- יכול להחזיר שורות שונות בכל פעם!

-- ✅ תוצאה עקבית
SELECT * FROM customers
ORDER BY customer_id
LIMIT 10;
```

---

### 2️⃣ OFFSET גדול מדי

```sql
-- ❌ איטי מאוד!
SELECT * FROM products
LIMIT 10 OFFSET 1000000;
-- דולג על מליון שורות בכל שאילתה!

-- ✅ Keyset Pagination
SELECT * FROM products
WHERE product_id > @last_seen_id
ORDER BY product_id
LIMIT 10;
```

---

### 3️⃣ שימוש ב-LIMIT על COUNT

```sql
-- ❌ מיותר!
SELECT COUNT(*) FROM orders LIMIT 1;
-- COUNT תמיד מחזיר שורה אחת

-- ✅ פשוט
SELECT COUNT(*) FROM orders;
```

---

### 4️⃣ LIMIT על Subquery ללא ORDER BY

```sql
-- ❌ בעיה!
SELECT * FROM (
    SELECT * FROM orders LIMIT 100
) sub
ORDER BY order_date DESC;
-- ה-100 השורות יכולות להיות אקראיות!

-- ✅ נכון
SELECT * FROM (
    SELECT * FROM orders
    ORDER BY order_date DESC
    LIMIT 100
) sub
ORDER BY order_date DESC;
```

---

### 5️⃣ שכחת שה-LIMIT חל אחרי GROUP BY

```sql
-- ❌ חושב שמגביל לפני GROUP BY
SELECT category, COUNT(*)
FROM products
LIMIT 5
GROUP BY category;
-- שגיאה!

-- ✅ נכון - LIMIT אחרי GROUP BY
SELECT category, COUNT(*) as total
FROM products
GROUP BY category
ORDER BY COUNT(*) DESC
LIMIT 5;
```

---

## 💡 טיפים מקצועיים

### ✅ טיפ 1: חישוב סה"כ עמודים

```sql
-- MySQL
SELECT 
    COUNT(*) as total_items,
    CEIL(COUNT(*) / 20) as total_pages
FROM products
WHERE category = 'electronics';

-- שימוש בשאילתה אחת
SELECT 
    SQL_CALC_FOUND_ROWS *
FROM products
LIMIT 20 OFFSET 0;

SELECT FOUND_ROWS() as total_items;
```

---

### ✅ טיפ 2: LIMIT עם UNION

```sql
-- Top 5 מכל קטגוריה
(SELECT 'Electronics' as category, product_name, sales
 FROM products 
 WHERE category = 'Electronics'
 ORDER BY sales DESC
 LIMIT 5)
 
UNION ALL

(SELECT 'Clothing' as category, product_name, sales
 FROM products 
 WHERE category = 'Clothing'
 ORDER BY sales DESC
 LIMIT 5);
```

---

### ✅ טיפ 3: Random Rows

```sql
-- MySQL - שורות אקראיות
SELECT * FROM products
ORDER BY RAND()
LIMIT 5;

-- SQL Server
SELECT TOP 5 * FROM products
ORDER BY NEWID();

-- PostgreSQL
SELECT * FROM products
ORDER BY RANDOM()
LIMIT 5;
```

---

### ✅ טיפ 4: LIMIT עם CTE

```sql
WITH top_customers AS (
    SELECT 
        customer_id,
        SUM(total_amount) as lifetime_value
    FROM orders
    GROUP BY customer_id
    ORDER BY SUM(total_amount) DESC
    LIMIT 100
)
SELECT 
    c.name,
    tc.lifetime_value,
    COUNT(o.order_id) as order_count
FROM top_customers tc
JOIN customers c ON tc.customer_id = c.customer_id
JOIN orders o ON tc.customer_id = o.customer_id
GROUP BY c.customer_id, c.name, tc.lifetime_value
ORDER BY tc.lifetime_value DESC;
```

---

### ✅ טיפ 5: Pagination Metadata

```sql
-- MySQL - מידע מלא על עימוד
SET @page = 3;
SET @page_size = 20;
SET @total = (SELECT COUNT(*) FROM products);

SELECT 
    @page as current_page,
    @page_size as page_size,
    @total as total_items,
    CEIL(@total / @page_size) as total_pages,
    (@page - 1) * @page_size + 1 as from_item,
    LEAST(@page * @page_size, @total) as to_item,
    @page > 1 as has_previous,
    @page < CEIL(@total / @page_size) as has_next;
```

---

## 📋 טבלת סיכום

| מערכת | תחביר בסיסי | עימוד | אחוזים | קשרים |
|-------|-------------|-------|--------|-------|
| **MySQL** | `LIMIT n` | `LIMIT n OFFSET m` | ❌ | ❌ |
| **PostgreSQL** | `LIMIT n` | `LIMIT n OFFSET m` | ❌ | ❌ |
| **SQL Server** | `TOP n` | `OFFSET m FETCH n` | `TOP n PERCENT` | `WITH TIES` |
| **Oracle** | `FETCH FIRST n` | `OFFSET m FETCH n` | `FETCH n PERCENT` | `WITH TIES` |

---

## 🎯 מתי להשתמש במה?

| צורך | פתרון | דוגמה |
|------|--------|--------|
| הצצה מהירה | `LIMIT 10` | בדיקת טבלה |
| Top N | `LIMIT n` + `ORDER BY` | 5 המוצרים הנמכרים |
| עימוד | `LIMIT n OFFSET m` | עמוד 3 מתוך 10 |
| אחוזים | `TOP n PERCENT` | 10% הלקוחות הטובים |
| כולל שוויון | `WITH TIES` | Top 10 עם קשרים |
| תקן מודרני | `FETCH FIRST` | קוד portable |

---

## ❓ שאלות נפוצות

**Q: מה ההבדל בין LIMIT ל-TOP?**
A: אותו דבר! LIMIT = MySQL/PostgreSQL. TOP = SQL Server.

**Q: למה OFFSET איטי?**
A: כי צריך לדלג על כל השורות עד ה-OFFSET. Keyset Pagination מהיר יותר.

**Q: איך לדעת כמה עמודים יש?**
A: `CEIL(total_rows / page_size)`

**Q: מה קורה אם LIMIT גדול מהמספר שורות?**
A: מחזיר את כל השורות שיש. לא שגיאה.

**Q: האם LIMIT 0 חוקי?**
A: כן! מחזיר 0 שורות (שימושי לבדיקת מבנה טבלה).

**Q: האם אפשר LIMIT על UPDATE/DELETE?**
A: תלוי במערכת. MySQL כן, SQL Server לא ישירות.

---

## 🎉 סיכום

עכשיו אתם יודעים:
✅ הבדלים בין TOP/LIMIT/FETCH בכל מערכת  
✅ 6 סוגי שימושים: פשוט, ORDER BY, OFFSET, PERCENT, TIES, FETCH  
✅ עימוד (Pagination) נכון ויעיל  
✅ דוגמאות מעשיות מציאות  
✅ אופטימיזציה וביצועים  
✅ Keyset Pagination  
✅ טעויות נפוצות ופתרונות  

**TOP/LIMIT הם כלים חיוניים - תשתמשו בהם נכון!** 🔝✨

---

**חזרה למדריך הקודם:** [מדריך WITH/CTE](18_WITH_CTE_Guide.md)
