# 🧮 מדריך פונקציות צבירה ב-SQL - צעד אחר צעד

## 📌 מה זה פונקציות צבירה?

פונקציות צבירה (Aggregate Functions) הן פונקציות שמבצעות **חישוב על קבוצת שורות** ומחזירות **ערך בודד**.

במקום לקבל הרבה שורות, נקבל תשובה אחת ברורה! 🎯

**דוגמה פשוטה:**
- יש לכם 100 הזמנות
- רוצים לדעת: כמה סך הכל הרווחתם?
- **פתרון:** `SUM(amount)` → תשובה אחת! 💰

---

## 🎯 הפונקציות העיקריות

| פונקציה | מה היא עושה | דוגמה |
|---------|-------------|--------|
| **COUNT()** | סופרת שורות | `COUNT(*)` → 156 |
| **SUM()** | מסכמת ערכים | `SUM(amount)` → 45,600 |
| **AVG()** | מחשבת ממוצע | `AVG(price)` → 292.5 |
| **MIN()** | מוצאת מינימום | `MIN(age)` → 18 |
| **MAX()** | מוצאת מקסימום | `MAX(salary)` → 25,000 |

---

## 1️⃣ COUNT - ספירה

### COUNT(*) - סופר את כל השורות

```sql
SELECT COUNT(*) AS total_orders
FROM orders;
```

**תוצאה:**
```
total_orders
-------------
156
```

**מה קרה?** ספרנו כמה שורות יש בטבלה! ✅

---

### COUNT(column) - סופר ערכים שאינם NULL

```sql
SELECT COUNT(email) AS customers_with_email
FROM customers;
```

**ההבדל:**
- `COUNT(*)` - סופר **הכל** כולל NULL
- `COUNT(email)` - סופר רק שורות שיש בהן email (לא NULL)

**דוגמה:**
```sql
SELECT 
    COUNT(*) AS total_customers,
    COUNT(email) AS with_email,
    COUNT(phone) AS with_phone
FROM customers;
```

**תוצאה:**
```
total_customers | with_email | with_phone
----------------|------------|------------
100             | 85         | 92
```

**פירוש:** יש 100 לקוחות, ל-85 יש אימייל, ל-92 יש טלפון.

---

### COUNT(DISTINCT column) - ספירת ערכים ייחודיים

```sql
SELECT COUNT(DISTINCT customer_city) AS number_of_cities
FROM customers;
```

**תוצאה:** כמה ערים שונות יש במערכת!

**דוגמה נוספת:**
```sql
SELECT 
    COUNT(*) AS total_orders,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM orders;
```

**תוצאה:**
```
total_orders | unique_customers
-------------|------------------
500          | 87
```

**פירוש:** 500 הזמנות ביצעו 87 לקוחות שונים.

---

## 2️⃣ SUM - סכום

### שימוש בסיסי

```sql
SELECT SUM(amount) AS total_revenue
FROM orders;
```

**תוצאה:**
```
total_revenue
--------------
145,600
```

**קיבלנו:** סך כל ההכנסות! 💰

---

### SUM עם תנאי (CASE)

```sql
SELECT 
    SUM(CASE WHEN status = 'completed' THEN amount ELSE 0 END) AS completed_revenue,
    SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS pending_revenue
FROM orders;
```

**תוצאה:**
```
completed_revenue | pending_revenue
------------------|----------------
98,500            | 12,300
```

**מדהים!** אפשר לסכום בתנאי! 🎯

---

### SUM עם NULL

```sql
SELECT SUM(discount) AS total_discounts
FROM orders;
```

⚠️ **חשוב:** SUM מתעלם מ-NULL! אם כל הערכים NULL, התוצאה תהיה NULL.

**פתרון:**
```sql
SELECT COALESCE(SUM(discount), 0) AS total_discounts
FROM orders;
```

---

## 3️⃣ AVG - ממוצע

### חישוב ממוצע בסיסי

```sql
SELECT AVG(amount) AS average_order_value
FROM orders;
```

**תוצאה:**
```
average_order_value
--------------------
292.50
```

---

### AVG מתעלם מ-NULL!

```sql
-- טבלה לדוגמה
prices: 100, 200, NULL, 300

SELECT AVG(price) AS avg_price
FROM products;
```

**תוצאה:** `200` (לא 150!)

**למה?** AVG מחשב: (100 + 200 + 300) / 3 = 200

⚠️ **NULL לא נספר!**

---

### עיגול ממוצע

```sql
SELECT ROUND(AVG(amount), 2) AS avg_order_value
FROM orders;
```

**תוצאה:** `292.50` (במקום 292.5031827)

---

### ממוצע עם תנאי

```sql
SELECT 
    AVG(CASE WHEN status = 'completed' THEN amount END) AS avg_completed,
    AVG(CASE WHEN status = 'pending' THEN amount END) AS avg_pending
FROM orders;
```

---

## 4️⃣ MIN - מינימום

### מציאת הערך הנמוך ביותר

```sql
SELECT MIN(price) AS cheapest_product
FROM products;
```

**תוצאה:**
```
cheapest_product
-----------------
9.99
```

---

### MIN עובד גם עם טקסט ותאריכים!

```sql
SELECT 
    MIN(product_name) AS first_alphabetically,
    MIN(created_date) AS oldest_record
FROM products;
```

**תוצאה:**
```
first_alphabetically | oldest_record
---------------------|---------------
אבטיח               | 2020-01-15
```

**MIN על טקסט:** מחזיר את הראשון לפי סדר א-ב!

---

### למצוא את השורה עם הערך המינימלי

```sql
-- רוצים לדעת מה המוצר הזול ביותר (לא רק המחיר!)
SELECT *
FROM products
WHERE price = (SELECT MIN(price) FROM products);
```

---

## 5️⃣ MAX - מקסימום

### מציאת הערך הגבוה ביותר

```sql
SELECT MAX(salary) AS highest_salary
FROM employees;
```

**תוצאה:**
```
highest_salary
---------------
25,000
```

---

### MAX עם תאריכים

```sql
SELECT MAX(order_date) AS last_order_date
FROM orders;
```

**תוצאה:** התאריך האחרון שבוצעה הזמנה! 📅

---

### שילוב MIN ו-MAX

```sql
SELECT 
    MIN(price) AS cheapest,
    MAX(price) AS most_expensive,
    MAX(price) - MIN(price) AS price_range
FROM products;
```

**תוצאה:**
```
cheapest | most_expensive | price_range
---------|----------------|-------------
9.99     | 2499.00        | 2489.01
```

**מעולה!** רואים את כל הטווח! 📊

---

## 🔥 שילוב כל הפונקציות

```sql
SELECT 
    COUNT(*) AS total_orders,
    COUNT(DISTINCT customer_id) AS unique_customers,
    SUM(amount) AS total_revenue,
    AVG(amount) AS avg_order_value,
    MIN(amount) AS smallest_order,
    MAX(amount) AS largest_order,
    MAX(amount) - MIN(amount) AS order_range
FROM orders
WHERE YEAR(order_date) = 2025;
```

**תוצאה:**
```
total_orders | unique_customers | total_revenue | avg_order_value | smallest_order | largest_order | order_range
-------------|------------------|---------------|-----------------|----------------|---------------|-------------
1,245        | 487              | 364,500       | 292.77          | 15.00          | 5,800.00      | 5,785.00
```

**דוח מלא ומקיף במשפט אחד!** 🎯

---

## 💼 תרחישים מעשיים

### תרחיש 1: ניתוח מכירות

```sql
SELECT 
    'מכירות היום' AS metric,
    COUNT(*) AS orders,
    SUM(amount) AS revenue,
    AVG(amount) AS avg_value
FROM orders
WHERE DATE(order_date) = CURRENT_DATE

UNION ALL

SELECT 
    'מכירות השבוע',
    COUNT(*),
    SUM(amount),
    AVG(amount)
FROM orders
WHERE order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 7 DAY)

UNION ALL

SELECT 
    'מכירות החודש',
    COUNT(*),
    SUM(amount),
    AVG(amount)
FROM orders
WHERE YEAR(order_date) = YEAR(CURRENT_DATE)
  AND MONTH(order_date) = MONTH(CURRENT_DATE);
```

**תוצאה:**
```
metric           | orders | revenue  | avg_value
-----------------|--------|----------|----------
מכירות היום     | 12     | 3,450    | 287.50
מכירות השבוע    | 89     | 26,100   | 293.26
מכירות החודש    | 387    | 112,500  | 290.70
```

---

### תרחיש 2: בקרת איכות

```sql
SELECT 
    'מוצרים עם ביקורות' AS metric,
    COUNT(*) AS count
FROM products
WHERE review_count > 0

UNION ALL

SELECT 
    'ממוצע ביקורות למוצר',
    ROUND(AVG(review_count), 1)
FROM products

UNION ALL

SELECT 
    'דירוג ממוצע',
    ROUND(AVG(rating), 2)
FROM products
WHERE review_count > 0;
```

---

### תרחיש 3: ניהול מלאי

```sql
SELECT 
    category,
    COUNT(*) AS products_in_category,
    SUM(stock_quantity) AS total_units,
    AVG(stock_quantity) AS avg_stock,
    MIN(stock_quantity) AS min_stock,
    MAX(stock_quantity) AS max_stock,
    COUNT(CASE WHEN stock_quantity < 10 THEN 1 END) AS low_stock_items
FROM products
GROUP BY category
ORDER BY total_units DESC;
```

**תוצאה:**
```
category       | products | total_units | avg_stock | min_stock | max_stock | low_stock_items
---------------|----------|-------------|-----------|-----------|-----------|----------------
אלקטרוניקה   | 45       | 1,250       | 27.8      | 0         | 150       | 8
ביגוד         | 78       | 2,300       | 29.5      | 2         | 200       | 3
```

---

### תרחיש 4: ניתוח זמן תגובה

```sql
SELECT 
    AVG(TIMESTAMPDIFF(HOUR, created_at, responded_at)) AS avg_response_hours,
    MIN(TIMESTAMPDIFF(HOUR, created_at, responded_at)) AS fastest_response,
    MAX(TIMESTAMPDIFF(HOUR, created_at, responded_at)) AS slowest_response,
    COUNT(CASE WHEN TIMESTAMPDIFF(HOUR, created_at, responded_at) > 24 THEN 1 END) AS over_24h
FROM support_tickets
WHERE responded_at IS NOT NULL;
```

---

### תרחיש 5: ROI (החזר השקעה)

```sql
SELECT 
    campaign_name,
    SUM(cost) AS total_cost,
    SUM(revenue) AS total_revenue,
    SUM(revenue) - SUM(cost) AS profit,
    ROUND(((SUM(revenue) - SUM(cost)) / SUM(cost)) * 100, 2) AS roi_percentage
FROM marketing_campaigns
GROUP BY campaign_name
HAVING SUM(revenue) > 0
ORDER BY roi_percentage DESC;
```

**תוצאה:**
```
campaign_name     | total_cost | total_revenue | profit | roi_percentage
------------------|------------|---------------|--------|----------------
קמפיין פייסבוק   | 5,000      | 18,500        | 13,500 | 270.00%
קמפיין גוגל      | 8,000      | 22,000        | 14,000 | 175.00%
```

---

## 🔧 פונקציות צבירה מתקדמות

### 1️⃣ STRING_AGG / GROUP_CONCAT - איחוד טקסט

```sql
-- MySQL
SELECT 
    customer_id,
    GROUP_CONCAT(product_name SEPARATOR ', ') AS products_ordered
FROM orders
GROUP BY customer_id;
```

**תוצאה:**
```
customer_id | products_ordered
------------|----------------------------------
101         | מחשב נייד, עכבר, מקלדת
102         | טלפון, אוזניות
```

**מדהים!** כל המוצרים של כל לקוח בשורה אחת! 🎯

---

### 2️⃣ COUNT עם CASE - ספירה מותנית

```sql
SELECT 
    COUNT(CASE WHEN status = 'completed' THEN 1 END) AS completed,
    COUNT(CASE WHEN status = 'pending' THEN 1 END) AS pending,
    COUNT(CASE WHEN status = 'cancelled' THEN 1 END) AS cancelled,
    COUNT(*) AS total
FROM orders;
```

**תוצאה:**
```
completed | pending | cancelled | total
----------|---------|-----------|-------
345       | 23      | 12        | 380
```

---

### 3️⃣ STDDEV - סטיית תקן

```sql
SELECT 
    AVG(amount) AS average,
    STDDEV(amount) AS standard_deviation,
    VARIANCE(amount) AS variance
FROM orders;
```

**שימושי:** לראות כמה מפוזרים הנתונים! 📊

---

### 4️⃣ PERCENTILE / MEDIAN - חציון

```sql
-- PostgreSQL
SELECT 
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY amount) AS median_order_value,
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY amount) AS q1,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY amount) AS q3
FROM orders;
```

**תוצאה:**
```
median_order_value | q1     | q3
-------------------|--------|--------
250.00             | 150.00 | 450.00
```

---

## 🚨 טעויות נפוצות

### ❌ טעות 1: ערבוב עמודות רגילות עם פונקציות צבירה

```sql
-- לא יעבוד! ❌
SELECT 
    customer_name,        -- עמודה רגילה
    SUM(amount)           -- פונקציית צבירה
FROM orders;
```

**הבעיה:** SQL לא יודע איזה `customer_name` להחזיר!

✅ **פתרון 1: הוסיפו GROUP BY**
```sql
SELECT 
    customer_name,
    SUM(amount)
FROM orders
GROUP BY customer_name;
```

✅ **פתרון 2: רק פונקציות צבירה**
```sql
SELECT SUM(amount) AS total
FROM orders;
```

---

### ❌ טעות 2: שכחת NULL

```sql
-- אם יש NULL values, התוצאה עלולה להפתיע!
SELECT AVG(rating) FROM products;
```

✅ **פתרון: טפלו ב-NULL**
```sql
SELECT AVG(COALESCE(rating, 0)) FROM products;
-- או
SELECT AVG(rating) FROM products WHERE rating IS NOT NULL;
```

---

### ❌ טעות 3: COUNT(*) vs COUNT(column)

```sql
-- לא אותו דבר!
SELECT 
    COUNT(*) AS all_rows,           -- 100 שורות
    COUNT(email) AS rows_with_email -- 85 שורות (15 הם NULL)
FROM customers;
```

**זכרו:** `COUNT(column)` מתעלם מ-NULL!

---

### ❌ טעות 4: חלוקה באפס

```sql
-- עלול לגרום לשגיאה!
SELECT 
    SUM(revenue) / COUNT(*) AS avg_per_order
FROM orders
WHERE order_date = '2025-12-25';  -- אולי אין הזמנות!
```

✅ **פתרון:**
```sql
SELECT 
    CASE 
        WHEN COUNT(*) = 0 THEN 0
        ELSE SUM(revenue) / COUNT(*)
    END AS avg_per_order
FROM orders
WHERE order_date = '2025-12-25';
```

---

## 🎓 תרגילים להתנסות

### תרגיל 1: סטטיסטיקות בסיסיות
חשבו את הסכום, הממוצע, המינימום והמקסימום של עמודת `amount` בטבלת `orders`.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    SUM(amount) AS total,
    AVG(amount) AS average,
    MIN(amount) AS minimum,
    MAX(amount) AS maximum
FROM orders;
```
</details>

---

### תרגיל 2: ספירת לקוחות פעילים
כמה לקוחות ביצעו לפחות הזמנה אחת? (השתמשו ב-DISTINCT)

<details>
<summary>💡 פתרון</summary>

```sql
SELECT COUNT(DISTINCT customer_id) AS active_customers
FROM orders;
```
</details>

---

### תרגיל 3: ניתוח טווח מחירים
מצאו את המוצר הזול והיקר ביותר בכל קטגוריה.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    category,
    MIN(price) AS cheapest,
    MAX(price) AS most_expensive,
    MAX(price) - MIN(price) AS price_range
FROM products
GROUP BY category;
```
</details>

---

### תרגיל 4: אחוז השלמה
חשבו כמה אחוז מההזמנות בסטטוס "completed".

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    COUNT(CASE WHEN status = 'completed' THEN 1 END) AS completed_orders,
    COUNT(*) AS total_orders,
    ROUND(100.0 * COUNT(CASE WHEN status = 'completed' THEN 1 END) / COUNT(*), 2) AS completion_rate
FROM orders;
```
</details>

---

## 💡 טיפים מקצועיים

### 1️⃣ COALESCE לטיפול ב-NULL

```sql
SELECT 
    COALESCE(SUM(amount), 0) AS total,
    COALESCE(AVG(rating), 0) AS avg_rating
FROM products;
```

**תמיד תקבלו ערך, גם אם הטבלה ריקה!** ✅

---

### 2️⃣ ROUND לעיגול

```sql
SELECT 
    ROUND(AVG(amount), 2) AS avg_rounded,     -- 2 ספרות אחרי הנקודה
    ROUND(SUM(amount), -2) AS sum_hundreds    -- עיגול למאות
FROM orders;
```

---

### 3️⃣ FILTER (PostgreSQL)

```sql
SELECT 
    COUNT(*) FILTER (WHERE status = 'completed') AS completed,
    AVG(amount) FILTER (WHERE amount > 100) AS avg_large_orders
FROM orders;
```

**נקי יותר מ-CASE!** (אבל לא נתמך בכל מערכות SQL)

---

### 4️⃣ שילוב עם Window Functions

```sql
SELECT 
    customer_name,
    amount,
    AVG(amount) OVER () AS overall_avg,
    amount - AVG(amount) OVER () AS diff_from_avg
FROM orders;
```

**מתקדם:** משווים כל הזמנה לממוצע הכללי! 📊

---

## 📊 טבלת סיכום - כל הפונקציות

| פונקציה | תיאור | דוגמה | תוצאה |
|---------|-------|--------|-------|
| `COUNT(*)` | סופר כל השורות | `SELECT COUNT(*) FROM orders` | `500` |
| `COUNT(col)` | סופר ערכים לא-NULL | `SELECT COUNT(email) FROM customers` | `487` |
| `COUNT(DISTINCT col)` | סופר ערכים ייחודיים | `SELECT COUNT(DISTINCT city) FROM customers` | `23` |
| `SUM(col)` | מסכם ערכים | `SELECT SUM(amount) FROM orders` | `145,600` |
| `AVG(col)` | ממוצע | `SELECT AVG(price) FROM products` | `292.50` |
| `MIN(col)` | מינימום | `SELECT MIN(age) FROM customers` | `18` |
| `MAX(col)` | מקסימום | `SELECT MAX(salary) FROM employees` | `25,000` |
| `STDDEV(col)` | סטיית תקן | `SELECT STDDEV(amount) FROM orders` | `87.23` |
| `VARIANCE(col)` | שונות | `SELECT VARIANCE(amount) FROM orders` | `7609.15` |

---

## ❓ שאלות נפוצות

**Q: מה ההבדל בין COUNT(*) ל-COUNT(1)?**
A: בביצועים אין הבדל משמעותי. שניהם סופרים את כל השורות.

**Q: למה AVG יכול להחזיר NULL?**
A: אם כל הערכים הם NULL, או אם אין שורות בכלל.

**Q: איך לחשב ממוצע משוקלל?**
A: `SUM(value * weight) / SUM(weight)`

**Q: פונקציות צבירה עובדות עם תאריכים?**
A: כן! MIN/MAX מחזירים את התאריך המוקדם/המאוחר ביותר.

---

## 🎉 סיימתם!

עכשיו אתם יודעים:
✅ את כל פונקציות הצבירה המרכזיות  
✅ איך להשתמש בהן במצבים מעשיים  
✅ איך לשלב מספר פונקציות ביחד  
✅ איך להימנע מטעויות נפוצות  
✅ טכניקות מתקדמות לניתוח נתונים  

**פונקציות צבירה הן הלב של ניתוח נתונים ב-SQL - תתרגלו אותן הרבה!** 💪

---

**חזרה למדריך הקודם:** [מדריך GROUP BY](08_GROUP_BY_Guide.md) | **המשך למדריך הבא:** [מדריך ORDER BY](10_ORDER_BY_Guide.md)
