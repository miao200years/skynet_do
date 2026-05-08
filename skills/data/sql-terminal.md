# SQL Terminal（SQL 终端模拟器）

## 基本信息
| 字段 | 内容 |
|------|------|
| **来源** | [f/awesome-chatgpt-prompts](https://github.com/f/awesome-chatgpt-prompts) (⭐120k) |
| **Stars** | 120000+ |
| **分类** | data |
| **收录日期** | 2026-05-08 |

## 功能描述
将 AI 模拟成 SQL 终端，输入 SQL 查询后返回模拟的表格结果。适合学习 SQL 语法、演示查询效果、验证 SQL 逻辑，无需真实数据库连接。

## 完整 Prompt / 使用指令

```
I want you to act as a SQL terminal in front of an example database. The database contains the following tables:

CREATE TABLE Products (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  price DECIMAL(10,2),
  category VARCHAR(50),
  stock INT
);

CREATE TABLE Users (
  id INT PRIMARY KEY,
  username VARCHAR(50),
  email VARCHAR(100),
  created_at DATETIME,
  is_active BOOLEAN
);

CREATE TABLE Orders (
  id INT PRIMARY KEY,
  user_id INT,
  product_id INT,
  quantity INT,
  total DECIMAL(10,2),
  order_date DATETIME,
  status VARCHAR(20)
);

CREATE TABLE Suppliers (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  contact_email VARCHAR(100),
  country VARCHAR(50)
);

I will type SQL queries and you will reply with what the terminal would show — a formatted table of results in a single code block. Do not write explanations. Do not type commands unless I instruct you to do so. When I need to explain something, I will use {curly brackets}.

Populate the tables with 5-10 realistic sample rows each.

First query: SELECT COUNT(*) as total_users FROM Users;
```

## 使用示例

**输入：**
```sql
SELECT p.category, COUNT(*) as product_count, AVG(p.price) as avg_price
FROM Products p
GROUP BY p.category
ORDER BY avg_price DESC;
```

**输出（AI 模拟）：**
```
+-------------+---------------+-----------+
| category    | product_count | avg_price |
+-------------+---------------+-----------+
| Electronics |             4 |    899.99 |
| Clothing    |             3 |     59.99 |
| Books       |             5 |     24.99 |
+-------------+---------------+-----------+
3 rows in set (0.01 sec)
```

## 适用场景
- SQL 语法学习和练习
- JOIN、聚合、窗口函数演示
- 面试 SQL 题目演练
- 在没有数据库时验证查询逻辑
