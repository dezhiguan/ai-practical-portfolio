# 通用 AI 技术底座 — 开发计划（0→1 任务拆解）

版本：2026.v2  
模块：`ai-common-infra/`  
范围：仅通用 AI 技术底座，不涉及小红书、求职 Agent 业务开发

---

## 一、文档说明

### 1.1 文档定位

本文档将通用 AI 技术底座从 **0 到 1** 的建设工作拆解为可单独执行的小任务，供按序逐步开发与验收。

每个任务统一包含：

| 字段 | 说明 |
|------|------|
| **任务目标** | 本任务要达成的结果 |
| **任务范围** | 本任务包含的工作边界 |
| **本次不做什么** | 防止范围蔓延的明确排除项 |
| **涉及文档** | 需对照的上游设计文档 |
| **涉及目录** | 主要改动的工程/文档路径 |
| **验收标准** | 可检查的完成条件 |

### 1.2 依据文档

| 文档 | 路径 | 状态 |
|------|------|------|
| 需求说明 | [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) | 已完成 |
| 产品设计 | [ai-common-infra-product.md](../02-product/ai-common-infra-product.md) | 已完成 |
| 架构设计 | [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) | 已完成 |
| 数据库设计 | [ai-common-infra-database.md](../05-database/ai-common-infra-database.md) | 已完成 |
| 接口设计 | [ai-common-infra-api.md](../06-api/ai-common-infra-api.md) | 已完成 |
| 技术栈 | [technology-stack.md](../03-architecture/technology-stack.md) | 已完成 |
| 全链路映射表 | [ai-common-infra-module-mapping.md](../02-product/ai-common-infra-module-mapping.md) | 已完成 |
| 项目总计划 | [development-plan.md](./development-plan.md) | 阶段目标对齐 |

### 1.3 编写约束

1. 只做任务拆解，**不写实现代码**（任务内可写迁移脚本、配置模板）
2. 不涉及 `xhs-mom-helper/`、`smart-job-agent/` 任何业务功能
3. 单任务控制在「单次会话或半天内可完成并验收」
4. 严格按推荐顺序执行，不跳过依赖

### 1.4 阶段验收总表（需求 AC）

| 编号 | 验收项 | 主要覆盖任务 |
|------|--------|--------------|
| AC-1 | 调用后可在日志列表按请求编号查到记录 | T-020～T-022、T-041～T-042 |
| AC-2 | 成功记录含业务类型、模型、Token、耗时、估算成本 | T-020、T-030、T-043 |
| AC-3 | 主模型失败可降级成功或记录完整降级链路 | T-024～T-026、T-042 |
| AC-4 | 失败可按类型筛选与统计 | T-021、T-027、T-044 |
| AC-5 | 可按日期、业务类型、模型汇总 Token/成本 | T-030～T-031、T-043 |
| AC-6 | 限流或日成本超限时调用方收到明确原因且有日志 | T-028、T-029、T-032 |
| AC-7 | 不同项目标识在统计中可区分 | T-006、T-031、T-043 |

### 1.6 需求 §7.2 能力 — 任务覆盖（专项一致性）

> 功能—页面—架构—API—表—技术栈六列对照见 [ai-common-infra-module-mapping.md](../02-product/ai-common-infra-module-mapping.md)。

| 需求能力（§7.2） | 覆盖任务 | 状态 |
|------------------|----------|------|
| 1 统一 AI 网关 | T-014～T-017、T-033 | ✅ |
| 2 多模型路由 | T-009、T-023 | ✅ |
| 3 模型降级 | T-025～T-026 | ✅ |
| 4 调用日志 | T-018～T-022、T-034、T-041～T-042 | ✅ |
| 5 Token 统计 | T-020、T-030 | ✅ |
| 6 成本统计 | T-030～T-031、T-035、T-043 | ✅ |
| 7 失败追踪 | T-007、T-027、T-036、T-044 | ✅ |
| 8 重试机制 | T-024 | ✅ |
| 9 基础限流 | T-028（全局+按业务类型；按用户延后） | ✅ |
| 10 基础熔断 | T-029 | ✅ |
| 11 基础可观测页面 | T-040～T-046 | ✅ |
| 日成本上限 subset | T-032、T-039、T-045 | ✅ |

**核心链路无遗漏：** AI Gateway、Model Router、Degradation、Invocation Logger、Token/Cost、Failure Tracker、Retry、Rate Limit、Circuit Breaker、Cost Guard 均在 §二 阶段总览与上表有对应任务。

**第一阶段不做（与需求 §八、§五排除项一致）：** Prompt 管理、动态路由、Agent 编排、RAG/Milvus、多租户计费、复杂告警导出、业务系统功能。

### 1.5 工程与包结构约定（T-001 后统一）

```text
ai-common-infra/
├── backend/                          # Spring Boot 主工程
│   ├── src/main/java/.../
│   │   ├── common/                   # 统一返回、异常、枚举
│   │   ├── config/                   # 配置加载
│   │   ├── gateway/                  # AI Gateway 编排
│   │   ├── router/                   # Model Router
│   │   ├── adapter/                  # Provider Adapter、Model Client
│   │   ├── resilience/               # 重试、降级、限流、熔断
│   │   ├── observability/            # 日志写入、成本计算、统计查询
│   │   ├── admin/                    # Admin API（日志、统计、配置查询）
│   │   └── persistence/              # MyBatis Plus、实体、Mapper
│   └── src/main/resources/
│       ├── application*.yml
│       └── db/migration/             # Flyway 脚本
├── frontend/                         # 可选：管理端 React（T-040 前确定）
└── README.md
```

---

## 二、推荐开发顺序总览

```text
阶段 A：工程与约定（T-001～T-003）
    ↓
阶段 B：核心语义与配置（T-004～T-010）
    ↓
阶段 C：供应商适配层（T-011～T-013）
    ↓
阶段 D：网关最小主链路（T-014～T-017）
    ↓
阶段 E：调用日志与追溯（T-018～T-022）
    ↓
阶段 F：模型路由（T-023）
    ↓
阶段 G：重试与降级（T-024～T-026）
    ↓
阶段 H：失败统计（T-027）
    ↓
阶段 I：Token 与成本（T-030～T-031）
    ↓
阶段 J：限流、熔断、成本防护（T-028～T-029、T-032）
    ↓
阶段 K：网关编排整合（T-033）
    ↓
阶段 L：Admin API 对接（T-034～T-036）
    ↓
阶段 M：管理端页面（T-040～T-046）
    ↓
阶段 N：阶段验收（T-050）
```

| 架构逻辑模块 | 任务编号 |
|--------------|----------|
| 工程骨架与公共约定 | T-001～T-003 |
| 请求/响应语义、枚举、配置 | T-004～T-010 |
| 供应商适配 | T-011～T-013 |
| Gateway 主链路 | T-014～T-017、T-033 |
| 日志持久化与查询 | T-018～T-022、T-034 |
| 路由 / 重试 / 降级 | T-023～T-026 |
| 失败聚合 | T-027、T-036 |
| 限流 / 熔断 / 日成本 | T-028～T-029、T-032 |
| Token 与成本 | T-030～T-031、T-035 |
| 管理端 UI | T-040～T-046 |

---

## 三、任务清单

> **图例：** 依赖关系见 §四。单次会话建议只完成 **1 个** 任务编号。

---

### 阶段 A：工程与约定

#### T-001 模块工程骨架初始化

| 项 | 内容 |
|----|------|
| **任务目标** | 建立可独立构建、可空启动的 `ai-common-infra` 后端工程骨架 |
| **任务范围** | Java 21 + Spring Boot 3 + Maven；MyBatis Plus + Flyway + PostgreSQL 驱动占位；包目录按 §1.5；`application-dev.yml` 示例；健康检查端点；模块 README |
| **本次不做什么** | 不实现 Gateway/Adapter/业务逻辑；不含 `xhs-mom-helper`、`smart-job-agent` 代码；不接入真实大模型 |
| **涉及文档** | [technology-stack.md](../03-architecture/technology-stack.md)、[development-plan.md](./development-plan.md)、[ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) §八 |
| **涉及目录** | `ai-common-infra/backend/`（`pom.xml`、`src/main/java`、`src/main/resources`、`README.md`） |
| **验收标准** | 1. `mvn package` 或等价构建通过<br>2. 应用可启动并返回健康检查<br>3. 包结构与架构逻辑模块可对应<br>4. 无业务系统代码混入 |

---

#### T-002 统一公共约定（返回、异常、追踪）

| 项 | 内容 |
|----|------|
| **任务目标** | 落地对内/对外的统一响应包络与异常体系 |
| **任务范围** | `ApiResponse`（code/message/data）；业务异常 vs 系统异常；`requestId` 贯穿；与 [api 文档](../06-api/ai-common-infra-api.md) §3.2 对齐 |
| **本次不做什么** | 不实现 Invoke/Admin 具体接口；不引入 Spring Security 完整登录 |
| **涉及文档** | [ai-common-infra-api.md](../06-api/ai-common-infra-api.md) §3、[ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) §4.1.3 |
| **涉及目录** | `backend/.../common/`；可选补充 [backend-development-guide.md](./backend-development-guide.md) |
| **验收标准** | 1. 成功/失败响应字段与接口文档一致<br>2. 全局异常处理返回统一结构<br>3. 错误详情不含 API Key 等敏感信息 |

---

#### T-003 配置与环境管理约定

| 项 | 内容 |
|----|------|
| **任务目标** | 建立多环境配置分层与密钥注入规范 |
| **任务范围** | `application.yml` / `application-dev.yml`；环境变量命名（DB、内部 Api-Key、厂商 API Key）；配置类占位；示例 `.env.example`（无真实密钥） |
| **本次不做什么** | 不填写真实密钥；不实现路由/限流具体阈值逻辑（仅配置项占位） |
| **涉及文档** | [technology-stack.md](../03-architecture/technology-stack.md)、需求 §4.8～4.9、[ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) |
| **涉及目录** | `backend/src/main/resources/`、`backend/.../config/`、[backend-development-guide.md](./backend-development-guide.md) |
| **验收标准** | 1. dev 环境可本地启动并连接 PostgreSQL（空库即可）<br>2. 密钥仅来自环境变量或外部配置<br>3. T-008～T-010 配置项有明确挂载点 |

---

### 阶段 B：核心语义与配置

#### T-004 统一 AI 调用请求语义（DTO + 校验）

| 项 | 内容 |
|----|------|
| **任务目标** | 实现 Invoke 请求体结构与校验规则 |
| **任务范围** | `InvokeRequest`：`businessType`、`projectId`、`userId`、`input`、`params`；必填校验；与 API 文档 §5.1 一致 |
| **本次不做什么** | 不调用供应商；不写响应持久化；不做路由 |
| **涉及文档** | [ai-common-infra-api.md](../06-api/ai-common-infra-api.md) §6.1、需求 §4.1.2 |
| **涉及目录** | `backend/.../gateway/dto/` 或 `api/dto/` |
| **验收标准** | 1. 缺 `businessType`/`input` 时校验失败<br>2. 字段名与接口文档一致<br>3. 非法请求不进入 Adapter |

---

#### T-005 统一 AI 调用响应语义（DTO）

| 项 | 内容 |
|----|------|
| **任务目标** | 实现 Invoke 响应体结构，含状态与失败子结构 |
| **任务范围** | `InvokeResponse`：`requestId`、`status`、`output`、`tokens`、`estimatedCost`、`durationMs`、`degraded`、`failure` 等 |
| **本次不做什么** | 不实现完整编排；不保证每次调用已有真实 Token（可占位） |
| **涉及文档** | [ai-common-infra-api.md](../06-api/ai-common-infra-api.md) §6.1、需求 §4.1.3 |
| **涉及目录** | `backend/.../gateway/dto/` |
| **验收标准** | 1. `SUCCESS` / `DEGRADED_SUCCESS` / `FAILED` 可表达<br>2. 与 T-004 可组成一次完整调用描述<br>3. 降级成功与普通成功可区分 |

---

#### T-006 业务类型与项目标识

| 项 | 内容 |
|----|------|
| **任务目标** | 登记业务类型与项目标识，支撑校验、路由与统计 |
| **任务范围** | `ai_business_type` 种子数据；项目标识枚举或常量；未知 `businessType` 拒绝策略；DB 或配置初始化 |
| **本次不做什么** | 不实现小红书/求职业务页面；不动态注册 UI |
| **涉及文档** | 需求 §4.1.4、[ai-common-infra-database.md](../05-database/ai-common-infra-database.md) §4.5 |
| **涉及目录** | `backend/.../common/enums/`、`db/migration/` 或 `data.sql` |
| **验收标准** | 1. 需求示例业务类型已登记<br>2. 含 `INFRA_SELF_TEST` 与两业务 `projectId` 预留<br>3. 未登记类型 Invoke 被拒绝（AC-7 数据基础） |

---

#### T-007 失败类型与调用状态枚举

| 项 | 内容 |
|----|------|
| **任务目标** | 统一失败类型与调用状态枚举，供日志、重试、降级共用 |
| **任务范围** | 8 类失败类型 + `COST_LIMIT_EXCEEDED`；`invocationStatus`；可重试/可降级标记（枚举属性或对照表） |
| **本次不做什么** | 不写供应商错误码映射实现（留给 T-012）；不实现重试/降级逻辑 |
| **涉及文档** | 需求 §4.4.2、§4.5、§4.3、[ai-common-infra-api.md](../06-api/ai-common-infra-api.md) §3.5 |
| **涉及目录** | `backend/.../common/enums/` |
| **验收标准** | 1. 失败类型齐全<br>2. 认证/参数/内容安全标记为不可重试、不可降级<br>3. 网络/超时/限流等标记可重试或可走降级 |

---

#### T-008 模型与供应商配置数据

| 项 | 内容 |
|----|------|
| **任务目标** | 落地供应商与逻辑模型配置数据 |
| **任务范围** | `ai_model_provider`、`ai_model` 表迁移 + 种子数据；至少 1 供应商、2 个逻辑模型；超时默认值 |
| **本次不做什么** | 不实现 Adapter HTTP 调用；不接入未登记模型 |
| **涉及文档** | [ai-common-infra-database.md](../05-database/ai-common-infra-database.md) §4.2～4.3、需求 §4.2 |
| **涉及目录** | `db/migration/`、`backend/.../persistence/`、`application*.yml` 厂商占位 |
| **验收标准** | 1. 至少 2 个模型可用于主备<br>2. `model_code` 与日志/响应展示一致<br>3. 未启用模型不可被选中 |

---

#### T-009 路由规则与备用模型配置

| 项 | 内容 |
|----|------|
| **任务目标** | 配置业务类型 → 主模型 → 备用模型顺序 |
| **任务范围** | `ai_route_rule`、`ai_route_fallback` 迁移 + 种子；每已登记业务类型一条主规则；至少一类配置 2 个以上备用 |
| **本次不做什么** | 不实现 Router 运行时逻辑；不做动态路由、拖拽配置 UI |
| **涉及文档** | 需求 §4.2、[database.md](../05-database/ai-common-infra-database.md) §4.6～4.7 |
| **涉及目录** | `db/migration/`、`backend/.../router/`（配置读取占位） |
| **验收标准** | 1. 每业务类型有且仅一条有效主规则<br>2. 备用 `fallback_order` 连续<br>3. 数据可被 T-023 读取 |

---

#### T-010 模型 Token 单价配置

| 项 | 内容 |
|----|------|
| **任务目标** | 为成本估算提供模型单价数据 |
| **任务范围** | `ai_model_pricing` 迁移 + 种子；每模型输入/输出单价；Token 未知时的按次估算规则说明 |
| **本次不做什么** | 不实现 T-030 计算逻辑；不做财务账单 |
| **涉及文档** | 需求 §4.6.4、[database.md](../05-database/ai-common-infra-database.md) §4.4 |
| **涉及目录** | `db/migration/`、`backend/.../observability/`（单价读取占位） |
| **验收标准** | 1. 已登记模型均有单价或按次规则<br>2. 估算结果将标注为估算（文档/注释）<br>3. T-030 可读取单价 |

---

### 阶段 C：供应商适配层

#### T-011 供应商适配层接口定义

| 项 | 内容 |
|----|------|
| **任务目标** | 定义 Adapter 统一入参/出参，与 Gateway 解耦 |
| **任务范围** | `ProviderAdapter` 接口；统一调用结果（内容、Token、错误语义）；文档说明与架构 Model Client 关系 |
| **本次不做什么** | 不实现 HTTP 调用；不做路由、日志、重试 |
| **涉及文档** | [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) §6.3～6.4、T-004～T-005 |
| **涉及目录** | `backend/.../adapter/` |
| **验收标准** | 1. Gateway 仅依赖 Adapter 抽象<br>2. 出参可映射到 T-007 失败类型<br>3. 接口文档可供 T-012 实现 |

---

#### T-012 首个供应商适配实现

| 项 | 内容 |
|----|------|
| **任务目标** | 完成第一个厂商的真实连通与单次文本生成 |
| **任务范围** | 一个 `ProviderAdapter` 实现（OpenAI 兼容 HTTP + WebClient/OkHttp）；单模型成功返回；错误映射（认证、超时） |
| **本次不做什么** | 不实现重试、降级、路由；不接第二厂商 |
| **涉及文档** | [technology-stack.md](../03-architecture/technology-stack.md) §6.1、T-008 |
| **涉及目录** | `backend/.../adapter/impl/` |
| **验收标准** | 1. 有效 Key 下可返回内容<br>2. 错误 Key → `AUTH_ERROR`<br>3. 可验证超时 → `TIMEOUT` |

---

#### T-013 第二模型适配复用验证

| 项 | 内容 |
|----|------|
| **任务目标** | 验证第二逻辑模型可复用同一 Adapter 模式 |
| **任务范围** | 第二 `model_code` 调用通；响应 `actualModelCode` 可区分 |
| **本次不做什么** | 不新增网关逻辑；不实现降级切换 |
| **涉及文档** | T-008、[database.md](../05-database/ai-common-infra-database.md) |
| **涉及目录** | `backend/.../adapter/`、配置数据 |
| **验收标准** | 1. 两模型均返回统一 DTO 语义<br>2. 为 T-025 多模型尝试做好准备 |

---

### 阶段 D：网关最小主链路

#### T-014 全局请求编号生成

| 项 | 内容 |
|----|------|
| **任务目标** | 每次 Invoke 生成全局唯一 `requestId` |
| **任务范围** | 编号生成器；格式说明；并发唯一性 |
| **本次不做什么** | 不写库；不实现完整 Invoke Controller（可与 T-015 合并会话但验收独立） |
| **涉及文档** | 需求 §4.7.2、[database.md](../05-database/ai-common-infra-database.md) |
| **涉及目录** | `backend/.../gateway/` |
| **验收标准** | 1. 任意两次调用编号不同<br>2. 响应始终带 `requestId`<br>3. 支持按编号反查（为 T-022 准备） |

---

#### T-015 网关最小调用链路

| 项 | 内容 |
|----|------|
| **任务目标** | 打通 `POST /api/v1/ai/invoke` 最小路径（固定单模型） |
| **任务范围** | Invoke Controller；校验 T-004；固定或默认单模型 → Adapter → T-005 响应；不含路由/重试/降级/持久化 |
| **本次不做什么** | 不写 `ai_invocation_log`；不做限流、成本检查 |
| **涉及文档** | [ai-common-infra-api.md](../06-api/ai-common-infra-api.md) §6.1、[architecture.md](../03-architecture/ai-common-infra-architecture.md) §6.1 |
| **涉及目录** | `backend/.../gateway/` |
| **验收标准** | 1. 合法请求返回成功语义 + `requestId` + `output` + `durationMs`<br>2. 非法业务类型返回参数类失败<br>3. 调用方不接触供应商 API |

---

#### T-016 网关调用耗时统计

| 项 | 内容 |
|----|------|
| **任务目标** | 统计端到端耗时并写入响应 |
| **任务范围** | Gateway 层计时；`durationMs` 字段；成功/失败均有时长 |
| **本次不做什么** | 不拆分 Adapter 内部耗时（可选字段后续再加） |
| **涉及文档** | 需求 §4.1.3、AC-2 |
| **涉及目录** | `backend/.../gateway/` |
| **验收标准** | 1. 毫秒正整数<br>2. 失败时可为部分耗时<br>3. 满足 AC-2 耗时要求 |

---

#### T-017 网关入参校验与快速失败

| 项 | 内容 |
|----|------|
| **任务目标** | 进入 Adapter 前完成业务类型/项目/输入校验 |
| **任务范围** | 集成 T-006；未登记类型、空 input 等 → `PARAM_ERROR`；不发起供应商调用 |
| **本次不做什么** | 不写失败日志表（T-020）；不实现限流 |
| **涉及文档** | T-004～T-007、[api.md](../06-api/ai-common-infra-api.md) §6.1 HTTP 错误表 |
| **涉及目录** | `backend/.../gateway/` |
| **验收标准** | 1. 未登记 `businessType` 不调供应商<br>2. 失败信息可读且不泄露配置<br>3. 为 T-020 预留写入点 |

---

### 阶段 E：调用日志与请求追溯

#### T-018 数据库表结构与持久化基础

| 项 | 内容 |
|----|------|
| **任务目标** | 按数据库设计文档落地表结构与 MyBatis Plus 基础层 |
| **任务范围** | Flyway 脚本：`ai_invocation_log`、`ai_invocation_attempt` 及阶段 B 配置表；实体与 Mapper 占位 |
| **本次不做什么** | 不重写 [database.md](../05-database/ai-common-infra-database.md)；不建业务系统表；不实现 `ai_quota_*`（留给 T-028/032） |
| **涉及文档** | [ai-common-infra-database.md](../05-database/ai-common-infra-database.md) §四 |
| **涉及目录** | `backend/src/main/resources/db/migration/`、`backend/.../persistence/` |
| **验收标准** | 1. 字段与 database.md §4.8～4.9 一致<br>2. `request_id` 唯一索引<br>3. 迁移可在空库执行成功 |

---

#### T-019 调用日志写入（成功路径）

| 项 | 内容 |
|----|------|
| **任务目标** | 成功调用（含后续降级成功）写入主表 |
| **任务范围** | `InvocationLogger`；写 `ai_invocation_log`；关联 `requestId`；计划/实际模型、耗时；尝试表写 PRIMARY 成功（可选同学会） |
| **本次不做什么** | 不实现 Token/成本计算（T-030）；不写失败路径 |
| **涉及文档** | 需求 §4.7、[database.md](../05-database/ai-common-infra-database.md) |
| **涉及目录** | `backend/.../observability/`、`persistence/` |
| **验收标准** | 1. 响应 `requestId` 与库记录一致<br>2. 写库失败不影响已返回成功（策略文档化）<br>3. AC-1 可查 |

---

#### T-020 调用日志写入（失败路径）

| 项 | 内容 |
|----|------|
| **任务目标** | 所有失败调用写入日志（含校验失败、供应商失败） |
| **任务范围** | 失败类型、详情脱敏、重试次数初值；无 Token 时 `token_unknown`；`blocked_by` 预留 |
| **本次不做什么** | 不实现 Admin 列表 API（T-021） |
| **涉及文档** | 需求 §4.4.3、§4.7、AC-1 |
| **涉及目录** | `backend/.../observability/` |
| **验收标准** | 1. 失败后按 `requestId` 可查到<br>2. 认证错误详情无密钥<br>3. 满足 AC-1 |

---

#### T-021 调用日志列表查询（Admin API）

| 项 | 内容 |
|----|------|
| **任务目标** | 实现 `GET /api/v1/admin/invocations` |
| **任务范围** | 筛选：时间、业务类型、项目、状态、降级、失败类型、requestId；分页；可选 `withSummary` |
| **本次不做什么** | 不做前端页面（T-041）；不做导出 |
| **涉及文档** | [ai-common-infra-api.md](../06-api/ai-common-infra-api.md) §6.2、[product.md](../02-product/ai-common-infra-product.md) §4.1 |
| **涉及目录** | `backend/.../admin/` |
| **验收标准** | 1. 筛选单独与组合可用<br>2. 列表字段与 API 文档一致<br>3. AC-1、AC-4 列表侧可验 |

---

#### T-022 调用日志详情查询（Admin API）

| 项 | 内容 |
|----|------|
| **任务目标** | 实现 `GET /api/v1/admin/invocations/{requestId}` |
| **任务范围** | 主表详情 + `attempts` 时间线；404 处理 |
| **本次不做什么** | 不做前端详情页（T-042） |
| **涉及文档** | [api.md](../06-api/ai-common-infra-api.md) §6.3、产品 §4.2 |
| **涉及目录** | `backend/.../admin/` |
| **验收标准** | 1. 不存在返回 40401<br>2. 降级成功时计划模型≠实际模型<br>3. 产品排查流程 5.1/5.3 数据齐全 |

---

### 阶段 F：模型路由

#### T-023 按业务类型的模型路由

| 项 | 内容 |
|----|------|
| **任务目标** | Gateway 按 `businessType` 选择计划主模型 |
| **任务范围** | `ModelRouter` 读取 T-009；替换 T-015 固定模型；日志记 `planned_model` |
| **本次不做什么** | 不实现降级换模；不实现熔断跳过（T-029） |
| **涉及文档** | [architecture.md](../03-architecture/ai-common-infra-architecture.md) §6.2、需求 §4.2.4 |
| **涉及目录** | `backend/.../router/`、`gateway/` |
| **验收标准** | 1. 至少 2 种业务类型命中不同模型<br>2. 未配置路由拒绝调用<br>3. 日志含计划模型 |

---

### 阶段 G：重试与降级

#### T-024 可重试错误自动重试

| 项 | 内容 |
|----|------|
| **任务目标** | 对 transient 错误在同模型上有限次重试 |
| **任务范围** | `RetryHandler`；次数/退避可配置；写 `ai_invocation_attempt`（RETRY）；更新 `retry_count` |
| **本次不做什么** | 不换模型（非降级）；认证/参数错误不重试 |
| **涉及文档** | 需求 §4.5、[architecture.md](../03-architecture/ai-common-infra-architecture.md) §6.8 |
| **涉及目录** | `backend/.../resilience/`、`adapter/` |
| **验收标准** | 1. 认证错误 0 重试<br>2. 模拟超时重试次数≤配置<br>3. 无无限重试 |

---

#### T-025 主模型失败后的模型降级

| 项 | 内容 |
|----|------|
| **任务目标** | 重试仍失败时按备用顺序尝试其他模型 |
| **任务范围** | `DegradationHandler`；同 `requestId`；`DEGRADED_SUCCESS`；写 FALLBACK 尝试记录 |
| **本次不做什么** | 不对认证/参数/内容安全降级；不实现熔断 |
| **涉及文档** | 需求 §4.3、AC-3、[database.md](../05-database/ai-common-infra-database.md) §7.2 |
| **涉及目录** | `backend/.../resilience/`、`gateway/` |
| **验收标准** | 1. AC-3 可验证降级成功或全失败<br>2. 响应 `degraded=true` 可区分<br>3. 主模型失败仍留在 attempt 记录 |

---

#### T-026 降级链路记录与展示数据

| 项 | 内容 |
|----|------|
| **任务目标** | 详情 API 与列表可展示完整降级/重试链路 |
| **任务范围** | 完善 T-022 `attempts`；列表 `degraded` 筛选；降级链路简述字段 |
| **本次不做什么** | 不做独立「降级统计」页（合并失败/成本页即可） |
| **涉及文档** | 产品 §4.2、§5.3 |
| **涉及目录** | `admin/`、`observability/` |
| **验收标准** | 1. 产品 5.3 流程可走通<br>2. `degraded` 筛选准确<br>3. 主模型失败原因可查 |

---

### 阶段 H：失败统计

#### T-027 失败类型聚合统计（Admin API）

| 项 | 内容 |
|----|------|
| **任务目标** | 实现 `GET /api/v1/admin/stats/failures` |
| **任务范围** | 失败率、按类型/模型/业务类型分布；`recentFailures` |
| **本次不做什么** | 不做告警推送；不做前端页（T-044） |
| **涉及文档** | [api.md](../06-api/ai-common-infra-api.md) §6.5、产品 §4.4、AC-4 |
| **涉及目录** | `backend/.../admin/` |
| **验收标准** | 1. AC-4 数据正确<br>2. 占比合理<br>3. 可钻取到日志列表（Query 预填） |

---

### 阶段 I：Token 与成本

#### T-030 单次 Token 与估算成本

| 项 | 内容 |
|----|------|
| **任务目标** | 成功调用后计算 Token 与估算成本，写入响应与日志 |
| **任务范围** | 读取 T-010 单价；`token_unknown` 处理；`estimatedCost` + `costEstimated` |
| **本次不做什么** | 不做多维汇总（T-031）；不做日上限拦截（T-032） |
| **涉及文档** | 需求 §4.6、AC-2 |
| **涉及目录** | `backend/.../observability/`、`gateway/` |
| **验收标准** | 1. AC-2 字段齐全<br>2. 标注估算<br>3. Token 未知不伪造 |

---

#### T-031 多维度 Token 与成本汇总（Admin API）

| 项 | 内容 |
|----|------|
| **任务目标** | 实现 `GET /api/v1/admin/stats/cost` |
| **任务范围** | 按日期 + `groupBy`（project/businessType/model）；overview + breakdown + dailyTrend |
| **本次不做什么** | 不建 `ai_stats_daily` 预聚合表（除非性能不达标）；不做 PDF 导出 |
| **涉及文档** | [api.md](../06-api/ai-common-infra-api.md) §6.4、需求 §4.6.2、AC-5/AC-7 |
| **涉及目录** | `backend/.../admin/` |
| **验收标准** | 1. AC-5、AC-7 通过<br>2. 三维度可解释<br>3. 产品 5.2 钻取可用 |

---

### 阶段 J：限流、熔断、成本防护

#### T-028 基础限流

| 项 | 内容 |
|----|------|
| **任务目标** | 全局 + 按业务类型限流 |
| **任务范围** | `ai_quota_policy` 迁移种子；Gateway 前置检查；`RATE_LIMIT` 失败 + 写日志 |
| **本次不做什么** | 不用 Redis（Caffeine/内存计数）；不做按用户限流（除非决策变更） |
| **涉及文档** | 需求 §4.8.1、[database.md](../05-database/ai-common-infra-database.md) §4.10、AC-6 |
| **涉及目录** | `backend/.../resilience/`、`db/migration/` |
| **验收标准** | 1. AC-6 限流场景<br>2. 超限不调供应商<br>3. 失败类型为限流 |

---

#### T-029 模型熔断基础能力

| 项 | 内容 |
|----|------|
| **任务目标** | 模型连续失败时暂停转发，优先备用或跳过 |
| **任务范围** | 内存熔断状态 + 冷却；Router 跳过熔断主模型；Admin 查询熔断状态（可合入 T-046） |
| **本次不做什么** | 不用 Redis 分布式熔断；不自动改路由配置 |
| **涉及文档** | 需求 §4.8.2、[architecture.md](../03-architecture/ai-common-infra-architecture.md) §6.10 |
| **涉及目录** | `backend/.../resilience/`、`router/` |
| **验收标准** | 1. 可模拟触发并观察状态<br>2. 熔断模型不作为新请求主模型<br>3. T-046 可展示 |

---

#### T-032 每日全局成本上限

| 项 | 内容 |
|----|------|
| **任务目标** | 日成本达上限拒绝新 Invoke |
| **任务范围** | `ai_quota_policy` + `ai_quota_usage_daily`；累加逻辑；`COST_LIMIT_EXCEEDED`；GET 日用量（可选，供 T-045） |
| **本次不做什么** | 不做月上限/用户上限完整 UI；不做多租户账单 |
| **涉及文档** | 需求 §4.9、§7.3、[database.md](../05-database/ai-common-infra-database.md) §4.10～4.11、AC-6 |
| **涉及目录** | `resilience/`、`observability/`、`db/migration/` |
| **验收标准** | 1. AC-6 成本超限场景<br>2. 超限响应可读<br>3. T-045 可展示占比 |

---

### 阶段 K：网关编排整合

#### T-033 网关完整调用编排

| 项 | 内容 |
|----|------|
| **任务目标** | 串联校验 → 限流 → 日成本 → 路由 → 调用 → 重试 → 降级 → 日志 → Token/成本 |
| **任务范围** | 统一 `GatewayOrchestrator`；替代 T-015 分散逻辑；编排顺序与架构 §6 一致 |
| **本次不做什么** | 不新增业务能力；不修改 API 路径与字段 |
| **涉及文档** | [architecture.md](../03-architecture/ai-common-infra-architecture.md) §六、需求 AC 全表 |
| **涉及目录** | `backend/.../gateway/` |
| **验收标准** | 1. 单次调用一个 `requestId`<br>2. 自测覆盖：成功、校验失败、重试成功、降级成功、全失败、限流、成本超限<br>3. 各失败符合 T-007 分类 |

---

### 阶段 L：Admin API 完善（与页面对接前）

#### T-034 Admin：日志 API 与产品对齐复核

| 项 | 内容 |
|----|------|
| **任务目标** | 复核 T-021/022 与产品第一阶段字段完全一致 |
| **任务范围** | 补缺失筛选/字段；`withSummary`；错误码对齐 |
| **本次不做什么** | 不开发 React 管理端页面 |
| **涉及文档** | [product.md](../02-product/ai-common-infra-product.md) §4.1～4.2 |
| **涉及目录** | `backend/.../admin/` |
| **验收标准** | 1. 产品 5.1 仅依赖 API 即可走通<br>2. 脱敏规则落实 |

---

#### T-035 Admin：成本统计 API 复核

| 项 | 内容 |
|----|------|
| **任务目标** | 复核 T-031 与产品 §4.3 一致 |
| **任务范围** | 钻取参数约定；`dailyTrend`；估算标注 |
| **本次不做什么** | 不做图表组件 |
| **涉及文档** | 产品 §4.3、§5.2 |
| **涉及目录** | `admin/` |
| **验收标准** | 1. AC-5 页面侧可验<br>2. 产品 5.2 流程可走 |

---

#### T-036 Admin：失败统计 API 复核

| 项 | 内容 |
|----|------|
| **任务目标** | 复核 T-027 与产品 §4.4 一致 |
| **任务范围** | `recentFailures`；钻取 Query 约定 |
| **本次不做什么** | 不做告警 |
| **涉及文档** | 产品 §4.4 |
| **涉及目录** | `admin/` |
| **验收标准** | 1. AC-4 页面侧可验<br>2. 点击类型可预填日志筛选 |

---

#### T-037 模型配置查询 API

| 项 | 内容 |
|----|------|
| **任务目标** | 实现 `GET /api/v1/admin/models` |
| **任务范围** | 模型 + 供应商 + 单价只读列表；`enabledOnly` |
| **本次不做什么** | 不做模型 CRUD 写接口 |
| **涉及文档** | [api.md](../06-api/ai-common-infra-api.md) §6.6 |
| **涉及目录** | `admin/` |
| **验收标准** | 1. 字段与 API 文档一致<br>2. 不返回密钥 |

---

#### T-038 路由配置查询 API

| 项 | 内容 |
|----|------|
| **任务目标** | 实现 `GET /api/v1/admin/routes` |
| **任务范围** | 路由 + 备用 + 可选 `health`；`enabledOnly` |
| **本次不做什么** | 不做路由写接口、拖拽配置 |
| **涉及文档** | [api.md](../06-api/ai-common-infra-api.md) §6.7、产品 §4.5 |
| **涉及目录** | `admin/`、`router/` |
| **验收标准** | 1. 主备关系可见<br>2. 熔断状态可展示（若已实现） |

---

#### T-039 日成本配额查询 API（可选）

| 项 | 内容 |
|----|------|
| **任务目标** | 实现 `GET /api/v1/admin/quota/daily`（若 T-045 需要） |
| **任务范围** | 上限、已用、占比；只读 |
| **本次不做什么** | 不强制实现 PUT（可配置文件改上限） |
| **涉及文档** | [api.md](../06-api/ai-common-infra-api.md) §8.3 |
| **涉及目录** | `admin/` |
| **验收标准** | 1. 与 T-032 数据一致<br>2. T-045 可展示 |

---

### 阶段 M：管理端页面

#### T-040 管理端工程与导航骨架

| 项 | 内容 |
|----|------|
| **任务目标** | 搭建管理端 React 工程与导航布局 |
| **任务范围** | `ai-common-infra/frontend/` 或 backend 静态资源二选一；React + TypeScript + Vite + pnpm + ShadCN UI；产品 §3.2 导航 |
| **本次不做什么** | 不接业务系统菜单；不做权限系统 |
| **涉及文档** | [technology-stack.md](../03-architecture/technology-stack.md) §4.2、[product.md](../02-product/ai-common-infra-product.md) §3.2 |
| **涉及目录** | `ai-common-infra/frontend/` 或 `backend/src/main/resources/static/admin/` |
| **验收标准** | 1. 可本地启动前端<br>2. P0 页面路由占位可跳转<br>3. 无小红书/求职菜单 |

---

#### T-041 调用日志列表页（P0）

| 项 | 内容 |
|----|------|
| **任务目标** | 交付产品 §4.1 列表页 |
| **任务范围** | 对接 T-021；筛选、分页、复制 requestId；可选汇总条 |
| **本次不做什么** | 不做导出、实时推送 |
| **涉及文档** | 产品 §4.1、[api.md](../06-api/ai-common-infra-api.md) §6.2 |
| **涉及目录** | `frontend/src/views/invocations/` |
| **验收标准** | 1. 同 T-034<br>2. 符合产品第一阶段不做项 |

---

#### T-042 调用日志详情页（P0）

| 项 | 内容 |
|----|------|
| **任务目标** | 交付产品 §4.2 详情页 |
| **任务范围** | 对接 T-022；时间线；降级区分展示 |
| **本次不做什么** | 不展示完整 Prompt/密钥 |
| **涉及文档** | 产品 §4.2 |
| **涉及目录** | `frontend/src/views/invocations/` |
| **验收标准** | 1. 降级成功可识别<br>2. 可复制 requestId<br>3. AC-3 链路可见 |

---

#### T-043 Token 与成本统计页（P0）

| 项 | 内容 |
|----|------|
| **任务目标** | 交付产品 §4.3 统计页 |
| **任务范围** | 对接 T-031；维度切换；钻取日志；估算标注 |
| **本次不做什么** | 趋势图可用表格代替 |
| **涉及文档** | 产品 §4.3、§5.2 |
| **涉及目录** | `frontend/src/views/stats/` |
| **验收标准** | 1. AC-5、AC-7<br>2. 产品 5.2 可走通 |

---

#### T-044 失败统计页（P0）

| 项 | 内容 |
|----|------|
| **任务目标** | 交付产品 §4.4 失败统计页 |
| **任务范围** | 对接 T-027；分布图/表；钻取日志 |
| **本次不做什么** | 不做外部监控联动 |
| **涉及文档** | 产品 §4.4 |
| **涉及目录** | `frontend/src/views/stats/` |
| **验收标准** | 1. AC-4<br>2. 点击失败类型预填列表筛选 |

---

#### T-045 系统设置页（P1）

| 项 | 内容 |
|----|------|
| **任务目标** | 日成本上限与策略说明页 |
| **任务范围** | 展示/修改日上限（若 PUT 实现）；用量占比；限流说明；模型单价只读；文档链接 |
| **本次不做什么** | 月上限/用户上限仅文案说明 |
| **涉及文档** | 产品 §4.6、T-032、T-028 |
| **涉及目录** | `frontend/src/views/settings/` |
| **验收标准** | 1. 改上限后 T-032 生效<br>2. 单价只读展示<br>3. 含 docs 链接 |

---

#### T-046 模型路由与状态页（P1）

| 项 | 内容 |
|----|------|
| **任务目标** | 路由只读 + 熔断/健康展示 |
| **任务范围** | 对接 T-037、T-038；可合并为设置子 Tab |
| **本次不做什么** | 不做拖拽配置、热更新 |
| **涉及文档** | 产品 §4.5 |
| **涉及目录** | `frontend/src/views/routes/` 或 `settings/` |
| **验收标准** | 1. 主备可见<br>2. 熔断中有标识<br>3. 产品 5.3 辅助排查可用 |

---

### 阶段 N：阶段验收

#### T-050 第一阶段集成验收与文档更新

| 项 | 内容 |
|----|------|
| **任务目标** | 按 AC-1～AC-7 完成底座验收并更新过程文档 |
| **任务范围** | 验收清单；端到端自测；更新 progress、task-board、ai-handoff；记录已知问题 |
| **本次不做什么** | 不启动小红书/求职开发；不补第四阶段增强功能 |
| **涉及文档** | 需求 §七～八、全部上游设计文档 |
| **涉及目录** | `docs/04-development/`、`docs/07-testing/`（可选新建验收记录） |
| **验收标准** | 1. AC-1～AC-7 有通过记录<br>2. 需求第八章无越界<br>3. handoff 下一步指向全项目第二阶段或业务模块 |

---

## 四、任务依赖简图

```text
T-001 → T-002 → T-003
         ↓
T-004 → T-005 → T-006 → T-007
         ↓              ↓
T-008 → T-009 → T-010
         ↓
T-011 → T-012 → T-013
         ↓
T-014 → T-015 → T-016 → T-017
         ↓
T-018 → T-019 → T-020 → T-021 → T-022
         ↓
T-023 → T-024 → T-025 → T-026
         ↓
T-027    T-030 → T-031
         ↓
T-028    T-029    T-032
         └────┬────┘
              ↓
            T-033
              ↓
    T-034～T-039（Admin API 完善）
              ↓
    T-040 → T-041～T-046
              ↓
            T-050
```

**可并行（依赖已满足时）：** T-008 与 T-011；T-027 与 T-030；T-028/T-029/T-032；T-037/T-038/T-039；T-041～T-044（T-040 后）。

---

## 五、全计划排除项（所有任务适用）

| 序号 | 排除项 |
|------|--------|
| 1 | `xhs-mom-helper/` 任何功能与代码 |
| 2 | `smart-job-agent/` 任何功能与代码 |
| 3 | Prompt 模板管理、动态 ML 选模 |
| 4 | Agent 编排、RAG、向量库 |
| 5 | 前端/业务直连大模型 |
| 6 | 业务后端直连模型供应商 |
| 7 | 多租户计费、复杂报表、告警推送 |
| 8 | 小红书/招聘自动化与风控绕过 |

---

## 六、执行建议

1. 严格按 **T-001 → T-050** 顺序，单次会话 **1 个任务编号**
2. 每完成任务更新 [task-board.md](./task-board.md)、[progress.md](./progress.md)
3. 密钥/厂商/路由变更记入 [decision-log.md](./decision-log.md)
4. 中断恢复：[ai-handoff.md](./ai-handoff.md) + 本文件当前 T 编号
5. 实现时以 **api / database / architecture** 为权威，本计划仅拆任务

---

## 七、关联文档

| 文档 | 路径 |
|------|------|
| 需求 | [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) |
| 产品 | [ai-common-infra-product.md](../02-product/ai-common-infra-product.md) |
| 架构 | [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) |
| 数据库 | [ai-common-infra-database.md](../05-database/ai-common-infra-database.md) |
| 接口 | [ai-common-infra-api.md](../06-api/ai-common-infra-api.md) |
| 技术栈 | [technology-stack.md](../03-architecture/technology-stack.md) |
| 看板 | [task-board.md](./task-board.md) |

---

## 八、文档版本记录

| 版本 | 日期 | 说明 |
|------|------|------|
| 2026.v1 | 2026-05-20 | 初版 T-001～T-050 |
| 2026.v2 | 2026-05-21 | 对齐上游设计文档；每任务增加「本次不做什么」「涉及文档」「涉及目录」；补充 T-037～T-039；锁定 Java21+MyBatis Plus |
| 2026.v2.1 | 2026-05-21 | 专项一致性：§1.6 需求能力任务覆盖表 |
