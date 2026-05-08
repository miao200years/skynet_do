# 🔄 每日 Skill 收集工作流

> 本文档说明如何每日自动/手动收集、整理、去重 Skill，并更新到本仓库 SKILL 分支。

---

## 工作流概览

```
[每日触发]
    │
    ├─ 1. 从 dodo 远端平台拉取最新 Skill 列表
    ├─ 2. 从 GitHub/网络搜索新兴 AI Skill / Prompt 工程
    ├─ 3. 去重 & 质量甄别
    ├─ 4. 分类归档（10个类别）
    └─ 5. 更新 README.md 总索引 → commit & push
```

---

## Step 1：拉取 dodo 远端 Skill 平台数据

```bash
# 通过 dodo skill-recommender 获取最新 Skill 列表
# 在 dodo 对话中发送：
"帮我列出所有可安装的 Skill，按类别分组"
```

或直接调用平台 API（需 ugate token）：

```bash
# 获取 skill 列表（dodo 内部接口）
curl -H "Authorization: Bearer $UGATE_TOKEN" \
  "https://dodo.baidu-int.com/api/skills/list"
```

---

## Step 2：GitHub / 网络搜索新 Skill

搜索关键词（在 GitHub / 各 AI 社区）：

| 平台 | 搜索词示例 |
|------|-----------|
| GitHub | `awesome-prompts`, `ai-skill`, `llm-tool`, `agent-skill` |
| HuggingFace | `tool`, `agent`, `function-calling` |
| PromptBase | 各类场景 Prompt |
| 各 AI 厂商文档 | `tools`, `function`, `skill` |

---

## Step 3：去重与质量甄别规则

### 判断是否重复

若两个 Skill **核心功能相同**（如都是"代码评审"），执行以下甄别：

| 维度 | 评分标准 |
|------|---------|
| **描述清晰度** | 触发场景明确、用法具体 +2 |
| **覆盖场景** | 支持更多使用场景 +2 |
| **活跃度** | 最近有更新/维护 +1 |
| **来源可信** | 官方/团队维护 +1 |
| **本地已安装** | 可直接使用 +1 |

**评分高者保留，评分相同则保留描述更详尽的版本。**

### 去重后处理

- 被淘汰的 Skill 移至 `archive/` 目录（不直接删除，保留历史）
- 在保留 Skill 的 `.md` 文件中添加"已合并"备注

---

## Step 4：分类归档

当收到一个新 Skill，按以下规则分类：

| 类别 | 关键词 |
|------|--------|
| `dev` | 代码、git、icode、review、debug、devops、API、测试 |
| `data` | 数据、excel、sql、报表、分析、统计、BI |
| `doc` | 文档、word、pdf、ppt、周报、写作、知识库 |
| `design` | 设计、图片、海报、主题、UI、原型、视觉 |
| `ai` | 图像生成、LLM、模型、Claude、Gemini、AI能力 |
| `search` | 搜索、查询、信息获取、实时、爬虫 |
| `ops` | 流水线、部署、CI/CD、运维、日志、监控 |
| `collab` | 协作、会议、日程、通知、群聊、周报、OKR |
| `browser` | 浏览器、WebView、网页、H5 |
| `util` | 日期、定时、鉴权、Token、通用工具 |

> 若一个 Skill 同时符合多个类别，选择**最主要功能**所属类别。

---

## Step 5：更新仓库

```bash
# 1. 切换到 SKILL 分支
git checkout SKILL

# 2. 为新 Skill 创建 .md 文件
# 模板见 skills/TEMPLATE.md

# 3. 更新 README.md 总索引（手动或脚本）
python3 scripts/update_readme.py

# 4. 提交
git add skills/ README.md
git commit -m "chore: daily skill update $(date +%Y-%m-%d)

新增: [skill名称]
更新: [skill名称]
归档: [skill名称]（与xxx合并）"

# 5. 推送
git push origin SKILL
```

---

## Skill .md 文件模板

```markdown
# {skill-name}

## 基本信息

| 字段 | 内容 |
|------|------|
| **名称** | `{skill-name}` |
| **分类** | {类别标签} ({cat}) |
| **来源** | 本地已安装 / 远端平台 |
| **状态** | ✅ 已安装 / 📦 安装后使用 |

## 功能描述

{一句话描述核心功能}

## 关键词 / 触发场景

`关键词1/关键词2/关键词3`

## 使用方式

{如何触发/使用该 Skill}

## 备注

- 收录日期：{YYYY-MM-DD}
- 来源：{来源说明}
```

---

## 自动化脚本（可选）

`scripts/collect.py` — 自动拉取 + 分类 + 生成文件：

```python
# 伪代码示意
skills = fetch_remote_skills()          # 拉取远端列表
existing = load_existing_skills()       # 读取本地已有
new_skills = deduplicate(skills, existing)  # 去重
for skill in new_skills:
    cat = classify(skill)               # 自动分类
    write_skill_md(skill, cat)          # 生成 .md
update_readme(all_skills)              # 更新总索引
git_commit_push()                       # 提交推送
```

---

*最后更新：2026-05-08 | by dodo 🦐*
