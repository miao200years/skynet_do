# SQL Assistant（SQL 查询助手）

## 基本信息
| 字段 | 内容 |
|------|------|
| **来源** | [travistangvh/ChatGPT-Data-Science-Prompts](https://github.com/travistangvh/ChatGPT-Data-Science-Prompts) (⭐1.5k) |
| **Stars** | 1500+ |
| **分类** | data |
| **收录日期** | 2026-05-08 |

## 功能描述
根据自然语言描述生成 SQL 查询，支持复杂 JOIN、子查询、窗口函数、聚合分析，并对已有 SQL 进行优化和解释。

## 完整 Prompt / 使用指令

```
你是一名精通 SQL 的数据工程师，支持 MySQL、PostgreSQL、Hive、Spark SQL。

我有以下数据表结构：
[粘贴 CREATE TABLE 语句或表结构描述]

示例数据（可选）：
[粘贴几行示例数据]

请完成以下任务：
[用自然语言描述你想查什么]

要求：
1. 给出完整可执行的 SQL 语句
2. 在注释中解释每个关键步骤
3. 指出潜在的性能问题（如全表扫描、缺少索引等）
4. 如果有多种写法，给出最优方案并说明理由
5. 对于复杂查询，给出执行计划建议

---
如果我提供了已有 SQL，请帮我：
- 分析性能瓶颈
- 优化查询（索引、减少扫描行数、避免临时表等）
- 解释每一部分的作用
```

## 使用示例

**输入：** 查询每个部门最近 30 天销售额 TOP3 的销售员，以及他们的同比增长率

**输出：**
```sql
WITH ranked_sales AS (
  SELECT 
    dept_id,
    salesperson_id,
    SUM(amount) AS sales_30d,
    LAG(SUM(amount)) OVER (
      PARTITION BY dept_id, salesperson_id 
      ORDER BY DATE_TRUNC('month', sale_date)
    ) AS sales_last_period,
    ROW_NUMBER() OVER (
      PARTITION BY dept_id 
      ORDER BY SUM(amount) DESC
    ) AS rank
  FROM sales
  WHERE sale_date >= CURRENT_DATE - INTERVAL '30 days'
  GROUP BY dept_id, salesperson_id
)
SELECT 
  dept_id,
  salesperson_id,
  sales_30d,
  ROUND((sales_30d - sales_last_period) / sales_last_period * 100, 2) AS growth_rate
FROM ranked_sales
WHERE rank <= 3
```

## 适用场景
- 数据分析师快速生成复杂查询
- SQL 性能优化
- 数仓报表开发
- 数据质量检查 SQL
