# AI 会话交接文档

## 一、一句话状态（2026-05-21）

```text
第一阶段 · 通用 AI 技术底座
✅ 需求/产品/架构/库表/接口/技术栈/开发计划 v2 / 全局文档一致性修订
⏳ T-001 编码（工程骨架）
❌ 未写任何业务代码
```

---

## 二、项目与阶段

- **仓库：** `ai-practical-portfolio`
- **当前：** 全项目第一阶段 = 通用 AI 技术底座
- **工程路径：** `ai-common-infra/backend/`
- **任务清单：** [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md)（**2026.v2**，T-001～T-050）
- **技术基准：** [technology-stack.md](../03-architecture/technology-stack.md)

---

## 三、本次会话已完成

1. **全局文档一致性修订**（README、guideline、progress、decision-log、ai-handoff）
2. 对齐技术栈：PostgreSQL、Maven、pnpm、React；前端不直连大模型；业务后端经底座 Invoke

---

## 四、下一次会话：建议做什么

**执行 T-001**（见开发计划 §三 阶段 A）

| 项 | 内容 |
|----|------|
| 技术栈 | Java 21、Spring Boot 3、**Maven**、MyBatis Plus、Flyway、**PostgreSQL** |
| 目录 | `ai-common-infra/backend/` 包结构见开发计划 §1.5 |
| 验收 | 可构建、可空启动、健康检查 |

完成后更新 task-board 勾选 T-001，并更新本文件当前 T 编号为 **T-002**。

---

## 五、恢复上下文：阅读顺序

1. 本文件 → [progress.md](./progress.md) → [task-board.md](./task-board.md)
2. [technology-stack.md](../03-architecture/technology-stack.md)
3. [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md)（当前 T-xxx）
4. [ai-common-infra-api.md](../06-api/ai-common-infra-api.md)
5. [ai-common-infra-database.md](../05-database/ai-common-infra-database.md)
6. [ai-command-templates.md](./ai-command-templates.md)

---

## 六、关键约束

- 单次会话 **1 个 T 编号**
- 不做 xhs / smart-job-agent 业务功能
- 所有 LLM 经 `POST /api/v1/ai/invoke`（业务后端调用，前端禁止直连）
- 主库 **PostgreSQL**；后端 **Maven**；前端 **pnpm**（业务阶段）

---

## 七、最近更新时间

2026-05-21
