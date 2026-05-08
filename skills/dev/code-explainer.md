# code-explainer

## 基本信息

| 字段 | 内容 |
|------|------|
| **名称** | `code-explainer` |
| **分类** | 开发工具 (dev) |
| **来源** | abilzerian/LLM-Prompt-Library (GitHub) |
| **状态** | ✅ 可直接使用 |

## 功能描述

用通俗易懂的自然语言解释任意代码片段的工作原理，使用类比、示例和简单术语，让非技术人员也能理解代码逻辑。

## 关键词 / 触发场景

`代码解释/看懂代码/通俗解释/代码可读性/新手友好`

## Prompt 原文

```
Take the code snippet provided and explain it in simple, easy-to-understand language. Break down the code's functionality, purpose, and key components. Use analogies, examples, and plain terms to make the explanation accessible to someone with minimal coding knowledge. Avoid using technical jargon unless absolutely necessary, and provide clear explanations for any jargon used.

Structure your explanation as follows:
1. **What this code does** (1-2 sentence summary of the overall purpose)
2. **Key components breakdown** (explain each major part in plain language)
3. **Step-by-step walkthrough** (trace through the execution like telling a story)
4. **Real-world analogy** (compare the logic to something from everyday life)
5. **Output / result** (what happens when this code runs)

Additional guidelines:
- Replace jargon with everyday language wherever possible
- Use numbered steps to show sequence of operations
- Highlight the "why" not just the "what" — explain the reasoning behind design choices
- If the code has potential issues or could be improved, mention it briefly at the end
- Keep the explanation concise but complete — aim for clarity over comprehensiveness

Rules:
- no quotes
- no self-reference
- no apologies
- no filler
- just explain
```

## 使用方式

粘贴 Prompt 原文后，紧接着粘贴需要解释的代码块。

## 备注

- 收录日期：2026-05-08
- 来源：https://github.com/abilzerian/LLM-Prompt-Library/blob/main/prompts/programming/Code_Explainer.md
- 适合代码 review 会议前准备材料，或向产品/运营同学解释技术实现
