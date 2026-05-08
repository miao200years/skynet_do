---
name: prompt-engineering-guide
source: https://github.com/langgptai/awesome-claude-prompts + Anthropic Docs
author: Community + Anthropic
category: ai
tags: [prompt-engineering, Claude, LLM, system-prompt, chain-of-thought]
collected: 2026-05-08
---

# prompt-engineering-guide

> Claude 和主流 LLM 的提示词工程完整指南：结构化、思维链、角色设定、输出控制。

## 触发场景

- 写 system prompt
- 优化提示词效果
- 让 AI 输出更稳定可控
- 构建 AI 应用的指令层

## 核心原则

### 1. 结构化优先

```
# 好的 System Prompt 结构

## 角色与目标
你是 [角色名称]，负责 [核心职责]。

## 能力范围
- 可以做：[列举]
- 不可以做：[列举]

## 输出格式
[明确指定格式：JSON/Markdown/纯文本等]

## 示例
输入：[示例输入]
输出：[期望输出]

## 注意事项
[特殊规则和边界条件]
```

### 2. 越具体越好

```
❌ 模糊
"总结这篇文章"

✅ 具体
"用 3 个要点总结这篇文章，每个要点不超过 20 字，
用中文，格式为 Markdown 无序列表，
专注于对商业决策最有影响的信息"
```

### 3. 思维链（Chain of Thought）

```
对于复杂推理任务，让模型"先想后答"：

方式A（直接触发）：
"一步一步思考这个问题，在给出答案前先列出推理过程"

方式B（XML 标签）：
"在 <thinking> 标签内写出推理过程，在 <answer> 标签内给出最终答案"

方式C（Few-shot）：
"问题：2 + 2 × 3 = ?
思考：先算乘法 2×3=6，再算加法 2+6=8
答案：8

问题：[你的问题]"
```

## Claude 专项技巧

### XML 标签结构化

```xml
<system>
你是专业的数据分析师。请分析用户提供的数据，
严格按照 <output_format> 中的格式输出。
</system>

<output_format>
{
  "summary": "一句话结论",
  "key_findings": ["发现1", "发现2", "发现3"],
  "recommendation": "建议行动",
  "confidence": "high|medium|low"
}
</output_format>

<data>
[用户数据放这里]
</data>
```

### 角色设定（Roleplay）

```
# 有效的角色设定

你是一位经验丰富的后端工程师，专注于 Python 和 FastAPI。
你总是：
- 先理解需求再写代码
- 写完整可运行的代码，不省略 import
- 代码中加注释解释关键决策
- 如果需求不清楚，先问清楚再动手

你从不：
- 写未经测试的代码就说"应该可以"
- 使用已弃用的 API
- 忽略错误处理
```

### 少样本学习（Few-Shot）

```
# 情感分析 Few-Shot 示例

将以下评论分类为：正面 / 负面 / 中性

评论："这个产品真的很好用，强烈推荐！"
分类：正面

评论："一般般，没什么特别的"
分类：中性

评论："完全是浪费钱，不推荐任何人买"
分类：负面

评论："[待分类内容]"
分类：
```

## 输出格式控制

### 强制 JSON 输出

```
请分析以下文本，严格以 JSON 格式输出，不要有任何其他文字：

{
  "sentiment": "positive|negative|neutral",
  "confidence": 0.0-1.0,
  "key_phrases": ["短语1", "短语2"],
  "summary": "一句话摘要"
}

文本：[输入]
```

### Markdown 结构化

```
请以 Markdown 格式输出分析报告，结构如下：
## 执行摘要（≤100字）
## 主要发现（3-5条要点）
## 风险与挑战
## 建议行动（按优先级排序）
```

## 常见陷阱与避免方法

| 问题 | 原因 | 解决方案 |
|------|------|---------|
| 输出不稳定 | 提示词过于开放 | 加示例+格式限定 |
| 拒绝执行任务 | 触发安全限制 | 澄清合法用途上下文 |
| 输出太啰嗦 | 没有长度限制 | 明确字数/要点数限制 |
| 忽略指令 | 指令在末尾被遗忘 | 重要指令放开头+结尾 |
| 幻觉问题 | 超出知识范围 | 提供参考资料，要求引用 |

## 评估提示词质量

```python
# 提示词 A/B 测试框架
test_inputs = ["输入1", "输入2", "输入3"]

def score_output(output, criteria):
    """
    criteria: {
      "format_compliance": "是否符合指定格式",
      "completeness": "是否涵盖所有要求",
      "accuracy": "内容是否准确"
    }
    """
    # 人工评分或用 GPT-4 / Claude 自动评分
    pass

# 对每个 prompt 变体跑相同测试，比较分数
```
