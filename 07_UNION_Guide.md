# 🔀 מדריך UNION ב-SQL - צעד אחר צעד

## 📌 מה זה UNION?

UNION הוא פקודה שמאפשרת לנו **לאחד תוצאות משתי שאילתות או יותר** לתוצאה אחת.

דמיינו שיש לכם:
- טבלה של **לקוחות ישנים**
- טבלה של **לקוחות חדשים**

איך נקבל רשימה אחת של כל הלקוחות? 🤔 בעזרת UNION!

---

## 🎯 UNION vs UNION ALL - מה ההבדל?

### UNION - ללא כפילויות

מחזיר רשומות **ייחודיות בלבד** (מסיר כפילויות).

```sql
SELECT name FROM old_customers
UNION
SELECT name FROM new_customers;
```

**תוצאה:** כל שם מופיע **פעם אחת בלבד** 🎯

---

### UNION ALL - עם כפילויות

מחזיר **את כל הרשומות**, כולל כפילויות.

```sql
SELECT name FROM old_customers
UNION ALL
SELECT name FROM new_customers;
```

**תוצאה:** אם שם מופיע בשתי הטבלאות, הוא יופיע **פעמיים** 📊

---

### 🚀 איזה להשתמש?

| מצב | השתמשו ב... | למה? |
|-----|------------|------|
| רוצים רשימה ייחודית | **UNION** | מסיר כפילויות |
| צריכים את כל הנתונים | **UNION ALL** | מהיר יותר! |
| לא בטוחים | **UNION ALL** | בטוח יותר (לא מאבד נתונים) |

> 💡 **טיפ מקצועי:** UNION ALL מהיר יותר כי לא צריך לבדוק כפילויות!

---

## 🎨 דוגמה מלאה - צעד אחר צעד

נניח שיש לנו שתי טבלאות:

**טבלת customers_2024:**
```
name    | city
--------|--------
אבי     | תל אביב
בני     | ירושלים
גלי     | חיפה
```

**טבלת customers_2025:**
```
name    | city
--------|--------
דני     | באר שבע
אבי     | תל אביב
הדס    | אילת
```

---

### דוגמה 1: איחוד עם UNION

```sql
SELECT name, city FROM customers_2024
UNION
SELECT name, city FROM customers_2025;
```

**תוצאה:**
```
name | city
-----|----------
אבי  | תל אביב    ← מופיע פעם אחת בלבד!
בני  | ירושלים
גלי  | חיפה
דני  | באר שבע
הדס  | אילת
```

⚠️ **שימו לב:** אבי מתל אביב מופיע רק **פעם אחת** למרות שהוא ב-2 טבלאות!

---

### דוגמה 2: איחוד עם UNION ALL

```sql
SELECT name, city FROM customers_2024
UNION ALL
SELECT name, city FROM customers_2025;
```

**תוצאה:**
```
name | city
-----|----------
אבי  | תל אביב    ← מהטבלה הראשונה
בני  | ירושלים
גלי  | חיפה
דני  | באר שבע
אבי  | תל אביב    ← מהטבלה השנייה!
הדס  | אילת
```

✅ **עכשיו:** אבי מופיע **פעמיים**!

---

## ⚙️ חוקים חשובים ל-UNION

### 1️⃣ מספר העמודות חייב להיות זהה

❌ **לא יעבוד:**
```sql
SELECT name, city FROM customers_2024
UNION
SELECT name FROM customers_2025;  -- רק עמודה אחת!
```

✅ **יעבוד:**
```sql
SELECT name, city FROM customers_2024
UNION
SELECT name, city FROM customers_2025;  -- שתי עמודות בשניהם
```

---

### 2️⃣ סוג הנתונים חייב להתאים

❌ **לא יעבוד:**
```sql
SELECT name, age FROM customers_2024      -- age = מספר
UNION
SELECT name, city FROM customers_2025;    -- city = טקסט
```

✅ **יעבוד:**
```sql
SELECT name, age FROM customers_2024
UNION
SELECT name, age FROM customers_2025;
```

---

### 3️⃣ סדר העמודות חשוב!

```sql
SELECT name, city FROM customers_2024
UNION
SELECT city, name FROM customers_2025;  -- הפוך!
```

**תוצאה:** השמות והערים יתערבבו! 😵

**הפתרון:** תמיד שמרו על אותו סדר עמודות!

---

### 4️⃣ שמות העמודות נקבעים לפי השאילתה הראשונה

```sql
SELECT name AS customer_name, city
FROM customers_2024
UNION
SELECT name, city  -- לא משנה איך קראנו לעמודה כאן
FROM customers_2025;
```

**תוצאה:** העמודה תיקרא `customer_name` 📝

---

## 🛠️ דוגמאות מתקדמות

### דוגמה 1: UNION עם WHERE

```sql
SELECT name, city 
FROM customers_2024
WHERE city = 'תל אביב'
UNION
SELECT name, city 
FROM customers_2025
WHERE city = 'תל אביב';
```

**תוצאה:** רק לקוחות מתל אביב משתי הטבלאות!

---

### דוגמה 2: UNION עם ORDER BY

```sql
SELECT name, city FROM customers_2024
UNION
SELECT name, city FROM customers_2025
ORDER BY name;  -- מיון בסוף, על התוצאה המאוחדת!
```

⚠️ **חשוב:** ORDER BY מגיע **אחרי** כל ה-UNION, לא באמצע!

---

### דוגמה 3: הוספת עמודה לזיהוי מקור

```sql
SELECT name, city, 'לקוח ישן' AS customer_type
FROM customers_2024
UNION ALL
SELECT name, city, 'לקוח חדש' AS customer_type
FROM customers_2025;
```

**תוצאה:**
```
name | city       | customer_type
-----|------------|---------------
אבי  | תל אביב    | לקוח ישן
בני  | ירושלים    | לקוח ישן
גלי  | חיפה       | לקוח ישן
דני  | באר שבע    | לקוח חדש
אבי  | תל אביב    | לקוח חדש
הדס  | אילת       | לקוח חדש
```

**סופר שימושי!** עכשיו אנחנו יודעים מאיפה כל רשומה 🎯

---

### דוגמה 4: UNION של יותר מ-2 שאילתות

```sql
SELECT name FROM customers_2023
UNION
SELECT name FROM customers_2024
UNION
SELECT name FROM customers_2025;
```

**פשוט מאחדים אחד אחרי השני!** 📚

---

### דוגמה 5: UNION עם חישובים

```sql
SELECT 
    'מכירות ינואר' AS period,
    SUM(amount) AS total
FROM sales
WHERE MONTH(sale_date) = 1

UNION ALL

SELECT 
    'מכירות פברואר' AS period,
    SUM(amount) AS total
FROM sales
WHERE MONTH(sale_date) = 2

UNION ALL

SELECT 
    'סך הכל' AS period,
    SUM(amount) AS total
FROM sales;
```

**תוצאה:**
```
period            | total
------------------|-------
מכירות ינואר     | 15000
מכירות פברואר    | 18000
סך הכל           | 33000
```

**מעולה לדוחות!** 📊

---

## 💼 תרחישים מעשיים

### תרחיש 1: איחוד טבלאות דומות

יש לכם טבלאות מכירות לפי שנים:

```sql
SELECT order_id, customer_name, amount, '2023' AS year
FROM sales_2023
UNION ALL
SELECT order_id, customer_name, amount, '2024' AS year
FROM sales_2024
UNION ALL
SELECT order_id, customer_name, amount, '2025' AS year
FROM sales_2025;
```

**תוצאה:** טבלה אחת מאוחדת של כל המכירות! 🎉

---

### תרחיש 2: חיפוש במספר טבלאות

```sql
SELECT 'customers' AS table_name, name 
FROM customers
WHERE name LIKE '%אבי%'

UNION ALL

SELECT 'suppliers' AS table_name, name 
FROM suppliers
WHERE name LIKE '%אבי%'

UNION ALL

SELECT 'employees' AS table_name, name 
FROM employees
WHERE name LIKE '%אבי%';
```

**שימושי:** מציאת שם בכל הטבלאות במערכת! 🔍

---

### תרחיש 3: יצירת דוח סיכום

```sql
-- סכום מכירות לפי חודש
SELECT 
    CONCAT('חודש ', MONTH(sale_date)) AS description,
    SUM(amount) AS value
FROM sales
WHERE YEAR(sale_date) = 2025
GROUP BY MONTH(sale_date)

UNION ALL

-- שורת סיכום
SELECT 
    'סה"כ' AS description,
    SUM(amount) AS value
FROM sales
WHERE YEAR(sale_date) = 2025;
```

---

### תרחיש 4: מציאת הבדלים בין טבלאות

רוצים לראות מי **נוסף** או **נמחק**?

```sql
-- לקוחות שנמצאים ב-2025 אבל לא ב-2024
SELECT name, 'חדש' AS status
FROM customers_2025
WHERE name NOT IN (SELECT name FROM customers_2024)

UNION ALL

-- לקוחות שנמצאים ב-2024 אבל לא ב-2025
SELECT name, 'נמחק' AS status
FROM customers_2024
WHERE name NOT IN (SELECT name FROM customers_2025);
```

**מושלם לניתוח שינויים!** 📈

---

## 🆚 UNION vs JOIN - מה ההבדל?

| | UNION | JOIN |
|---|-------|------|
| **כיוון** | מאנך ↕️ (שורות) | מאוזן ↔️ (עמודות) |
| **מה עושה?** | מאחד **שורות** משתי טבלאות | מאחד **עמודות** משתי טבלאות |
| **דוגמה** | כל הלקוחות מ-2024 + 2025 | לקוחות + ההזמנות שלהם |
| **מבנה טבלאות** | צריך להיות **דומה** | יכול להיות **שונה** |

**זכרו:**
- UNION = "עוד שורות" 📊
- JOIN = "עוד עמודות" 📈

---

## ⚡ טיפים לביצועים

### 1️⃣ השתמשו ב-UNION ALL כשאפשר

```sql
-- מהיר יותר! ✅
SELECT * FROM table1
UNION ALL
SELECT * FROM table2;

-- איטי יותר (בודק כפילויות) ⏱️
SELECT * FROM table1
UNION
SELECT * FROM table2;
```

---

### 2️⃣ סננו לפני האיחוד

```sql
-- טוב! ✅
SELECT name FROM customers WHERE city = 'תל אביב'
UNION
SELECT name FROM suppliers WHERE city = 'תל אביב';

-- לא טוב - מאחד הכל ואז מסנן ❌
SELECT name FROM 
(
    SELECT name FROM customers
    UNION
    SELECT name FROM suppliers
) AS combined
WHERE city = 'תל אביב';
```

---

### 3️⃣ השתמשו באינדקסים

וודאו שיש אינדקסים על העמודות שבהן אתם משתמשים ב-WHERE!

---

## 🎓 תרגילים להתנסות

### תרגיל 1: איחוד בסיסי
יש לכם שתי טבלאות: `products_store1` ו-`products_store2`.
אחדו אותם לרשימה אחת ייחודית.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT product_name, price 
FROM products_store1
UNION
SELECT product_name, price 
FROM products_store2;
```
</details>

---

### תרגיל 2: איחוד עם תיוג
אחדו את שתי הטבלאות מהתרגיל הקודם, אבל הוסיפו עמודה שמציינת מאיזה חנות המוצר.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT product_name, price, 'חנות 1' AS store 
FROM products_store1
UNION ALL
SELECT product_name, price, 'חנות 2' AS store 
FROM products_store2;
```
</details>

---

### תרגיל 3: דוח רבעוני
צרו דוח שמציג מכירות לפי רבעון + שורת סיכום.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 'רבעון 1' AS quarter, SUM(amount) AS total
FROM sales WHERE MONTH(sale_date) BETWEEN 1 AND 3
UNION ALL
SELECT 'רבעון 2', SUM(amount)
FROM sales WHERE MONTH(sale_date) BETWEEN 4 AND 6
UNION ALL
SELECT 'רבעון 3', SUM(amount)
FROM sales WHERE MONTH(sale_date) BETWEEN 7 AND 9
UNION ALL
SELECT 'רבעון 4', SUM(amount)
FROM sales WHERE MONTH(sale_date) BETWEEN 10 AND 12
UNION ALL
SELECT 'סה"כ שנתי', SUM(amount)
FROM sales;
```
</details>

---

## 🚨 טעויות נפוצות

### ❌ טעות 1: ORDER BY בשאילתה הראשונה

```sql
-- לא עובד! ❌
SELECT name FROM customers_2024 ORDER BY name
UNION
SELECT name FROM customers_2025;
```

✅ **פתרון:**
```sql
SELECT name FROM customers_2024
UNION
SELECT name FROM customers_2025
ORDER BY name;  -- בסוף!
```

---

### ❌ טעות 2: מספר עמודות שונה

```sql
-- לא עובד! ❌
SELECT name, age FROM customers
UNION
SELECT name FROM suppliers;
```

✅ **פתרון:**
```sql
SELECT name, age FROM customers
UNION
SELECT name, NULL AS age FROM suppliers;  -- NULL במקום age
```

---

### ❌ טעות 3: שימוש ב-UNION במקום UNION ALL

```sql
-- איטי מדי! ⏱️
SELECT * FROM huge_table1
UNION
SELECT * FROM huge_table2;
```

✅ **פתרון (אם לא צריך להסיר כפילויות):**
```sql
SELECT * FROM huge_table1
UNION ALL
SELECT * FROM huge_table2;  -- הרבה יותר מהיר!
```

---

## ❓ שאלות נפוצות

**Q: מתי להשתמש ב-UNION ומתי ב-JOIN?**
A: 
- UNION: כשרוצים לאחד **שורות** מטבלאות דומות
- JOIN: כשרוצים לחבר **מידע משלים** מטבלאות שונות

**Q: UNION עובד עם יותר מ-2 טבלאות?**
A: כן! אפשר לשרשר כמה שרוצים.

**Q: איך לסנן רק את אחת השאילתות?**
A: פשוט להוסיף WHERE לשאילתה הספציפית:
```sql
SELECT * FROM table1 WHERE status = 'active'
UNION ALL
SELECT * FROM table2;  -- בלי סינוח
```

**Q: UNION מאט את השאילתה?**
A: UNION (ללא ALL) כן, כי הוא בודק כפילויות. UNION ALL מהיר!

---

## 🎉 סיימתם!

עכשיו אתם יודעים:
✅ מה זה UNION וההבדל מ-UNION ALL  
✅ החוקים והמגבלות של UNION  
✅ איך להשתמש ב-UNION במצבים מעשיים  
✅ איך למנוע טעויות נפוצות  

**זכרו:** UNION ALL מהיר יותר - השתמשו בו כשלא צריך להסיר כפילויות! 🚀

---

**המשך למדריך הבא:** [מדריך GROUP BY](08_GROUP_BY_Guide.md)
