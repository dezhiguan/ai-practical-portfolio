# 开发进度记录

## 一、当前阶段

```text
第一阶段：通用 AI 技术底座
```

**整体状态：** 文档体系与目录骨架已就绪；底座架构正文已完成；编码未开始。

---

## 二、当前模块

```text
通用 AI 技术底座（ai-common-infra）
```

**当前焦点：** 在 `ai-common-infra/backend/` 执行 T-001（工程骨架初始化）。

---

## 三、已完成内容

### 3.1 项目原则与总纲

- 三部分组成、建设顺序（底座 → 小红书 → 求职 Agent → 增强）
- AI 辅助 + 人工确认；高风险自动化不做
- [README.md](../../README.md)、[00-project-guideline.md](../00-project-guideline.md)

### 3.2 需求与产品（2026.v1）

| 类型 | 文档 |
|------|------|
| 需求 | 底座（中等详细）+ 两业务（轻量），含阶段边界与 AC |
| 产品 | 底座（中等详细，管理端 MVP）+ 两业务（轻量） |

路径：`docs/01-requirements/`、`docs/02-product/`

### 3.3 开发计划

| 文档 | 说明 |
|------|------|
| [development-plan.md](./development-plan.md) | 全项目四阶段 |
| [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) | 底座 0→1，T-001～T-050 |

### 3.4 项目目录结构（本次已完成）

已与目标目录树对齐：

```text
ai-common-infra/          → backend/ + README.md（无 frontend，管理端待定）
xhs-mom-helper/           → backend/ + frontend/ + README.md（占位）
smart-job-agent/          → backend/ + frontend/ + README.md（占位）
docs/03-architecture/     → 三模块架构占位 md
docs/04-development/      → frontend/backend-development-guide 占位
```

[README.md](../../README.md) 第三节已更新为完整目录树（含 `ai-common-infra-development-plan.md`）。

### 3.5 文档一致性检查

已对 README、guideline、底座需求/产品、两份开发计划做比对。

- 阶段顺序、第一阶段边界、不做项、业务隔离：**一致**
- 架构：**底座架构正文已完成**（`ai-common-infra-architecture.md`）；两业务架构仍为占位
- 可选后续：README/guideline 第一阶段清单与需求 7.2 完全对齐；guideline 失败类型补「参数错误」

### 3.6 架构设计（2026-05-21）

- [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) 正文已完成
- 覆盖：架构目标、模块边界、调用链路、核心组件职责、业务复用、第一阶段范围、T-xxx 映射
- 未包含：数据库表结构、接口细节、代码实现

### 3.7 开发过程文档

持续维护：`progress.md`、`decision-log.md`、`ai-handoff.md`、`task-board.md`

---

## 四、正在进行

| 项 | 状态 |
|----|------|
| 底座编码（T-001～T-050） | 未开始 |
| `backend-development-guide.md` / `frontend-development-guide.md` | 占位已建，待编写（可与 T-002～T-003 同步） |
| 两业务架构正文 | 占位已建，第二阶段/第三阶段前再写 |

当前无「进行中」编码或文档编写任务（会话间隙）。

---

## 五、下一步任务

### 优先级 1：工程

1. 执行 **T-001**：在 `ai-common-infra/backend/` 初始化可构建的工程骨架
2. 单次会话只完成一个 T-xxx，完成后更新本目录下四份过程文档

### 优先级 2：文档（与编码并行）

3. 可选：充实 [backend-development-guide.md](./backend-development-guide.md)（与 T-002、T-003 同步）
4. T-018 前补充 `docs/05-database/` 底座库表说明

### 优先级 3：可选文档维护

5. 按一致性检查建议微调 README 4.1、guideline 相关章节

---

## 六、待确认事项（进入 T-008 / T-012 前）

1. Java 后端版本与构建方式（guideline 为 Java 技术栈）
2. 管理端前端形态及目录（是否增加 `ai-common-infra/frontend/`）
3. 调用日志存储方案（关系型 DB 等）
4. 首个大模型供应商
5. 第一阶段是否实现「按用户限流」（T-028 当前为全局 + 按业务类型）

---

## 七、当前不处理

- 小红书、求职 Agent 业务编码
- 自动发布 / 登录 / 抓取 / 自动回复 HR
- Prompt 管理、Agent 编排、RAG、动态路由（底座第一阶段）
- `05-database` / `06-api` 详设（架构稳定后）

---

## 八、最近更新时间

2026-05-21
