---
name: webapp-testing
source: https://github.com/anthropics/skills/tree/main/skills/webapp-testing
author: Anthropic (Official)
category: dev
tags: [testing, Playwright, E2E, UI, automation, browser]
collected: 2026-05-08
---

# webapp-testing

> Toolkit for interacting with and testing local web applications using Playwright.

## 触发场景

- 验证前端功能是否正常
- 调试 UI 行为
- 捕获浏览器截图
- 查看 browser console 日志
- E2E 自动化测试

## 决策树：选择测试方式

```
用户任务
  ├─ 是静态 HTML？
  │   ├─ 是 → 直接读 HTML 找选择器 → 写 Playwright 脚本
  │   └─ 否 ↓
  └─ 动态 Web 应用
      ├─ 服务器没运行？
      │   └─ 用 with_server.py 启动服务 → 写简化 Playwright 脚本
      └─ 服务器已运行？
          1. navigate + 等待 networkidle
          2. 截图 或 inspect DOM
          3. 从渲染结果找选择器
          4. 用找到的选择器执行操作
```

## 核心使用方式

### 启动服务 + 测试（单服务）

```bash
python scripts/with_server.py --server "npm run dev" --port 5173 -- python your_test.py
```

### 多服务（前后端）

```bash
python scripts/with_server.py \
  --server "cd backend && python server.py" --port 3000 \
  --server "cd frontend && npm run dev" --port 5173 \
  -- python your_test.py
```

### Playwright 测试脚本模板

```python
from playwright.sync_api import sync_playwright

def run_test():
    with sync_playwright() as p:
        browser = p.chromium.launch()
        page = browser.new_page()

        # 1. 导航并等待加载完成
        page.goto("http://localhost:5173")
        page.wait_for_load_state("networkidle")

        # 2. 截图查看当前状态
        page.screenshot(path="before.png")

        # 3. 找到元素并交互
        page.click("button[data-testid='submit']")
        page.fill("input[name='email']", "test@example.com")

        # 4. 断言
        assert page.is_visible("text=Success")

        # 5. 截图验证
        page.screenshot(path="after.png")

        browser.close()

run_test()
```

### 查看 Console 日志

```python
page.on("console", lambda msg: print(f"[{msg.type}] {msg.text}"))
page.on("pageerror", lambda err: print(f"[ERROR] {err}"))
```

## 常见场景

| 场景 | 做法 |
|------|------|
| 检查按钮点击效果 | click → screenshot → assert |
| 验证表单提交 | fill + submit → check response |
| 调试 UI 渲染问题 | screenshot + console log |
| 测试路由跳转 | goto + wait_for_url |
| 检查 API 请求 | 监听 network 事件 |

## 注意事项

- 优先用 `data-testid` 属性定位元素，比 CSS class 更稳定
- `networkidle` 比 `domcontentloaded` 更可靠（等待所有请求完成）
- 截图前加 `wait_for_load_state` 避免空白截图
