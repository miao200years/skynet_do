---
name: security-code-review
source: https://github.com/trail-of-bits/skills (Trail of Bits)
author: Trail of Bits (Official Security Firm)
category: dev
tags: [security, code-review, audit, vulnerability, SAST, CodeQL]
collected: 2026-05-08
---

# security-code-review

> 安全代码审查：静态分析、漏洞检测、变体分析。来自知名安全公司 Trail of Bits。

## 触发场景

- 对代码进行安全审计
- 检测常见漏洞（注入、越权、加密缺陷等）
- 在 CI/CD 中集成 SAST 扫描
- Code Review 时关注安全维度

## 漏洞检查清单

### 注入类（高危）

```python
# ❌ SQL 注入
query = f"SELECT * FROM users WHERE id = {user_input}"
cursor.execute(query)

# ✅ 参数化查询
cursor.execute("SELECT * FROM users WHERE id = %s", (user_input,))

# ❌ 命令注入
import subprocess
result = subprocess.run(f"ls {user_path}", shell=True)

# ✅ 参数列表（不经过 shell 解析）
result = subprocess.run(["ls", user_path], capture_output=True)
```

### 身份验证与授权

```python
# ❌ 不安全的直接对象引用（IDOR）
@app.route('/api/document/<doc_id>')
def get_document(doc_id):
    return Document.get(doc_id)  # 没有验证当前用户是否有权限！

# ✅ 验证所有权
@app.route('/api/document/<doc_id>')
@login_required
def get_document(doc_id):
    doc = Document.get(doc_id)
    if doc.owner_id != current_user.id:
        abort(403)
    return doc
```

### 加密与密钥

```python
# ❌ 弱加密
import md5
password_hash = md5.new(password).hexdigest()

# ✅ 强密码哈希（bcrypt/argon2）
from argon2 import PasswordHasher
ph = PasswordHasher()
hash = ph.hash(password)

# ❌ 硬编码密钥
SECRET_KEY = "my-secret-key-123"

# ✅ 从环境变量读取
import os
SECRET_KEY = os.environ["SECRET_KEY"]  # 启动时验证非空
```

### XSS（跨站脚本）

```javascript
// ❌ 直接插入 HTML
element.innerHTML = userContent

// ✅ 文本节点（自动转义）
element.textContent = userContent

// ✅ 如果必须插入 HTML，先消毒
import DOMPurify from 'dompurify'
element.innerHTML = DOMPurify.sanitize(userContent)
```

## 静态分析工具配置

### CodeQL（GitHub Actions）

```yaml
# .github/workflows/codeql.yml
name: CodeQL Security Analysis
on: [push, pull_request]

jobs:
  analyze:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: github/codeql-action/init@v3
        with:
          languages: python  # 或 javascript, java, go, etc.
      - uses: github/codeql-action/analyze@v3
```

### Semgrep（轻量级，本地可运行）

```bash
# 安装
pip install semgrep

# 扫描（使用官方规则集）
semgrep --config=auto .

# 扫描特定类别
semgrep --config=p/owasp-top-ten .
semgrep --config=p/secrets .       # 查找硬编码密钥
semgrep --config=p/python .        # Python 最佳实践
```

## 安全 Review 关注点

### 高风险区域

```
1. 所有接受用户输入的地方 → 验证 + 消毒
2. 数据库查询构造 → 参数化
3. 文件操作路径 → 路径遍历检查
4. 身份验证逻辑 → 权限验证在服务端
5. 加密/哈希实现 → 使用标准库，不自己实现
6. 依赖版本 → 检查 CVE（npm audit / safety check）
7. 日志输出 → 不记录密码/Token/PII
```

### 依赖安全检查

```bash
# Python
pip install safety
safety check

# Node.js
npm audit
npm audit fix  # 自动修复

# Go
govulncheck ./...
```

## OWASP Top 10 快速对照

| 漏洞 | 关键检查点 |
|------|----------|
| A01 访问控制失效 | 每个 API 都验证权限 |
| A02 加密失败 | 敏感数据加密存储，HTTPS 传输 |
| A03 注入 | 参数化查询，不拼 SQL/命令 |
| A04 不安全设计 | 威胁建模，最小权限原则 |
| A05 安全配置错误 | 关闭调试模式，删除默认凭据 |
| A07 身份验证失败 | MFA、安全密码策略 |
| A09 安全日志不足 | 记录认证失败、权限拒绝 |
