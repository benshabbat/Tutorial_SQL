# 📊 מדריך GROUP BY ב-SQL - צעד אחר צעד

## 📌 מה זה GROUP BY?

GROUP BY הוא פקודה שמאפשרת לנו **לקבץ שורות לפי ערך משותף** ולבצע חישובים על כל קבוצה.

דמיינו שיש לכם טבלת מכירות:
- איך נדע כמה מכר כל איש מכירות?
- כמה מכירות היו בכל חודש?
- מה הממוצע לפי עיר?

**התשובה:** GROUP BY! 🎯

---

## 🎯 הרעיון הבסיסי

**לפני GROUP BY:**
```
שם      | סכום
--------|------
אבי     | 100
בני     | 200
אבי     | 150
בני     | 300
אבי     | 50
```

**אחרי GROUP BY name:**
```
שם      | סה"כ
--------|------
אבי     | 300  (100+150+50)
בני     | 500  (200+300)
```

**מה קרה?** קיבצנו לפי שם וחישבנו סכום! 📊

---

## 🛠️ תחביר בסיסי

```sql
SELECT 
    column_name,
    AGGREGATE_FUNCTION(column_name)
FROM table_name
GROUP BY column_name;
```

**פונקציות צבירה (Aggregate Functions):**
- `COUNT()` - ספירה
- `SUM()` - סכום
- `AVG()` - ממוצע
- `MIN()` - מינימום
- `MAX()` - מקסימום

---

## 🎨 דוגמאות בסיסיות - צעד אחר צעד

### דוגמה 1: COUNT - ספירת רשומות

נניח שיש לנו טבלת הזמנות:

```sql
SELECT 
    customer_name,
    COUNT(*) AS order_count
FROM orders
GROUP BY customer_name;
```

**תוצאה:**
```
customer_name | order_count
--------------|-------------
אבי           | 3
בני           | 5
גלי           | 2
```

**מה קרה?**
1. קיבצנו את כל ההזמנות לפי שם לקוח
2. ספרנו כמה הזמנות יש לכל לקוח ✅

---

### דוגמה 2: SUM - חישוב סכום

```sql
SELECT 
    customer_name,
    SUM(amount) AS total_amount
FROM orders
GROUP BY customer_name;
```

**תוצאה:**
```
customer_name | total_amount
--------------|-------------
אבי           | 1500
בני           | 2800
גלי           | 900
```

**קיבלנו:** סכום כל ההזמנות של כל לקוח! 💰

---

### דוגמה 3: AVG - חישוב ממוצע

```sql
SELECT 
    customer_name,
    AVG(amount) AS avg_amount
FROM orders
GROUP BY customer_name;
```

**תוצאה:**
```
customer_name | avg_amount
--------------|------------
אבי           | 500
בני           | 560
גלי           | 450
```

**קיבלנו:** הממוצע של הזמנות לכל לקוח! 📈

---

### דוגמה 4: MIN ו-MAX

```sql
SELECT 
    customer_name,
    MIN(amount) AS smallest_order,
    MAX(amount) AS largest_order
FROM orders
GROUP BY customer_name;
```

**תוצאה:**
```
customer_name | smallest_order | largest_order
--------------|----------------|---------------
אבי           | 100            | 800
בני           | 200            | 1000
גלי           | 400            | 500
```

---

## 🔥 קיבוץ לפי מספר עמודות

אפשר לקבץ לפי יותר מעמודה אחת!

```sql
SELECT 
    customer_name,
    product_category,
    SUM(amount) AS total
FROM orders
GROUP BY customer_name, product_category;
```

**תוצאה:**
```
customer_name | product_category | total
--------------|------------------|-------
אבי           | אלקטרוניקה     | 2000
אבי           | ביגוד           | 500
בני           | אלקטרוניקה     | 3000
גלי           | ביגוד           | 1200
```

**מה קרה?** קיבצנו לפי **צירוף** של לקוח + קטגוריה! 🎯

---

## ⚙️ HAVING - סינון אחרי GROUP BY

`WHERE` מסנן **לפני** הקיבוץ.  
`HAVING` מסנן **אחרי** הקיבוץ!

### דוגמה: לקוחות עם יותר מ-3 הזמנות

```sql
SELECT 
    customer_name,
    COUNT(*) AS order_count
FROM orders
GROUP BY customer_name
HAVING COUNT(*) > 3;
```

**תוצאה:** רק לקוחות שביצעו יותר מ-3 הזמנות!

---

### ההבדל בין WHERE ל-HAVING

```sql
SELECT 
    customer_name,
    COUNT(*) AS order_count
FROM orders
WHERE amount > 100          -- סינון לפני הקיבוץ
GROUP BY customer_name
HAVING COUNT(*) > 2;        -- סינון אחרי הקיבוץ
```

**מה קורה פה?**
1. ⬇️ **WHERE:** מסנן רק הזמנות מעל 100 ₪
2. 📊 **GROUP BY:** מקבץ לפי לקוח
3. ⬆️ **HAVING:** מציג רק לקוחות עם יותר מ-2 הזמנות (מעל 100 ₪)

---

## 🎯 סדר ביצוע השאילתה

```sql
SELECT                  -- 5️⃣ בחירת עמודות להצגה
FROM                    -- 1️⃣ מאיזו טבלה
WHERE                   -- 2️⃣ סינון שורות
GROUP BY                -- 3️⃣ קיבוץ
HAVING                  -- 4️⃣ סינון קבוצות
ORDER BY                -- 6️⃣ מיון תוצאות
LIMIT                   -- 7️⃣ הגבלת כמות
```

**חשוב לדעת!** זה הסדר שבו SQL מעבד את השאילתה! 🔄

---

## 💼 תרחישים מעשיים

### תרחיש 1: דוח מכירות חודשי

```sql
SELECT 
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    COUNT(*) AS total_orders,
    SUM(amount) AS total_sales,
    AVG(amount) AS avg_order_value
FROM orders
GROUP BY YEAR(order_date), MONTH(order_date)
ORDER BY year DESC, month DESC;
```

**תוצאה:**
```
year | month | total_orders | total_sales | avg_order_value
-----|-------|--------------|-------------|----------------
2025 | 11    | 156          | 45600       | 292.31
2025 | 10    | 142          | 39800       | 280.28
2025 | 9     | 138          | 41200       | 298.55
```

**מושלם למעקב אחר ביצועים!** 📊

---

### תרחיש 2: TOP 10 לקוחות

```sql
SELECT 
    customer_name,
    COUNT(*) AS order_count,
    SUM(amount) AS total_spent
FROM orders
GROUP BY customer_name
ORDER BY total_spent DESC
LIMIT 10;
```

**תוצאה:** 10 הלקוחות שהכי הוציאו! 🏆

---

### תרחיש 3: ניתוח לפי יום בשבוע

```sql
SELECT 
    DAYNAME(order_date) AS day_of_week,
    COUNT(*) AS order_count,
    AVG(amount) AS avg_amount
FROM orders
GROUP BY DAYNAME(order_date), DAYOFWEEK(order_date)
ORDER BY DAYOFWEEK(order_date);
```

**תוצאה:**
```
day_of_week | order_count | avg_amount
------------|-------------|------------
Sunday      | 45          | 320
Monday      | 52          | 280
Tuesday     | 48          | 295
...
```

**שימושי:** לדעת באיזה ימים יש הכי הרבה מכירות! 📅

---

### תרחיש 4: זיהוי לקוחות חד-פעמיים

```sql
SELECT 
    customer_name,
    COUNT(*) AS order_count
FROM orders
GROUP BY customer_name
HAVING COUNT(*) = 1;
```

**תוצאה:** לקוחות שביצעו רק הזמנה אחת (צריך לפנות אליהם!) 📞

---

### תרחיש 5: השוואת ביצועים

```sql
SELECT 
    salesperson,
    COUNT(*) AS deals_closed,
    SUM(amount) AS total_sales,
    AVG(amount) AS avg_deal_size,
    MAX(amount) AS biggest_deal
FROM sales
WHERE YEAR(sale_date) = 2025
GROUP BY salesperson
HAVING SUM(amount) > 50000
ORDER BY total_sales DESC;
```

**תוצאה:** דירוג אנשי מכירות שעברו יעד של 50,000! 🎯

---

## 🔧 טכניקות מתקדמות

### 1️⃣ שימוש ב-CASE בתוך GROUP BY

```sql
SELECT 
    CASE 
        WHEN amount < 100 THEN 'קטן'
        WHEN amount < 500 THEN 'בינוני'
        ELSE 'גדול'
    END AS order_size,
    COUNT(*) AS order_count
FROM orders
GROUP BY 
    CASE 
        WHEN amount < 100 THEN 'קטן'
        WHEN amount < 500 THEN 'בינוני'
        ELSE 'גדול'
    END;
```

**תוצאה:**
```
order_size | order_count
-----------|-------------
קטן        | 45
בינוני     | 78
גדול       | 23
```

---

### 2️⃣ חישוב אחוזים

```sql
SELECT 
    product_category,
    SUM(amount) AS category_total,
    ROUND(100.0 * SUM(amount) / (SELECT SUM(amount) FROM orders), 2) AS percentage
FROM orders
GROUP BY product_category
ORDER BY category_total DESC;
```

**תוצאה:**
```
product_category | category_total | percentage
-----------------|----------------|------------
אלקטרוניקה     | 45000          | 42.50%
ביגוד           | 32000          | 30.20%
ספרים           | 18000          | 17.00%
```

---

### 3️⃣ GROUP BY עם JOIN

```sql
SELECT 
    c.customer_name,
    c.city,
    COUNT(o.order_id) AS order_count,
    SUM(o.amount) AS total_spent
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name, c.city
ORDER BY total_spent DESC;
```

**מציג:** כל הלקוחות כולל אלה שלא הזמינו (עם 0)! ✅

---

### 4️⃣ ROLLUP - סיכומי ביניים

```sql
SELECT 
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    SUM(amount) AS total
FROM orders
GROUP BY YEAR(order_date), MONTH(order_date) WITH ROLLUP;
```

**תוצאה:**
```
year | month | total
-----|-------|-------
2025 | 1     | 15000
2025 | 2     | 18000
2025 | NULL  | 33000  ← סכום שנת 2025
2024 | 1     | 12000
2024 | NULL  | 12000  ← סכום שנת 2024
NULL | NULL  | 45000  ← סכום כולל!
```

**מדהים לדוחות!** 📊

---

## 🚨 טעויות נפוצות

### ❌ טעות 1: עמודה ב-SELECT שלא ב-GROUP BY

```sql
-- לא יעבוד! ❌
SELECT 
    customer_name,
    city,              -- city לא ב-GROUP BY!
    COUNT(*) AS orders
FROM orders
GROUP BY customer_name;
```

**הבעיה:** SQL לא יודע איזה `city` לבחור אם ללקוח יש כתובות שונות!

✅ **פתרון:**
```sql
SELECT 
    customer_name,
    city,
    COUNT(*) AS orders
FROM orders
GROUP BY customer_name, city;  -- שניהם בקיבוץ!
```

---

### ❌ טעות 2: שימוש ב-WHERE במקום HAVING

```sql
-- לא יעבוד! ❌
SELECT 
    customer_name,
    COUNT(*) AS order_count
FROM orders
WHERE COUNT(*) > 3              -- WHERE לא מכיר פונקציות צבירה!
GROUP BY customer_name;
```

✅ **פתרון:**
```sql
SELECT 
    customer_name,
    COUNT(*) AS order_count
FROM orders
GROUP BY customer_name
HAVING COUNT(*) > 3;            -- HAVING הוא הנכון!
```

---

### ❌ טעות 3: שכחת GROUP BY לגמרי

```sql
-- לא יעבוד! ❌
SELECT 
    customer_name,
    SUM(amount) AS total
FROM orders;                     -- איפה ה-GROUP BY?
```

✅ **פתרון:**
```sql
SELECT 
    customer_name,
    SUM(amount) AS total
FROM orders
GROUP BY customer_name;          -- חובה!
```

---

### ❌ טעות 4: בלבול בין COUNT(*) ל-COUNT(column)

```sql
SELECT 
    customer_name,
    COUNT(*) AS all_rows,           -- כל השורות
    COUNT(email) AS rows_with_email -- רק שורות עם email (לא NULL)
FROM customers
GROUP BY customer_name;
```

**הבדל חשוב!**
- `COUNT(*)` - סופר הכל כולל NULL
- `COUNT(column)` - סופר רק ערכים שאינם NULL

---

## 🎓 תרגילים להתנסות

### תרגיל 1: סיכום מכירות לפי עיר
יש לכם טבלת `orders` עם עמודות: `order_id`, `customer_city`, `amount`.
הציגו כמה הזמנות וסכום כולל לכל עיר.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    customer_city,
    COUNT(*) AS order_count,
    SUM(amount) AS total_sales
FROM orders
GROUP BY customer_city
ORDER BY total_sales DESC;
```
</details>

---

### תרגיל 2: לקוחות VIP
מצאו לקוחות שביצעו יותר מ-5 הזמנות והוציאו יותר מ-10,000 ₪.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    customer_name,
    COUNT(*) AS order_count,
    SUM(amount) AS total_spent
FROM orders
GROUP BY customer_name
HAVING COUNT(*) > 5 AND SUM(amount) > 10000
ORDER BY total_spent DESC;
```
</details>

---

### תרגיל 3: מוצרים פופולריים
הציגו את 5 המוצרים הנמכרים ביותר (כמות יחידות).

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    product_name,
    SUM(quantity) AS total_units_sold
FROM order_items
GROUP BY product_name
ORDER BY total_units_sold DESC
LIMIT 5;
```
</details>

---

### תרגיל 4: ניתוח טווח מחירים
קבצו הזמנות לטווחי מחירים וספרו כמה בכל טווח.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    CASE 
        WHEN amount < 100 THEN '0-100'
        WHEN amount < 500 THEN '100-500'
        WHEN amount < 1000 THEN '500-1000'
        ELSE '1000+'
    END AS price_range,
    COUNT(*) AS order_count
FROM orders
GROUP BY 
    CASE 
        WHEN amount < 100 THEN '0-100'
        WHEN amount < 500 THEN '100-500'
        WHEN amount < 1000 THEN '500-1000'
        ELSE '1000+'
    END
ORDER BY MIN(amount);
```
</details>

---

## 💡 טיפים מקצועיים

### 1️⃣ השתמשו ב-Alias לקריאות

```sql
-- קשה לקרוא ❌
SELECT customer_name, SUM(amount)
FROM orders
GROUP BY customer_name;

-- קל לקרוא ✅
SELECT 
    customer_name AS לקוח,
    SUM(amount) AS סה"כ_מכירות
FROM orders
GROUP BY customer_name;
```

---

### 2️⃣ DISTINCT עם COUNT

```sql
-- כמה לקוחות שונים?
SELECT COUNT(DISTINCT customer_id) AS unique_customers
FROM orders;

-- כמה הזמנות לכל לקוח?
SELECT 
    customer_name,
    COUNT(DISTINCT order_id) AS order_count
FROM orders
GROUP BY customer_name;
```

---

### 3️⃣ GROUP BY עם אינדקסים

```sql
-- צרו אינדקס על עמודות שבהן אתם מקבצים
CREATE INDEX idx_customer_date ON orders(customer_id, order_date);
```

**זה יזרז משמעותית שאילתות עם GROUP BY!** ⚡

---

### 4️⃣ NULL values ב-GROUP BY

```sql
SELECT 
    COALESCE(customer_city, 'לא ידוע') AS city,
    COUNT(*) AS order_count
FROM orders
GROUP BY customer_city;
```

**NULL מטופל כקבוצה נפרדת!** השתמשו ב-COALESCE להצגה יפה.

---

## ❓ שאלות נפוצות

**Q: מה ההבדל בין WHERE ל-HAVING?**
A: 
- WHERE: מסנן שורות **לפני** הקיבוץ
- HAVING: מסנן קבוצות **אחרי** הקיבוץ

**Q: האם חייבים להשתמש בפונקציית צבירה עם GROUP BY?**
A: לא חובה, אבל אז זה כמו DISTINCT:
```sql
SELECT customer_name FROM orders GROUP BY customer_name;
-- זהה ל:
SELECT DISTINCT customer_name FROM orders;
```

**Q: מה יותר מהיר - GROUP BY או DISTINCT?**
A: תלוי במקרה, אבל בדרך כלל דומה. GROUP BY גמיש יותר.

**Q: איך לקבץ לפי תאריך בלי השעה?**
A: השתמשו ב-DATE():
```sql
GROUP BY DATE(order_datetime)
```

---

## 🎉 סיימתם!

עכשיו אתם יודעים:
✅ מה זה GROUP BY ואיך להשתמש בו  
✅ כל פונקציות הצבירה (SUM, COUNT, AVG, MIN, MAX)  
✅ ההבדל בין WHERE ל-HAVING  
✅ איך לקבץ לפי מספר עמודות  
✅ טכניקות מתקדמות ותרחישים מעשיים  

**GROUP BY הוא אחד הכלים החזקים ב-SQL - תתרגלו אותו הרבה!** 💪

---

**חזרה למדריך הקודם:** [מדריך UNION](07_UNION_Guide.md)
