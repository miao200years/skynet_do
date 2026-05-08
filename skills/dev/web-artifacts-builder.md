---
name: web-artifacts-builder
source: https://github.com/anthropics/skills/tree/main/skills/web-artifacts-builder
author: Anthropic (Official)
category: dev
tags: [React, Tailwind, shadcn, artifact, HTML, frontend]
collected: 2026-05-08
---

# web-artifacts-builder

> 使用 React + Tailwind CSS + shadcn/ui 构建复杂多组件 HTML 单文件 artifact。

## 触发场景

需要状态管理、路由、或 shadcn/ui 组件的复杂交互 artifact。**简单单文件 HTML/JSX 不需要此 Skill。**

## 技术栈

- React 18 + TypeScript
- Vite（开发）+ Parcel（打包）
- Tailwind CSS 3.4.1
- shadcn/ui（40+ 预置组件）

## 工作流（5步）

### Step 1：初始化项目

```bash
bash scripts/init-artifact.sh my-app
cd my-app
```

自动配置好：React + TypeScript + Tailwind + shadcn/ui + 路径别名 + Parcel

### Step 2：开发 Artifact

**核心文件结构：**
```
my-app/
├── index.html          # 入口 HTML（Parcel 需要）
├── src/
│   ├── main.tsx        # React 入口
│   ├── App.tsx         # 主组件
│   └── components/     # 子组件
└── tailwind.config.js
```

**组件开发示例：**
```tsx
// src/App.tsx
import { useState } from "react"
import { Button } from "@/components/ui/button"
import { Card, CardHeader, CardContent } from "@/components/ui/card"

export default function App() {
  const [count, setCount] = useState(0)

  return (
    <div className="min-h-screen bg-background p-8">
      <Card className="max-w-md mx-auto">
        <CardHeader>
          <h1 className="text-2xl font-bold">Counter App</h1>
        </CardHeader>
        <CardContent className="space-y-4">
          <p className="text-4xl text-center font-mono">{count}</p>
          <div className="flex gap-2">
            <Button onClick={() => setCount(c => c - 1)} variant="outline">
              −
            </Button>
            <Button onClick={() => setCount(c => c + 1)} className="flex-1">
              +
            </Button>
          </div>
        </CardContent>
      </Card>
    </div>
  )
}
```

### Step 3：打包为单文件 HTML

```bash
bash scripts/bundle-artifact.sh
# 生成 bundle.html（所有 JS/CSS 内联，可直接分享）
```

### Step 4：分享给用户

将 `bundle.html` 内容作为 artifact 展示给用户。

### Step 5：可选测试

```bash
# 用 Playwright 截图验证
python -c "
from playwright.sync_api import sync_playwright
with sync_playwright() as p:
    b = p.chromium.launch()
    page = b.new_page()
    page.goto('file://$(pwd)/bundle.html')
    page.screenshot(path='preview.png')
    b.close()
"
```

## 设计规范（避免 AI 审美陷阱）

```
❌ 避免：居中布局 + 紫色渐变 + 统一圆角 + Inter 字体
         （这是"AI slop"的标志性特征）

✅ 推荐：
   - 明确的设计风格（极简/编辑/工业/暗黑等）
   - 有主见的字体搭配
   - 空间节奏感（而非填满）
   - 有特点的色彩系统
```

## 常用 shadcn/ui 组件

```tsx
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Card, CardContent, CardHeader } from "@/components/ui/card"
import { Badge } from "@/components/ui/badge"
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs"
import { Dialog, DialogContent, DialogHeader } from "@/components/ui/dialog"
import { Select, SelectContent, SelectItem, SelectTrigger } from "@/components/ui/select"
import { Checkbox } from "@/components/ui/checkbox"
import { Switch } from "@/components/ui/switch"
import { Progress } from "@/components/ui/progress"
import { Separator } from "@/components/ui/separator"
import { ScrollArea } from "@/components/ui/scroll-area"
import { Toast, useToast } from "@/components/ui/toast"
```
