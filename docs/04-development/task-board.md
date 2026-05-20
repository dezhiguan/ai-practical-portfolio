# 任务看板

## 一、任务状态说明

| 状态 | 含义 |
|------|------|
| 待处理 | 未开始 |
| 进行中 | 当前会话正在做 |
| 已完成 | 已验收或文档已交付 |
| 暂缓处理 | 明确延后到后续全项目阶段 |

---

## 二、进行中任务

当前无进行中任务。

---

## 三、待处理任务（按优先级）

### 3.1 文档

| 任务 | 状态 | 备注 |
|------|------|------|
| 编写 `docs/03-architecture/ai-common-infra-architecture.md` | 待处理 | **最高优先级** |
| 对齐 README / guideline 第一阶段清单（可选） | 待处理 | 一致性检查建议 |
| 数据库说明 `docs/05-database/` | 待处理 | 架构后，约 T-018 |
| 接口说明 `docs/06-api/` | 待处理 | 架构后 |
| 测试说明 `docs/07-testing/` | 待处理 | 架构后 |

### 3.2 工程初始化

| 任务 | 状态 | 对应开发计划 |
|------|------|--------------|
| 完善 `ai-common-infra` 工程骨架 | 待处理 | **T-001** |
| `xhs-mom-helper` 目录占位 | 待处理 | 非当前阶段 |
| `smart-job-agent` 目录占位 | 待处理 | 非当前阶段 |

### 3.3 底座开发（详见 ai-common-infra-development-plan.md）

以下全部为**待处理**，请按 T-001 → T-050 顺序执行，勿跳项。

| 阶段 | 任务编号 | 摘要 |
|------|----------|------|
| A 工程与约定 | T-001～T-003 | 骨架、公共约定、配置约定 |
| B 语义与配置 | T-004～T-010 | 请求/响应、枚举、模型与路由配置 |
| C 适配层 | T-011～T-013 | 适配边界、首供应商、第二模型 |
| D 网关最小链路 | T-014～T-017 | 请求编号、最小调用、耗时、校验 |
| E 调用日志 | T-018～T-022 | 存储、写入、列表、详情 |
| F 路由 | T-023 | 按业务类型路由 |
| G 重试降级 | T-024～T-026 | 重试、降级、降级链路记录 |
| H 失败统计 | T-027 | 失败聚合 |
| I Token/成本 | T-030～T-031 | 单次计算、汇总查询 |
| J 防护 | T-028、T-029、T-032 | 限流、熔断、日成本上限 |
| K 编排 | T-033 | 网关完整流水线 |
| L 查询对接 | T-034～T-036 | 日志/成本/失败查询（管理端数据层） |
| M 管理端页面 | T-040～T-046 | 导航、日志、成本、失败、设置、路由状态 |
| N 验收 | T-050 | AC-1～AC-7 集成验收 |

> 完整目标、范围、验收标准见 [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md)。

---

## 四、已完成任务

### 4.1 项目与原则

- [x] 确定三部分组成与建设顺序
- [x] 确定目录结构与文档分层
- [x] 确定高风险自动化不做项
- [x] 确定维护开发过程文档

### 4.2 总纲与过程文档

- [x] `README.md`、`00-project-guideline.md`
- [x] `development-plan.md`
- [x] `progress.md`、`decision-log.md`、`ai-handoff.md`、`task-board.md`
- [x] 各 `docs/` 子目录占位

### 4.3 需求与产品（2026-05-20）

- [x] `ai-common-infra-requirements.md`
- [x] `xhs-mom-helper-requirements.md`
- [x] `smart-job-agent-requirements.md`
- [x] `ai-common-infra-product.md`
- [x] `xhs-mom-helper-product.md`
- [x] `smart-job-agent-product.md`

### 4.4 底座开发计划（2026-05-20）

- [x] `ai-common-infra-development-plan.md`（T-001～T-050 任务拆解）

### 4.5 文档质量

- [x] 底座相关文档一致性检查（架构专文除外）

---

## 五、暂缓处理任务

- [ ] 小红书 / 求职 Agent 需求加深（轻量版已有）
- [ ] 两业务系统架构、库表、接口详设
- [ ] 小红书、求职 Agent 编码（全项目第二、三阶段）
- [ ] 第四阶段能力增强（Prompt、混合检索、动态路由等）
- [ ] 开发计划 T-001～T-050 的实际编码执行（**尚未开始**）

---

## 六、下一步优先任务（Top 3）

1. **编写** `docs/03-architecture/ai-common-infra-architecture.md`  
2. **执行** [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) **T-001**（工程骨架）  
3. 每完成一个 T-xxx：更新本看板、`progress.md`、`ai-handoff.md`（标出当前 T 编号）

---

## 七、最近更新时间

2026-05-20
