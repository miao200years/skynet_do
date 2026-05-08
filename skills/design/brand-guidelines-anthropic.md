---
name: brand-guidelines-anthropic
source: https://github.com/anthropics/skills/tree/main/skills/brand-guidelines
author: Anthropic (Official)
category: design
tags: [brand, design-system, colors, typography, Anthropic]
collected: 2026-05-08
---

# brand-guidelines-anthropic

> Anthropic 官方品牌配色和排版规范，可应用于 artifact、文档、演示文稿等。

## 触发场景

需要应用 Anthropic 品牌视觉风格时，包括品牌演示、对外材料、Anthropic 风格的设计稿。

## 品牌色板

### 主色系

```css
/* 主要颜色 */
--color-dark:       #141413;  /* 主文字色、深色背景 */
--color-light:      #faf9f5;  /* 浅色背景、深色上的文字 */
--color-mid-gray:   #b0aea5;  /* 次要元素、占位文字 */
--color-light-gray: #e8e6dc;  /* 细腻的背景分隔 */

/* 强调色 */
--color-orange: #d97757;  /* 主要强调色（CTA、高亮） */
--color-blue:   #6a9bcc;  /* 次要强调色（链接、信息） */
--color-green:  #788c5d;  /* 第三强调色（成功、自然） */
```

### 配色使用规则

```
深色背景场景：
  背景 → #141413
  文字 → #faf9f5
  强调 → #d97757（橙）

浅色背景场景（默认）：
  背景 → #faf9f5
  文字 → #141413
  边框/分割线 → #e8e6dc
  次要文字 → #b0aea5

强调色优先级：橙 > 蓝 > 绿（循环使用）
```

## 字体系统

| 用途 | 字体 | 备用 |
|------|------|------|
| 标题（≥24pt/px） | Poppins | Arial |
| 正文 | Lora | Georgia |

### CSS 应用

```css
/* Google Fonts 引入 */
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&family=Lora:ital,wght@0,400;0,700;1,400&display=swap');

body {
  font-family: 'Lora', Georgia, serif;
  background-color: #faf9f5;
  color: #141413;
}

h1, h2, h3, h4 {
  font-family: 'Poppins', Arial, sans-serif;
  font-weight: 600;
}
```

## 设计原则

```
1. 简约克制：留白是设计语言的一部分
2. 暖色系基调：米白（#faf9f5）而非纯白，减少视觉疲劳
3. 橙色点睛：橙色只用于最重要的 CTA 或视觉焦点
4. 中性衬底：大面积用深/浅主色，强调色不超过10%面积
```

## 完整 HTML 模板

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&family=Lora:ital,wght@0,400;1,400&display=swap" rel="stylesheet">
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: 'Lora', Georgia, serif;
      background: #faf9f5;
      color: #141413;
      line-height: 1.6;
    }
    h1, h2, h3 {
      font-family: 'Poppins', Arial, sans-serif;
      font-weight: 600;
    }
    .accent { color: #d97757; }
    .secondary { color: #b0aea5; }
    .btn-primary {
      background: #d97757;
      color: #faf9f5;
      border: none;
      padding: 12px 24px;
      font-family: 'Poppins', sans-serif;
      font-weight: 600;
      cursor: pointer;
    }
    .divider {
      border: none;
      border-top: 1px solid #e8e6dc;
      margin: 24px 0;
    }
    /* 深色版 */
    .dark-section {
      background: #141413;
      color: #faf9f5;
      padding: 48px;
    }
  </style>
</head>
<body>
  <!-- 你的内容 -->
</body>
</html>
```
