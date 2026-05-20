# 开发进度记录

## 一、当前阶段

当前处于：

```text
第一阶段：通用 AI 技术底座
```

当前状态：**文档与计划已就绪，尚未开始编码**。

---

## 二、当前模块

当前正在推进：

```text
通用 AI 技术底座 — 架构文档补充（优先）→ 按开发计划 T-001 起逐步实施
```

---

## 三、已完成内容

### 3.1 项目结构与原则

1. 确定项目整体由三个部分组成（底座、小红书、求职 Agent）
2. 确定项目根目录与 `docs/` 分层目录结构
3. 确定建设顺序：先底座 → 再小红书 → 再求职 Agent → 能力增强
4. 确定 AI 辅助、人工确认模式及高风险自动化不做项

### 3.2 总纲与开发过程文档

| 文档 | 路径 |
|------|------|
| 项目入口 | [README.md](../../README.md) |
| 项目总纲 | [docs/00-project-guideline.md](../00-project-guideline.md) |
| 整体开发计划 | [development-plan.md](./development-plan.md) |
| 进度 / 决策 / 交接 / 看板 | `progress.md`、`decision-log.md`、`ai-handoff.md`、`task-board.md` |

### 3.3 需求文档（已完成，2026.v1）

| 文档 | 说明 |
|------|------|
| [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) | 中等详细，含 AC-1～AC-7 |
| [xhs-mom-helper-requirements.md](../01-requirements/xhs-mom-helper-requirements.md) | 轻量 |
| [smart-job-agent-requirements.md](../01-requirements/smart-job-agent-requirements.md) | 轻量 |

### 3.4 产品文档（已完成，2026.v1）

| 文档 | 说明 |
|------|------|
| [ai-common-infra-product.md](../02-product/ai-common-infra-product.md) | 中等详细，含管理端 MVP 页面 |
| [xhs-mom-helper-product.md](../02-product/xhs-mom-helper-product.md) | 轻量 |
| [smart-job-agent-product.md](../02-product/smart-job-agent-product.md) | 轻量 |

### 3.5 底座专项开发计划（已完成，2026.v1）

| 文档 | 说明 |
|------|------|
| [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) | 0→1 任务拆解，共 34 项任务（T-001～T-046、T-050），每项含目标/范围/输入/输出/验收标准 |

要点：

- 仅覆盖 `ai-common-infra`，不涉及两个业务系统编码
- 推荐顺序：工程约定 → 语义与配置 → 适配层 → 网关 → 日志 → 路由 → 重试降级 → 成本 → 限流熔断 → 编排整合 → 管理端 → 阶段验收
- 与需求第七章、产品第七章对齐

### 3.6 文档一致性检查（已完成）

已对 README、guideline、底座需求/产品、整体开发计划、底座开发计划进行交叉比对（架构专文尚未编写，未纳入比对）。

主要结论：

- 阶段顺序、第一阶段边界、不做项、业务隔离：**一致**
- **缺口**：`docs/03-architecture/ai-common-infra-architecture.md` 尚未创建
- **待对齐**：README / guideline 第一阶段能力清单略简于需求 7.2；guideline 失败类型少「参数错误」一类

---

## 四、正在进行

| 项 | 状态 |
|----|------|
| 编码开发 | 未开始 |
| 架构文档 | 待编写（`docs/03-architecture/ai-common-infra-architecture.md`） |
| 开发计划执行 | 待从 **T-001** 或先完成架构文档后启动 |

---

## 五、下一步任务

### 5.1 文档（优先）

1. 编写 [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md)（模块边界、调用链、与需求章节及 T-xxx 对照）
2. 可选：按一致性检查建议微调 README 4.1、guideline 第三章/第七章

### 5.2 底座开发（架构文档后或 T-003 后）

按 [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) 顺序执行：

1. **T-001** 模块工程骨架初始化
2. **T-002** 统一公共约定
3. **T-003** 配置与环境管理约定
4. 后续任务严格按依赖顺序推进，单次会话建议只完成 1 个任务编号

第一阶段能力目标（与需求一致）：网关、路由、降级、日志、Token/成本、失败追踪、重试、限流、熔断、可观测页面。

---

## 六、当前待确认事项

进入 T-001 前或架构文档中逐步确认：

1. 后端基础技术版本（guideline 提及 Java 技术栈）
2. 前端基础技术版本（管理端）
3. 数据库类型（调用日志、统计存储）
4. 大模型供应商接入顺序（T-008、T-012）
5. 本地开发与部署方式
6. 第一阶段是否实现「按用户限流」（需求 4.8 有述，开发计划 T-028 当前仅全局+业务类型）

---

## 七、当前不处理事项

1. 小红书、求职 Agent 业务功能编码
2. 小红书/招聘平台自动发布、登录、抓取、自动回复
3. Prompt 管理、Agent 编排、RAG、动态路由（底座第一阶段不做）
4. 绕过平台限制类功能

---

## 八、最近更新时间

2026-05-20
