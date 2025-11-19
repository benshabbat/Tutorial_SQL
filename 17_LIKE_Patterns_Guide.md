# 🔍 מדריך מקיף על LIKE ב-SQL

## 📌 מה זה LIKE ולמה אנחנו צריכים אותו?

**LIKE** הוא אופרטור ב-SQL שמאפשר לנו לחפש דפוסים (patterns) בתוך מחרוזות טקסט.

**דוגמה פשוטה:**
במקום לחפש בדיוק `'אבי כהן'`, אנחנו יכולים לחפש **כל מי שיש לו "כהן" בשם** 🎯

```sql
-- חיפוש מדויק (לא גמיש)
SELECT * FROM customers WHERE name = 'אבי כהן';  
-- מוצא רק "אבי כהן" בדיוק ❌

-- חיפוש עם LIKE (גמיש!)
SELECT * FROM customers WHERE name LIKE '%כהן%';
-- מוצא: "אבי כהן", "דני כהן", "כהן משה", וכו' ✅
```

---

## 🎯 מתי נשתמש ב-LIKE?

✅ **כדאי להשתמש כש:**
- רוצים לחפש חלק ממחרוזת
- לא יודעים את הערך המדויק
- רוצים לסנן לפי תבנית מסוימת
- חיפוש חלקי בשדה טקסט גדול

❌ **לא כדאי להשתמש כש:**
- יודעים את הערך המדויק (השתמשו ב-`=`)
- צריכים ביצועים מהירים על טבלה ענקית
- יש פתרון טוב יותר (Full-Text Search)

---

## 🔤 תווים מיוחדים (Wildcards) ב-LIKE

LIKE משתמש בתווים מיוחדים כדי להגדיר דפוסים:

### 1️⃣ סימן האחוז `%` - כל דבר (0+ תווים)

**פירוש:** תו או יותר, או אפילו שום דבר!

```sql
-- מתחיל ב...
SELECT * FROM customers WHERE name LIKE 'אבי%';
-- מוצא: "אבי", "אבי כהן", "אביגיל", "אבינועם"

-- מסתיים ב...
SELECT * FROM customers WHERE name LIKE '%כהן';
-- מוצא: "דני כהן", "משה כהן", "כהן"

-- מכיל בכל מקום...
SELECT * FROM customers WHERE name LIKE '%לוי%';
-- מוצא: "לוי", "דני לוי", "לויטן", "בנימין לוי משה"
```

---

### 2️⃣ קו תחתון `_` - תו בודד (בדיוק 1 תו)

**פירוש:** בדיוק תו אחד, לא יותר ולא פחות!

```sql
-- שם עם 3 תווים שמתחיל באבי
SELECT * FROM customers WHERE name LIKE 'אב_';
-- מוצא: "אבי", "אבא"
-- לא מוצא: "אביגיל" (יותר מ-3 תווים)

-- מספר טלפון בפורמט ספציפי
SELECT * FROM customers WHERE phone LIKE '05_-1234567';
-- מוצא: "050-1234567", "052-1234567", "054-1234567"

-- קוד מוצר בפורמט ABC123
SELECT * FROM products WHERE code LIKE 'AB___3';
-- מוצא: "AB123", "AB453", "ABXXX3"
-- לא מוצא: "AB13" (חסר תו)
```

---

### 3️⃣ שילוב של `%` ו-`_`

אפשר לשלב את שני הסוגים! 💪

```sql
-- מתחיל באב ואחר כך כל דבר
SELECT * FROM customers WHERE name LIKE 'אב_%';
-- מוצא: "אבי", "אבא", "אביגיל"
-- לא מוצא: "אב" (צריך לפחות 3 תווים)

-- אימייל שמסתיים ב-gmail.com
SELECT * FROM customers WHERE email LIKE '%@gmail.com';
-- מוצא: "user@gmail.com", "test123@gmail.com"

-- מספר כרטיס אשראי (4 קבוצות של 4 ספרות)
SELECT * FROM payments WHERE card LIKE '____-____-____-____';
-- מוצא: "1234-5678-9012-3456"
```

---

## 📊 דוגמאות מעשיות לכל תרחיש

### תרחיש 1: חיפוש לקוחות לפי שם 👤

```sql
-- כל מי ששמו מתחיל באבי
SELECT customer_id, name, phone
FROM customers
WHERE name LIKE 'אבי%';

-- תוצאה:
-- 1, אבי כהן, 050-1234567
-- 2, אביגיל לוי, 052-9876543
-- 3, אבינועם משה, 054-1111111
```

---

### תרחיש 2: חיפוש מוצרים 📦

```sql
-- כל המוצרים שיש להם "מחשב" בשם
SELECT product_id, product_name, price
FROM products
WHERE product_name LIKE '%מחשב%';

-- תוצאה:
-- 1, מחשב נייד Dell, 3500.00
-- 2, מחשב שולחני HP, 2800.00
-- 3, תיק למחשב נייד, 150.00
```

---

### תרחיש 3: סינון אימיילים 📧

```sql
-- כל האימיילים מ-Gmail
SELECT name, email
FROM customers
WHERE email LIKE '%@gmail.com';

-- אימיילים שמתחילים במספר
SELECT name, email
FROM customers
WHERE email LIKE '[0-9]%@%';  -- SQL Server
-- או
WHERE email REGEXP '^[0-9]';  -- MySQL/PostgreSQL
```

---

### תרחיש 4: חיפוש לפי תאריך 📅

```sql
-- כל ההזמנות מחודש מרץ 2025
SELECT order_id, order_date, amount
FROM orders
WHERE DATE_FORMAT(order_date, '%Y-%m') LIKE '2025-03%';
-- או פשוט:
WHERE order_date LIKE '2025-03%';
```

---

### תרחיש 5: קודים וסדרתיים 🔢

```sql
-- מוצרים שהקוד שלהם מתחיל ב-AB
SELECT product_code, product_name
FROM products
WHERE product_code LIKE 'AB%';

-- מספרי הזמנה בפורמט ORD-2025-XXXX
SELECT order_id, order_number
FROM orders
WHERE order_number LIKE 'ORD-2025-%';

-- מספרי חשבונית (8 ספרות שמתחילות ב-20)
SELECT invoice_number
FROM invoices
WHERE invoice_number LIKE '20______';  -- 2 + 6 underscores
```

---

## 🎭 LIKE vs NOT LIKE

### NOT LIKE - הכל חוץ מ...

```sql
-- כל הלקוחות שהשם שלהם לא מכיל "כהן"
SELECT name, city
FROM customers
WHERE name NOT LIKE '%כהן%';

-- כל האימיילים שלא מ-Gmail
SELECT name, email
FROM customers
WHERE email NOT LIKE '%@gmail.com';

-- מוצרים שלא מתחילים ב-AB
SELECT product_name
FROM products
WHERE product_code NOT LIKE 'AB%';
```

---

## 🔥 טכניקות מתקדמות

### 1️⃣ LIKE עם OR - כמה דפוסים

```sql
-- מוצאים שמות שמתחילים באבי או בדני
SELECT name
FROM customers
WHERE name LIKE 'אבי%' 
   OR name LIKE 'דני%';

-- גרסה יותר נקייה עם IN (לא עובד!)
-- WHERE name LIKE IN ('אבי%', 'דני%')  ❌

-- אבל אפשר ככה:
WHERE name LIKE 'אבי%' 
   OR name LIKE 'דני%'
   OR name LIKE 'משה%';
```

---

### 2️⃣ LIKE עם AND - תנאים משולבים

```sql
-- שם שמכיל "כהן" אבל לא מתחיל בו
SELECT name
FROM customers
WHERE name LIKE '%כהן%' 
  AND name NOT LIKE 'כהן%';

-- תוצאה:
-- ✅ "אבי כהן"
-- ✅ "דני כהן לוי"
-- ❌ "כהן משה" (מתחיל בכהן)
```

---

### 3️⃣ LIKE עם CASE - חיפוש תלוי-תנאי

```sql
SELECT 
    name,
    CASE 
        WHEN name LIKE 'אבי%' THEN 'מתחיל באבי'
        WHEN name LIKE '%כהן%' THEN 'מכיל כהן'
        WHEN name LIKE '%לוי' THEN 'מסתיים בלוי'
        ELSE 'אחר'
    END AS סוג_שם
FROM customers;
```

---

### 4️⃣ LIKE עם פונקציות מחרוזת

```sql
-- חיפוש case-insensitive (לא תלוי באותיות גדולות/קטנות)
SELECT name
FROM customers
WHERE LOWER(name) LIKE LOWER('%COHEN%');

-- הסרת רווחים לפני חיפוש
SELECT name
FROM customers
WHERE TRIM(name) LIKE 'אבי%';

-- חיפוש בשני שדות
SELECT name, description
FROM products
WHERE CONCAT(name, ' ', description) LIKE '%מחשב%';
```

---

### 5️⃣ Escape Characters - תווים מיוחדים בחיפוש

מה קורה אם אנחנו רוצים לחפש **ממש** `%` או `_`?

```sql
-- בעיה: רוצים לחפש מוצרים עם 50% הנחה
SELECT product_name, discount
FROM products
WHERE discount LIKE '50%';  -- ❌ זה מוצא 50, 500, 5000...

-- פתרון: Escape
-- MySQL/PostgreSQL:
SELECT product_name, discount
FROM products
WHERE discount LIKE '50\%' ESCAPE '\';

-- SQL Server:
SELECT product_name, discount
FROM products
WHERE discount LIKE '50[%]';
-- או
WHERE discount LIKE '50!%' ESCAPE '!';
```

**דוגמאות נוספות:**

```sql
-- חיפוש קובץ עם _ בשם
SELECT file_name
FROM files
WHERE file_name LIKE '%test\_file%' ESCAPE '\';
-- מוצא: "my_test_file.txt"
-- לא מוצא: "mytestXfile.txt"

-- חיפוש נוסחה עם %
SELECT formula
FROM calculations
WHERE formula LIKE '%\% increase%' ESCAPE '\';
-- מוצא: "15% increase"
```

---

## 🌍 LIKE עם שפות שונות

### עברית 🇮🇱

```sql
-- MySQL
SELECT name
FROM customers
WHERE name LIKE '%כהן%' COLLATE utf8mb4_unicode_ci;

-- SQL Server
SELECT name
FROM customers
WHERE name LIKE N'%כהן%' COLLATE Hebrew_CI_AS;
-- N חשוב לעברית!

-- PostgreSQL
SELECT name
FROM customers
WHERE name LIKE '%כהן%';
```

---

### Case Sensitivity - רגישות לאותיות גדולות/קטנות

```sql
-- MySQL (default case-insensitive)
SELECT name FROM users WHERE name LIKE 'john%';
-- מוצא: "John", "john", "JOHN"

-- PostgreSQL (default case-sensitive)
SELECT name FROM users WHERE name LIKE 'john%';
-- מוצא רק: "john"

-- פתרון - ILIKE (case-insensitive)
SELECT name FROM users WHERE name ILIKE 'john%';
-- מוצא: "John", "john", "JOHN"

-- SQL Server
SELECT name FROM users 
WHERE name LIKE 'john%' COLLATE SQL_Latin1_General_CP1_CI_AS;
-- CI = Case Insensitive
```

---

## ⚡ LIKE vs חלופות אחרות

### LIKE vs `=` (שווה)

```sql
-- = (מדויק)
SELECT * FROM customers WHERE name = 'אבי כהן';
-- ✅ מהיר מאוד
-- ❌ חייב התאמה מדויקת

-- LIKE
SELECT * FROM customers WHERE name LIKE '%אבי%';
-- ✅ גמיש
-- ❌ איטי יותר
```

**מתי להשתמש בכל אחד?**
- **`=`** - כשיודעים את הערך המדויק ✅
- **`LIKE`** - כשצריכים חיפוש חלקי 🔍

---

### LIKE vs IN

```sql
-- IN (רשימה קבועה)
SELECT * FROM customers WHERE city IN ('תל אביב', 'חיפה', 'ירושלים');
-- ✅ מהיר
-- ❌ רק ערכים מדויקים

-- LIKE עם OR
SELECT * FROM customers WHERE city LIKE 'תל%' OR city LIKE 'חי%';
-- ✅ דפוסים
-- ❌ איטי יותר
```

---

### LIKE vs REGEXP (Regular Expressions)

```sql
-- LIKE (פשוט)
SELECT * FROM customers WHERE email LIKE '%@gmail.com';

-- REGEXP (מתקדם)
-- MySQL:
SELECT * FROM customers WHERE email REGEXP '^[a-zA-Z0-9]+@gmail\\.com$';

-- PostgreSQL:
SELECT * FROM customers WHERE email ~ '^[a-zA-Z0-9]+@gmail\.com$';
```

**מתי להשתמש?**
- **LIKE** - דפוסים פשוטים, קל ללמוד, מהיר יחסית ✅
- **REGEXP** - דפוסים מורכבים, חזק יותר, יותר קשה ללמוד 🔥

---

### LIKE vs Full-Text Search

```sql
-- LIKE
SELECT * FROM products WHERE description LIKE '%מחשב נייד%';
-- ❌ איטי על טבלאות גדולות
-- ❌ לא חכם (לא מוצא "מחשבים ניידים")

-- Full-Text Search (MySQL)
SELECT * FROM products 
WHERE MATCH(description) AGAINST('מחשב נייד' IN NATURAL LANGUAGE MODE);
-- ✅ מהיר מאוד
-- ✅ חכם (מוצא גם צורות שונות)
-- ✅ דירוג רלוונטיות
```

**מתי להשתמש?**
- **LIKE** - חיפושים פשוטים, טבלאות קטנות-בינוניות
- **Full-Text** - חיפושים מורכבים, טבלאות גדולות, מנועי חיפוש

---

## 🎯 דוגמאות מציאות

### דוגמה 1: מערכת CRM - חיפוש לקוחות

```sql
-- חיפוש לקוח לפי חלק מהשם או טלפון או אימייל
SELECT 
    customer_id,
    name,
    phone,
    email,
    city
FROM customers
WHERE name LIKE '%חיפוש%'
   OR phone LIKE '%חיפוש%'
   OR email LIKE '%חיפוש%'
ORDER BY name;
```

**שימוש בפרמטר:**
```sql
SET @search_term = '%כהן%';

SELECT customer_id, name, phone
FROM customers
WHERE name LIKE @search_term
   OR phone LIKE @search_term
   OR email LIKE @search_term;
```

---

### דוגמה 2: חנות אונליין - חיפוש מוצרים

```sql
-- חיפוש מוצרים לפי שם, תיאור, או קטגוריה
SELECT 
    product_id,
    product_name,
    category,
    price,
    description
FROM products
WHERE product_name LIKE '%מחשב%'
   OR description LIKE '%מחשב%'
   OR category LIKE '%אלקטרוניקה%'
ORDER BY 
    CASE 
        WHEN product_name LIKE '%מחשב%' THEN 1  -- עדיפות גבוהה
        WHEN description LIKE '%מחשב%' THEN 2
        ELSE 3
    END,
    price DESC;
```

---

### דוגמה 3: מערכת תקלות - פילטר לוגים

```sql
-- מציאת כל השגיאות (ERROR) בלוגים
SELECT 
    log_id,
    log_date,
    log_level,
    message
FROM system_logs
WHERE message LIKE '%ERROR%'
   OR message LIKE '%CRITICAL%'
   OR message LIKE '%FATAL%'
ORDER BY log_date DESC
LIMIT 100;
```

---

### דוגמה 4: מערכת HR - חיפוש עובדים

```sql
-- חיפוש עובדים לפי מחלקה, תפקיד, או כישורים
SELECT 
    employee_id,
    full_name,
    department,
    position,
    skills
FROM employees
WHERE department LIKE '%פיתוח%'
   OR position LIKE '%מנהל%'
   OR skills LIKE '%Python%'
   OR skills LIKE '%SQL%';
```

---

### דוגמה 5: בלוג - חיפוש מאמרים

```sql
-- חיפוש מאמרים לפי כותרת, תוכן, או תגיות
SELECT 
    article_id,
    title,
    author,
    tags,
    published_date
FROM blog_articles
WHERE (title LIKE '%SQL%' OR content LIKE '%SQL%' OR tags LIKE '%SQL%')
  AND status = 'published'
ORDER BY published_date DESC;
```

---

## 🚀 אופטימיזציה וביצועים

### ⚠️ בעיות ביצועים עם LIKE

```sql
-- ❌ איטי מאוד! (leading wildcard)
SELECT * FROM customers WHERE name LIKE '%כהן%';
-- לא יכול להשתמש באינדקס!

-- ❌ גם זה איטי
SELECT * FROM customers WHERE name LIKE '%כהן';

-- ✅ מהיר! (trailing wildcard)
SELECT * FROM customers WHERE name LIKE 'כהן%';
-- יכול להשתמש באינדקס!
```

**למה?**
- `%` בהתחלה = סריקה מלאה של הטבלה 🐌
- `%` רק בסוף = שימוש באינדקס ⚡

---

### ✅ טיפים לשיפור ביצועים

#### 1. צמצמו עם WHERE נוסף

```sql
-- ❌ איטי
SELECT * FROM orders WHERE customer_name LIKE '%כהן%';

-- ✅ מהיר יותר
SELECT * FROM orders 
WHERE city = 'תל אביב'  -- מסנן קודם
  AND customer_name LIKE '%כהן%';
```

---

#### 2. Full-Text Index למילים

```sql
-- יצירת Full-Text Index (MySQL)
ALTER TABLE products ADD FULLTEXT(product_name, description);

-- שימוש
SELECT * FROM products
WHERE MATCH(product_name, description) AGAINST('מחשב נייד');
-- הרבה יותר מהיר מ-LIKE!
```

---

#### 3. חלקו חיפושים מורכבים

```sql
-- ❌ איטי
SELECT * FROM customers 
WHERE CONCAT(first_name, ' ', last_name) LIKE '%אבי כהן%';

-- ✅ מהיר יותר
SELECT * FROM customers 
WHERE first_name LIKE '%אבי%' 
  AND last_name LIKE '%כהן%';
```

---

#### 4. שמירת תוצאות חיפוש נפוצות

```sql
-- Materialized View (PostgreSQL)
CREATE MATERIALIZED VIEW popular_searches AS
SELECT * FROM products
WHERE product_name LIKE '%מחשב%'
   OR product_name LIKE '%טלפון%';

-- רענון תקופתי
REFRESH MATERIALIZED VIEW popular_searches;
```

---

#### 5. אינדקס Trigram (PostgreSQL)

```sql
-- מאפשר LIKE מהיר גם עם % בהתחלה!
CREATE EXTENSION pg_trgm;

CREATE INDEX idx_name_trgm ON customers USING gin (name gin_trgm_ops);

-- עכשיו זה מהיר:
SELECT * FROM customers WHERE name LIKE '%כהן%';
```

---

## 🎓 תרגילים

### תרגיל 1: חיפוש לקוחות
יש לכם טבלת `customers` עם שדות: `name`, `email`, `phone`, `city`.
מצאו את כל הלקוחות:
1. ששמם מתחיל ב"דני"
2. שהאימייל שלהם ב-Gmail
3. שהטלפון שלהם מתחיל ב-054

<details>
<summary>💡 פתרון</summary>

```sql
-- 1. שם מתחיל בדני
SELECT * FROM customers WHERE name LIKE 'דני%';

-- 2. אימייל Gmail
SELECT * FROM customers WHERE email LIKE '%@gmail.com';

-- 3. טלפון 054
SELECT * FROM customers WHERE phone LIKE '054%';

-- הכל ביחד:
SELECT name, email, phone, city
FROM customers
WHERE name LIKE 'דני%'
   OR email LIKE '%@gmail.com'
   OR phone LIKE '054%';
```
</details>

---

### תרגיל 2: סינון מוצרים
טבלת `products` עם: `product_name`, `description`, `price`, `category`.
מצאו:
1. מוצרים שיש להם "נייד" בשם או בתיאור
2. מוצרים מקטגוריה שמכילה "אלקטרוניקה"
3. מוצרים שהשם שלהם בדיוק 10 תווים

<details>
<summary>💡 פתרון</summary>

```sql
-- 1. נייד בשם או תיאור
SELECT * FROM products 
WHERE product_name LIKE '%נייד%' 
   OR description LIKE '%נייד%';

-- 2. קטגוריה עם אלקטרוניקה
SELECT * FROM products 
WHERE category LIKE '%אלקטרוניקה%';

-- 3. שם בדיוק 10 תווים
SELECT * FROM products 
WHERE product_name LIKE '__________';  -- 10 underscores

-- או עם LENGTH:
SELECT * FROM products 
WHERE LENGTH(product_name) = 10;
```
</details>

---

### תרגיל 3: פילטור הזמנות
טבלת `orders` עם: `order_number`, `customer_name`, `order_date`, `status`.
מצאו:
1. הזמנות שמספרן מתחיל ב-"ORD-2025"
2. לקוחות ששמם מכיל "כהן" אבל לא מתחיל בו
3. הזמנות מחודש מרץ (כל שנה)

<details>
<summary>💡 פתרון</summary>

```sql
-- 1. מספר הזמנה ORD-2025
SELECT * FROM orders 
WHERE order_number LIKE 'ORD-2025%';

-- 2. כהן בשם אבל לא בהתחלה
SELECT * FROM orders 
WHERE customer_name LIKE '%כהן%' 
  AND customer_name NOT LIKE 'כהן%';

-- 3. כל המרץ (כל השנים)
SELECT * FROM orders 
WHERE DATE_FORMAT(order_date, '%m') = '03';
-- או
WHERE order_date LIKE '%-03-%';
-- או
WHERE MONTH(order_date) = 3;
```
</details>

---

### תרגיל 4: אימייל ולידציה
טבלת `users` עם: `username`, `email`.
מצאו:
1. משתמשים עם אימייל לא תקין (לא מכיל @)
2. אימיילים מדומיינים ספציפיים (gmail, yahoo, hotmail)
3. שמות משתמש שמתחילים באות ומסתיימים במספר

<details>
<summary>💡 פתרון</summary>

```sql
-- 1. אימייל לא תקין
SELECT * FROM users 
WHERE email NOT LIKE '%@%';

-- 2. דומיינים ספציפיים
SELECT * FROM users 
WHERE email LIKE '%@gmail.com' 
   OR email LIKE '%@yahoo.com' 
   OR email LIKE '%@hotmail.com';

-- 3. שם משתמש מתחיל באות ומסתיים במספר (זקוק ל-REGEXP)
-- MySQL:
SELECT * FROM users 
WHERE username REGEXP '^[a-zA-Z].*[0-9]$';

-- PostgreSQL:
SELECT * FROM users 
WHERE username ~ '^[a-zA-Z].*[0-9]$';
```
</details>

---

## ❌ טעויות נפוצות

### 1️⃣ שכחת את ה-% 

```sql
-- ❌ לא נכון
SELECT * FROM customers WHERE name LIKE 'כהן';
-- זה בדיוק כמו =, רק מוצא "כהן" ממש!

-- ✅ נכון
SELECT * FROM customers WHERE name LIKE '%כהן%';
```

---

### 2️⃣ בלבול בין _ ל-%

```sql
-- ❌ טעות
SELECT * FROM products WHERE code LIKE 'AB%23';
-- חושב שזה AB + כל דבר + 23, אבל % לא יעצור ב-2!

-- ✅ נכון
SELECT * FROM products WHERE code LIKE 'AB__23';
-- AB + 2 תווים + 23
```

---

### 3️⃣ Case Sensitivity לא מטופל

```sql
-- ❌ בעיה (PostgreSQL)
SELECT * FROM users WHERE name LIKE 'john%';
-- לא מוצא "John", "JOHN"

-- ✅ פתרון
SELECT * FROM users WHERE name ILIKE 'john%';
-- או
SELECT * FROM users WHERE LOWER(name) LIKE LOWER('john%');
```

---

### 4️⃣ שכחת N בעברית (SQL Server)

```sql
-- ❌ לא עובד
SELECT * FROM customers WHERE name LIKE '%כהן%';

-- ✅ עובד
SELECT * FROM customers WHERE name LIKE N'%כהן%';
```

---

### 5️⃣ שימוש ב-LIKE כש-IN מתאים יותר

```sql
-- ❌ לא יעיל
SELECT * FROM customers 
WHERE city LIKE 'תל אביב' 
   OR city LIKE 'חיפה' 
   OR city LIKE 'ירושלים';

-- ✅ יותר טוב
SELECT * FROM customers 
WHERE city IN ('תל אביב', 'חיפה', 'ירושלים');
```

---

## 💡 טיפים מקצועיים

### ✅ טיפ 1: חיפוש דינמי

```sql
-- Stored Procedure עם חיפוש גמיש
DELIMITER //
CREATE PROCEDURE search_customers(IN search_term VARCHAR(100))
BEGIN
    SELECT customer_id, name, email, phone
    FROM customers
    WHERE name LIKE CONCAT('%', search_term, '%')
       OR email LIKE CONCAT('%', search_term, '%')
       OR phone LIKE CONCAT('%', search_term, '%');
END //
DELIMITER ;

-- שימוש
CALL search_customers('כהן');
```

---

### ✅ טיפ 2: LIKE עם Prepared Statements (אבטחה!)

```sql
-- Python example (מניעת SQL Injection)
import mysql.connector

search = input("חפש לקוח: ")

# ❌ מסוכן!
query = f"SELECT * FROM customers WHERE name LIKE '%{search}%'"

# ✅ בטוח!
query = "SELECT * FROM customers WHERE name LIKE %s"
cursor.execute(query, (f'%{search}%',))
```

---

### ✅ טיפ 3: יצירת פונקציה לחיפוש חכם

```sql
-- MySQL Function
DELIMITER //
CREATE FUNCTION smart_search(field_value VARCHAR(255), search_term VARCHAR(100))
RETURNS BOOLEAN
DETERMINISTIC
BEGIN
    RETURN LOWER(field_value) LIKE LOWER(CONCAT('%', search_term, '%'));
END //
DELIMITER ;

-- שימוש
SELECT * FROM customers 
WHERE smart_search(name, 'כהן') 
   OR smart_search(email, 'כהן');
```

---

### ✅ טיפ 4: Logging חיפושים

```sql
-- שמירת חיפושים פופולריים
CREATE TABLE search_log (
    search_id INT AUTO_INCREMENT PRIMARY KEY,
    search_term VARCHAR(255),
    search_count INT DEFAULT 1,
    last_search TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Update או Insert
INSERT INTO search_log (search_term, search_count)
VALUES ('מחשב נייד', 1)
ON DUPLICATE KEY UPDATE 
    search_count = search_count + 1,
    last_search = CURRENT_TIMESTAMP;
```

---

### ✅ טיפ 5: Auto-complete עם LIKE

```sql
-- חיפוש לאוטו-קומפליט (מהיר!)
SELECT DISTINCT name
FROM customers
WHERE name LIKE 'אב%'  -- מתחיל ב...
LIMIT 10;

-- עם דירוג פופולריות
SELECT c.name, COUNT(o.order_id) as order_count
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE c.name LIKE 'אב%'
GROUP BY c.customer_id, c.name
ORDER BY order_count DESC
LIMIT 10;
```

---

## 📋 טבלת סיכום מהיר

| דפוס | פירוש | דוגמה | מוצא |
|------|--------|--------|------|
| `%` | 0+ תווים | `'אבי%'` | אבי, אביגיל, אבינועם |
| `_` | תו בודד | `'אב_'` | אבי, אבא |
| `%text%` | מכיל | `'%כהן%'` | אבי כהן, כהן דני |
| `text%` | מתחיל | `'תל%'` | תל אביב, תלמה |
| `%text` | מסתיים | `'%ביב'` | תל אביב, חביב |
| `__%` | 2+ תווים | `'__%'` | כל שם מעל 2 תווים |
| `NOT LIKE` | לא מכיל | `NOT LIKE '%test%'` | כל דבר בלי test |

---

## 🎯 מתי להשתמש במה?

| צורך | פתרון | דוגמה |
|------|--------|--------|
| התאמה מדויקת | `=` | `WHERE name = 'אבי'` |
| מתחיל ב... | `LIKE 'text%'` | `WHERE name LIKE 'אבי%'` |
| מסתיים ב... | `LIKE '%text'` | `WHERE email LIKE '%@gmail.com'` |
| מכיל | `LIKE '%text%'` | `WHERE description LIKE '%מחשב%'` |
| רשימת ערכים | `IN` | `WHERE city IN ('תל אביב', 'חיפה')` |
| דפוס מורכב | `REGEXP` | `WHERE email REGEXP '^[a-z]+@'` |
| חיפוש טקסט מלא | Full-Text | `MATCH(text) AGAINST('term')` |

---

## ❓ שאלות נפוצות

**Q: מה ההבדל בין LIKE ל-ILIKE?**
A: `LIKE` case-sensitive (ברירת מחדל בPostgreSQL). `ILIKE` case-insensitive.

**Q: האם LIKE איטי?**
A: תלוי איך משתמשים. `text%` מהיר, `%text%` איטי.

**Q: איך לחפש תו % עצמו?**
A: `LIKE '50\%' ESCAPE '\'` (MySQL) או `LIKE '50[%]'` (SQL Server)

**Q: האם אפשר LIKE עם מספרים?**
A: כן, אבל המספר יהפוך למחרוזת. לדוגמה: `WHERE price LIKE '99%'`

**Q: מה עדיף - LIKE או REGEXP?**
A: LIKE פשוט ומהיר לדפוסים בסיסיים. REGEXP חזק יותר אבל מורכב.

---

## 🎉 סיכום

עכשיו אתם יודעים:
✅ מה זה LIKE ומתי להשתמש בו  
✅ תווים מיוחדים: `%` (הכל) ו-`_` (תו בודד)  
✅ דוגמאות מעשיות לכל תרחיש  
✅ טכניקות מתקדמות (ESCAPE, COLLATE, וכו')  
✅ LIKE vs חלופות (=, IN, REGEXP, Full-Text)  
✅ אופטימיזציה וביצועים  
✅ טעויות נפוצות ופתרונות  

**LIKE הוא כלי חזק לחיפוש גמיש - אבל תשתמשו בחוכמה!** 🔍✨

---

**חזרה למדריך הקודם:** [מדריך נתונים בעברית](16_Hebrew_Data_Guide.md)
