# ⚙️ מדריך CRUD ב-SQL - צעד אחר צעד

## 📌 מה זה CRUD?

CRUD הם **4 הפעולות הבסיסיות** שאפשר לבצע על נתונים במסד נתונים:

- **C**reate - יצירה ➕
- **R**ead - קריאה 📖
- **U**pdate - עדכון ✏️
- **D**elete - מחיקה 🗑️

**זה הכל!** כל מה שאתם עושים עם נתונים נופל לאחת מהקטגוריות האלה. 🎯

---

## 1️⃣ CREATE - יצירה (INSERT)

### INSERT - הוספת שורה אחת

```sql
INSERT INTO customers (name, email, age, city)
VALUES ('אבי כהן', 'avi@example.com', 28, 'תל אביב');
```

**מה קרה?** הוספנו לקוח חדש למערכת! ✅

---

### INSERT - הוספת מספר שורות

```sql
INSERT INTO customers (name, email, age, city)
VALUES 
    ('בני לוי', 'benny@example.com', 35, 'חיפה'),
    ('גלי כהן', 'gali@example.com', 42, 'ירושלים'),
    ('דני אברהם', 'danny@example.com', 29, 'באר שבע');
```

**מעולה!** 3 לקוחות במשפט אחד! 🚀

---

### INSERT ללא ציון עמודות (לא מומלץ!)

```sql
-- אם הסדר תואם בדיוק את מבנה הטבלה
INSERT INTO customers
VALUES (5, 'הדס מזרחי', 'hadas@example.com', 31, 'אילת');
```

⚠️ **זהירות!** אם מבנה הטבלה ישתנה, זה יישבר!

✅ **מומלץ:** תמיד לציין את שמות העמודות!

---

### INSERT עם ערכי ברירת מחדל

```sql
-- אם יש ערך ברירת מחדל או AUTO_INCREMENT
INSERT INTO customers (name, email)
VALUES ('יוסי פרץ', 'yossi@example.com');
-- age יהיה NULL, city יהיה NULL, id יווצר אוטומטית
```

---

### INSERT מתוצאת SELECT

```sql
-- העתקת נתונים מטבלה אחת לשנייה
INSERT INTO customers_archive (name, email, age, city)
SELECT name, email, age, city
FROM customers
WHERE registration_date < '2020-01-01';
```

**שימושי:** גיבוי נתונים ישנים! 📦

---

### INSERT ... ON DUPLICATE KEY UPDATE (MySQL)

```sql
INSERT INTO products (product_id, name, price)
VALUES (101, 'מחשב נייד', 3500)
ON DUPLICATE KEY UPDATE 
    name = 'מחשב נייד',
    price = 3500;
```

**מה קורה?**
- אם `product_id = 101` לא קיים → יוצר חדש
- אם `product_id = 101` כבר קיים → מעדכן אותו

**מדהים!** 🎯

---

## 2️⃣ READ - קריאה (SELECT)

### SELECT בסיסי - הכל

```sql
SELECT * FROM customers;
```

**תוצאה:** כל העמודות וכל השורות! 📊

---

### SELECT עמודות ספציפיות

```sql
SELECT name, email, city FROM customers;
```

**תוצאה:** רק העמודות שביקשנו! 📝

---

### SELECT עם תנאים (WHERE)

```sql
SELECT name, age, city
FROM customers
WHERE age > 25 AND city = 'תל אביב';
```

**תוצאה:** רק לקוחות מתל אביב מעל גיל 25! 🎯

---

### SELECT עם מיון (ORDER BY)

```sql
SELECT name, age
FROM customers
ORDER BY age DESC;
```

**תוצאה:** לקוחות ממוינים מהגדול לקטן! ⬇️

---

### SELECT עם הגבלה (LIMIT)

```sql
SELECT name, email
FROM customers
ORDER BY registration_date DESC
LIMIT 10;
```

**תוצאה:** 10 הלקוחות האחרונים שנרשמו! 🔟

---

### SELECT עם קיבוץ (GROUP BY)

```sql
SELECT 
    city,
    COUNT(*) AS customer_count,
    AVG(age) AS avg_age
FROM customers
GROUP BY city
ORDER BY customer_count DESC;
```

**תוצאה:**
```
city       | customer_count | avg_age
-----------|----------------|--------
תל אביב    | 450            | 32.5
חיפה       | 280            | 35.2
ירושלים    | 190            | 38.1
```

---

### SELECT עם JOIN

```sql
SELECT 
    c.name,
    o.order_id,
    o.amount,
    o.order_date
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_date >= '2025-01-01'
ORDER BY o.order_date DESC;
```

**תוצאה:** לקוחות עם ההזמנות שלהם! 🔗

---

### SELECT עם תת-שאילתה

```sql
SELECT name, age
FROM customers
WHERE age > (SELECT AVG(age) FROM customers);
```

**תוצאה:** לקוחות מעל גיל הממוצע! 📈

---

## 3️⃣ UPDATE - עדכון

### UPDATE בסיסי

```sql
UPDATE customers
SET email = 'new_email@example.com'
WHERE customer_id = 5;
```

**מה קרה?** עדכנו את האימייל של לקוח מספר 5! ✏️

---

### ⚠️ אזהרה חשובה!

```sql
-- זה יעדכן את כל הלקוחות! 😱
UPDATE customers
SET city = 'תל אביב';
-- אין WHERE = כולם מתל אביב עכשיו!
```

**תמיד תשתמשו ב-WHERE עם UPDATE!** (אלא אם אתם באמת רוצים לעדכן הכל)

---

### UPDATE מספר עמודות

```sql
UPDATE customers
SET 
    email = 'updated@example.com',
    city = 'חיפה',
    age = 30
WHERE customer_id = 5;
```

**עדכנו 3 עמודות במשפט אחד!** 🎯

---

### UPDATE עם חישוב

```sql
-- העלאת מחירים ב-10%
UPDATE products
SET price = price * 1.10
WHERE category = 'Electronics';
```

**כל המוצרים באלקטרוניקה עלו ב-10%!** 📈

---

### UPDATE לפי תנאי

```sql
-- הנחה ללקוחות VIP
UPDATE customers
SET discount_rate = 15
WHERE total_purchases > 10000;
```

---

### UPDATE עם JOIN (MySQL)

```sql
UPDATE customers c
JOIN orders o ON c.customer_id = o.customer_id
SET c.last_order_date = o.order_date
WHERE o.order_id = (
    SELECT MAX(order_id) 
    FROM orders 
    WHERE customer_id = c.customer_id
);
```

**מתקדם!** עדכון לקוחות לפי ההזמנה האחרונה שלהם. 🔥

---

### UPDATE עם CASE

```sql
UPDATE employees
SET salary = CASE 
    WHEN performance_rating = 'excellent' THEN salary * 1.15
    WHEN performance_rating = 'good' THEN salary * 1.10
    WHEN performance_rating = 'average' THEN salary * 1.05
    ELSE salary
END
WHERE department = 'Sales';
```

**העלאות משכורת לפי ביצועים!** 💰

---

## 4️⃣ DELETE - מחיקה

### DELETE בסיסי

```sql
DELETE FROM customers
WHERE customer_id = 5;
```

**מה קרה?** מחקנו את לקוח מספר 5! 🗑️

---

### ⚠️ אזהרה חשובה!

```sql
-- זה ימחק את כל הלקוחות! 😱😱😱
DELETE FROM customers;
-- אין WHERE = הכל נמחק!
```

**תמיד תשתמשו ב-WHERE עם DELETE!** (אלא אם אתם באמת רוצים למחוק הכל)

---

### DELETE עם תנאי

```sql
-- מחיקת הזמנות ישנות
DELETE FROM orders
WHERE order_date < '2020-01-01'
  AND status = 'cancelled';
```

---

### DELETE עם JOIN (MySQL)

```sql
-- מחיקת לקוחות ללא הזמנות
DELETE c
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;
```

---

### DELETE עם LIMIT (זהירות!)

```sql
-- מחיקת 10 השורות הישנות ביותר
DELETE FROM logs
ORDER BY created_date
LIMIT 10;
```

---

### TRUNCATE - מחיקה מהירה של כל הטבלה

```sql
TRUNCATE TABLE logs;
```

**ההבדל מ-DELETE:**
- ⚡ **מהיר יותר** (לא רושם כל שורה בלוג)
- 🔄 **מאפס את AUTO_INCREMENT**
- ❌ **לא אפשר להשתמש ב-WHERE**
- ⚠️ **לא אפשר לבטל** (אין ROLLBACK במצבים מסוימים)

---

## 🔥 תרחישים מעשיים מלאים

### תרחיש 1: רישום לקוח חדש

```sql
-- שלב 1: הוספת לקוח
INSERT INTO customers (name, email, phone, city, registration_date)
VALUES ('שרה לוי', 'sara@example.com', '052-1234567', 'תל אביב', CURRENT_DATE);

-- שלב 2: קבלת ה-ID שנוצר
SELECT LAST_INSERT_ID() AS new_customer_id;

-- שלב 3: יצירת הזמנה ראשונה
INSERT INTO orders (customer_id, amount, status, order_date)
VALUES (LAST_INSERT_ID(), 299.90, 'pending', CURRENT_TIMESTAMP);
```

---

### תרחיש 2: עדכון מלאי אחרי מכירה

```sql
-- שלב 1: בדיקת מלאי
SELECT stock_quantity 
FROM products 
WHERE product_id = 101;

-- שלב 2: עדכון מלאי
UPDATE products
SET 
    stock_quantity = stock_quantity - 5,
    last_updated = CURRENT_TIMESTAMP
WHERE product_id = 101
  AND stock_quantity >= 5;  -- ודא שיש מספיק מלאי!

-- שלב 3: רישום המכירה
INSERT INTO sales (product_id, quantity, sale_date)
VALUES (101, 5, CURRENT_TIMESTAMP);
```

---

### תרחיש 3: מחיקת חשבון לקוח (עם כל הנתונים הקשורים)

```sql
-- שלב 1: מחיקת הזמנות
DELETE FROM orders
WHERE customer_id = 123;

-- שלב 2: מחיקת ביקורות
DELETE FROM reviews
WHERE customer_id = 123;

-- שלב 3: מחיקת העגלה
DELETE FROM cart_items
WHERE customer_id = 123;

-- שלב 4: מחיקת הלקוח עצמו
DELETE FROM customers
WHERE customer_id = 123;
```

**טוב יותר:** השתמשו ב-`ON DELETE CASCADE` בהגדרת הטבלאות!

---

### תרחיש 4: העברת לקוחות לארכיון

```sql
-- שלב 1: העתקה לארכיון
INSERT INTO customers_archive
SELECT * 
FROM customers
WHERE last_order_date < DATE_SUB(CURRENT_DATE, INTERVAL 2 YEAR);

-- שלב 2: מחיקה מהטבלה הראשית
DELETE FROM customers
WHERE last_order_date < DATE_SUB(CURRENT_DATE, INTERVAL 2 YEAR);

-- שלב 3: אישור
SELECT 
    (SELECT COUNT(*) FROM customers) AS active_customers,
    (SELECT COUNT(*) FROM customers_archive) AS archived_customers;
```

---

### תרחיש 5: עדכון מחירים בהמוני

```sql
-- העלאת מחירים לפי קטגוריה
UPDATE products
SET 
    old_price = price,
    price = CASE 
        WHEN category = 'Electronics' THEN price * 1.15
        WHEN category = 'Clothing' THEN price * 1.08
        WHEN category = 'Books' THEN price * 1.05
        ELSE price
    END,
    last_price_update = CURRENT_TIMESTAMP
WHERE active = 1;
```

---

## 🛡️ טיפי אבטחה וזהירות

### 1️⃣ תמיד השתמשו ב-WHERE!

```sql
-- מסוכן! ❌
UPDATE customers SET discount = 20;
DELETE FROM orders;

-- בטוח! ✅
UPDATE customers SET discount = 20 WHERE customer_id = 5;
DELETE FROM orders WHERE order_id = 123;
```

---

### 2️⃣ בדקו לפני מחיקה/עדכון

```sql
-- קודם תבדקו מה אתם הולכים למחוק
SELECT * FROM customers WHERE customer_id = 5;

-- אם זה נכון, אז מחקו
DELETE FROM customers WHERE customer_id = 5;
```

---

### 3️⃣ השתמשו ב-TRANSACTIONS

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

-- אם הכל טוב:
COMMIT;

-- אם היתה בעיה:
ROLLBACK;
```

**Transaction מבטיחה:** או הכל מצליח, או הכל מתבטל! 🔒

---

### 4️⃣ גיבויים!

```sql
-- לפני עדכון/מחיקה גדולה, תעשו גיבוי
CREATE TABLE customers_backup AS
SELECT * FROM customers;

-- עכשיו בטוח לבצע שינויים
DELETE FROM customers WHERE inactive = 1;

-- אם הכל טוב, מחקו את הגיבוי
DROP TABLE customers_backup;
```

---

### 5️⃣ הגבלת הרשאות

```sql
-- תנו למשתמשים רק את ההרשאות שהם צריכים
GRANT SELECT ON database.customers TO 'readonly_user'@'localhost';
GRANT SELECT, INSERT, UPDATE ON database.orders TO 'sales_user'@'localhost';
GRANT ALL PRIVILEGES ON database.* TO 'admin_user'@'localhost';
```

---

## 🚨 טעויות נפוצות

### ❌ טעות 1: INSERT ללא ערכים לכל העמודות הנדרשות

```sql
-- אם age הוא NOT NULL, זה ייכשל! ❌
INSERT INTO customers (name, email)
VALUES ('משה', 'moshe@example.com');
```

✅ **פתרון:**
```sql
INSERT INTO customers (name, email, age)
VALUES ('משה', 'moshe@example.com', 25);
```

---

### ❌ טעות 2: UPDATE/DELETE ללא WHERE

```sql
-- עדכן את כולם! 😱
UPDATE products SET price = 100;
```

✅ **פתרון:** תמיד תוסיפו WHERE!

---

### ❌ טעות 3: INSERT עם ID ידני כש-AUTO_INCREMENT פעיל

```sql
-- אל תעשו את זה! ❌
INSERT INTO customers (customer_id, name, email)
VALUES (999, 'דני', 'danny@example.com');
```

✅ **פתרון:** תנו ל-AUTO_INCREMENT לעשות את העבודה:
```sql
INSERT INTO customers (name, email)
VALUES ('דני', 'danny@example.com');
```

---

### ❌ טעות 4: עדכון עמודה לפי עצמה ללא הבנה

```sql
-- לא יעבוד כמצופה!
UPDATE products
SET price = price + 100,
    old_price = price;  -- זה יהיה המחיר החדש, לא הישן!
```

✅ **פתרון:**
```sql
UPDATE products
SET 
    old_price = price,      -- קודם שומר את הישן
    price = price + 100;    -- אחר כך מעדכן
```

---

## 🎓 תרגילים להתנסות

### תרגיל 1: הוספת מוצר חדש
הוסיפו מוצר חדש לטבלת products עם שם, מחיר, קטגוריה ומלאי.

<details>
<summary>💡 פתרון</summary>

```sql
INSERT INTO products (name, price, category, stock_quantity)
VALUES ('אוזניות בלוטוס', 299.90, 'Electronics', 50);
```
</details>

---

### תרגיל 2: עדכון מחיר
העלו את מחיר כל המוצרים בקטגוריה 'Books' ב-5%.

<details>
<summary>💡 פתרון</summary>

```sql
UPDATE products
SET price = price * 1.05
WHERE category = 'Books';
```
</details>

---

### תרגיל 3: מחיקת הזמנות ישנות
מחקו הזמנות שבוטלו לפני יותר משנה.

<details>
<summary>💡 פתרון</summary>

```sql
DELETE FROM orders
WHERE status = 'cancelled'
  AND order_date < DATE_SUB(CURRENT_DATE, INTERVAL 1 YEAR);
```
</details>

---

### תרגיל 4: העתקת נתונים
העתיקו את כל המוצרים שאזלו מהמלאי לטבלת out_of_stock.

<details>
<summary>💡 פתרון</summary>

```sql
INSERT INTO out_of_stock (product_id, name, category, last_available)
SELECT product_id, name, category, CURRENT_DATE
FROM products
WHERE stock_quantity = 0;
```
</details>

---

## 💡 טיפים מקצועיים

### 1️⃣ RETURNING (PostgreSQL) - קבלת ערכים מ-INSERT

```sql
INSERT INTO customers (name, email)
VALUES ('חנה', 'hana@example.com')
RETURNING customer_id, created_at;
```

**מקבלים מיד את ה-ID שנוצר!** 🎯

---

### 2️⃣ REPLACE (MySQL) - החלפה או הוספה

```sql
REPLACE INTO products (product_id, name, price)
VALUES (101, 'מקלדת', 150);
```

**אם קיים - מחליף. אם לא - מוסיף.** 🔄

---

### 3️⃣ UPSERT (INSERT ... ON CONFLICT) - PostgreSQL

```sql
INSERT INTO products (product_id, name, price)
VALUES (101, 'מקלדת', 150)
ON CONFLICT (product_id) 
DO UPDATE SET 
    name = EXCLUDED.name,
    price = EXCLUDED.price;
```

**הפקודה החכמה ביותר!** 🧠

---

### 4️⃣ SOFT DELETE - מחיקה רכה

במקום למחוק, סמנו כמחוק:

```sql
-- במקום DELETE
UPDATE customers
SET 
    deleted = 1,
    deleted_at = CURRENT_TIMESTAMP
WHERE customer_id = 5;

-- בשאילתות, תמיד תוסיפו:
SELECT * FROM customers WHERE deleted = 0;
```

**יתרון:** אפשר לשחזר! 🔄

---

## 📊 טבלת סיכום - CRUD

| פעולה | פקודה SQL | דוגמה | מה עושה |
|-------|-----------|--------|---------|
| **Create** | `INSERT INTO` | `INSERT INTO customers VALUES (...)` | מוסיף שורות חדשות |
| **Read** | `SELECT` | `SELECT * FROM customers WHERE...` | קורא נתונים |
| **Update** | `UPDATE` | `UPDATE customers SET... WHERE...` | מעדכן שורות קיימות |
| **Delete** | `DELETE FROM` | `DELETE FROM customers WHERE...` | מוחק שורות |

---

## ❓ שאלות נפוצות

**Q: מה קורה אם אשכח WHERE ב-UPDATE/DELETE?**
A: 😱 כל השורות יעודכנו/יימחקו! תמיד תבדקו עם SELECT לפני!

**Q: איך לדעת כמה שורות עודכנו/נמחקו?**
A: רוב המערכות מחזירות "X rows affected". אפשר גם:
```sql
SELECT ROW_COUNT();  -- MySQL
```

**Q: האם אפשר לבטל DELETE/UPDATE?**
A: כן! אם אתם בתוך TRANSACTION ולא עשיתם COMMIT:
```sql
ROLLBACK;  -- מבטל את כל השינויים
```

**Q: מה ההבדל בין DELETE ל-TRUNCATE?**
A: 
- DELETE: איטי יותר, עם WHERE, ניתן לבטל
- TRUNCATE: מהיר, הכל, קשה לבטל, מאפס AUTO_INCREMENT

---

## 🎉 סיימתם!

עכשיו אתם יודעים:
✅ איך ליצור נתונים חדשים (INSERT)  
✅ איך לקרוא נתונים (SELECT)  
✅ איך לעדכן נתונים (UPDATE)  
✅ איך למחוק נתונים (DELETE)  
✅ איך לעבוד בצורה בטוחה עם TRANSACTIONS  
✅ טעויות נפוצות ואיך להימנע מהן  

**CRUD הם 4 הפעולות שמרכיבות את כל העבודה עם מסדי נתונים!** 💪

---

**חזרה למדריך הקודם:** [מדריך תנאים](11_Conditions_Guide.md)
