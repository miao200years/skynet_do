# Smart Commit Message（智能 Commit 信息生成）

## 基本信息
| 字段 | 内容 |
|------|------|
| **来源** | [f/awesome-chatgpt-prompts](https://github.com/f/awesome-chatgpt-prompts) (⭐120k) + Conventional Commits 规范 |
| **Stars** | 120000+ |
| **分类** | dev |
| **收录日期** | 2026-05-08 |

## 功能描述
根据 git diff 或代码变更描述，自动生成符合 Conventional Commits 规范的高质量 commit message，包含 type、scope、subject、body 和 footer。

## 完整 Prompt / 使用指令

```
你是一名遵循 Conventional Commits 规范的代码提交专家。
请根据以下代码变更，生成一条高质量的 git commit message：

**代码变更 diff：**
[粘贴 git diff 内容，或用自然语言描述变更内容]

**Conventional Commits 格式要求：**
<type>(<scope>): <subject>

<body>

<footer>

**type 取值：**
- feat: 新功能
- fix: Bug 修复
- docs: 文档变更
- style: 代码格式（不影响逻辑）
- refactor: 重构（非新功能、非 Bug 修复）
- perf: 性能优化
- test: 新增或修改测试
- chore: 构建工具、依赖更新等杂项
- revert: 回滚提交
- ci: CI/CD 配置变更

**规则：**
1. subject 不超过 50 字符，使用祈使句，首字母小写，末尾不加句号
2. body 解释"为什么"做这个改动（而非"做了什么"），每行不超过 72 字符
3. 如有 Breaking Change，在 footer 写：BREAKING CHANGE: <描述>
4. 如关联 issue，在 footer 写：Closes #<issue号>

请输出：
1. 推荐的 commit message（完整格式）
2. 备选 commit message（简短版，仅 subject 行）
3. 说明：为什么选择这个 type 和 scope
```

## 使用示例

**输入：** 修复了用户登录时密码错误提示不显示的问题，在 `LoginForm.tsx` 第 45 行增加了错误状态检查。

**输出：**
```
fix(auth): show password error message on login failure

Previously, when users entered wrong credentials, the error
message was not displayed due to missing state check in the
form component.

Closes #1234
```

## 适用场景
- 提交代码前生成标准化 commit message
- 团队统一 commit 风格
- 生成 changelog 的基础
