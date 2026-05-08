---
name: python-data-analysis
source: Community Best Practices (pandas/numpy/matplotlib ecosystem)
author: Community
category: data
tags: [Python, pandas, data-analysis, visualization, EDA, numpy]
collected: 2026-05-08
---

# python-data-analysis

> Python 数据分析完整工作流：数据加载、清洗、EDA、可视化、统计分析。

## 环境配置

```bash
pip install pandas numpy matplotlib seaborn plotly scipy jupyter
```

## 标准工作流

### 1. 数据加载

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# CSV
df = pd.read_csv('data.csv', encoding='utf-8')

# Excel
df = pd.read_excel('data.xlsx', sheet_name='Sheet1')

# JSON
df = pd.read_json('data.json')

# 数据库（SQLite 示例）
import sqlite3
conn = sqlite3.connect('database.db')
df = pd.read_sql("SELECT * FROM users WHERE created_at > '2024-01-01'", conn)

# 快速查看
print(df.shape)          # (行数, 列数)
print(df.dtypes)         # 数据类型
print(df.head())         # 前5行
print(df.describe())     # 统计摘要
```

### 2. 数据清洗

```python
# 检查缺失值
print(df.isnull().sum())
print(f"缺失率:\n{df.isnull().mean().round(3) * 100}%")

# 处理缺失值
df['age'].fillna(df['age'].median(), inplace=True)    # 数值 → 中位数
df['city'].fillna('Unknown', inplace=True)             # 类别 → 固定值
df.dropna(subset=['user_id'], inplace=True)            # 关键字段不能缺

# 处理重复行
print(f"重复行数: {df.duplicated().sum()}")
df.drop_duplicates(inplace=True)

# 数据类型转换
df['date'] = pd.to_datetime(df['date'])
df['price'] = pd.to_numeric(df['price'], errors='coerce')
df['category'] = df['category'].astype('category')

# 处理异常值（IQR 方法）
Q1 = df['revenue'].quantile(0.25)
Q3 = df['revenue'].quantile(0.75)
IQR = Q3 - Q1
df = df[~((df['revenue'] < Q1 - 1.5 * IQR) | (df['revenue'] > Q3 + 1.5 * IQR))]

# 字符串清洗
df['name'] = df['name'].str.strip().str.lower()
df['phone'] = df['phone'].str.replace(r'\D', '', regex=True)
```

### 3. 探索性分析（EDA）

```python
# 数值分布
df['revenue'].hist(bins=50, edgecolor='black')
plt.title('Revenue Distribution')
plt.show()

# 分类变量频率
df['category'].value_counts().plot(kind='bar')

# 相关性热图
plt.figure(figsize=(10, 8))
sns.heatmap(df.select_dtypes(include=np.number).corr(),
            annot=True, fmt='.2f', cmap='coolwarm',
            center=0, square=True)
plt.title('Correlation Matrix')
plt.tight_layout()
plt.show()

# 散点图矩阵
sns.pairplot(df[['revenue', 'cost', 'profit', 'category']], hue='category')

# 箱型图（检查分组差异）
sns.boxplot(data=df, x='category', y='revenue')
```

### 4. 数据聚合与分析

```python
# 分组统计
summary = df.groupby('category').agg({
    'revenue': ['sum', 'mean', 'median', 'std'],
    'user_id': 'nunique',
    'order_id': 'count'
}).round(2)
summary.columns = ['_'.join(col) for col in summary.columns]
print(summary)

# 时间序列分析
df['date'] = pd.to_datetime(df['date'])
df.set_index('date', inplace=True)

monthly = df['revenue'].resample('ME').sum()  # 月度汇总
weekly = df['revenue'].resample('W').mean()   # 周均值

# 滚动平均（7日移动平均）
df['revenue_7d'] = df['revenue'].rolling(window=7).mean()

# 同比/环比
df['revenue_yoy'] = df['revenue'] / df['revenue'].shift(365) - 1
df['revenue_mom'] = df['revenue'] / df['revenue'].shift(30) - 1
```

### 5. 可视化（生产级）

```python
# 设置风格
plt.style.use('seaborn-v0_8-whitegrid')
sns.set_palette("husl")

fig, axes = plt.subplots(2, 2, figsize=(14, 10))
fig.suptitle('销售分析仪表板', fontsize=16, fontweight='bold')

# 趋势图
ax1 = axes[0, 0]
monthly.plot(ax=ax1, color='steelblue', linewidth=2)
ax1.set_title('月度收入趋势')
ax1.set_ylabel('收入（元）')

# 分布图
ax2 = axes[0, 1]
df['revenue'].hist(ax=ax2, bins=30, color='coral', edgecolor='white')
ax2.set_title('收入分布')

# 分类对比
ax3 = axes[1, 0]
category_rev = df.groupby('category')['revenue'].sum().sort_values()
category_rev.plot(kind='barh', ax=ax3, color='teal')
ax3.set_title('各类别收入对比')

# 散点图
ax4 = axes[1, 1]
ax4.scatter(df['cost'], df['revenue'], alpha=0.3, c='purple')
ax4.set_xlabel('成本')
ax4.set_ylabel('收入')
ax4.set_title('成本与收入关系')

plt.tight_layout()
plt.savefig('dashboard.png', dpi=150, bbox_inches='tight')
plt.show()
```

### 6. 输出报告

```python
# 输出到 Excel（带多个 sheet）
with pd.ExcelWriter('analysis_report.xlsx', engine='openpyxl') as writer:
    df.to_excel(writer, sheet_name='原始数据', index=False)
    summary.to_excel(writer, sheet_name='分类汇总')
    monthly.to_frame('月度收入').to_excel(writer, sheet_name='趋势')
    print("✅ 报告已保存到 analysis_report.xlsx")
```

## 常用 Pandas 速查

```python
# 筛选
df[df['revenue'] > 1000]
df[df['category'].isin(['A', 'B'])]
df.query("revenue > 1000 and category == 'A'")

# 新增列
df['profit'] = df['revenue'] - df['cost']
df['profit_margin'] = df['profit'] / df['revenue']

# 条件赋值
df['level'] = np.where(df['revenue'] > 10000, 'High', 'Low')

# 高级：多条件
df['tier'] = pd.cut(df['revenue'],
                    bins=[0, 1000, 5000, 10000, np.inf],
                    labels=['Bronze', 'Silver', 'Gold', 'Platinum'])

# Apply 自定义函数
df['clean_name'] = df['name'].apply(lambda x: x.title() if pd.notna(x) else x)
```
