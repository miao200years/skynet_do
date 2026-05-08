---
name: ui-accessibility-review
source: https://github.com/vercel-labs/agent-skills
author: Vercel (Official)
category: dev
tags: [accessibility, a11y, UI, review, WCAG, UX]
collected: 2026-05-08
---

# ui-accessibility-review

> 审查 Web UI 代码，检查 100+ 条可访问性、性能、UX 最佳实践。

## 触发场景

- "Review 我的 UI"
- "检查可访问性"
- "审查设计"
- "检查 UX"
- "对照最佳实践检查我的网站"

## 可访问性（Accessibility）

### ARIA 标签

```html
<!-- ❌ 无意义的按钮 -->
<button onclick="closeModal()">×</button>

<!-- ✅ 描述性 aria-label -->
<button onclick="closeModal()" aria-label="关闭对话框">×</button>

<!-- ❌ 图片无替代文字 -->
<img src="chart.png">

<!-- ✅ 有意义的 alt -->
<img src="chart.png" alt="2024年Q4销售额同比增长23%的柱状图">
<!-- 装饰性图片 -->
<img src="decoration.png" alt="" role="presentation">
```

### 语义化 HTML

```html
<!-- ❌ 无语义结构 -->
<div class="header">...</div>
<div class="nav">...</div>
<div class="content">...</div>

<!-- ✅ 语义元素 -->
<header>...</header>
<nav aria-label="主导航">...</nav>
<main>
  <article>...</article>
  <aside>...</aside>
</main>
<footer>...</footer>
```

### 键盘可访问性

```html
<!-- ❌ 不可键盘聚焦的交互元素 -->
<div onclick="handleClick()">点击我</div>

<!-- ✅ 使用 button 或添加 tabindex -->
<button onclick="handleClick()">点击我</button>
<!-- 或 -->
<div
  role="button"
  tabindex="0"
  onclick="handleClick()"
  onkeydown="e.key==='Enter' && handleClick()">
  点击我
</div>
```

## 焦点状态

```css
/* ❌ 完全移除焦点样式（破坏键盘导航） */
:focus { outline: none; }

/* ✅ 自定义焦点样式（保持可见但更美观） */
:focus-visible {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
  border-radius: 2px;
}

/* 鼠标点击不显示，键盘 Tab 显示 */
:focus:not(:focus-visible) { outline: none; }
```

## 表单

```html
<!-- ❌ 无 label 的输入框 -->
<input type="email" placeholder="邮箱">

<!-- ✅ 关联 label -->
<label for="email">邮箱地址</label>
<input
  id="email"
  type="email"
  name="email"
  autocomplete="email"
  required
  aria-describedby="email-error"
>
<span id="email-error" role="alert">请输入有效的邮箱地址</span>
```

## 动效与偏好

```css
/* ✅ 尊重用户的减少动效偏好 */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

## 排版

```html
<!-- ❌ 英文排版糟糕 -->
<p>He said "hello" to the user's account...</p>

<!-- ✅ 正确标点 -->
<p>He said &#x201C;hello&#x201D; to the user&#x2019;s account&#x2026;</p>
<!-- 即："hello"、's、… -->
```

## 性能相关 UX

```html
<!-- ✅ 图片懒加载 -->
<img src="..." loading="lazy" alt="...">

<!-- ✅ 预连接关键第三方域名 -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="dns-prefetch" href="https://cdn.example.com">
```

## 深色模式支持

```css
/* ✅ 响应系统深色模式 */
:root {
  color-scheme: light dark;
  --bg: #ffffff;
  --text: #1a1a1a;
}

@media (prefers-color-scheme: dark) {
  :root {
    --bg: #1a1a1a;
    --text: #f5f5f5;
  }
}

body {
  background: var(--bg);
  color: var(--text);
}
```

## Review 检查清单

```
可访问性：
□ 所有图片有有意义的 alt 文字
□ 所有表单输入有关联的 label
□ 交互元素可键盘操作（Tab + Enter/Space）
□ 焦点样式可见
□ 颜色对比度 ≥ 4.5:1（WCAG AA）
□ 不只用颜色传递信息

性能 UX：
□ 图片有 loading="lazy"
□ 关键字体有 preconnect
□ 动效有 prefers-reduced-motion 适配

响应式：
□ 触控区域 ≥ 44×44px
□ 水平不滚动（meta viewport 正确）
□ 深色模式支持
```
