# AI 会话交接文档

## 一、一句话状态（2026-05-21）

```text
第一阶段 · 通用 AI 技术底座
✅ 需求/产品/架构/数据库/接口设计正文
⏳ T-001 编码（工程骨架）
❌ 未写任何业务系统代码
```

---

## 二、项目与阶段

- **仓库：** `ai-practical-portfolio`
- **当前：** 全项目第一阶段 = 通用 AI 技术底座
- **工程路径：** `ai-common-infra/backend/`

---

## 三、本次会话已完成

1. **底座接口设计正文** — [ai-common-infra-api.md](../06-api/ai-common-infra-api.md)  
   - `POST /api/v1/ai/invoke`（唯一 AI 调用入口）  
   - Admin：日志列表/详情、成本统计、失败统计、模型/路由查询  
2. **过程文档** 更新：progress、decision-log、task-board、本文件

此前已完成：架构、数据库、需求/产品等（勿重复做）。

---

## 四、底座第一阶段边界

| 做 | 不做 |
|----|------|
| Invoke + Admin 只读查询 + 统一错误约定 | 流式、批量、路由写 API、报表导出 |

验收：需求 **AC-1～AC-7**；接口见 api 文档 §五～§七。

---

## 五、下一次会话：建议做什么

**推荐：执行 T-001**

| 项 | 说明 |
|----|------|
| 任务 | T-001 模块工程骨架初始化 |
| 路径 | `ai-common-infra/backend/` |
| 参考 | [ai-common-infra-api.md](../06-api/ai-common-infra-api.md) §三 通用约定、[ai-common-infra-database.md](../05-database/ai-common-infra-database.md) |
| 规则 | 一次会话只做一个 T-xxx |

T-004/T-005 可直接引用接口文档 §5.1 字段，无需重定义。

---

## 六、恢复上下文：阅读顺序

1. 本文件 → [progress.md](./progress.md) → [task-board.md](./task-board.md)  
2. [ai-common-infra-api.md](../06-api/ai-common-infra-api.md)  
3. [ai-common-infra-database.md](../05-database/ai-common-infra-database.md)  
4. [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md)  
5. [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) — 当前 T-xxx  

---

## 七、关键约束（必守）

- 唯一 AI HTTP 入口：`POST /api/v1/ai/invoke`（业务后端调用）  
- 业务前端、任何前端不得直连大模型或直连 Invoke  
- Invoke 失败且已编排完成：HTTP 200 + `data.status=FAILED`  

---

## 八、文档速查

```text
docs/06-api/ai-common-infra-api.md   ← 接口 ✅
docs/05-database/...               ← 库表 ✅
docs/03-architecture/...             ← 架构 ✅
ai-common-infra/backend/             ← T-001 起
```

---

## 九、待决策（非阻塞）

1. MySQL vs PostgreSQL（T-001）  
2. 管理端独立 `frontend/` 或后端托管静态页  
3. Admin 鉴权：仅 Api-Key vs 登录 Token  
4. 首个模型供应商  

---

## 十、最近更新时间

2026-05-21
