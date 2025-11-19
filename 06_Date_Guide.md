# 📅 מדריך עבודה עם תאריכים ב-SQL

## 🎯 למה תאריכים זה חשוב?

תאריכים הם חלק בסיסי בכל מסד נתונים:
- מתי הלקוח נרשם?
- מתי בוצעה ההזמנה?
- כמה זמן עבר מאז?
- מה קרה בחודש שעבר?

בואו נלמד לעבוד עם תאריכים בצורה פשוטה! 🚀

---

## 📌 סוגי נתוני תאריך ושעה

| סוג | מה הוא מכיל | דוגמה |
|-----|-------------|--------|
| **DATE** | תאריך בלבד | `2025-11-19` |
| **TIME** | שעה בלבד | `14:30:00` |
| **DATETIME** | תאריך + שעה | `2025-11-19 14:30:00` |
| **TIMESTAMP** | תאריך + שעה + אזור זמן | `2025-11-19 14:30:00 +02:00` |

> 💡 **טיפ:** רוב המקרים נשתמש ב-DATE או DATETIME

---

## 🔄 המרה מ-STRING לתאריך

### 1️⃣ CAST - הדרך הפשוטה

```sql
SELECT CAST('2025-11-19' AS DATE);
```

**תוצאה:** `2025-11-19` (כתאריך, לא כטקסט)

---

### 2️⃣ CONVERT - עם פורמטים שונים

```sql
-- SQL Server
SELECT CONVERT(DATE, '19/11/2025', 103);  -- פורמט dd/mm/yyyy
```

**קודי פורמט נפוצים (SQL Server):**
- `103` = dd/mm/yyyy (19/11/2025)
- `101` = mm/dd/yyyy (11/19/2025)
- `120` = yyyy-mm-dd (2025-11-19)

---

### 3️⃣ STR_TO_DATE - MySQL

```sql
SELECT STR_TO_DATE('19-11-2025', '%d-%m-%Y');
```

**סמלי פורמט ב-MySQL:**
- `%d` = יום (01-31)
- `%m` = חודש (01-12)
- `%Y` = שנה בת 4 ספרות
- `%y` = שנה בת 2 ספרות
- `%H` = שעה (00-23)
- `%i` = דקות (00-59)
- `%s` = שניות (00-59)

---

### 4️⃣ TO_DATE - PostgreSQL ו-Oracle

```sql
SELECT TO_DATE('19/11/2025', 'DD/MM/YYYY');
```

---

## 🎨 דוגמאות מעשיות - צעד אחר צעד

### דוגמה 1: המרת עמודת טקסט לתאריך

נניח שיש לנו טבלה עם תאריכים כטקסט:

```sql
CREATE TABLE orders (
    order_id INT,
    order_date_text VARCHAR(20)
);

INSERT INTO orders VALUES 
    (1, '2025-01-15'),
    (2, '2025-02-20'),
    (3, '2025-03-10');
```

**המרה לתאריך:**

```sql
SELECT 
    order_id,
    order_date_text,
    CAST(order_date_text AS DATE) AS order_date
FROM orders;
```

---

### דוגמה 2: עבודה עם פורמטים שונים

```sql
-- MySQL
SELECT 
    STR_TO_DATE('15-01-2025', '%d-%m-%Y') AS date1,
    STR_TO_DATE('15/01/2025', '%d/%m/%Y') AS date2,
    STR_TO_DATE('2025-01-15 14:30:00', '%Y-%m-%d %H:%i:%s') AS datetime1;
```

**תוצאה:**
```
date1      | date2      | datetime1
-----------|------------|-------------------
2025-01-15 | 2025-01-15 | 2025-01-15 14:30:00
```

---

## 🛠️ פונקציות שימושיות לתאריכים

### 1. קבלת התאריך הנוכחי

```sql
-- פועל ברוב מערכות SQL
SELECT CURRENT_DATE;          -- תאריך בלבד
SELECT CURRENT_TIMESTAMP;     -- תאריך + שעה

-- SQL Server
SELECT GETDATE();             -- תאריך + שעה

-- MySQL
SELECT NOW();                 -- תאריך + שעה
SELECT CURDATE();             -- תאריך בלבד
```

---

### 2. חילוץ חלקים מתאריך

```sql
SELECT 
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    DAY(order_date) AS day,
    DAYOFWEEK(order_date) AS day_of_week
FROM orders;
```

**דוגמה:**
```sql
-- MySQL
SELECT 
    YEAR('2025-11-19') AS year,      -- 2025
    MONTH('2025-11-19') AS month,    -- 11
    DAY('2025-11-19') AS day;        -- 19
```

---

### 3. חישובים עם תאריכים

#### הוספת ימים/חודשים/שנים

```sql
-- MySQL
SELECT DATE_ADD('2025-11-19', INTERVAL 7 DAY);      -- +7 ימים
SELECT DATE_ADD('2025-11-19', INTERVAL 1 MONTH);    -- +1 חודש
SELECT DATE_ADD('2025-11-19', INTERVAL 1 YEAR);     -- +1 שנה

-- SQL Server
SELECT DATEADD(DAY, 7, '2025-11-19');               -- +7 ימים
SELECT DATEADD(MONTH, 1, '2025-11-19');             -- +1 חודש
```

#### חיסור תאריכים

```sql
-- MySQL
SELECT DATEDIFF('2025-11-19', '2025-11-01') AS days_diff;  -- 18 ימים

-- SQL Server
SELECT DATEDIFF(DAY, '2025-11-01', '2025-11-19');          -- 18 ימים
```

---

### 4. עיצוב תאריך להצגה

```sql
-- MySQL
SELECT DATE_FORMAT('2025-11-19', '%d/%m/%Y');              -- 19/11/2025
SELECT DATE_FORMAT('2025-11-19', '%W, %M %d, %Y');         -- Tuesday, November 19, 2025

-- SQL Server
SELECT FORMAT(CAST('2025-11-19' AS DATE), 'dd/MM/yyyy');   -- 19/11/2025
```

---

## 💼 תרחישים מעשיים

### תרחיש 1: מציאת הזמנות מהחודש האחרון

```sql
SELECT *
FROM orders
WHERE order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 1 MONTH);
```

או:

```sql
SELECT *
FROM orders
WHERE order_date >= CURRENT_DATE - INTERVAL '1 month';
```

---

### תרחיש 2: חישוב גיל לקוח

```sql
SELECT 
    name,
    birth_date,
    YEAR(CURRENT_DATE) - YEAR(birth_date) AS age
FROM customers;
```

**גרסה מדויקת יותר:**

```sql
SELECT 
    name,
    birth_date,
    TIMESTAMPDIFF(YEAR, birth_date, CURRENT_DATE) AS exact_age
FROM customers;
```

---

### תרחיש 3: קיבוץ לפי חודש/שנה

```sql
SELECT 
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    COUNT(*) AS order_count,
    SUM(amount) AS total_amount
FROM orders
GROUP BY YEAR(order_date), MONTH(order_date)
ORDER BY year, month;
```

**תוצאה:**
```
year | month | order_count | total_amount
-----|-------|-------------|-------------
2025 | 1     | 45          | 12500
2025 | 2     | 52          | 15800
2025 | 3     | 38          | 9200
```

---

### תרחיש 4: מציאת יום בשבוע

```sql
-- MySQL
SELECT 
    order_date,
    DAYNAME(order_date) AS day_name,
    CASE DAYOFWEEK(order_date)
        WHEN 1 THEN 'ראשון'
        WHEN 2 THEN 'שני'
        WHEN 3 THEN 'שלישי'
        WHEN 4 THEN 'רביעי'
        WHEN 5 THEN 'חמישי'
        WHEN 6 THEN 'שישי'
        WHEN 7 THEN 'שבת'
    END AS hebrew_day
FROM orders;
```

---

### תרחיש 5: טיפול ב-NULL ובתאריכים לא תקינים

```sql
SELECT 
    order_id,
    CASE 
        WHEN order_date_text IS NULL THEN NULL
        WHEN order_date_text = '' THEN NULL
        ELSE TRY_CAST(order_date_text AS DATE)
    END AS clean_date
FROM orders;
```

---

## 🚨 טעויות נפוצות וכיצד להימנע מהן

### ❌ טעות 1: השוואת מחרוזת לתאריך

```sql
-- לא עובד טוב!
SELECT * FROM orders WHERE order_date > '15/11/2025';
```

✅ **פתרון:**
```sql
SELECT * FROM orders WHERE order_date > CAST('2025-11-15' AS DATE);
```

---

### ❌ טעות 2: שכחת את השעה

```sql
-- אם order_date הוא DATETIME, זה לא יתפוס הזמנות מ-2025-11-19!
SELECT * FROM orders WHERE order_date = '2025-11-19';
```

✅ **פתרון:**
```sql
SELECT * FROM orders 
WHERE DATE(order_date) = '2025-11-19';

-- או
SELECT * FROM orders 
WHERE order_date >= '2025-11-19' 
  AND order_date < '2025-11-20';
```

---

### ❌ טעות 3: פורמט תאריך לא תואם

```sql
-- עלול לא לעבוד!
SELECT CAST('19/11/2025' AS DATE);
```

✅ **פתרון - השתמשו בפורמט ISO:**
```sql
SELECT CAST('2025-11-19' AS DATE);  -- תמיד עובד!
```

---

## 📊 טבלת סיכום - פונקציות לפי מערכת

| פעולה | MySQL | SQL Server | PostgreSQL |
|-------|-------|------------|------------|
| תאריך נוכחי | `CURDATE()` | `GETDATE()` | `CURRENT_DATE` |
| המרת טקסט | `STR_TO_DATE()` | `CONVERT()` | `TO_DATE()` |
| הוספת זמן | `DATE_ADD()` | `DATEADD()` | `date + INTERVAL` |
| הפרש תאריכים | `DATEDIFF()` | `DATEDIFF()` | `date1 - date2` |
| עיצוב | `DATE_FORMAT()` | `FORMAT()` | `TO_CHAR()` |

---

## 🎓 תרגילים להתנסות

### תרגיל 1: המרת פורמט תאריך
יש לכם טבלה עם תאריכים בפורמט `dd-mm-yyyy`. המירו אותם ל-DATE.

<details>
<summary>💡 פתרון (MySQL)</summary>

```sql
SELECT 
    order_id,
    STR_TO_DATE(date_string, '%d-%m-%Y') AS order_date
FROM orders;
```
</details>

---

### תרגיל 2: הזמנות מהשבוע האחרון
כתבו שאילתה שמציגה הזמנות מ-7 הימים האחרונים.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT *
FROM orders
WHERE order_date >= CURRENT_DATE - INTERVAL 7 DAY;
```
</details>

---

### תרגיל 3: דוח חודשי
הציגו סיכום של כמות הזמנות וסכום כולל לכל חודש.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    COUNT(*) AS order_count,
    SUM(amount) AS total
FROM orders
GROUP BY YEAR(order_date), MONTH(order_date)
ORDER BY year DESC, month DESC;
```
</details>

---

## 🎁 טיפים מקצועיים

### 1️⃣ תמיד אחסנו תאריכים כ-DATE/DATETIME
❌ אל תאחסנו תאריכים כטקסט!
✅ השתמשו בטיפוס DATE/DATETIME/TIMESTAMP

### 2️⃣ השתמשו בפורמט ISO (yyyy-mm-dd)
זה הפורמט שעובד בכל מערכות ה-SQL!

### 3️⃣ שימו לב לאזור זמן
אם אתם עובדים עם משתמשים ממדינות שונות, השתמשו ב-TIMESTAMP.

### 4️⃣ אינדקסים על עמודות תאריך
```sql
CREATE INDEX idx_order_date ON orders(order_date);
```
זה יזרז שאילתות שמסננות לפי תאריך!

---

## ❓ שאלות נפוצות

**Q: מה ההבדל בין DATETIME ל-TIMESTAMP?**
A: TIMESTAMP שומר את אזור הזמן ומתאים יותר ליישומים גלובליים.

**Q: איך לטפל בשעות ודקות?**
A: השתמשו ב-DATETIME ובפונקציות כמו `HOUR()`, `MINUTE()`, `SECOND()`.

**Q: מה קורה כשהמרה נכשלת?**
A: השתמשו ב-`TRY_CAST` (SQL Server) או בדוק עם `IS_DATE()` לפני ההמרה.

**Q: איך לעבוד עם תאריכים עבריים?**
A: רוב מערכות SQL לא תומכות בלוח עברי ישירות. תצטרכו ספרייה חיצונית או המרה ידנית.

---

## 🎉 סיימתם!

עכשיו אתם יודעים:
✅ להמיר טקסט לתאריך  
✅ לעבוד עם פונקציות תאריך  
✅ לבצע חישובים עם תאריכים  
✅ לעצב תאריכים להצגה  

**המשיכו לתרגל - תאריכים הם כלי עוצמתי ב-SQL!** 💪

---

**חזרה למדריך הקודם:** [מדריך JOIN](05_JOIN_Guide.md)
