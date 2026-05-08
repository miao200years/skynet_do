---
name: stripe-best-practices
source: https://github.com/anthropics/skills/tree/main/skills/stripe-best-practices
author: Stripe (Official)
category: dev
tags: [Stripe, payments, API, integration, SaaS, billing]
collected: 2026-05-08
---

# stripe-best-practices

> Stripe 支付集成最佳实践：API 选择、Connect 平台、订阅计费、安全规范。

## 触发场景

构建、修改或审查任何 Stripe 集成时使用。包括：接受支付、构建市场、处理订阅、创建关联账户、安全密钥管理。

**当前最新 Stripe API 版本：`2026-04-22.dahlia`**

## 集成路由决策表

| 构建场景 | 推荐 API |
|---------|---------|
| 一次性支付 | Checkout Sessions |
| 嵌入式自定义支付表单 | Checkout Sessions + Payment Element |
| 保存支付方式（后续扣款） | Setup Intents |
| Connect 平台/市场 | Accounts v2 (`/v2/core/accounts`) |
| 订阅/定期计费 | Billing APIs + Checkout Sessions |
| 嵌入式金融账户/银行功能 | v2 Financial Accounts |

## 关键规则（必读）

```
❌ 永远不要在 Stripe API 调用中添加 payment_method_types 参数
   （唯一例外：Terminal 现场支付必须传 payment_method_types: ['card_present']）

✅ 始终使用最新 API 版本和 SDK

✅ Webhook 必须验证签名
   stripe.webhooks.constructEvent(payload, sig, webhookSecret)

✅ API Key 管理：
   - 生产环境用受限密钥（Restricted Keys），不用完全权限密钥
   - 服务器端密钥永远不传到前端
   - 不同环境（dev/staging/prod）使用不同密钥
```

## 代码示例

### Checkout Session（推荐方式）

```python
import stripe
stripe.api_key = "sk_..."  # 使用受限密钥

# 创建 Checkout Session
session = stripe.checkout.Session.create(
    mode="payment",  # 或 "subscription"
    line_items=[{
        "price": "price_xxx",
        "quantity": 1,
    }],
    success_url="https://yoursite.com/success?session_id={CHECKOUT_SESSION_ID}",
    cancel_url="https://yoursite.com/cancel",
)
# 重定向到 session.url
```

### Webhook 处理

```python
@app.route("/webhook", methods=["POST"])
def webhook():
    payload = request.data
    sig_header = request.headers.get("Stripe-Signature")

    try:
        event = stripe.webhooks.constructEvent(
            payload, sig_header, webhook_secret
        )
    except ValueError:
        return "Invalid payload", 400
    except stripe.error.SignatureVerificationError:
        return "Invalid signature", 400

    if event["type"] == "checkout.session.completed":
        session = event["data"]["object"]
        # 处理支付成功逻辑

    return "", 200
```

### 订阅管理

```python
# 创建订阅
subscription = stripe.Subscription.create(
    customer=customer_id,
    items=[{"price": "price_monthly_xxx"}],
    payment_behavior="default_incomplete",
    payment_settings={"save_default_payment_method": "on_subscription"},
    expand=["latest_invoice.payment_intent"],
)
```

## 安全检查清单

- [ ] 使用受限 API 密钥（最小权限原则）
- [ ] 所有 Webhook 验证签名
- [ ] 密钥存储在环境变量中（非代码）
- [ ] 前端不接触 secret key
- [ ] 启用 Radar 欺诈检测规则
- [ ] 测试环境和生产环境密钥隔离
