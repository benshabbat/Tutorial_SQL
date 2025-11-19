# ⚡ מדריך תנאים ב-SQL - צעד אחר צעד

## 📌 מה זה תנאים?

תנאים ב-SQL מאפשרים לנו **לסנן, להשוות ולקבל החלטות** על הנתונים.

**במקום לקבל את כל הנתונים**, נקבל רק מה שאנחנו צריכים! 🎯

**דוגמה:**
- יש לכם 10,000 לקוחות
- רוצים רק לקוחות מתל אביב מעל גיל 25
- **פתרון:** תנאים! ✅

---

## 🎯 WHERE - התנאי הבסיסי

### תחביר

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

### דוגמה פשוטה

```sql
SELECT name, age
FROM customers
WHERE age > 25;
```

**תוצאה:** רק לקוחות מעל גיל 25! 🎂

---

## 🔧 אופרטורים להשוואה

| אופרטור | משמעות | דוגמה |
|---------|--------|--------|
| `=` | שווה ל | `WHERE age = 25` |
| `!=` או `<>` | לא שווה ל | `WHERE status != 'active'` |
| `>` | גדול מ | `WHERE price > 100` |
| `<` | קטן מ | `WHERE stock < 10` |
| `>=` | גדול או שווה | `WHERE age >= 18` |
| `<=` | קטן או שווה | `WHERE discount <= 50` |

### דוגמאות

```sql
-- לקוחות בדיוק בגיל 30
SELECT * FROM customers WHERE age = 30;

-- מוצרים מעל 500 ₪
SELECT * FROM products WHERE price > 500;

-- הזמנות שלא הושלמו
SELECT * FROM orders WHERE status <> 'completed';

-- מלאי נמוך (פחות מ-10 יחידות)
SELECT * FROM products WHERE stock <= 10;
```

---

## 🔗 AND, OR, NOT - שילוב תנאים

### AND - כל התנאים חייבים להתקיים

```sql
SELECT name, age, city
FROM customers
WHERE age > 25 AND city = 'תל אביב';
```

**תוצאה:** לקוחות שהם **גם** מעל 25 **וגם** מתל אביב! ✅✅

---

### OR - לפחות תנאי אחד חייב להתקיים

```sql
SELECT name, city
FROM customers
WHERE city = 'תל אביב' OR city = 'חיפה';
```

**תוצאה:** לקוחות מתל אביב **או** מחיפה! ✅

---

### NOT - היפוך התנאי

```sql
SELECT name, city
FROM customers
WHERE NOT city = 'ירושלים';
```

**תוצאה:** כל הלקוחות **חוץ** מירושלים! ❌

**זהה ל:**
```sql
WHERE city != 'ירושלים';
```

---

### שילוב של AND ו-OR

```sql
SELECT name, age, city, status
FROM customers
WHERE (city = 'תל אביב' OR city = 'חיפה')
  AND age >= 18
  AND status = 'active';
```

**תוצאה:** לקוחות פעילים מעל 18 מתל אביב או חיפה!

⚠️ **שימו לב לסוגריים!** הם קובעים את סדר הבדיקה.

---

## 📋 IN - בדיקה מול רשימה

במקום לכתוב הרבה OR:

```sql
-- לא נוח ❌
WHERE city = 'תל אביב' OR city = 'חיפה' OR city = 'ירושלים' OR city = 'באר שבע';

-- הרבה יותר טוב! ✅
WHERE city IN ('תל אביב', 'חיפה', 'ירושלים', 'באר שבע');
```

### דוגמאות נוספות

```sql
-- סטטוסים מסוימים
SELECT * FROM orders
WHERE status IN ('pending', 'processing', 'shipped');

-- מספרים ספציפיים
SELECT * FROM products
WHERE product_id IN (1, 5, 12, 25, 100);

-- עם NOT IN
SELECT * FROM customers
WHERE city NOT IN ('ירושלים', 'באר שבע');
```

---

## 📊 BETWEEN - בין טווח

```sql
-- מחיר בין 100 ל-500
SELECT * FROM products
WHERE price BETWEEN 100 AND 500;
```

**זהה ל:**
```sql
WHERE price >= 100 AND price <= 500;
```

### דוגמאות נוספות

```sql
-- גילאים בין 18 ל-65
SELECT * FROM customers
WHERE age BETWEEN 18 AND 65;

-- תאריכים בטווח
SELECT * FROM orders
WHERE order_date BETWEEN '2025-01-01' AND '2025-12-31';

-- עם NOT BETWEEN
SELECT * FROM products
WHERE price NOT BETWEEN 50 AND 200;  -- פחות מ-50 או יותר מ-200
```

---

## 🔍 LIKE - חיפוש בטקסט

### תווים מיוחדים

- `%` - מייצג **כמה תווים שהם** (כולל אפס)
- `_` - מייצג **תו אחד בדיוק**

### דוגמאות

```sql
-- שמות שמתחילים ב"אב"
SELECT * FROM customers
WHERE name LIKE 'אב%';
-- תוצאה: אבי, אבנר, אברהם

-- שמות שמסתיימים ב"ה"
SELECT * FROM customers
WHERE name LIKE '%ה';
-- תוצאה: שרה, רחל, לאה

-- שמות שמכילים "אל" באמצע
SELECT * FROM customers
WHERE name LIKE '%אל%';
-- תוצאה: דניאל, מיכאל, רפאל

-- שם בדיוק בן 4 תווים
SELECT * FROM customers
WHERE name LIKE '____';  -- 4 קווים תחתונים
-- תוצאה: אבי (3 תווים), דני (3 תווים) לא יופיעו

-- אימייל של Gmail
SELECT * FROM customers
WHERE email LIKE '%@gmail.com';

-- טלפון שמתחיל ב-052
SELECT * FROM customers
WHERE phone LIKE '052%';

-- עם NOT LIKE
SELECT * FROM customers
WHERE email NOT LIKE '%@gmail.com';  -- לא Gmail
```

---

## ❓ IS NULL / IS NOT NULL - בדיקת NULL

### מה זה NULL?

NULL = **אין ערך** (לא 0, לא רווח, לא טקסט ריק - פשוט אין!)

### דוגמאות

```sql
-- לקוחות ללא אימייל
SELECT * FROM customers
WHERE email IS NULL;

-- לקוחות עם אימייל
SELECT * FROM customers
WHERE email IS NOT NULL;

-- הזמנות שעדיין לא נשלחו
SELECT * FROM orders
WHERE shipping_date IS NULL;

-- מוצרים עם תיאור
SELECT * FROM products
WHERE description IS NOT NULL;
```

⚠️ **טעות נפוצה:**
```sql
-- לא יעבוד! ❌
WHERE email = NULL

-- נכון! ✅
WHERE email IS NULL
```

---

## 🎨 CASE - תנאים בתוך SELECT

CASE מאפשר לנו **להחליט מה להציג** לפי תנאים.

### תחביר

```sql
SELECT 
    name,
    age,
    CASE 
        WHEN age < 18 THEN 'קטין'
        WHEN age >= 18 AND age < 65 THEN 'מבוגר'
        ELSE 'פנסיונר'
    END AS age_group
FROM customers;
```

**תוצאה:**
```
name  | age | age_group
------|-----|----------
אבי   | 15  | קטין
בני   | 35  | מבוגר
גלי   | 70  | פנסיונר
```

### דוגמאות נוספות

```sql
-- דירוג מחיר
SELECT 
    product_name,
    price,
    CASE 
        WHEN price < 100 THEN 'זול'
        WHEN price < 500 THEN 'בינוני'
        ELSE 'יקר'
    END AS price_category
FROM products;

-- סטטוס הזמנה בעברית
SELECT 
    order_id,
    CASE status
        WHEN 'pending' THEN 'ממתין'
        WHEN 'processing' THEN 'בטיפול'
        WHEN 'shipped' THEN 'נשלח'
        WHEN 'delivered' THEN 'נמסר'
        ELSE 'לא ידוע'
    END AS status_hebrew
FROM orders;

-- חישוב הנחה
SELECT 
    product_name,
    price,
    CASE 
        WHEN price > 1000 THEN price * 0.8  -- 20% הנחה
        WHEN price > 500 THEN price * 0.9   -- 10% הנחה
        ELSE price                          -- ללא הנחה
    END AS final_price
FROM products;

-- דגל ישראלי/זר
SELECT 
    customer_name,
    country,
    CASE 
        WHEN country = 'Israel' THEN 'ישראלי'
        ELSE 'זר'
    END AS customer_type
FROM customers;
```

---

## 💼 תרחישים מעשיים

### תרחיש 1: מציאת לקוחות VIP

```sql
SELECT 
    customer_name,
    total_purchases,
    CASE 
        WHEN total_purchases > 10000 THEN 'VIP Gold'
        WHEN total_purchases > 5000 THEN 'VIP Silver'
        WHEN total_purchases > 1000 THEN 'VIP Bronze'
        ELSE 'Regular'
    END AS membership_level
FROM customers
WHERE total_purchases > 0
ORDER BY total_purchases DESC;
```

---

### תרחיש 2: התראות מלאי נמוך

```sql
SELECT 
    product_name,
    stock_quantity,
    CASE 
        WHEN stock_quantity = 0 THEN 'אזל מהמלאי! 🔴'
        WHEN stock_quantity < 5 THEN 'מלאי נמוך! ⚠️'
        WHEN stock_quantity < 20 THEN 'מלאי בינוני 🟡'
        ELSE 'במלאי ✅'
    END AS stock_status
FROM products
WHERE stock_quantity < 20
ORDER BY stock_quantity;
```

---

### תרחיש 3: סינון הזמנות אחרונות

```sql
SELECT 
    order_id,
    customer_name,
    order_date,
    amount
FROM orders
WHERE order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY)
  AND status IN ('completed', 'shipped')
  AND amount > 100
ORDER BY order_date DESC;
```

**מה עושה?** הזמנות מושלמות מ-30 הימים האחרונים מעל 100 ₪!

---

### תרחיש 4: זיהוי לקוחות לא פעילים

```sql
SELECT 
    customer_id,
    customer_name,
    last_order_date,
    DATEDIFF(CURRENT_DATE, last_order_date) AS days_since_last_order
FROM customers
WHERE last_order_date < DATE_SUB(CURRENT_DATE, INTERVAL 6 MONTH)
   OR last_order_date IS NULL
ORDER BY last_order_date;
```

**מטרה:** למצוא לקוחות שלא הזמינו 6 חודשים! 📞

---

### תרחיש 5: ניתוח ביצועים

```sql
SELECT 
    salesperson,
    COUNT(*) AS deals_count,
    SUM(amount) AS total_sales,
    AVG(amount) AS avg_deal_size,
    CASE 
        WHEN SUM(amount) > 100000 THEN 'מצטיין ⭐⭐⭐'
        WHEN SUM(amount) > 50000 THEN 'טוב מאוד ⭐⭐'
        WHEN SUM(amount) > 25000 THEN 'טוב ⭐'
        ELSE 'דורש שיפור'
    END AS performance
FROM sales
WHERE YEAR(sale_date) = 2025
GROUP BY salesperson
HAVING SUM(amount) > 0
ORDER BY total_sales DESC;
```

---

### תרחיש 6: חיפוש מתקדם

```sql
SELECT * FROM products
WHERE 
    (
        product_name LIKE '%מחשב%' 
        OR product_name LIKE '%לפטופ%'
        OR category = 'Computers'
    )
    AND price BETWEEN 2000 AND 8000
    AND stock_quantity > 0
    AND rating >= 4.0
ORDER BY price;
```

**חיפוש:** מחשבים זמינים במלאי, במחיר 2000-8000 ₪, עם דירוג 4+ כוכבים!

---

## 🔥 תנאים מתקדמים

### 1️⃣ EXISTS - בדיקת קיום

```sql
-- לקוחות שביצעו לפחות הזמנה אחת
SELECT customer_name
FROM customers c
WHERE EXISTS (
    SELECT 1 
    FROM orders o 
    WHERE o.customer_id = c.customer_id
);
```

---

### 2️⃣ NOT EXISTS - לא קיים

```sql
-- לקוחות שלא ביצעו אף הזמנה
SELECT customer_name
FROM customers c
WHERE NOT EXISTS (
    SELECT 1 
    FROM orders o 
    WHERE o.customer_id = c.customer_id
);
```

---

### 3️⃣ תנאי עם תת-שאילתה

```sql
-- מוצרים שמחירם מעל הממוצע
SELECT product_name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);
```

```sql
-- לקוחות שהוציאו יותר מהממוצע
SELECT 
    customer_name,
    total_spent
FROM (
    SELECT 
        customer_id,
        customer_name,
        SUM(amount) AS total_spent
    FROM orders
    GROUP BY customer_id, customer_name
) AS customer_totals
WHERE total_spent > (
    SELECT AVG(total_spent) 
    FROM (
        SELECT SUM(amount) AS total_spent
        FROM orders
        GROUP BY customer_id
    ) AS avg_calculation
);
```

---

### 4️⃣ REGEXP - ביטויים רגולריים (מתקדם)

```sql
-- MySQL
-- מספרי טלפון תקינים (052-1234567)
SELECT * FROM customers
WHERE phone REGEXP '^05[0-9]-[0-9]{7}$';

-- אימיילים תקינים
SELECT * FROM customers
WHERE email REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$';
```

---

### 5️⃣ תנאים עם פונקציות

```sql
-- לקוחות שנרשמו בשנה האחרונה
SELECT * FROM customers
WHERE YEAR(registration_date) = YEAR(CURRENT_DATE);

-- הזמנות מסוף השבוע
SELECT * FROM orders
WHERE DAYOFWEEK(order_date) IN (1, 7);  -- ראשון ושבת

-- מחירים עגולים
SELECT * FROM products
WHERE price = ROUND(price);  -- 100, 200 אבל לא 99.99

-- שמות באורך 4 תווים
SELECT * FROM customers
WHERE LENGTH(name) = 4;
```

---

## 🚨 טעויות נפוצות

### ❌ טעות 1: = במקום IS NULL

```sql
-- לא יעבוד! ❌
SELECT * FROM customers
WHERE email = NULL;

-- נכון! ✅
SELECT * FROM customers
WHERE email IS NULL;
```

---

### ❌ טעות 2: OR ללא סוגריים

```sql
-- לא מה שרציתם! ❌
SELECT * FROM products
WHERE category = 'Electronics' 
   OR category = 'Computers'
   AND price > 1000;
-- זה יחזיר: (כל האלקטרוניקה) OR (מחשבים מעל 1000)

-- נכון! ✅
SELECT * FROM products
WHERE (category = 'Electronics' OR category = 'Computers')
  AND price > 1000;
```

---

### ❌ טעות 3: LIKE עם רווחים

```sql
-- עלול לא לעבוד אם יש רווחים! ❌
WHERE name LIKE 'אבי%'  -- לא ימצא "אבי " (עם רווח)

-- פתרון: ✅
WHERE TRIM(name) LIKE 'אבי%'
-- או
WHERE name LIKE 'אבי%' OR name LIKE 'אבי %'
```

---

### ❌ טעות 4: IN עם NULL

```sql
-- לא יעבוד כמצופה! ❌
SELECT * FROM customers
WHERE city IN ('תל אביב', 'חיפה', NULL);
-- NULL לא יתפוס שורות עם NULL!

-- פתרון: ✅
SELECT * FROM customers
WHERE city IN ('תל אביב', 'חיפה')
   OR city IS NULL;
```

---

## 🎓 תרגילים להתנסות

### תרגיל 1: סינון בסיסי
מצאו כל המוצרים במחיר 100-500 ₪ שיש מהם במלאי.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT * FROM products
WHERE price BETWEEN 100 AND 500
  AND stock_quantity > 0;
```
</details>

---

### תרגיל 2: חיפוש טקסט
מצאו לקוחות ששמם מתחיל ב"א" או שאימייל שלהם מכיל "gmail".

<details>
<summary>💡 פתרון</summary>

```sql
SELECT * FROM customers
WHERE name LIKE 'א%'
   OR email LIKE '%gmail%';
```
</details>

---

### תרגיל 3: תנאים מורכבים
מצאו הזמנות מהחודש האחרון, בסטטוס "completed" או "shipped", מעל 200 ₪.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT * FROM orders
WHERE order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 1 MONTH)
  AND status IN ('completed', 'shipped')
  AND amount > 200;
```
</details>

---

### תרגיל 4: CASE
צרו עמודה שמסווגת מוצרים לפי מחיר: זול (עד 50), בינוני (50-200), יקר (מעל 200).

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    product_name,
    price,
    CASE 
        WHEN price <= 50 THEN 'זול'
        WHEN price <= 200 THEN 'בינוני'
        ELSE 'יקר'
    END AS price_category
FROM products;
```
</details>

---

## 💡 טיפים מקצועיים

### 1️⃣ השתמשו באינדקסים

```sql
-- יצירת אינדקס על עמודות בהן אתם מסננים הרבה
CREATE INDEX idx_city ON customers(city);
CREATE INDEX idx_status ON orders(status);

-- עכשיו שאילתות עם WHERE יהיו מהירות יותר!
SELECT * FROM customers WHERE city = 'תל אביב';  -- ⚡ מהיר!
```

---

### 2️⃣ תנאים בסדר נכון

```sql
-- טוב! ✅ (הבדיקה המהירה ביותר קודם)
WHERE status = 'active'  -- מהיר (אינדקס)
  AND YEAR(created_date) = 2025  -- איטי יותר
  AND complex_calculation() > 100  -- הכי איטי

-- לא אופטימלי ❌
WHERE complex_calculation() > 100  -- בודק את זה קודם!
  AND status = 'active'
```

---

### 3️⃣ COALESCE לערכי ברירת מחדל

```sql
SELECT 
    customer_name,
    COALESCE(email, 'אין אימייל') AS email,
    COALESCE(phone, 'אין טלפון') AS phone
FROM customers;
```

---

### 4️⃣ NULLIF למניעת חלוקה באפס

```sql
SELECT 
    product_name,
    total_revenue / NULLIF(units_sold, 0) AS avg_price_per_unit
FROM products;
-- אם units_sold = 0, התוצאה תהיה NULL ולא שגיאה!
```

---

## 📊 טבלת סיכום - כל האופרטורים

| קטגוריה | אופרטור | דוגמה |
|----------|---------|--------|
| השוואה | `=, !=, <, >, <=, >=` | `WHERE age = 25` |
| לוגי | `AND, OR, NOT` | `WHERE age > 18 AND city = 'TA'` |
| טווח | `BETWEEN` | `WHERE price BETWEEN 100 AND 500` |
| רשימה | `IN` | `WHERE city IN ('TA', 'Haifa')` |
| טקסט | `LIKE` | `WHERE name LIKE 'A%'` |
| NULL | `IS NULL, IS NOT NULL` | `WHERE email IS NULL` |
| תנאי | `CASE WHEN` | `CASE WHEN age < 18 THEN 'minor'` |
| קיום | `EXISTS` | `WHERE EXISTS (SELECT...)` |

---

## ❓ שאלות נפוצות

**Q: מה ההבדל בין AND ל-OR?**
A: AND = כל התנאים חייבים להתקיים. OR = מספיק שתנאי אחד יתקיים.

**Q: למה צריך IS NULL ולא = NULL?**
A: NULL אומר "אין ערך". לא אפשר להשוות לאין-ערך עם =.

**Q: LIKE רגיש לאותיות גדולות/קטנות?**
A: תלוי במערכת ובהגדרות. ב-MySQL בדרך כלל לא רגיש. ב-PostgreSQL כן.

**Q: מה מהיר יותר - IN או OR?**
A: IN מהיר יותר וקריא יותר. השתמשו ב-IN!

---

## 🎉 סיימתם!

עכשיו אתם יודעים:
✅ כל אופרטורי ההשוואה והלוגיקה  
✅ איך להשתמש ב-WHERE, IN, BETWEEN, LIKE  
✅ איך לבנות תנאים מורכבים עם AND/OR  
✅ איך להשתמש ב-CASE לתנאים בתוך SELECT  
✅ תנאים מתקדמים עם EXISTS ותת-שאילתות  
✅ איך למנוע טעויות נפוצות  

**תנאים הם הבסיס לסינון נתונים ב-SQL - שלטו בהם ותשלטו בנתונים!** 💪

---

**המשך למדריך הבא:** [מדריך CRUD](12_CRUD_Guide.md)
