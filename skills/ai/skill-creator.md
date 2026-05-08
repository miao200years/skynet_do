---
name: skill-creator
source: https://github.com/anthropics/skills/tree/main/skills/skill-creator
author: Anthropic (Official)
category: ai
tags: [skill, meta, prompt-engineering, evaluation, optimization]
collected: 2026-05-08
---

# skill-creator

> 交互式创建新 Skill、优化已有 Skill，并通过评估循环衡量性能。

## 触发场景

- 从零创建新 Skill
- 迭代改进已有 Skill 的提示词
- 优化 Skill 的触发描述（让 AI 更准确地选择 Skill）
- 对 Skill 进行 A/B 测试和基准测量

## Skill 创建流程

```
1. 确定 Skill 要做什么（大方向）
    ↓
2. 写第一稿
    ↓
3. 创建 3-5 个测试 prompt
    ↓
4. 用"带 Skill 的 Claude"跑测试 → 看结果
    ↓
5. 评估：定性（看输出好不好）+ 定量（自动化指标）
    ↓
6. 根据反馈重写 Skill
    ↓
7. 重复，直到满意
    ↓
8. 扩大测试集规模，最终验证
```

## SKILL.md 文件结构规范

```markdown
---
name: my-skill-name          # 唯一标识符（kebab-case）
description: >               # 关键！AI 靠这个决定是否调用
  One paragraph describing when to use this skill.
  Be specific about trigger scenarios.
  Include example phrases that should activate it.
---

# My Skill Name

## 触发场景（当...时使用）

明确的触发条件，越具体越好。

## 核心指令

这里放核心 prompt 内容。

## 使用示例

具体的输入输出示例。

## 注意事项

边界情况和不应触发的场景。
```

## 触发描述优化技巧

**好的触发描述特征：**
```yaml
# ✅ 具体、含例子、说明何时用/何时不用
description: >
  Guide for building Stripe payment integrations.
  Use when: accepting payments, building subscriptions, creating connected accounts.
  Trigger phrases: "add Stripe", "payment integration", "checkout flow", "billing".
  NOT for: general API design, non-Stripe payment processors.
```

```yaml
# ❌ 太模糊
description: Helps with payments.
```

## 评估框架

### 定性评估（人工）
对每个测试 case 问：
1. 是否触发了正确的 Skill？（精确度）
2. 输出质量是否达标？（相关性/正确性）
3. 有没有明显遗漏或错误？

### 定量指标
```python
# 评估脚本框架
test_cases = [
    {"input": "...", "expected_skill": "my-skill", "expected_keywords": ["..."]},
]

results = []
for case in test_cases:
    output = run_claude_with_skill(case["input"])
    results.append({
        "skill_triggered": check_skill_in_output(output),
        "keyword_match": all(k in output for k in case["expected_keywords"]),
    })

accuracy = sum(r["skill_triggered"] for r in results) / len(results)
print(f"触发准确率: {accuracy:.1%}")
```

## 常见问题

| 问题 | 解决方案 |
|------|---------|
| Skill 很少被触发 | 在 description 中加更多触发词和示例句 |
| Skill 被错误触发 | 加"NOT for"排除场景 |
| 输出质量不稳定 | 在 SKILL.md 中加更多示例和格式要求 |
| Skill 太长导致混乱 | 拆分为多个专注小 Skill |
