# typescript-code-organization

## 基本信息

| 字段 | 内容 |
|------|------|
| **名称** | `typescript-code-organization` |
| **分类** | 开发工具 (dev) |
| **来源** | cloudflare/agents (GitHub ⭐2.3k) |
| **状态** | ✅ 可直接使用 |
| **语言** | TypeScript |

## 功能描述

TypeScript 代码组织决策助手——在"消除重复"和"保持类型安全"之间做出正确权衡，避免为了 DRY 而引入 `!` 类型断言和不必要的类耦合。

## 关键词 / 触发场景

`TypeScript/类型安全/DRY原则/代码重构/类设计/接口设计`

## Prompt 原文

```
When making code organization decisions, consider the broader impact beyond just eliminating duplication or following patterns. Sometimes a slightly less "clean" solution is preferable when it preserves type safety, maintains clear class responsibilities, or avoids exposing unnecessary implementation details.

Key considerations:
- **Type safety over DRY**: Minor code duplication may be acceptable if extraction would require type assertions or null checks
- **Class focus**: Avoid adding functionality to existing classes just for convenience; consider separate specialized classes instead
- **Interface clarity**: Don't implement interfaces that expose methods irrelevant to the class's primary purpose

When reviewing or writing TypeScript code, apply this decision framework:
1. Would extracting this code require adding `!` (non-null assertion) or type casting?
   → If yes, duplication is likely preferable
2. Does the extracted helper logically belong to this class's core responsibility?
   → If no, consider a separate utility or class
3. Would implementing this interface expose methods the class shouldn't have?
   → If yes, reconsider the interface design

Example of type-safety-first thinking:
- PREFER explicit branches that preserve TypeScript narrowing
- AVOID ternary/conditional expressions that lose type information and require assertions
- PREFER small focused classes over large "helper" classes that accumulate responsibilities
- AVOID interface implementation just for convenience when it adds irrelevant methods

The goal is maintainable code that serves developers well, not just adherence to abstract principles.
```

## 使用方式

在做 TypeScript 代码重构决策时，发送此 Prompt 后粘贴你的代码，让 AI 按此框架评审是否值得提取公共代码。

## 备注

- 收录日期：2026-05-08
- 来源：https://github.com/cloudflare/agents（⭐2312，Cloudflare 官方 AI Agents 框架）
- 核心理念：**类型安全 > DRY 原则**——写出开发者友好的代码，而非仅追求形式上的"干净"
