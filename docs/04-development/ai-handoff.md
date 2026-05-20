# AI 会话交接文档

## 一、一句话状态（2026-05-21）

```text
第一阶段 · 通用 AI 技术底座
✅ 需求/产品/架构/库表/接口/技术栈文档
⏳ T-001 编码（工程骨架）
❌ 未写任何业务代码
```

---

## 二、项目与阶段

- **仓库：** `ai-practical-portfolio`
- **当前：** 全项目第一阶段 = 通用 AI 技术底座
- **工程路径：** `ai-common-infra/backend/`

---

## 三、本次会话已完成

1. **全项目技术栈说明** — [technology-stack.md](../03-architecture/technology-stack.md)  
   - 整体/后端/前端/DB/缓存/文件/AI/RAG/Agent  
   - 分阶段必须 vs 扩展；关键技术选型原因  
2. **过程文档** 更新：progress、decision-log、task-board、本文件

---

## 四、技术栈速记（T-001 用）

| 类别 | 第一阶段 |
|------|----------|
| 后端 | Java 17、Spring Boot 3、MySQL 8、Flyway/Liquibase |
| AI | 自研 Gateway、WebClient/OkHttp Adapter、同步 Invoke |
| 缓存 | Caffeine（不用 Redis） |
| 前端 | 管理端 Vue3 可延后；Invoke/Admin API 先通 |
| 不用 | Spring AI、RAG、Agent 框架、MQ、Spring Cloud |

待 T-001 锁定：JPA vs MyBatis-Plus、Maven vs Gradle、首个模型厂商。

---

## 五、下一次会话：建议做什么

**推荐：执行 T-001**

| 项 | 说明 |
|----|------|
| 任务 | 模块工程骨架初始化 |
| 参考 | [technology-stack.md](../03-architecture/technology-stack.md) §3、§7.1 |
| 产出 | 可构建空 Spring Boot 工程 + MySQL 连接占位 + 包结构对齐架构模块 |

---

## 六、恢复上下文：阅读顺序

1. 本文件 → [progress.md](./progress.md) → [task-board.md](./task-board.md)  
2. [technology-stack.md](../03-architecture/technology-stack.md)  
3. [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md)  
4. [ai-common-infra-api.md](../06-api/ai-common-infra-api.md)  
5. [ai-common-infra-database.md](../05-database/ai-common-infra-database.md)  
6. [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) — T-001  

---

## 七、关键约束（必守）

- Java 17 + Spring Boot 3；主库 MySQL 8  
- 不自建第二套 LLM 客户端在业务/底座重复实现  
- 第一阶段不引入 Redis、向量库、Agent 框架  

---

## 八、文档速查

```text
docs/03-architecture/technology-stack.md  ← 技术栈 ✅
docs/06-api/ai-common-infra-api.md
docs/05-database/ai-common-infra-database.md
ai-common-infra/backend/                   ← T-001
```

---

## 九、待决策（T-001 写入 decision-log）

1. JPA vs MyBatis-Plus  
2. Maven vs Gradle  
3. 管理端：独立 frontend 或 static  
4. 首个大模型供应商（OpenAI 兼容优先）  

---

## 十、最近更新时间

2026-05-21
