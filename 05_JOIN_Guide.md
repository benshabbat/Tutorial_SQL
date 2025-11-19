# 🔗 מדריך JOIN ב-SQL - צעד אחר צעד

## 📌 מה זה JOIN?

JOIN הוא פקודה שמאפשרת לנו לחבר מידע משתי טבלאות או יותר על בסיס עמודה משותפת.

דמיינו שיש לכם:
- טבלה של **לקוחות** (שם, מספר לקוח)
- טבלה של **הזמנות** (מספר הזמנה, מספר לקוח, סכום)

איך נדע איזה לקוח ביצע איזו הזמנה? 🤔 בעזרת JOIN!

---

## 🎯 סוגי JOIN - הסבר פשוט

### 1️⃣ INNER JOIN - החיתוך

**מתי נשתמש?** כשאנחנו רוצים רק את הרשומות שיש להן התאמה **בשתי הטבלאות**.

```sql
SELECT customers.name, orders.amount
FROM customers
INNER JOIN orders ON customers.customer_id = orders.customer_id;
```

**מה קורה פה?**
- `FROM customers` - מתחילים מטבלת הלקוחות
- `INNER JOIN orders` - מצטרפים לטבלת ההזמנות
- `ON customers.customer_id = orders.customer_id` - מתאימים לפי מספר לקוח

**תוצאה:** רק לקוחות שביצעו הזמנות! 🎯

---

### 2️⃣ LEFT JOIN - הכל משמאל

**מתי נשתמש?** כשאנחנו רוצים **את כל הרשומות מהטבלה השמאלית** + הרשומות התואמות מהטבלה הימנית.

```sql
SELECT customers.name, orders.amount
FROM customers
LEFT JOIN orders ON customers.customer_id = orders.customer_id;
```

**תוצאה:** כל הלקוחות (גם אלה שלא ביצעו הזמנות). אם לקוח לא ביצע הזמנה, `amount` יהיה NULL.

**דוגמה:**
```
שם הלקוח  | סכום ההזמנה
---------|-------------
אבי      | 500
בני      | NULL (לא הזמין)
גלי      | 750
```

---

### 3️⃣ RIGHT JOIN - הכל מימין

**מתי נשתמש?** כמו LEFT JOIN, אבל הפוך - **את כל הרשומות מהטבלה הימנית** + הרשומות התואמות מהשמאלית.

```sql
SELECT customers.name, orders.amount
FROM customers
RIGHT JOIN orders ON customers.customer_id = orders.customer_id;
```

**תוצאה:** כל ההזמנות (גם אם אין לנו פרטי לקוח).

> 💡 **טיפ:** LEFT JOIN נפוץ יותר מ-RIGHT JOIN!

---

### 4️⃣ FULL OUTER JOIN - הכל!

**מתי נשתמש?** כשאנחנו רוצים **את כל הרשומות משתי הטבלאות**.

```sql
SELECT customers.name, orders.amount
FROM customers
FULL OUTER JOIN orders ON customers.customer_id = orders.customer_id;
```

**תוצאה:** כל הלקוחות וכל ההזמנות, עם NULL במקומות שאין התאמה.

---

## 🎨 דוגמה מלאה - צעד אחר צעד

נניח שיש לנו שתי טבלאות:

**טבלת customers:**
```
customer_id | name
------------|-------
1           | אבי
2           | בני
3           | גלי
```

**טבלת orders:**
```
order_id | customer_id | amount
---------|-------------|--------
101      | 1           | 500
102      | 1           | 300
103      | 3           | 750
```

### דוגמה 1: כל ההזמנות עם שמות הלקוחות

```sql
SELECT 
    customers.name,
    orders.order_id,
    orders.amount
FROM customers
INNER JOIN orders ON customers.customer_id = orders.customer_id;
```

**תוצאה:**
```
name | order_id | amount
-----|----------|--------
אבי  | 101      | 500
אבי  | 102      | 300
גלי  | 103      | 750
```

⚠️ **שימו לב:** בני לא מופיע כי לא ביצע הזמנות!

---

### דוגמה 2: כל הלקוחות (כולל אלה שלא הזמינו)

```sql
SELECT 
    customers.name,
    orders.order_id,
    orders.amount
FROM customers
LEFT JOIN orders ON customers.customer_id = orders.customer_id;
```

**תוצאה:**
```
name | order_id | amount
-----|----------|--------
אבי  | 101      | 500
אבי  | 102      | 300
בני  | NULL     | NULL
גלי  | 103      | 750
```

✅ **עכשיו:** בני מופיע עם NULL!

---

## 🔧 טיפים מתקדמים

### 1. שימוש ב-Alias (כינויים)

במקום לכתוב את השם המלא של הטבלה בכל פעם:

```sql
SELECT 
    c.name,
    o.amount
FROM customers AS c
INNER JOIN orders AS o ON c.customer_id = o.customer_id;
```

**קצר יותר וקריא יותר!** 😊

---

### 2. JOIN של יותר מ-2 טבלאות

```sql
SELECT 
    c.name,
    o.order_id,
    p.product_name
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
INNER JOIN products p ON o.product_id = p.product_id;
```

**הסדר:** customers → orders → products

---

### 3. סינון תוצאות עם WHERE

```sql
SELECT 
    c.name,
    o.amount
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.amount > 400 OR o.amount IS NULL;
```

**תוצאה:** לקוחות עם הזמנות מעל 400 ₪ או בכלל ללא הזמנות.

---

### 4. איך למצוא לקוחות ללא הזמנות?

```sql
SELECT c.name
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;
```

**הטריק:** LEFT JOIN + WHERE IS NULL 🎯

---

## 📊 סיכום מהיר

| סוג JOIN | מה הוא מחזיר |
|----------|---------------|
| **INNER JOIN** | רק רשומות עם התאמה בשתי הטבלאות |
| **LEFT JOIN** | כל הרשומות מהטבלה השמאלית + התאמות מהימנית |
| **RIGHT JOIN** | כל הרשומות מהטבלה הימנית + התאמות מהשמאלית |
| **FULL OUTER JOIN** | כל הרשומות משתי הטבלאות |

---

## 🎓 תרגילים להתנסות

### תרגיל 1: הצגת שמות לקוחות וסכומי הזמנות
נסו לכתוב שאילתה שמציגה את כל הלקוחות והסכום הכולל של ההזמנות שלהם.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    c.name,
    SUM(o.amount) AS total_amount
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name;
```
</details>

---

### תרגיל 2: מציאת לקוחות VIP
הציגו לקוחות שביצעו יותר מ-3 הזמנות.

<details>
<summary>💡 פתרון</summary>

```sql
SELECT 
    c.name,
    COUNT(o.order_id) AS order_count
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name
HAVING COUNT(o.order_id) > 3;
```
</details>

---

## ❓ שאלות נפוצות

**Q: מה ההבדל בין INNER JOIN ל-WHERE?**
A: INNER JOIN מתאים לחיבור טבלאות. WHERE מסנן את התוצאות אחרי החיבור.

**Q: מתי להשתמש ב-LEFT JOIN ומתי ב-INNER JOIN?**
A: 
- INNER JOIN: כשצריך רק רשומות עם התאמה מלאה
- LEFT JOIN: כשרוצים לשמור את כל הרשומות מטבלה אחת

**Q: האם יש הבדל בין ביצועים?**
A: INNER JOIN בדרך כלל מהיר יותר כי מחזיר פחות נתונים.

---

## 🎉 סיימתם!

עכשיו אתם יודעים להשתמש ב-JOIN! 
התחילו עם INNER JOIN ו-LEFT JOIN - אלה המקרים הנפוצים ביותר.

**זכרו:** תרגול עושה את ההבדל! 💪

---

**המשך למדריך הבא:** [עבודה עם תאריכים ב-SQL](06_Date_Guide.md)
