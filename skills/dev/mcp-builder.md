---
name: mcp-builder
source: https://github.com/anthropics/skills/tree/main/skills/mcp-builder
author: Anthropic (Official)
category: dev
tags: [MCP, API, integration, server, tools]
collected: 2026-05-08
---

# mcp-builder

> Guide for creating high-quality MCP (Model Context Protocol) servers that enable LLMs to interact with external services.

## 触发场景

构建 MCP server、接入外部 API/服务时使用。支持 Python（FastMCP）和 TypeScript（MCP SDK）。

## 核心工作流（4阶段）

### Phase 1：深度调研与规划

**API 覆盖 vs 工作流工具的取舍：**
- 工作流工具对特定任务更便利
- 全量 API 覆盖给 Agent 更大的组合空间
- 不确定时**优先选全量 API 覆盖**

**工具命名规范：**
```
# 使用一致的前缀 + 动作命名
github_create_issue
github_list_repos
stripe_create_payment_intent
```

**错误信息设计：**
错误信息必须**可操作**，包含具体建议和下一步操作。

### Phase 2：实现规范

```python
# Python FastMCP 示例
from fastmcp import FastMCP

mcp = FastMCP("my-service")

@mcp.tool()
def get_user(user_id: str) -> dict:
    """Get user by ID. Returns user profile including name, email, created_at."""
    # 返回聚焦、相关的数据，避免噪音
    ...
```

```typescript
// TypeScript MCP SDK 示例
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";

const server = new McpServer({ name: "my-service", version: "1.0.0" });

server.tool("get_user", { user_id: z.string() }, async ({ user_id }) => {
  // 实现
});
```

### Phase 3：工具设计原则

```
✅ 工具描述要清晰说明：
   - 什么时候用这个工具
   - 参数的含义和格式
   - 返回值的结构

✅ 支持分页/过滤，避免返回超大数据集

✅ 幂等操作（GET类）和副作用操作（POST/DELETE）明确区分

❌ 避免：
   - 一个工具做太多事
   - 模糊的参数名（用 user_id 而非 id）
   - 不加说明的错误码
```

### Phase 4：测试与发布

1. 用 Claude 作为客户端测试所有工具
2. 验证错误处理路径
3. 检查工具描述是否让 LLM 能正确选择工具

## 快速开始模板

```python
# requirements.txt
fastmcp>=0.1.0

# server.py
from fastmcp import FastMCP
import httpx

mcp = FastMCP("service-name")

@mcp.tool()
async def call_api(endpoint: str, params: dict = {}) -> dict:
    """Call the service API. endpoint: API path without base URL."""
    async with httpx.AsyncClient() as client:
        r = await client.get(f"https://api.example.com/{endpoint}", params=params)
        return r.json()

if __name__ == "__main__":
    mcp.run()
```
