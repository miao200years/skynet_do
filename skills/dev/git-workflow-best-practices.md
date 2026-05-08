---
name: git-workflow-best-practices
source: Community Best Practices (GitHub Engineering Blog + Conventional Commits)
author: Community
category: dev
tags: [git, workflow, commit, PR, branch, code-review]
collected: 2026-05-08
---

# git-workflow-best-practices

> Git 工作流规范：Commit 格式、分支策略、PR 最佳实践、代码评审指南。

## Commit Message 规范（Conventional Commits）

### 格式

```
<type>(<scope>): <description>

[可选 body]

[可选 footer]
```

### Type 类型

| Type | 含义 | 例子 |
|------|------|------|
| `feat` | 新功能 | `feat(auth): add OAuth2 login` |
| `fix` | Bug 修复 | `fix(cart): prevent duplicate items` |
| `docs` | 文档更新 | `docs: update API README` |
| `style` | 格式（不影响逻辑） | `style: fix indentation` |
| `refactor` | 重构（非 feat/fix） | `refactor(db): extract query builder` |
| `test` | 测试相关 | `test: add unit tests for payment` |
| `chore` | 构建/工具/依赖 | `chore: upgrade React to 18.3` |
| `perf` | 性能优化 | `perf(list): virtualize long lists` |
| `ci` | CI/CD 配置 | `ci: add GitHub Actions workflow` |
| `revert` | 回退提交 | `revert: feat(auth): ...` |

### 好的 Commit 示例

```bash
# ✅ 清晰说明做了什么、为什么
feat(payment): add retry logic for failed Stripe charges

Previously, failed charges would immediately return an error to the user.
Now we retry up to 3 times with exponential backoff before failing.

Resolves: #234
```

```bash
# ✅ 简洁的 one-liner（足够清楚时）
fix(nav): close mobile menu after route change
```

```bash
# ❌ 无意义的 commit
fix bug
update
WIP
asdfgh
```

## 分支命名规范

```bash
# 格式：<type>/<ticket>-<description>
feature/AUTH-123-oauth-login
fix/CART-456-duplicate-items
hotfix/PROD-789-payment-crash
chore/upgrade-dependencies
docs/update-api-guide
```

## 分支策略（GitHub Flow，推荐）

```
main（始终可部署）
  ├── feature/xxx  ← 从 main 创建
  ├── feature/yyy
  └── hotfix/zzz  ← 修复后直接 PR 到 main
```

```bash
# 标准功能开发流程
git checkout main
git pull origin main
git checkout -b feature/TICKET-123-new-feature

# 开发...
git add -p  # 交互式暂存，检查每个改动
git commit -m "feat(module): add new feature"

# 推送并创建 PR
git push -u origin feature/TICKET-123-new-feature
# 在 GitHub/GitLab 创建 PR
```

## PR（Pull Request）最佳实践

### PR 描述模板

```markdown
## 变更说明
<!-- 这个 PR 做了什么？为什么这么做？ -->

## 变更类型
- [ ] Bug 修复
- [ ] 新功能
- [ ] 重构
- [ ] 文档更新
- [ ] 其他：___

## 测试方式
<!-- 如何验证这些变更是正确的？ -->
1. 步骤一
2. 步骤二

## 相关 Issue
Closes #123

## 截图（如有 UI 变更）
| 变更前 | 变更后 |
|--------|--------|
| [截图] | [截图] |

## 检查清单
- [ ] 代码自审完成
- [ ] 测试通过
- [ ] 文档已更新（如需要）
```

### PR 大小控制

```
✅ 理想 PR：< 400 行变更
✅ 聚焦：一个 PR 解决一件事
✅ 方便 Review：大功能拆成多个小 PR

❌ 避免：巨型 PR（>1000行），难以 Review
```

## Code Review 指南

### Reviewer 清单

```
代码质量：
□ 逻辑是否正确？
□ 边界情况是否处理？
□ 有无明显性能问题？

安全：
□ 用户输入是否校验？
□ 权限检查是否完整？
□ 有无敏感信息泄露？

可维护性：
□ 命名是否清晰？
□ 复杂逻辑是否有注释？
□ 是否有重复代码可以抽取？

测试：
□ 关键逻辑是否有测试覆盖？
□ 测试用例是否覆盖边界情况？
```

### Review 评论风格

```bash
# ✅ 建设性：提供具体建议
"这里可以用 Array.reduce 替代循环，
代码会更简洁：
const sum = arr.reduce((acc, x) => acc + x, 0)"

# ✅ 提问式：不确定时询问
"这里为什么用 setTimeout 而不是 requestAnimationFrame？
是有特殊原因吗？"

# ❌ 避免：主观批评
"这段代码写得很烂"
"这完全是错的"
```

## 实用 Git 命令

```bash
# 交互式暂存（推荐替代 git add .）
git add -p

# 修改最近一次 commit（未推送时）
git commit --amend

# 整理本地提交（合并/修改/重排）
git rebase -i HEAD~3

# 查看谁写了这行代码
git blame -L 10,20 file.py

# 找出引入 bug 的 commit
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
# 二分查找...

# 恢复误删的文件
git checkout HEAD -- path/to/file

# 查看分支图
git log --oneline --graph --all
```
