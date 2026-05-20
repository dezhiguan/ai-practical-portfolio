# AI 会话交接文档

## 一、一句话状态（2026-05-21）

```text
第一阶段 · 通用 AI 技术底座
✅ 需求/产品/开发计划/架构/数据库设计正文
⏳ T-001 编码（工程骨架）
❌ 未写任何业务系统代码
```

---

## 二、项目与阶段

- **仓库：** `ai-practical-portfolio`
- **当前：** 全项目第一阶段 = 通用 AI 技术底座
- **工程路径：** `ai-common-infra/backend/`（编码从这里开始）

---

## 三、本次会话已完成

1. **底座数据库设计正文** — [ai-common-infra-database.md](../05-database/ai-common-infra-database.md)  
   - 10 张第一阶段表、扩展表说明  
   - 数据来源：业务请求 / 模型响应 / 系统统计  
   - 重点：调用日志、尝试明细、模型/路由/单价、额度策略、日成本用量  
2. **过程文档** 更新：progress、decision-log、task-board、本文件

此前已完成：架构正文、需求/产品、目录骨架等（勿重复做）。

---

## 四、底座第一阶段边界

| 做 | 不做 |
|----|------|
| 网关、路由、降级、日志、Token/成本、失败追踪、重试、限流、熔断、管理端可观测、日成本上限 | Prompt、动态路由、Agent 编排、RAG、多租户计费、业务功能 |

验收：需求文档 **AC-1～AC-7**。

---

## 五、下一次会话：建议做什么

**推荐：执行 T-001**

| 项 | 说明 |
|----|------|
| 任务 | T-001 模块工程骨架初始化 |
| 路径 | `ai-common-infra/backend/` |
| 输入 | [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md)、[ai-common-infra-database.md](../05-database/ai-common-infra-database.md) §四 |
| 产出 | 可构建、可空启动的工程骨架；包/目录与逻辑模块可对应 |
| 规则 | 一次会话只做一个 T-xxx |

T-018 实施时直接对照数据库文档字段集，无需重新设计表。

---

## 六、恢复上下文：阅读顺序

1. 本文件 → [progress.md](./progress.md) → [task-board.md](./task-board.md)  
2. [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md)  
3. [ai-common-infra-database.md](../05-database/ai-common-infra-database.md)  
4. [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) — 当前 T-xxx  
5. [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) §七、§八  

---

## 七、关键约束（必守）

- 所有 AI 调用经统一网关；业务不得直连模型 API  
- 日志库表不存 API Key、默认不存完整输入输出全文  
- 不在底座阶段开发小红书 / 求职 Agent 功能  

---

## 八、工程与文档速查

```text
ai-common-infra/backend/     ← T-001 起
docs/03-architecture/        ← 架构正文 ✅
docs/05-database/            ← 库表设计 ✅
docs/06-api/                 ← 接口说明，待补充
```

**第一阶段核心表：** `ai_invocation_log`、`ai_invocation_attempt`、`ai_model`、`ai_route_rule`、`ai_route_fallback`、`ai_model_pricing`、`ai_quota_policy`、`ai_quota_usage_daily`

---

## 九、待决策（非阻塞）

1. MySQL vs PostgreSQL（T-001 定）  
2. 管理端是否独立 `frontend/`  
3. 按用户限流是否第一阶段落库（`ai_quota_policy.USER_RATE_LIMIT`）  
4. 首个模型供应商  

---

## 十、最近更新时间

2026-05-21
