# SECTION 4: SQL 技能题 (Q46–Q70)

---

**Q46. WHERE 和 HAVING 子句有什么区别？分别在什么情况下使用？**

请解释两者的区别，并给出一个示例说明何时必须用 HAVING 而不能用 WHERE。

**参考答案要点**：

核心区别：
- **WHERE**：在**分组之前**过滤行，作用于原始行数据，不能使用聚合函数（SUM, COUNT, AVG 等）
- **HAVING**：在**分组之后**过滤组，作用于聚合结果，可以使用聚合函数

执行顺序：`FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY`

示例场景：
```sql
-- 找出2024年销售额超过100万的销售员
SELECT salesperson_id, SUM(amount) AS total_sales
FROM orders
WHERE order_year = 2024         -- WHERE：先过滤年份（行级别）
GROUP BY salesperson_id
HAVING SUM(amount) > 1000000;   -- HAVING：再过滤聚合结果（组级别）
```
这里不能把 `SUM(amount) > 1000000` 写在 WHERE 中，因为 SUM 在 WHERE 执行时还未计算。

WHERE 中可以使用的条件：原始列值、非聚合条件（如日期、状态、字符串）
HAVING 中可以使用的条件：聚合结果（COUNT、SUM、AVG 等）及 GROUP BY 中包含的列

*参考考察点: SQL执行顺序理解、聚合过滤逻辑*

---

**Q47. SQL 中有哪几种 JOIN 类型？分别返回什么结果？请用文氏图解释。**

请解释 INNER JOIN、LEFT JOIN、RIGHT JOIN 和 FULL OUTER JOIN 的区别，并各举一个业务场景。

**参考答案要点**：

假设有两张表：`users`（用户表）和 `orders`（订单表），通过 `user_id` 关联。

- **INNER JOIN（内连接）**：
  - 返回两表中**都存在匹配记录**的行
  - 结果：只有下过订单的用户才会出现
  ```sql
  SELECT u.name, o.amount FROM users u INNER JOIN orders o ON u.user_id = o.user_id;
  ```

- **LEFT JOIN（左连接）**：
  - 返回左表**所有行**，右表无匹配则填 NULL
  - 结果：所有用户都出现，没有订单的用户 amount 为 NULL
  ```sql
  SELECT u.name, o.amount FROM users u LEFT JOIN orders o ON u.user_id = o.user_id;
  ```
  常用场景：找出未下单的用户（`WHERE o.user_id IS NULL`）

- **RIGHT JOIN（右连接）**：
  - 返回右表**所有行**，左表无匹配则填 NULL（较少使用，通常改写为 LEFT JOIN）

- **FULL OUTER JOIN（全连接）**：
  - 返回两表中**所有行**，无论是否有匹配，无匹配部分填 NULL
  - MySQL 不支持，需用 `LEFT JOIN UNION RIGHT JOIN` 模拟

- **CROSS JOIN（笛卡尔积）**：返回所有可能的行组合（M行 × N行），通常需要避免

*参考考察点: SQL JOIN类型、数据关联逻辑、NULL处理*

---

**Q48. UNION 和 UNION ALL 的区别是什么？各在什么场景下使用？**

**参考答案要点**：

- **UNION**：合并两个查询的结果，**自动去除重复行**（需要额外的排序和比较操作，性能较慢）
- **UNION ALL**：合并两个查询的结果，**保留所有行（含重复）**，性能更好

使用要求：两个 SELECT 语句必须有相同数量的列且对应列数据类型兼容

```sql
-- UNION：合并并去重
SELECT user_id FROM app_users
UNION
SELECT user_id FROM web_users;

-- UNION ALL：合并保留全部（包含在两端都出现的用户）
SELECT user_id, 'app' AS source FROM app_users
UNION ALL
SELECT user_id, 'web' AS source FROM web_users;
```

使用场景：
- **UNION**：需要合并两张表的数据并去重时（如合并两个来源的客户名单）
- **UNION ALL**：需要保留所有记录（含重复）时，或明确知道两个结果集无重叠时（性能优先）

*参考考察点: 集合操作、去重逻辑、查询性能意识*

---

**Q49. 请解释 GROUP BY 的工作原理，并写一个查询：统计每个城市的用户数量，按用户数降序排列，只显示用户数超过 100 的城市。**

表结构：`users(user_id, name, city, register_date)`

**参考答案**：
```sql
SELECT
    city,
    COUNT(user_id) AS user_count
FROM users
GROUP BY city
HAVING COUNT(user_id) > 100
ORDER BY user_count DESC;
```

工作原理说明：
1. `FROM users`：读取全表
2. `GROUP BY city`：将所有行按城市分组，每个城市成为一组
3. `COUNT(user_id)`：对每组计算用户数
4. `HAVING COUNT(...) > 100`：过滤掉用户数 ≤ 100 的城市
5. `ORDER BY user_count DESC`：按用户数降序排列

*参考考察点: GROUP BY与聚合函数、HAVING过滤、ORDER BY排序*

---

**Q50. 写一个 SQL 查询：找出在 orders 表中没有下过单的用户（来自 users 表）。提供两种写法。**

表结构：`users(user_id, name)` 和 `orders(order_id, user_id, amount, order_date)`

**参考答案**：

方法一：LEFT JOIN + IS NULL
```sql
SELECT u.user_id, u.name
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
WHERE o.user_id IS NULL;
```

方法二：NOT IN 子查询
```sql
SELECT user_id, name
FROM users
WHERE user_id NOT IN (
    SELECT DISTINCT user_id FROM orders
);
```
注意：若 orders 中 user_id 有 NULL 值，NOT IN 会返回空结果集（SQL 的 NULL 传播问题），需加 `WHERE user_id IS NOT NULL`

方法三：NOT EXISTS
```sql
SELECT u.user_id, u.name
FROM users u
WHERE NOT EXISTS (
    SELECT 1 FROM orders o WHERE o.user_id = u.user_id
);
```
性能：通常 `LEFT JOIN + IS NULL` 或 `NOT EXISTS` 比 `NOT IN` 性能更好，且对 NULL 更安全

*参考考察点: 反连接模式、NULL处理、多种等价写法性能对比*

---

**Q51. 什么是窗口函数（Window Function）？请解释 ROW_NUMBER()、RANK() 和 DENSE_RANK() 的区别，并举例说明。**

**参考答案要点**：

窗口函数定义：
- 窗口函数在不压缩行数的情况下，对每一行执行基于"窗口"（相关行集合）的计算
- 语法：`函数名() OVER (PARTITION BY ... ORDER BY ...)`

三种排名函数区别（假设成绩：90, 90, 85, 80）：

| 名次 | ROW_NUMBER() | RANK() | DENSE_RANK() |
|------|-------------|--------|-------------|
| 90 | 1 | 1 | 1 |
| 90 | 2 | 1 | 1 |
| 85 | 3 | 3 | 2 |
| 80 | 4 | 4 | 3 |

- `ROW_NUMBER()`：无论是否相同，每行分配唯一连续编号（并列时结果不确定，取决于顺序）
- `RANK()`：相同值相同排名，但跳过后续序号（1,1,3,4）
- `DENSE_RANK()`：相同值相同排名，不跳过序号（1,1,2,3）

示例：按销售额对每个区域的销售员排名
```sql
SELECT
    salesperson_id,
    region,
    total_sales,
    RANK() OVER (PARTITION BY region ORDER BY total_sales DESC) AS rank_in_region
FROM sales_summary;
```

*参考考察点: 窗口函数基础、排名函数区别、PARTITION BY理解*

---

**Q52. 写一个 SQL 查询：使用窗口函数找出每个部门薪资最高的员工。**

表结构：`employees(emp_id, name, department, salary)`

**参考答案**：

方法一：ROW_NUMBER() 窗口函数
```sql
WITH ranked AS (
    SELECT
        emp_id,
        name,
        department,
        salary,
        ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS rn
    FROM employees
)
SELECT emp_id, name, department, salary
FROM ranked
WHERE rn = 1;
```

方法二：RANK()（若允许并列第一）
```sql
WITH ranked AS (
    SELECT
        emp_id, name, department, salary,
        RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rnk
    FROM employees
)
SELECT emp_id, name, department, salary
FROM ranked
WHERE rnk = 1;
```
区别：ROW_NUMBER() 每部门只返回1人（随机选并列者之一），RANK() 会返回所有并列第一的员工

方法三：子查询（传统写法）
```sql
SELECT e.emp_id, e.name, e.department, e.salary
FROM employees e
WHERE e.salary = (
    SELECT MAX(salary) FROM employees WHERE department = e.department
);
```

*参考考察点: 窗口函数PARTITION BY、CTE（WITH子句）、Top-N-per-Group模式*

---

**Q53. 什么是 CTE（Common Table Expression）？它与子查询相比有什么优势？**

**参考答案要点**：

CTE 定义：
- CTE 是一种临时的命名结果集，使用 `WITH` 关键字定义，仅在当前查询中可用
- 语法：
```sql
WITH cte_name AS (
    SELECT ...
)
SELECT * FROM cte_name WHERE ...;
```

CTE vs. 子查询的优势：
1. **可读性**：将复杂逻辑分步命名，代码结构更清晰（类似函数分解）
2. **可复用**：同一 CTE 可以在主查询中被引用多次（子查询每次都要重写）
3. **递归查询**：CTE 支持递归（Recursive CTE），可以处理层级结构（如组织架构、树形菜单）
4. **调试友好**：可以单独运行 WITH 部分来检查中间结果

示例：多步骤分析用 CTE 拆解
```sql
WITH monthly_sales AS (
    SELECT
        DATE_FORMAT(order_date, '%Y-%m') AS month,
        SUM(amount) AS total
    FROM orders
    GROUP BY month
),
growth AS (
    SELECT
        month,
        total,
        LAG(total) OVER (ORDER BY month) AS prev_total
    FROM monthly_sales
)
SELECT
    month,
    total,
    ROUND((total - prev_total) / prev_total * 100, 2) AS mom_growth_pct
FROM growth;
```

*参考考察点: CTE语法、可读性与代码组织、窗口函数结合使用*

---

**Q54. 写一个 SQL 查询：找出订单表中金额排名第二高的订单金额（不使用 LIMIT/TOP）。**

表结构：`orders(order_id, user_id, amount)`

**参考答案**：

方法一：子查询（经典写法）
```sql
SELECT MAX(amount) AS second_highest
FROM orders
WHERE amount < (SELECT MAX(amount) FROM orders);
```

方法二：DENSE_RANK() 窗口函数
```sql
SELECT amount AS second_highest
FROM (
    SELECT amount, DENSE_RANK() OVER (ORDER BY amount DESC) AS rnk
    FROM orders
) ranked
WHERE rnk = 2
LIMIT 1;
```
注意：使用 DENSE_RANK 而不是 RANK，确保处理并列情况时第二高是真正的第二个不同值

方法三：NOT IN
```sql
SELECT MAX(amount)
FROM orders
WHERE amount NOT IN (SELECT MAX(amount) FROM orders);
```

扩展：若要找第 N 高（通用解法）
```sql
SELECT amount
FROM (
    SELECT DISTINCT amount, DENSE_RANK() OVER (ORDER BY amount DESC) AS rnk
    FROM orders
) t
WHERE rnk = N;  -- 替换N为目标名次
```

*参考考察点: 子查询、窗口函数、DENSE_RANK解决Top-N问题*

---

**Q55. 写一个 SQL 查询：计算每个月的销售额，以及环比增长率（Month-over-Month Growth）。**

表结构：`orders(order_id, amount, order_date)`

**参考答案**：
```sql
WITH monthly AS (
    SELECT
        DATE_FORMAT(order_date, '%Y-%m') AS month,
        SUM(amount) AS total_sales
    FROM orders
    GROUP BY DATE_FORMAT(order_date, '%Y-%m')
),
with_prev AS (
    SELECT
        month,
        total_sales,
        LAG(total_sales, 1) OVER (ORDER BY month) AS prev_month_sales
    FROM monthly
)
SELECT
    month,
    total_sales,
    prev_month_sales,
    ROUND(
        (total_sales - prev_month_sales) / prev_month_sales * 100, 2
    ) AS mom_growth_pct
FROM with_prev
ORDER BY month;
```

关键点说明：
- `LAG(total_sales, 1)`：取上一行（上个月）的销售额，OVER (ORDER BY month) 按月份排序
- 第一个月的 prev_month_sales 为 NULL，MoM 增长率也为 NULL
- 若需要同比（Year-over-Year），使用 `LAG(total_sales, 12)` 取12个月前的值

*参考考察点: LAG窗口函数、环比计算、CTE组合使用*

---

**Q56. 如何在 SQL 中找出连续登录超过 3 天的用户？**

表结构：`user_logins(user_id, login_date)`（每行代表一次登录，同一用户同一天可能有多次）

**参考答案**：
```sql
-- Step 1: 去重，每用户每天只保留一条登录记录
WITH daily_logins AS (
    SELECT DISTINCT user_id, login_date
    FROM user_logins
),
-- Step 2: 用 ROW_NUMBER 生成行号，与日期相减得到"分组键"
-- 连续日期的差值恒定，不连续则差值变化
with_group AS (
    SELECT
        user_id,
        login_date,
        DATE_SUB(login_date, INTERVAL ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) DAY) AS grp
    FROM daily_logins
),
-- Step 3: 按用户和分组键聚合，统计每段连续登录的天数
consecutive AS (
    SELECT
        user_id,
        grp,
        MIN(login_date) AS start_date,
        MAX(login_date) AS end_date,
        COUNT(*) AS consecutive_days
    FROM with_group
    GROUP BY user_id, grp
)
SELECT DISTINCT user_id, start_date, end_date, consecutive_days
FROM consecutive
WHERE consecutive_days >= 3;
```

核心思路解析：
- 对同一用户，用行号减去日期：若日期连续，每天日期增加1天，行号也增加1，差值不变 → 分组到同一"段"
- 若某天断开，差值改变 → 新的"段"
- 对每段统计 COUNT 即得连续天数

*参考考察点: 连续日期问题、ROW_NUMBER应用、日期运算、分析逻辑拆解*

---

**Q57. 写一个 SQL 查询：计算用户留存率。定义：在第 1 天注册的用户中，有多少人在第 7 天也活跃？**

表结构：`user_activity(user_id, activity_date)` 和 `users(user_id, register_date)`

**参考答案**：
```sql
-- Day-7 Retention Rate
SELECT
    COUNT(DISTINCT a7.user_id) AS retained_users,
    COUNT(DISTINCT u.user_id) AS total_day1_users,
    ROUND(
        COUNT(DISTINCT a7.user_id) * 100.0 / COUNT(DISTINCT u.user_id), 2
    ) AS day7_retention_rate
FROM users u
LEFT JOIN user_activity a7
    ON u.user_id = a7.user_id
    AND a7.activity_date = DATE_ADD(u.register_date, INTERVAL 6 DAY)
-- Day 1 = register_date, Day 7 = register_date + 6 days
WHERE u.register_date = '2024-01-01';  -- 指定注册日期队列
```

扩展：按注册日期队列计算多个留存天数
```sql
SELECT
    u.register_date AS cohort_date,
    DATEDIFF(a.activity_date, u.register_date) AS days_after_register,
    COUNT(DISTINCT a.user_id) AS active_users,
    COUNT(DISTINCT u.user_id) AS cohort_size,
    ROUND(COUNT(DISTINCT a.user_id) * 100.0 / COUNT(DISTINCT u.user_id), 2) AS retention_rate
FROM users u
LEFT JOIN user_activity a ON u.user_id = a.user_id
    AND DATEDIFF(a.activity_date, u.register_date) IN (1, 3, 7, 14, 30)
GROUP BY u.register_date, DATEDIFF(a.activity_date, u.register_date)
ORDER BY cohort_date, days_after_register;
```

*参考考察点: 留存分析SQL、Cohort分析、DATEDIFF日期计算*

---

**Q58. 写一个 SQL 查询：找出"流失用户"（上个月活跃但本月不活跃的用户）和"新增活跃用户"（本月活跃但上个月不活跃的用户）。**

表结构：`user_activity(user_id, activity_month)`（每个用户每月活跃一条记录）

**参考答案**：
```sql
-- 流失用户：上月活跃，本月不活跃
SELECT
    user_id,
    'churned' AS user_type
FROM user_activity
WHERE activity_month = '2024-11'   -- 上个月
AND user_id NOT IN (
    SELECT user_id FROM user_activity WHERE activity_month = '2024-12'  -- 本月
);

-- 新增活跃用户：本月活跃，上月不活跃
SELECT
    user_id,
    'new_active' AS user_type
FROM user_activity
WHERE activity_month = '2024-12'
AND user_id NOT IN (
    SELECT user_id FROM user_activity WHERE activity_month = '2024-11'
);

-- 合并结果
SELECT user_id, 'churned' AS user_type
FROM user_activity WHERE activity_month = '2024-11'
AND user_id NOT IN (SELECT user_id FROM user_activity WHERE activity_month = '2024-12')
UNION ALL
SELECT user_id, 'new_active' AS user_type
FROM user_activity WHERE activity_month = '2024-12'
AND user_id NOT IN (SELECT user_id FROM user_activity WHERE activity_month = '2024-11');
```

使用 LEFT JOIN 的更优写法（避免 NOT IN 的 NULL 问题）：
```sql
-- 流失用户
SELECT last_month.user_id, 'churned' AS type
FROM user_activity last_month
LEFT JOIN user_activity this_month
    ON last_month.user_id = this_month.user_id
    AND this_month.activity_month = '2024-12'
WHERE last_month.activity_month = '2024-11'
AND this_month.user_id IS NULL;
```

*参考考察点: 用户生命周期分析、反连接、UNION合并、留存/流失计算*

---

**Q59. 写一个 SQL 查询：统计各用户的累计消费额（Running Total / Cumulative Sum）。**

表结构：`orders(order_id, user_id, amount, order_date)`

**参考答案**：
```sql
SELECT
    order_id,
    user_id,
    order_date,
    amount,
    SUM(amount) OVER (
        PARTITION BY user_id
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS cumulative_spend
FROM orders
ORDER BY user_id, order_date;
```

关键点：
- `PARTITION BY user_id`：每个用户独立计算累计值（重置）
- `ORDER BY order_date`：按日期累加
- `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`：窗口从第一行到当前行（这是默认的 RANGE 行为，可以省略但显式写出更清晰）

计算全局（不按用户）的累计销售额（如展示销售增长曲线）：
```sql
SELECT
    order_date,
    SUM(amount) AS daily_sales,
    SUM(SUM(amount)) OVER (ORDER BY order_date) AS cumulative_sales
FROM orders
GROUP BY order_date
ORDER BY order_date;
```

*参考考察点: 累计求和窗口函数、ROWS BETWEEN子句、时序累计分析*

---

**Q60. 写一个 SQL 查询：从每个类别中找出销售额排名前 3 的商品。**

表结构：`products(product_id, product_name, category, total_sales)`

**参考答案**：
```sql
WITH ranked_products AS (
    SELECT
        product_id,
        product_name,
        category,
        total_sales,
        DENSE_RANK() OVER (PARTITION BY category ORDER BY total_sales DESC) AS rnk
    FROM products
)
SELECT
    product_id,
    product_name,
    category,
    total_sales,
    rnk
FROM ranked_products
WHERE rnk <= 3
ORDER BY category, rnk;
```

说明：
- 使用 `DENSE_RANK()` 而非 `ROW_NUMBER()`：若销售额并列，允许超过3条记录（例如第3名有两个并列，则返回4条）
- 若严格每类只要3条，用 `ROW_NUMBER()`
- `PARTITION BY category`：按类别重置排名

*参考考察点: Top-N per group模式、PARTITION BY分组排名、DENSE_RANK vs ROW_NUMBER选择*

---

**Q61. 解释 SQL 中的 NULL 值：NULL 与空字符串（''）有什么区别？如何处理 NULL？**

**参考答案要点**：

NULL vs 空字符串：
- **NULL**：表示"值未知"或"值缺失"，不占存储空间（部分数据库），不等于任何值（包括它自己）
- **空字符串 `''`**：是一个长度为0的字符串，是有值的，只是值为空

关键行为差异：
- `NULL = NULL` → NULL（不是 TRUE），必须用 `IS NULL` 或 `IS NOT NULL` 检查
- `NULL + 任何值` → NULL（NULL 在运算中会传播）
- `NULL` 在 `COUNT(column)` 中被忽略（COUNT(*) 包含 NULL 行）
- `NULL` 在 `SUM/AVG` 中被忽略

处理 NULL 的函数：
```sql
-- COALESCE：返回第一个非NULL值（最常用）
SELECT COALESCE(phone, email, '联系方式未知') AS contact FROM users;

-- IFNULL（MySQL）：如果为NULL则返回默认值
SELECT IFNULL(discount, 0) AS discount FROM orders;

-- NULLIF：若两值相等则返回NULL（用于防止除以0）
SELECT revenue / NULLIF(cost, 0) AS roi FROM campaigns;

-- IS NULL / IS NOT NULL：检查NULL
SELECT * FROM users WHERE phone IS NULL;
```

*参考考察点: NULL语义理解、COALESCE/IFNULL、NULL在聚合和连接中的行为*

---

**Q62. 什么是 SQL 中的索引（Index）？何时应该使用索引？有哪些注意事项？**

**参考答案要点**：

索引定义：
- 索引是一种数据库对象，用于加速数据检索，类似于书的目录
- 常见类型：B-Tree 索引（默认）、哈希索引、全文索引、复合索引

何时应建索引：
1. **高频查询的 WHERE 条件列**（如 user_id、order_date）
2. **JOIN 的关联列**（外键通常应建索引）
3. **ORDER BY 和 GROUP BY 的列**
4. **唯一性约束列**（主键自动有索引）

注意事项（什么时候不要建索引）：
1. **写操作频繁的表**：每次 INSERT/UPDATE/DELETE 都需要同步更新索引，增加写开销
2. **小表**：全表扫描可能比索引查找更快
3. **低选择性列**（如性别只有男/女）：索引效果差
4. **索引不会被使用的情况**：
   - `WHERE YEAR(create_date) = 2024`（函数包裹列，索引失效）
   - `WHERE name LIKE '%王'`（前缀模糊查询，索引失效）
   - OR 条件未覆盖所有索引列

*参考考察点: 数据库性能优化基础、索引原理、SQL查询优化意识*

---

**Q63. 写一个 SQL 查询：计算每个用户的首次购买日期、最近购买日期和购买总次数。**

表结构：`orders(order_id, user_id, amount, order_date)`

**参考答案**：
```sql
SELECT
    user_id,
    MIN(order_date) AS first_purchase_date,
    MAX(order_date) AS last_purchase_date,
    COUNT(order_id) AS total_orders,
    SUM(amount) AS total_spend,
    DATEDIFF(MAX(order_date), MIN(order_date)) AS days_since_first_purchase
FROM orders
GROUP BY user_id
ORDER BY total_spend DESC;
```

扩展：结合 users 表获取更多信息，并计算平均购买间隔
```sql
SELECT
    u.user_id,
    u.name,
    MIN(o.order_date) AS first_purchase,
    MAX(o.order_date) AS last_purchase,
    COUNT(o.order_id) AS order_count,
    SUM(o.amount) AS total_spend,
    ROUND(SUM(o.amount) / COUNT(o.order_id), 2) AS avg_order_value
FROM users u
JOIN orders o ON u.user_id = o.user_id
GROUP BY u.user_id, u.name;
```

业务应用：这是 RFM（Recency, Frequency, Monetary）分析的数据基础

*参考考察点: 聚合函数综合运用、MIN/MAX/COUNT/SUM、日期函数、RFM数据准备*

---

**Q64. 什么是 SQL 中的子查询（Subquery）？标量子查询、行子查询和表子查询有什么区别？**

**参考答案要点**：

子查询定义：嵌套在另一个 SQL 语句中的查询（也称内部查询或嵌套查询）

分类：
1. **标量子查询（Scalar Subquery）**：返回**单一值（1行1列）**，可用在 SELECT、WHERE 中
   ```sql
   SELECT name, salary,
          (SELECT AVG(salary) FROM employees) AS company_avg
   FROM employees;
   ```

2. **行子查询（Row Subquery）**：返回**单行多列**，可与行构造器比较
   ```sql
   SELECT * FROM orders
   WHERE (user_id, amount) = (SELECT user_id, MAX(amount) FROM orders WHERE user_id = 1);
   ```

3. **表子查询（Table Subquery）**：返回**多行多列**，用在 FROM 中（也称派生表/内联视图）
   ```sql
   SELECT dept, avg_salary
   FROM (
       SELECT department AS dept, AVG(salary) AS avg_salary
       FROM employees GROUP BY department
   ) dept_summary
   WHERE avg_salary > 50000;
   ```

4. **相关子查询（Correlated Subquery）**：子查询引用外部查询的列，每行都要执行一次（性能较差）
   ```sql
   SELECT name FROM employees e
   WHERE salary > (SELECT AVG(salary) FROM employees WHERE department = e.department);
   ```

*参考考察点: SQL子查询类型、派生表、相关子查询理解*

---

**Q65. 写一个 SQL 查询：找出下单金额超过该用户平均订单金额 2 倍的订单。**

表结构：`orders(order_id, user_id, amount, order_date)`

**参考答案**：

方法一：窗口函数（推荐）
```sql
SELECT order_id, user_id, amount, avg_amount
FROM (
    SELECT
        order_id,
        user_id,
        amount,
        AVG(amount) OVER (PARTITION BY user_id) AS avg_amount
    FROM orders
) t
WHERE amount > 2 * avg_amount;
```

方法二：相关子查询
```sql
SELECT o.order_id, o.user_id, o.amount
FROM orders o
WHERE o.amount > 2 * (
    SELECT AVG(amount) FROM orders WHERE user_id = o.user_id
);
```

方法三：JOIN 子查询
```sql
SELECT o.order_id, o.user_id, o.amount, u_avg.avg_amount
FROM orders o
JOIN (
    SELECT user_id, AVG(amount) AS avg_amount
    FROM orders GROUP BY user_id
) u_avg ON o.user_id = u_avg.user_id
WHERE o.amount > 2 * u_avg.avg_amount;
```

*参考考察点: 窗口函数 vs 子查询、用户级别异常检测、per-user统计*

---

**Q66. 写一个 SQL 查询：实现行转列（Pivot）操作。将按月的销售记录转换为每个产品一行、每个月一列的格式。**

**参考答案**：

原始数据格式（长表）：
```
product | month   | sales
手机     | 2024-01 | 500
手机     | 2024-02 | 600
电视     | 2024-01 | 300
```

目标格式（宽表/透视表）：
```
product | jan_sales | feb_sales
手机     | 500       | 600
电视     | 300       | NULL
```

MySQL 实现（使用 CASE WHEN）：
```sql
SELECT
    product,
    SUM(CASE WHEN month = '2024-01' THEN sales ELSE 0 END) AS jan_sales,
    SUM(CASE WHEN month = '2024-02' THEN sales ELSE 0 END) AS feb_sales,
    SUM(CASE WHEN month = '2024-03' THEN sales ELSE 0 END) AS mar_sales
FROM monthly_sales
GROUP BY product;
```

SQL Server / Oracle 使用 PIVOT：
```sql
SELECT product, [2024-01], [2024-02], [2024-03]
FROM monthly_sales
PIVOT (SUM(sales) FOR month IN ([2024-01], [2024-02], [2024-03])) AS pvt;
```

动态列数时：需要用动态 SQL（EXECUTE 或 sp_executesql）构建列名

*参考考察点: 行转列Pivot操作、CASE WHEN聚合、报表查询技能*

---

**Q67. 写一个 SQL 查询：找出 7 天滑动平均销售额（Rolling 7-Day Average）。**

表结构：`daily_sales(sale_date, total_sales)`

**参考答案**：
```sql
SELECT
    sale_date,
    total_sales,
    AVG(total_sales) OVER (
        ORDER BY sale_date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS rolling_7day_avg
FROM daily_sales
ORDER BY sale_date;
```

关键语法：
- `ROWS BETWEEN 6 PRECEDING AND CURRENT ROW`：当前行及前6行（共7行）的窗口
- `RANGE BETWEEN` vs `ROWS BETWEEN`：
  - `ROWS BETWEEN`：按物理行数计算窗口（精确）
  - `RANGE BETWEEN`：按值范围计算（处理并列日期时有差异）

变体：
```sql
-- 30天滑动平均
AVG(total_sales) OVER (ORDER BY sale_date ROWS BETWEEN 29 PRECEDING AND CURRENT ROW)

-- 排除当天，只看前7天平均（可用于预测当天）
AVG(total_sales) OVER (ORDER BY sale_date ROWS BETWEEN 7 PRECEDING AND 1 PRECEDING)
```

*参考考察点: 滑动窗口分析、ROWS BETWEEN子句、时序数据分析*

---

**Q68. 解释 SQL 中 DELETE、TRUNCATE 和 DROP 的区别。**

**参考答案要点**：

| 维度 | DELETE | TRUNCATE | DROP |
|------|--------|----------|------|
| 作用对象 | 表中的行 | 表中的所有行 | 整张表（含结构） |
| WHERE 子句 | 支持 | 不支持 | 不支持 |
| 事务回滚 | 可回滚（DML） | 通常不可回滚（DDL，数据库相关）| 不可回滚 |
| 速度 | 慢（逐行，记录日志）| 快（重置页）| 最快 |
| 触发器 | 触发 DELETE 触发器 | 不触发 | 不适用 |
| 自增ID | 不重置 | 重置为初始值（通常）| 消失 |
| 释放存储空间 | 不立即释放 | 立即释放 | 完全释放 |

使用建议：
- 删除特定行 → `DELETE WHERE`
- 清空表但保留结构（如清空临时表）→ `TRUNCATE`（更快）
- 完全删除表（结构+数据）→ `DROP`（谨慎！不可恢复）

*参考考察点: SQL数据操作语言、DDL vs DML理解、数据库安全操作*

---

**Q69. 写一个 SQL 查询：找出同时购买了 A 商品和 B 商品的用户。**

表结构：`orders(order_id, user_id, product_name, order_date)`

**参考答案**：

方法一：两次过滤后 INNER JOIN
```sql
SELECT a.user_id
FROM (SELECT DISTINCT user_id FROM orders WHERE product_name = '商品A') a
INNER JOIN (SELECT DISTINCT user_id FROM orders WHERE product_name = '商品B') b
ON a.user_id = b.user_id;
```

方法二：GROUP BY + HAVING 计数
```sql
SELECT user_id
FROM orders
WHERE product_name IN ('商品A', '商品B')
GROUP BY user_id
HAVING COUNT(DISTINCT product_name) = 2;
```
这种方法简洁，且容易扩展为"购买了 A、B、C 所有三种商品的用户"（改为 HAVING COUNT = 3）

方法三：子查询嵌套（EXISTS）
```sql
SELECT DISTINCT user_id
FROM orders o1
WHERE product_name = '商品A'
AND EXISTS (
    SELECT 1 FROM orders o2
    WHERE o2.user_id = o1.user_id
    AND o2.product_name = '商品B'
);
```

业务场景：商品关联分析（购物篮分析）、捆绑销售效果评估

*参考考察点: 商品购买路径分析、多条件过滤、EXISTS/IN/JOIN等价写法*

---

**Q70. 写一个 SQL 查询：计算用户的 RFM 分层（Recency-Frequency-Monetary）基础数据。**

表结构：`orders(order_id, user_id, amount, order_date)`，假设分析日期为 '2024-12-31'。

**参考答案**：
```sql
WITH rfm_base AS (
    SELECT
        user_id,
        DATEDIFF('2024-12-31', MAX(order_date)) AS recency,      -- 最近一次购买距今天数（越小越好）
        COUNT(DISTINCT order_id) AS frequency,                    -- 购买次数（越大越好）
        SUM(amount) AS monetary                                    -- 累计消费金额（越大越好）
    FROM orders
    WHERE order_date <= '2024-12-31'
    GROUP BY user_id
),
rfm_scores AS (
    SELECT
        user_id,
        recency,
        frequency,
        monetary,
        -- 使用 NTILE 将每个维度分成5档（5分最优，1分最差）
        NTILE(5) OVER (ORDER BY recency ASC) AS r_score,    -- recency越小越好，升序使得最新的得5分
        NTILE(5) OVER (ORDER BY frequency DESC) AS f_score,
        NTILE(5) OVER (ORDER BY monetary DESC) AS m_score
    FROM rfm_base
)
SELECT
    user_id,
    recency, frequency, monetary,
    r_score, f_score, m_score,
    CONCAT(r_score, f_score, m_score) AS rfm_segment,
    r_score + f_score + m_score AS total_rfm_score
FROM rfm_scores
ORDER BY total_rfm_score DESC;
```

注意：`NTILE(5) OVER (ORDER BY recency ASC)` 中，recency 越小（最近购买）排名越靠前，分到高分（5分），所以用 ASC；frequency 和 monetary 越大越好，用 DESC。

业务应用：
- RFM 得分 555：最高价值用户（Champion）
- RFM 得分 111：流失高风险用户，需唤回策略

*参考考察点: RFM分析、NTILE窗口函数、业务指标到SQL的转化*

---
