# SQL Practice Exam - Quick Start Guide
## מדריך מהיר למבחן ההכנה

### 📁 קבצים שנוצרו:

1. **`practice_exam_data.csv`** - טבלת הזמנות (100 רשומות)
2. **`practice_exam_customers.csv`** - טבלת לקוחות (33 לקוחות)
3. **`Practice_Exam.md`** - המבחן עצמו (17 שאלות)
4. **`Practice_Exam_Solutions.md`** - הפתרונות המלאים
5. **`Practice_Exam_Setup.md`** - המדריך הזה

---

## 🚀 איך להתחיל?

### שלב 1: טעינת הדאטה למסד הנתונים

**אופציה A - SQLite (מומלץ למתחילים):**

```sql
-- יצירת טבלת orders
CREATE TABLE orders (
    order_id INTEGER PRIMARY KEY,
    customer_name TEXT,
    product_category TEXT,
    product_name TEXT,
    quantity INTEGER,
    price_per_unit REAL,
    order_date DATE,
    shipping_country TEXT,
    payment_method TEXT,
    customer_age INTEGER
);

-- יצירת טבלת customers
CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    customer_name TEXT,
    email TEXT UNIQUE,
    registration_date DATE,
    country TEXT,
    city TEXT,
    loyalty_tier TEXT,
    total_lifetime_orders INTEGER
);

-- טעינת הדאטה מה-CSV
.mode csv
.import practice_exam_data.csv orders
.import practice_exam_customers.csv customers

-- בדיקה
SELECT COUNT(*) FROM orders;
SELECT COUNT(*) FROM customers;
```

**אופציה B - ייבוא דרך Python:**

```python
import sqlite3
import pandas as pd

# קריאת ה-CSVs
df_orders = pd.read_csv('practice_exam_data.csv')
df_customers = pd.read_csv('practice_exam_customers.csv')

# יצירת חיבור למסד נתונים
conn = sqlite3.connect('ecommerce.db')

# טעינה לטבלאות
df_orders.to_sql('orders', conn, if_exists='replace', index=False)
df_customers.to_sql('customers', conn, if_exists='replace', index=False)

print(f"Orders loaded: {len(df_orders)}")
print(f"Customers loaded: {len(df_customers)}")
conn.close()
```

**אופציה C - MySQL/PostgreSQL:**

```sql
-- MySQL
LOAD DATA INFILE 'practice_exam_data.csv'
INTO TABLE orders
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

LOAD DATA INFILE 'practice_exam_customers.csv'
INTO TABLE customers
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

-- PostgreSQL
COPY orders FROM 'practice_exam_data.csv' DELIMITER ',' CSV HEADER;
COPY customers FROM 'practice_exam_customers.csv' DELIMITER ',' CSV HEADER;
```

---

### שלב 2: בדיקה שהדאטה נטענה בהצלחה

```sql
-- בדיקת כמות שורות
SELECT COUNT(*) FROM orders;    -- צריך להיות 100
SELECT COUNT(*) FROM customers; -- צריך להיות 33

-- הצגת 5 שורות ראשונות
SELECT * FROM orders LIMIT 5;
SELECT * FROM customers LIMIT 5;

-- בדיקת קטגוריות
SELECT DISTINCT product_category FROM orders;

-- בדיקת Loyalty Tiers
SELECT DISTINCT loyalty_tier FROM customers;

-- בדיקת JOIN בסיסי
SELECT 
    o.order_id,
    c.customer_name,
    c.email,
    o.product_name
FROM orders o
JOIN customers c ON o.customer_name = c.customer_name
LIMIT 5;
```

**תוצאה צפויה:**
- **טבלת orders:** 100 הזמנות
- **טבלת customers:** 33 לקוחות
- **קטגוריות:** Electronics, Books, Clothing, Home & Garden, Sports
- **Loyalty Tiers:** Bronze, Silver, Gold, Platinum

---

### שלב 3: התחלת המבחן

1. פתחו את `Practice_Exam.md`
2. התחילו לפתור שאלה אחר שאלה
3. בדקו את התשובות שלכם מול `Practice_Exam_Solutions.md`

---

## 📊 מבנה הדאטה

### טבלה 1: orders (הזמנות)

| שדה | סוג | תיאור |
|-----|-----|-------|
| `order_id` | INTEGER | מזהה ייחודי להזמנה |
| `customer_name` | TEXT | שם הלקוח |
| `product_category` | TEXT | קטגוריית המוצר |
| `product_name` | TEXT | שם המוצר |
| `quantity` | INTEGER | כמות |
| `price_per_unit` | REAL | מחיר ליחידה |
| `order_date` | DATE | תאריך ההזמנה |
| `shipping_country` | TEXT | מדינת משלוח |
| `payment_method` | TEXT | אמצעי תשלום |
| `customer_age` | INTEGER | גיל הלקוח |

### טבלה 2: customers (לקוחות)

| שדה | סוג | תיאור |
|-----|-----|-------|
| `customer_id` | INTEGER | מזהה ייחודי ללקוח |
| `customer_name` | TEXT | שם הלקוח |
| `email` | TEXT | כתובת אימייל |
| `registration_date` | DATE | תאריך הרשמה |
| `country` | TEXT | מדינת מגורים |
| `city` | TEXT | עיר |
| `loyalty_tier` | TEXT | רמת נאמנות (Bronze/Silver/Gold/Platinum) |
| `total_lifetime_orders` | INTEGER | סה"כ הזמנות (מתועד) |

### 🔗 קשר בין הטבלאות:

```
customers.customer_name = orders.customer_name
```

**שימו לב:** ה-JOIN נעשה על `customer_name` (לא על ID) - זה מכוון לתרגול סוגים שונים של JOINs!

### סטטיסטיקות על הדאטה:

```sql
-- כמות הזמנות לפי קטגוריה
SELECT product_category, COUNT(*) as count
FROM orders
GROUP BY product_category;

-- התפלגות לקוחות לפי Loyalty Tier
SELECT loyalty_tier, COUNT(*) as count
FROM customers
GROUP BY loyalty_tier
ORDER BY 
    CASE loyalty_tier
        WHEN 'Platinum' THEN 1
        WHEN 'Gold' THEN 2
        WHEN 'Silver' THEN 3
        WHEN 'Bronze' THEN 4
    END;

-- ממוצע הזמנות ללקוח
SELECT 
    AVG(order_count) as avg_orders_per_customer
FROM (
    SELECT customer_name, COUNT(*) as order_count
    FROM orders
    GROUP BY customer_name
);
```

**תוצאות משוערות:**
- **Electronics:** ~30 הזמנות
- **Books:** ~20 הזמנות  
- **Clothing:** ~20 הזמנות
- **Home & Garden:** ~15 הזמנות
- **Sports:** ~15 הזמנות

**Loyalty Tiers:**
- **Platinum:** 5-6 לקוחות
- **Gold:** 10-12 לקוחות
- **Silver:** 10-12 לקוחות
- **Bronze:** 5-7 לקוחות

---

## 💡 טיפים למבחן

### 1. **תכנון לפני כתיבה**
- קראו את השאלה בעיון
- זהו מה השאילתה צריכה להחזיר
- תכננו את השלבים (WHERE → GROUP BY → HAVING → ORDER BY)

### 2. **סדר ביצוע SQL**
```
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
```

### 3. **בדיקת תוצאות**
```sql
-- בדקו תמיד כמה שורות חוזרות
SELECT COUNT(*) FROM (
    -- השאילתה שלכם כאן
);

-- בדקו דוגמה קטנה ראשונה
SELECT ... LIMIT 10;
```

### 4. **שאלות נפוצות**

**Q: מה ההבדל בין WHERE ל-HAVING?**
- `WHERE` מסנן שורות לפני קיבוץ
- `HAVING` מסנן קבוצות אחרי קיבוץ

**Q: מתי להשתמש ב-DISTINCT?**
- כאשר רוצים רק ערכים ייחודיים
- למשל: רשימת לקוחות ללא כפילויות

**Q: איך לחשב אחוזים?**
```sql
(ערך / סכום_כולל) * 100
```

---

## 🎯 אסטרטגיית פתרון

### חלק א' (בסיסי) - 15-20 דקות
- שאלות אלו צריכות להיות מהירות
- בעיקר SELECT, WHERE, GROUP BY בסיסי

### חלק ב' (בינוני) - 25-30 דקות
- שאלות עם subqueries
- חישובים מורכבים יותר
- HAVING, LIMIT

### חלק ג' (מתקדם) - 30-35 דקות
- Window Functions
- CTEs (WITH)
- RANK, PARTITION BY

### חלק ד' (אתגר) - 20-25 דקות
- שאלות מורכבות
- NOT EXISTS, Self Joins
- Running totals

### בונוס - זמן שנותר
- רק אם יש לכם זמן
- לא חובה

---

## 🔍 דוגמה מלאה - פתרון שאלה

**שאלה:** מצאו את הלקוח שהוציא הכי הרבה כסף על Electronics והציגו את פרטי הנאמנות שלו

**תהליך חשיבה:**
1. סינון רק Electronics ✓
2. JOIN עם טבלת customers ✓
3. חישוב סכום לכל לקוח ✓
4. מיון ומציאת המקסימום ✓

**הפתרון:**
```sql
SELECT 
    c.customer_name,
    c.email,
    c.loyalty_tier,
    SUM(o.quantity * o.price_per_unit) AS total_spent
FROM orders o
JOIN customers c ON o.customer_name = c.customer_name
WHERE o.product_category = 'Electronics'
GROUP BY c.customer_name, c.email, c.loyalty_tier
ORDER BY total_spent DESC
LIMIT 1;
```

**בדיקה:**
```sql
-- לראות את כל הלקוחות שקנו Electronics (לא רק הראשון)
SELECT 
    c.customer_name,
    c.email,
    c.loyalty_tier,
    SUM(o.quantity * o.price_per_unit) AS total_spent
FROM orders o
JOIN customers c ON o.customer_name = c.customer_name
WHERE o.product_category = 'Electronics'
GROUP BY c.customer_name, c.email, c.loyalty_tier
ORDER BY total_spent DESC;
```

---

## 📝 רשימת בדיקה לפני הגשה

- [ ] כל השאילתות רצות בלי שגיאות?
- [ ] השתמשתם בשמות עמודות נכונים?
- [ ] הוספתם ALIAS שמתאר (AS)?
- [ ] המיון נכון (ASC/DESC)?
- [ ] ספרתם את הנקודות שצברתם?

---

## 🛠️ כלים שימושיים

### Online SQL Editors (אם אין לכם התקנה מקומית):
- **SQLite Online**: https://sqliteonline.com/
- **DB Fiddle**: https://www.db-fiddle.com/
- **SQL Fiddle**: http://sqlfiddle.com/

### להתקנה מקומית:
- **SQLite Browser**: https://sqlitebrowser.org/
- **DBeaver**: https://dbeaver.io/
- **MySQL Workbench**: https://www.mysql.com/products/workbench/

---

## 📚 משאבים נוספים

בפרויקט יש לכם גם:
- `01_Beginner_SQL.md` - יסודות SQL
- `02_Intermediate_SQL.md` - SQL ברמה בינונית
- `03_Advanced_SQL.md` - SQL מתקדם
- `04_Database_Design.md` - עיצוב מסדי נתונים

**עברו עליהם אם אתם תקועים!**

---

## ⚡ Quick Reference - פקודות מהירות

```sql
-- SELECT בסיסי
SELECT column1, column2 FROM table;

-- עם תנאי
SELECT * FROM table WHERE condition;

-- JOIN בין טבלאות
SELECT o.*, c.email, c.loyalty_tier
FROM orders o
JOIN customers c ON o.customer_name = c.customer_name;

-- LEFT JOIN (כולל לקוחות ללא הזמנות)
SELECT c.*, COUNT(o.order_id) as order_count
FROM customers c
LEFT JOIN orders o ON c.customer_name = o.customer_name
GROUP BY c.customer_id;

-- קיבוץ
SELECT column, COUNT(*) FROM table GROUP BY column;

-- מיון
SELECT * FROM table ORDER BY column DESC;

-- הגבלה
SELECT * FROM table LIMIT 10;

-- Subquery
SELECT * FROM table WHERE column IN (SELECT column FROM other_table);

-- Window Function
SELECT column, SUM(value) OVER (PARTITION BY category) FROM table;

-- CTE (Common Table Expression)
WITH temp_table AS (
    SELECT * FROM table WHERE condition
)
SELECT * FROM temp_table;
```

---

**בהצלחה במבחן! אתם מוכנים! 💪**

אם יש שאלות, תמיד אפשר לחזור לקבצי ההסבר או לפתרונות.
