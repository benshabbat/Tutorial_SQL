# 🌍 מדריך עבודה עם נתונים לא-אנגליים ב-SQL

## 📌 למה זה חשוב?

אם אתם עובדים עם מערכות בעברית, ערבית, רוסית, סינית או כל שפה אחרת - אתם **חייבים** לדעת איך להתמודד עם:
- 🏷️ שמות עמודות בשפות אחרות
- 📝 תוכן נתונים (data) בשפות אחרות
- 🔤 קידוד תווים (Character Encoding)
- 🔍 חיפוש וסינון טקסט
- 📊 מיון (Sorting) נכון

**בלי זה - הכל יהיה ??? או גיבריש!** 😵

---

## 🎯 שלושת העולמות

### 1️⃣ שמות טבלאות ועמודות
### 2️⃣ תוכן הנתונים (Data)
### 3️⃣ קידוד התווים (Encoding)

בואו נעבור על כל אחד! 🚀

---

## 1️⃣ שמות טבלאות ועמודות בעברית

### האם כדאי? מתי? איך?

**שאלה חשובה:** האם לתת שמות בעברית לטבלאות ועמודות?

**תשובה קצרה:** לרוב **לא מומלץ!** אבל **אפשר**. ✅❌

---

### ✅ יתרונות שמות בעברית

```sql
-- קל לקרוא ולהבין למי שלא יודע אנגלית
SELECT שם, גיל, עיר
FROM לקוחות
WHERE גיל > 25;
```

**יתרונות:**
- 📖 קריא למשתמשים עבריים
- 🎯 אינטואיטיבי למתחילים
- 💬 תואם את השפה העסקית

---

### ❌ חסרונות שמות בעברית

```sql
-- בעיות פוטנציאליות:
-- 1. בעיות תאימות עם כלים שונים
-- 2. קשיים בתיעוד קוד
-- 3. בעיות בתכנות (Python, Java, etc.)
-- 4. צריך מרכאות תמיד!

SELECT "שם", "גיל"  -- חייב מרכאות!
FROM "לקוחות"
WHERE "גיל" > 25;
```

---

### 🎯 המלצה מקצועית - הפרדה!

**הפתרון הטוב ביותר:**
- 🔤 שמות טבלאות/עמודות: **אנגלית**
- 📝 תוכן הנתונים: **כל שפה שתרצו**
- 🏷️ תיאורים (Comments): **עברית**

```sql
-- מבנה הטבלה באנגלית
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),      -- שם הלקוח
    city VARCHAR(50),       -- עיר מגורים
    age INT                 -- גיל
);

-- הנתונים בעברית
INSERT INTO customers (customer_id, name, city, age)
VALUES 
    (1, 'אבי כהן', 'תל אביב', 28),
    (2, 'בני לוי', 'חיפה', 35),
    (3, 'גלי מזרחי', 'ירושלים', 42);

-- שאילתה
SELECT name, city, age
FROM customers
WHERE city = 'תל אביב';
```

**הטוב משני העולמות!** ✨

---

### 🔧 אם בכל זאת עובדים עם שמות בעברית

#### MySQL

```sql
-- צריך מרכאות גרשיים `` (backticks)
CREATE TABLE `לקוחות` (
    `מספר_לקוח` INT PRIMARY KEY,
    `שם` VARCHAR(100),
    `עיר` VARCHAR(50)
);

SELECT `שם`, `עיר`
FROM `לקוחות`
WHERE `עיר` = 'תל אביב';
```

#### SQL Server

```sql
-- צריך סוגריים מרובעים []
CREATE TABLE [לקוחות] (
    [מספר_לקוח] INT PRIMARY KEY,
    [שם] NVARCHAR(100),
    [עיר] NVARCHAR(50)
);

SELECT [שם], [עיר]
FROM [לקוחות]
WHERE [עיר] = N'תל אביב';  -- N לפני מחרוזת!
```

#### PostgreSQL

```sql
-- צריך מרכאות כפולות ""
CREATE TABLE "לקוחות" (
    "מספר_לקוח" INT PRIMARY KEY,
    "שם" VARCHAR(100),
    "עיר" VARCHAR(50)
);

SELECT "שם", "עיר"
FROM "לקוחות"
WHERE "עיר" = 'תל אביב';
```

⚠️ **חשוב:** תמיד תשתמשו במרכאות מסוג מסוים לפי המערכת!

---

## 2️⃣ תוכן נתונים בעברית (Data)

### זה הנפוץ והמומלץ! ✅

רוב המערכות **צריכות** לאחסן נתונים בעברית:
- שמות לקוחות 👤
- כתובות 📍
- תיאורים 📝
- הערות 💬

---

### 🎯 סוגי עמודות נכונים

#### MySQL

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci,
    city VARCHAR(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci,
    description TEXT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci
);

-- או להגדיר לכל הטבלה:
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    city VARCHAR(50),
    description TEXT
) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

**חשוב:**
- ✅ `utf8mb4` - תומך בכל התווים כולל Emoji! 😊
- ❌ `utf8` - תמיכה חלקית, לא מומלץ
- ✅ `utf8mb4_unicode_ci` - Collation נכון לעברית

---

#### SQL Server

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name NVARCHAR(100),        -- N = Unicode!
    city NVARCHAR(50),
    description NVARCHAR(MAX),
    note VARCHAR(100)          -- לא תומך עברית!
);

-- הוספת נתונים
INSERT INTO customers (customer_id, name, city)
VALUES 
    (1, N'אבי כהן', N'תל אביב'),      -- N לפני כל מחרוזת!
    (2, N'בני לוי', N'חיפה');
```

**חשוב:**
- ✅ `NVARCHAR` / `NCHAR` / `NTEXT` - תומכים Unicode (עברית)
- ❌ `VARCHAR` / `CHAR` / `TEXT` - לא תומכים עברית!
- ⚠️ חייבים `N` לפני מחרוזות: `N'טקסט'`

---

#### PostgreSQL

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),         -- תומך Unicode אוטומטית
    city VARCHAR(50),
    description TEXT
);

-- הוספת נתונים - פשוט!
INSERT INTO customers (customer_id, name, city)
VALUES 
    (1, 'אבי כהן', 'תל אביב'),
    (2, 'בני לוי', 'חיפה');
```

**PostgreSQL תומך Unicode out-of-the-box!** ✅

---

### 🔍 חיפוש וסינון בעברית

#### MySQL

```sql
-- חיפוש מדויק
SELECT * FROM customers WHERE name = 'אבי כהן';

-- חיפוש חלקי
SELECT * FROM customers WHERE name LIKE '%אבי%';

-- חיפוש case-insensitive (לא משנה אותיות גדולות/קטנות)
SELECT * FROM customers 
WHERE name COLLATE utf8mb4_unicode_ci = 'אבי כהן';

-- חיפוש מתחיל ב...
SELECT * FROM customers WHERE name LIKE 'אבי%';

-- חיפוש מסתיים ב...
SELECT * FROM customers WHERE city LIKE '%ירושלים';
```

---

#### SQL Server

```sql
-- חיפוש מדויק - חייב N!
SELECT * FROM customers WHERE name = N'אבי כהן';

-- חיפוש חלקי
SELECT * FROM customers WHERE name LIKE N'%אבי%';

-- חיפוש case-insensitive
SELECT * FROM customers 
WHERE name COLLATE Hebrew_CI_AI = N'אבי כהן';
-- CI = Case Insensitive
-- AI = Accent Insensitive

-- חיפוש בכמה עמודות
SELECT * FROM customers 
WHERE name LIKE N'%כהן%' OR city LIKE N'%תל אביב%';
```

---

#### PostgreSQL

```sql
-- חיפוש מדויק
SELECT * FROM customers WHERE name = 'אבי כהן';

-- חיפוש לא תלוי רישיות (case-insensitive)
SELECT * FROM customers WHERE LOWER(name) = LOWER('אבי כהן');
-- או
SELECT * FROM customers WHERE name ILIKE 'אבי כהן';

-- חיפוש חלקי
SELECT * FROM customers WHERE name LIKE '%אבי%';

-- חיפוש עם regex
SELECT * FROM customers WHERE name ~ 'אבי|בני';  -- אבי או בני
```

---

### 📊 מיון (Sorting) בעברית

#### MySQL

```sql
-- מיון אלפביתי עברי
SELECT name, city
FROM customers
ORDER BY name COLLATE utf8mb4_unicode_ci;

-- מיון הפוך
SELECT name, city
FROM customers
ORDER BY name COLLATE utf8mb4_unicode_ci DESC;

-- מיון לפי עיר ואז שם
SELECT name, city
FROM customers
ORDER BY city COLLATE utf8mb4_unicode_ci, 
         name COLLATE utf8mb4_unicode_ci;
```

---

#### SQL Server

```sql
-- מיון אלפביתי עברי
SELECT name, city
FROM customers
ORDER BY name COLLATE Hebrew_CI_AS;

-- Collations נפוצים לעברית:
-- Hebrew_CI_AS - Case Insensitive, Accent Sensitive
-- Hebrew_CS_AS - Case Sensitive, Accent Sensitive
-- Hebrew_100_CI_AI - SQL Server 2008+

SELECT name, city
FROM customers
ORDER BY name COLLATE Hebrew_100_CI_AI;
```

---

#### PostgreSQL

```sql
-- מיון אלפביתי
SELECT name, city
FROM customers
ORDER BY name;

-- מיון עם Collation ספציפי
SELECT name, city
FROM customers
ORDER BY name COLLATE "he_IL.utf8";  -- עברית ישראל

-- מיון מותאם אישית
SELECT name, city
FROM customers
ORDER BY name COLLATE "he_IL";
```

---

## 3️⃣ קידוד תווים (Character Encoding)

### 🎯 UTF-8 הוא המלך!

**כלל זהב:** תמיד תשתמשו ב-**UTF-8** (או UTF8MB4 ב-MySQL)!

---

### MySQL - הגדרת קידוד נכון

#### בעת יצירת מסד נתונים

```sql
CREATE DATABASE my_database
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;
```

#### בעת יצירת טבלה

```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    city VARCHAR(50)
) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

#### שינוי קידוד של טבלה קיימת

```sql
ALTER TABLE customers 
CONVERT TO CHARACTER SET utf8mb4 
COLLATE utf8mb4_unicode_ci;
```

#### בדיקת קידוד נוכחי

```sql
-- קידוד מסד נתונים
SELECT DEFAULT_CHARACTER_SET_NAME, DEFAULT_COLLATION_NAME
FROM information_schema.SCHEMATA
WHERE SCHEMA_NAME = 'my_database';

-- קידוד טבלה
SHOW TABLE STATUS LIKE 'customers';

-- קידוד עמודה
SHOW FULL COLUMNS FROM customers;
```

---

### SQL Server - הגדרת קידוד

#### בעת יצירת מסד נתונים

```sql
CREATE DATABASE my_database
COLLATE Hebrew_CI_AS;
```

#### שינוי Collation של עמודה

```sql
ALTER TABLE customers
ALTER COLUMN name NVARCHAR(100) COLLATE Hebrew_100_CI_AI;
```

#### בדיקת Collation

```sql
-- Collation של מסד נתונים
SELECT name, collation_name
FROM sys.databases
WHERE name = 'my_database';

-- Collation של עמודה
SELECT COLUMN_NAME, COLLATION_NAME
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'customers';
```

---

### PostgreSQL - הגדרת קידוד

#### בעת יצירת מסד נתונים

```sql
CREATE DATABASE my_database
WITH ENCODING 'UTF8'
LC_COLLATE = 'he_IL.UTF-8'
LC_CTYPE = 'he_IL.UTF-8';
```

#### בדיקת קידוד

```sql
-- קידוד מסד נתונים
SELECT datname, pg_encoding_to_char(encoding), datcollate
FROM pg_database
WHERE datname = 'my_database';

-- Collation זמין
SELECT * FROM pg_collation WHERE collname LIKE '%he%';
```

---

## 🔥 תרחישים מעשיים

### תרחיש 1: חיפוש לקוחות בעברית

```sql
-- MySQL
SELECT 
    customer_id,
    name AS שם_לקוח,
    city AS עיר,
    phone AS טלפון
FROM customers
WHERE city IN ('תל אביב', 'חיפה', 'ירושלים')
  AND name LIKE '%כהן%'
ORDER BY city COLLATE utf8mb4_unicode_ci, 
         name COLLATE utf8mb4_unicode_ci;
```

```sql
-- SQL Server
SELECT 
    customer_id,
    name AS שם_לקוח,
    city AS עיר,
    phone AS טלפון
FROM customers
WHERE city IN (N'תל אביב', N'חיפה', N'ירושלים')
  AND name LIKE N'%כהן%'
ORDER BY city COLLATE Hebrew_CI_AS, 
         name COLLATE Hebrew_CI_AS;
```

---

### תרחיש 2: דוח מכירות בעברית

```sql
SELECT 
    c.name AS 'שם הלקוח',
    c.city AS 'עיר',
    COUNT(o.order_id) AS 'מספר הזמנות',
    SUM(o.amount) AS 'סה"כ הוצאה',
    AVG(o.amount) AS 'ממוצע הזמנה',
    CASE 
        WHEN SUM(o.amount) > 10000 THEN 'VIP זהב'
        WHEN SUM(o.amount) > 5000 THEN 'VIP כסף'
        WHEN SUM(o.amount) > 1000 THEN 'VIP ברונזה'
        ELSE 'רגיל'
    END AS 'סוג לקוח'
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name, c.city
HAVING SUM(o.amount) > 0
ORDER BY SUM(o.amount) DESC;
```

**תוצאה:**
```
שם הלקוח  | עיר       | מספר הזמנות | סה"כ הוצאה | ממוצע הזמנה | סוג לקוח
-----------|-----------|-------------|------------|-------------|----------
אבי כהן    | תל אביב   | 25          | 15000      | 600         | VIP זהב
בני לוי    | חיפה      | 18          | 7200       | 400         | VIP כסף
```

---

### תרחיש 3: חיפוש מתקדם בעברית

```sql
-- MySQL
SELECT 
    product_id,
    product_name AS שם_מוצר,
    category AS קטגוריה,
    price AS מחיר,
    description AS תיאור
FROM products
WHERE 
    (product_name LIKE '%מחשב%' OR description LIKE '%מחשב%')
    AND category IN ('אלקטרוניקה', 'מחשבים')
    AND price BETWEEN 1000 AND 5000
ORDER BY price;
```

---

### תרחיש 4: טיפול במחרוזות בעברית

```sql
-- MySQL
SELECT 
    name,
    UPPER(name) AS שם_גדול,
    LOWER(name) AS שם_קטן,
    LENGTH(name) AS אורך_שם,
    CONCAT('שלום ', name) AS ברכה,
    SUBSTRING(name, 1, 3) AS שלושה_תווים,
    REPLACE(name, 'כהן', 'כ.') AS שם_מקוצר
FROM customers;
```

**תוצאה:**
```
name      | שם_גדול   | שם_קטן    | אורך_שם | ברכה            | שלושה_תווים | שם_מקוצר
----------|-----------|-----------|---------|-----------------|------------|----------
אבי כהן   | אבי כהן   | אבי כהן   | 7       | שלום אבי כהן    | אבי        | אבי כ.
```

---

### תרחיש 5: עבודה עם תאריכים בעברית

```sql
-- MySQL
SELECT 
    order_id,
    customer_name,
    order_date,
    CASE DAYOFWEEK(order_date)
        WHEN 1 THEN 'ראשון'
        WHEN 2 THEN 'שני'
        WHEN 3 THEN 'שלישי'
        WHEN 4 THEN 'רביעי'
        WHEN 5 THEN 'חמישי'
        WHEN 6 THEN 'שישי'
        WHEN 7 THEN 'שבת'
    END AS יום_בשבוע,
    CASE MONTH(order_date)
        WHEN 1 THEN 'ינואר'
        WHEN 2 THEN 'פברואר'
        WHEN 3 THEN 'מרץ'
        WHEN 4 THEN 'אפריל'
        WHEN 5 THEN 'מאי'
        WHEN 6 THEN 'יוני'
        WHEN 7 THEN 'יולי'
        WHEN 8 THEN 'אוגוסט'
        WHEN 9 THEN 'ספטמבר'
        WHEN 10 THEN 'אוקטובר'
        WHEN 11 THEN 'נובמבר'
        WHEN 12 THEN 'דצמבר'
    END AS חודש_עברי
FROM orders
WHERE YEAR(order_date) = 2025;
```

---

## 🚨 בעיות נפוצות ופתרונות

### ❌ בעיה 1: תווי שאלה (???) או גיבריש

**תסמינים:**
```sql
SELECT name FROM customers;
-- תוצאה: ??? ??? או Ã×××
```

**סיבה:** קידוד לא נכון!

✅ **פתרון:**

```sql
-- MySQL: שנה ל-UTF8MB4
ALTER TABLE customers 
CONVERT TO CHARACTER SET utf8mb4 
COLLATE utf8mb4_unicode_ci;

-- SQL Server: השתמש ב-NVARCHAR ו-N
ALTER TABLE customers 
ALTER COLUMN name NVARCHAR(100);

-- הוסף מחדש את הנתונים עם N
INSERT INTO customers (name) VALUES (N'אבי כהן');
```

---

### ❌ בעיה 2: חיפוש לא עובד

**תסמינים:**
```sql
SELECT * FROM customers WHERE name = 'אבי';
-- לא מוצא כלום!
```

**סיבה:** רווחים נסתרים או Collation לא נכון

✅ **פתרון:**

```sql
-- נקה רווחים
SELECT * FROM customers WHERE TRIM(name) = 'אבי';

-- חיפוש לא רגיש לרווחים
SELECT * FROM customers WHERE name LIKE '%אבי%';

-- בדוק את הנתונים
SELECT name, LENGTH(name), HEX(name) FROM customers;
```

---

### ❌ בעיה 3: מיון לא נכון

**תסמינים:**
```sql
SELECT name FROM customers ORDER BY name;
-- מיון לא אלפביתי!
```

✅ **פתרון:**

```sql
-- MySQL
SELECT name FROM customers 
ORDER BY name COLLATE utf8mb4_unicode_ci;

-- SQL Server
SELECT name FROM customers 
ORDER BY name COLLATE Hebrew_CI_AS;

-- PostgreSQL
SELECT name FROM customers 
ORDER BY name COLLATE "he_IL.utf8";
```

---

### ❌ בעיה 4: INSERT נכשל עם עברית

**תסמינים:**
```sql
INSERT INTO customers (name) VALUES ('אבי');
-- Error: Incorrect string value
```

✅ **פתרון:**

```sql
-- MySQL: ודא UTF8MB4
SET NAMES utf8mb4;
INSERT INTO customers (name) VALUES ('אבי');

-- SQL Server: השתמש ב-N
INSERT INTO customers (name) VALUES (N'אבי');

-- בדוק את סוג העמודה
SHOW FULL COLUMNS FROM customers;
```

---

### ❌ בעיה 5: Connection String לא נכון

**Python + MySQL:**
```python
# לא נכון ❌
connection = mysql.connector.connect(
    host="localhost",
    user="user",
    password="pass",
    database="my_db"
)

# נכון ✅
connection = mysql.connector.connect(
    host="localhost",
    user="user",
    password="pass",
    database="my_db",
    charset='utf8mb4',
    collation='utf8mb4_unicode_ci'
)
```

**Python + SQL Server:**
```python
# נכון ✅
connection_string = (
    "DRIVER={ODBC Driver 17 for SQL Server};"
    "SERVER=localhost;"
    "DATABASE=my_db;"
    "UID=user;"
    "PWD=pass;"
    "charset=UTF-8"
)
```

---

## 💡 טיפים מקצועיים

### 1️⃣ בדיקת תמיכה ב-Unicode

```sql
-- MySQL: בדוק קידוד
SHOW VARIABLES LIKE 'character_set%';
SHOW VARIABLES LIKE 'collation%';

-- SQL Server: רשימת Collations
SELECT * FROM sys.fn_helpcollations()
WHERE name LIKE '%Hebrew%';

-- PostgreSQL: רשימת Encodings
SELECT * FROM pg_available_encodings;
```

---

### 2️⃣ יצירת View עם שמות בעברית

```sql
CREATE VIEW דוח_לקוחות AS
SELECT 
    customer_id AS 'מספר לקוח',
    name AS 'שם',
    city AS 'עיר',
    phone AS 'טלפון',
    email AS 'אימייל'
FROM customers;

-- שימוש
SELECT * FROM דוח_לקוחות WHERE עיר = 'תל אביב';
```

---

### 3️⃣ פונקציה לניקוי טקסט עברי

```sql
-- MySQL
DELIMITER //
CREATE FUNCTION clean_hebrew_text(input_text VARCHAR(255))
RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
    RETURN TRIM(REPLACE(REPLACE(input_text, '  ', ' '), CHAR(13), ''));
END //
DELIMITER ;

-- שימוש
SELECT clean_hebrew_text(name) FROM customers;
```

---

### 4️⃣ Stored Procedure עם פרמטרים בעברית

```sql
-- MySQL
DELIMITER //
CREATE PROCEDURE חפש_לקוחות_לפי_עיר(
    IN עיר_חיפוש VARCHAR(50)
)
BEGIN
    SELECT 
        name AS שם,
        city AS עיר,
        phone AS טלפון
    FROM customers
    WHERE city = עיר_חיפוש
    ORDER BY name COLLATE utf8mb4_unicode_ci;
END //
DELIMITER ;

-- קריאה
CALL חפש_לקוחות_לפי_עיר('תל אביב');
```

---

### 5️⃣ Full-Text Search בעברית

```sql
-- MySQL
ALTER TABLE products 
ADD FULLTEXT INDEX ft_description (description);

-- חיפוש
SELECT product_name, description
FROM products
WHERE MATCH(description) AGAINST('מחשב נייד' IN NATURAL LANGUAGE MODE);
```

---

## 🎓 תרגילים להתנסות

### תרגיל 1: יצירת טבלה עם תמיכה בעברית
צרו טבלת מוצרים עם עמודות name, description בעברית.

<details>
<summary>💡 פתרון (MySQL)</summary>

```sql
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci,
    description TEXT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci,
    price DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

INSERT INTO products (name, description, price)
VALUES 
    ('מחשב נייד', 'מחשב נייד מתקדם עם מסך 15 אינץ', 3500.00),
    ('עכבר אלחוטי', 'עכבר ארגונומי עם חיבור Bluetooth', 89.90);
```
</details>

---

### תרגיל 2: חיפוש מתקדם
מצאו לקוחות ששמם מכיל "כהן" או "לוי" ומהעיר תל אביב.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    name AS שם_לקוח,
    city AS עיר,
    phone AS טלפון
FROM customers
WHERE (name LIKE '%כהן%' OR name LIKE '%לוי%')
  AND city = 'תל אביב'
ORDER BY name COLLATE utf8mb4_unicode_ci;
```
</details>

---

### תרגיל 3: תיקון קידוד
יש לכם טבלה עם נתונים משובשים. תקנו אותה.

<details>
<summary>💡 פתרון (MySQL)</summary>

```sql
-- גיבוי
CREATE TABLE customers_backup AS SELECT * FROM customers;

-- תיקון קידוד
ALTER TABLE customers 
CONVERT TO CHARACTER SET utf8mb4 
COLLATE utf8mb4_unicode_ci;

-- אם הנתונים משובשים, צריך להכניס מחדש
DELETE FROM customers;
INSERT INTO customers SELECT * FROM customers_backup;
```
</details>

---

## 📊 טבלת סיכום - Encoding לפי מערכת

| מערכת | קידוד מומלץ | סוג עמודה | דוגמה |
|-------|-------------|-----------|--------|
| **MySQL** | `utf8mb4` | `VARCHAR`, `TEXT` | `CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci` |
| **SQL Server** | Unicode | `NVARCHAR`, `NTEXT` | `NVARCHAR(100)` + `N'טקסט'` |
| **PostgreSQL** | UTF8 | `VARCHAR`, `TEXT` | `ENCODING 'UTF8' LC_COLLATE 'he_IL.UTF-8'` |
| **Oracle** | UTF8 | `NVARCHAR2`, `NCLOB` | `NVARCHAR2(100)` |
| **SQLite** | UTF-8 | `TEXT` | תמיכה אוטומטית |

---

## ❓ שאלות נפוצות

**Q: האם אפשר לעבוד עם כמה שפות בבת אחת?**
A: כן! UTF-8 תומך בכל השפות. אפשר לאחסן עברית, ערבית, אנגלית, סינית וכו' באותה טבלה.

**Q: מה עדיף - שמות עמודות באנגלית או עברית?**
A: באנגלית! זה התקן המקובל ויש פחות בעיות תאימות.

**Q: למה יש ??? במקום עברית?**
A: בעיית קידוד. ודאו שהטבלה ב-UTF8/UTF8MB4 ושה-Connection משתמש באותו קידוד.

**Q: מה ההבדל בין utf8 ל-utf8mb4?**
A: utf8mb4 תומך ב-4 bytes (כולל Emoji). תמיד תשתמשו ב-utf8mb4!

**Q: איך לבדוק אם יש בעיית קידוד?**
A: הכניסו נתונים בעברית ובדקו. אם רואים ??? או גיבריש - יש בעיה.

---

## 🎉 סיימתם!

עכשיו אתם יודעים:
✅ איך לעבוד עם שמות עמודות בעברית (ומתי כדאי)  
✅ איך לאחסן נתונים בעברית בצורה נכונה  
✅ קידוד תווים (UTF-8, UTF8MB4, Unicode)  
✅ חיפוש, סינון ומיון בעברית  
✅ פתרון בעיות נפוצות  
✅ עבודה עם כל המערכות (MySQL, SQL Server, PostgreSQL)  

**עברית ב-SQL זה לא מפחיד - רק צריך לדעת איך!** 💪🇮🇱

---

**חזרה למדריך הקודם:** [מדריך AS](15_AS_Keyword_Guide.md)
