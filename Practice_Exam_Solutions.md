# פתרונות מלאים - SQL Practice Exam Solutions
## חנות אינטרנטית - E-Commerce Database

---

## חלק א': פתרונות לשאלות בסיסיות

### פתרון שאלה 1
```sql
SELECT 
    order_id, 
    customer_name, 
    product_name, 
    price_per_unit
FROM orders
WHERE product_category = 'Electronics';
```

**הסבר:** שאילתת SELECT בסיסית עם תנאי WHERE לסינון קטגוריה ספציפית.

---

### פתרון שאלה 2
```sql
SELECT DISTINCT customer_name
FROM orders
WHERE payment_method = 'PayPal'
ORDER BY customer_name;
```

**הסבר:** שימוש ב-DISTINCT למניעת כפילויות, סינון לפי אמצעי תשלום, מיון אלפביתי.

---

### פתרון שאלה 3
```sql
SELECT 
    product_category,
    COUNT(*) AS total_orders
FROM orders
GROUP BY product_category
ORDER BY total_orders DESC;
```

**הסבר:** שימוש ב-GROUP BY לקיבוץ לפי קטגוריה ו-COUNT לספירת הזמנות.

---

### פתרון שאלה 4
```sql
SELECT 
    order_id,
    customer_name,
    order_date,
    product_name
FROM orders
WHERE order_date BETWEEN '2024-02-01' AND '2024-02-29';

-- או:
WHERE strftime('%Y-%m', order_date) = '2024-02';
```

**הסבר:** שימוש ב-BETWEEN או בפונקציית תאריך לסינון לפי חודש ספציפי.

---

### פתרון שאלה 5
```sql
SELECT 
    product_category,
    ROUND(AVG(price_per_unit), 2) AS avg_price
FROM orders
GROUP BY product_category;
```

**הסבר:** שימוש ב-AVG לחישוב ממוצע ו-ROUND לעיגול לשתי ספרות.

---

## חלק ב': פתרונות לשאלות ביניים

### פתרון שאלה 6
```sql
SELECT 
    c.customer_name,
    c.email,
    SUM(o.quantity * o.price_per_unit) AS total_spent
FROM orders o
JOIN customers c ON o.customer_name = c.customer_name
GROUP BY c.customer_name, c.email
ORDER BY total_spent DESC
LIMIT 5;
```

**הסבר:** JOIN בין שתי הטבלאות, חישוב סכום כולל, קיבוץ והגבלה ל-5.

---

### פתרון שאלה 7
```sql
SELECT 
    c.loyalty_tier,
    COUNT(DISTINCT c.customer_id) AS customer_count,
    SUM(o.quantity * o.price_per_unit) AS total_revenue,
    ROUND(COUNT(o.order_id) * 1.0 / COUNT(DISTINCT c.customer_id), 2) AS avg_orders_per_customer
FROM customers c
JOIN orders o ON c.customer_name = o.customer_name
GROUP BY c.loyalty_tier
ORDER BY total_revenue DESC;
```

**הסבר:** קיבוץ לפי Loyalty Tier עם חישובים סטטיסטיים מורכבים.

---

### פתרון שאלה 8
```sql
SELECT 
    strftime('%Y-%m', order_date) AS month,
    SUM(quantity * price_per_unit) AS monthly_revenue
FROM orders
GROUP BY strftime('%Y-%m', order_date)
ORDER BY month;
```

**הסבר:** שימוש בפונקציית strftime לחילוץ שנה-חודש, קיבוץ וסכימה.

---

### פתרון שאלה 9
```sql
SELECT 
    customer_name,
    COUNT(*) AS number_of_orders,
    SUM(quantity * price_per_unit) AS total_spent
FROM orders
GROUP BY customer_name
HAVING COUNT(*) > 3
ORDER BY number_of_orders DESC;
```

**הסבר:** שימוש ב-HAVING לסינון אחרי קיבוץ (לקוחות עם יותר מ-3 הזמנות).

---

### פתרון שאלה 10
```sql
-- גרסה 1: עם LEFT JOIN
SELECT 
    c.customer_name,
    c.email,
    c.loyalty_tier,
    MAX(o.order_date) AS last_order_date
FROM customers c
LEFT JOIN orders o ON c.customer_name = o.customer_name 
    AND strftime('%Y-%m', o.order_date) = '2024-04'
WHERE c.loyalty_tier = 'Platinum'
    AND o.order_id IS NULL
GROUP BY c.customer_name, c.email, c.loyalty_tier;

-- גרסה 2: עם NOT EXISTS
SELECT 
    c.customer_name,
    c.email,
    c.loyalty_tier,
    (SELECT MAX(order_date) FROM orders WHERE customer_name = c.customer_name) AS last_order_date
FROM customers c
WHERE c.loyalty_tier = 'Platinum'
    AND NOT EXISTS (
        SELECT 1 
        FROM orders o 
        WHERE o.customer_name = c.customer_name
        AND strftime('%Y-%m', o.order_date) = '2024-04'
    );
```

**הסבר:** זיהוי לקוחות Platinum ללא הזמנות באפריל.

---

## חלק ג': פתרונות לשאלות מתקדמות

### פתרון שאלה 11
```sql
SELECT 
    CASE 
        WHEN c.registration_date >= '2023-07-01' THEN 'New Customer'
        ELSE 'Veteran Customer'
    END AS customer_type,
    COUNT(DISTINCT c.customer_id) AS customer_count,
    SUM(o.quantity * o.price_per_unit) AS total_revenue,
    ROUND(SUM(o.quantity * o.price_per_unit) / COUNT(DISTINCT c.customer_id), 2) AS avg_revenue_per_customer
FROM customers c
JOIN orders o ON c.customer_name = o.customer_name
GROUP BY customer_type;
```

**הסבר:** שימוש ב-CASE לחלוקת לקוחות לקבוצות והשוואה סטטיסטית.

---

### פתרון שאלה 12
```sql
WITH CustomerCategorySpending AS (
    SELECT 
        c.customer_name,
        o.product_category,
        SUM(o.quantity * o.price_per_unit) AS category_spending,
        SUM(SUM(o.quantity * o.price_per_unit)) OVER (PARTITION BY c.customer_name) AS total_customer_spending
    FROM customers c
    JOIN orders o ON c.customer_name = o.customer_name
    GROUP BY c.customer_name, o.product_category
),
RankedCategories AS (
    SELECT 
        customer_name,
        product_category,
        category_spending,
        total_customer_spending,
        ROW_NUMBER() OVER (PARTITION BY customer_name ORDER BY category_spending DESC) AS rn
    FROM CustomerCategorySpending
)
SELECT 
    customer_name,
    product_category AS top_category,
    category_spending,
    ROUND((category_spending * 100.0 / total_customer_spending), 2) AS percentage_of_total
FROM RankedCategories
WHERE rn = 1
ORDER BY category_spending DESC;
```

**הסבר:** שימוש ב-CTEs ו-Window Functions למציאת הקטגוריה המובילה לכל לקוח.

---

### פתרון שאלה 13
```sql
SELECT 
    c.customer_name,
    c.email,
    c.loyalty_tier,
    COUNT(DISTINCT o.product_category) AS number_of_categories,
    COUNT(o.order_id) AS total_orders
FROM customers c
JOIN orders o ON c.customer_name = o.customer_name
GROUP BY c.customer_name, c.email, c.loyalty_tier
HAVING COUNT(DISTINCT o.product_category) >= 3
ORDER BY number_of_categories DESC;
```

**הסבר:** ספירת קטגוריות ייחודיות עם JOIN בין הטבלאות.

---

### פתרון שאלה 14
```sql
WITH CustomerRevenue AS (
    SELECT 
        c.loyalty_tier,
        c.customer_name,
        c.email,
        SUM(o.quantity * o.price_per_unit) AS total_revenue,
        RANK() OVER (
            PARTITION BY c.loyalty_tier 
            ORDER BY SUM(o.quantity * o.price_per_unit) DESC
        ) AS rank_in_tier
    FROM customers c
    JOIN orders o ON c.customer_name = o.customer_name
    GROUP BY c.loyalty_tier, c.customer_name, c.email
)
SELECT 
    loyalty_tier,
    customer_name,
    email,
    total_revenue,
    rank_in_tier
FROM CustomerRevenue
WHERE rank_in_tier <= 3
ORDER BY loyalty_tier, rank_in_tier;
```

**הסבר:** דירוג לקוחות בתוך כל Loyalty Tier באמצעות RANK() ו-PARTITION BY.

---

## חלק ד': פתרונות לשאלות אתגר

### פתרון שאלה 15
```sql
SELECT 
    c.customer_name,
    c.email,
    c.total_lifetime_orders AS recorded_orders,
    COUNT(o.order_id) AS actual_orders,
    c.total_lifetime_orders - COUNT(o.order_id) AS difference
FROM customers c
LEFT JOIN orders o ON c.customer_name = o.customer_name
GROUP BY c.customer_id, c.customer_name, c.email, c.total_lifetime_orders
HAVING c.total_lifetime_orders != COUNT(o.order_id)
ORDER BY ABS(difference) DESC;
```

**הסבר:** בדיקת Data Quality - השוואה בין נתונים רשומים לספירה אמיתית.

---

### פתרון שאלה 16
```sql
WITH MonthlyCustomers AS (
    SELECT DISTINCT
        strftime('%Y-%m', order_date) AS month,
        customer_name
    FROM orders
    WHERE strftime('%Y-%m', order_date) BETWEEN '2024-02' AND '2024-04'
),
CustomersByMonth AS (
    SELECT 
        month,
        COUNT(DISTINCT customer_name) AS customers_this_month
    FROM MonthlyCustomers
    GROUP BY month
),
ReturningCustomers AS (
    SELECT 
        curr.month,
        COUNT(DISTINCT curr.customer_name) AS returning_customers
    FROM MonthlyCustomers curr
    JOIN MonthlyCustomers prev 
        ON curr.customer_name = prev.customer_name
        AND date(curr.month || '-01', '-1 month') = date(prev.month || '-01')
    GROUP BY curr.month
)
SELECT 
    cm.month,
    cm.customers_this_month,
    COALESCE(rc.returning_customers, 0) AS returning_customers,
    ROUND(COALESCE(rc.returning_customers * 100.0 / cm.customers_this_month, 0), 2) AS retention_rate
FROM CustomersByMonth cm
LEFT JOIN ReturningCustomers rc ON cm.month = rc.month
ORDER BY cm.month;
```

**הסבר:** ניתוח Retention - זיהוי לקוחות שקנו גם בחודש הקודם באמצעות Self-Join.

---

## בונוס - פתרון שאלה 17

### פתרון שאלה BONUS
```sql
WITH RegistrationCohorts AS (
    SELECT 
        strftime('%Y-%m', registration_date) AS registration_month,
        customer_name,
        registration_date
    FROM customers
),
OrdersWithCohort AS (
    SELECT 
        rc.registration_month,
        rc.customer_name,
        o.order_date,
        CAST((julianday(o.order_date) - julianday(rc.registration_date)) / 30 AS INTEGER) AS month_since_registration
    FROM RegistrationCohorts rc
    LEFT JOIN orders o ON rc.customer_name = o.customer_name
)
SELECT 
    registration_month,
    COUNT(DISTINCT customer_name) AS total_customers,
    COUNT(DISTINCT CASE WHEN month_since_registration = 0 THEN customer_name END) AS active_month_1,
    COUNT(DISTINCT CASE WHEN month_since_registration = 1 THEN customer_name END) AS active_month_2,
    COUNT(DISTINCT CASE WHEN month_since_registration = 2 THEN customer_name END) AS active_month_3
FROM (
    SELECT DISTINCT
        registration_month,
        customer_name
    FROM RegistrationCohorts
) cohorts
LEFT JOIN OrdersWithCohort owc USING (registration_month, customer_name)
GROUP BY registration_month
ORDER BY registration_month;
```

**הסבר:** ניתוח Cohort מתקדם - מעקב אחר התנהגות לקוחות לפי חודש הרשמה.

---

## טיפים נוספים למבחן:

### 1. **אופטימיזציה של שאילתות**
```sql
-- במקום:
SELECT * FROM orders WHERE product_category = 'Electronics';

-- עדיף:
SELECT order_id, customer_name, product_name 
FROM orders 
WHERE product_category = 'Electronics';
```

### 2. **שימוש ב-WITH (CTE) לקריאות**
```sql
WITH MonthlySales AS (
    SELECT 
        strftime('%Y-%m', order_date) AS month,
        SUM(quantity * price_per_unit) AS revenue
    FROM orders
    GROUP BY month
)
SELECT * FROM MonthlySales WHERE revenue > 10000;
```

### 3. **טיפול ב-NULL values**
```sql
SELECT 
    customer_name,
    COALESCE(phone_number, 'No Phone') AS contact
FROM customers;
```

### 4. **שימוש נכון ב-HAVING vs WHERE**
- **WHERE** - מסנן שורות לפני קיבוץ
- **HAVING** - מסנן קבוצות אחרי קיבוץ

```sql
-- נכון:
SELECT customer_name, COUNT(*) as orders
FROM orders
WHERE order_date >= '2024-01-01'  -- WHERE לפני GROUP BY
GROUP BY customer_name
HAVING COUNT(*) > 5;  -- HAVING אחרי GROUP BY
```

---

## פונקציות שימושיות שכדאי לזכור:

### פונקציות אגרגציה:
- `COUNT()` - ספירה
- `SUM()` - סכום
- `AVG()` - ממוצע
- `MIN()` - מינימום
- `MAX()` - מקסימום

### פונקציות מחרוזות:
- `UPPER()` / `LOWER()` - המרה לאותיות גדולות/קטנות
- `SUBSTR()` - חילוץ תת-מחרוזת
- `LENGTH()` - אורך מחרוזת
- `TRIM()` - הסרת רווחים

### פונקציות תאריך:
- `DATE()` - חילוץ תאריך
- `strftime()` - פורמט תאריך מותאם אישית
- `julianday()` - המרה למספר ימים

### Window Functions:
- `ROW_NUMBER()` - מספור שורות
- `RANK()` - דירוג עם דילוגים
- `DENSE_RANK()` - דירוג רצוף
- `PARTITION BY` - חלוקה לקבוצות

---

**בהצלחה במבחן!** 🎯
