# 通用 AI 技术底座 — 接口设计

版本：2026.v1  
模块：`ai-common-infra/`  
状态：正文已完成（第一阶段范围）

---

## 一、文档说明

### 1.1 文档定位

本文档描述通用 AI 技术底座对外提供的 **HTTP API** 设计：用途、方法、路径、主要参数、响应结构与错误情况。

面向：

1. 业务系统后端集成（统一 AI 调用）
2. 底座管理端页面（日志、统计、配置查询）
3. T-004～T-005、T-015、T-021～T-022、T-031、T-034～T-036、T-044 等任务实现对照

### 1.2 设计前说明（模板输出）

| 项 | 内容 |
|----|------|
| **文档名称** | `docs/06-api/ai-common-infra-api.md` |
| **设计范围** | 8 类核心接口 + 通用约定与错误码；第一阶段以只读管理接口为主 |
| **涉及页面/能力** | 产品：调用日志列表/详情、Token 成本统计、失败统计、模型路由与状态 |
| **涉及数据表** | `ai_invocation_log`、`ai_invocation_attempt`、`ai_model`、`ai_model_pricing`、`ai_model_provider`、`ai_route_rule`、`ai_route_fallback`、`ai_business_type` |
| **是否涉及 AI 调用** | 是：`POST /api/v1/ai/invoke` 为唯一大模型调用入口 |
| **本次不设计** | 流式输出、批量调用、路由/模型写接口、报表导出、Webhook、业务系统 API |

### 1.3 不在本文档展开的内容

1. Controller/Service 实现代码
2. OpenAPI YAML 全文（可按本文档后续生成）
3. 业务系统（小红书、求职 Agent）自身 REST API

### 1.4 依据文档

| 文档 | 关系 |
|------|------|
| [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) | 4.1 请求/响应语义、AC 验收 |
| [ai-common-infra-product.md](../02-product/ai-common-infra-product.md) | 筛选字段、详情信息块 |
| [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) | Gateway 边界、调用链 |
| [ai-common-infra-database.md](../05-database/ai-common-infra-database.md) | 字段与表映射 |
| [technology-stack.md](../03-architecture/technology-stack.md) | 后端 Java 21 + Spring Boot 3 + Maven；接口经 Gateway |

---

## 二、技术实现与接口风格

| 项 | 约定 |
|----|------|
| 实现框架 | Spring Boot 3.x + Spring Web（REST Controller） |
| 构建 | Maven（`pom.xml`）；**禁止** Gradle |
| 参数校验 | Spring Validation（`@Valid` 等） |
| 路径风格 | `/api/v1/{资源}`；管理端 `/api/v1/admin/*` |
| HTTP 方法 | GET 查询、POST 提交（Invoke 与写操作） |
| 响应体 | 统一 `code` / `message` / `data` JSON 结构（§3.2） |
| 错误 | HTTP 状态码 + 业务 `code`；见本文档错误码章节 |
| AI 调用 | 仅 `POST /api/v1/ai/invoke`；供应商 API **仅底座内部** HTTP 客户端调用 |

---

## 三、调用关系与约束

### 2.1 谁调用谁

```text
┌─────────────────┐     ┌─────────────────┐
│ 业务前端         │     │ 底座管理端前端    │
│ (小红书/求职)    │     │ (可选独立前端)    │
└────────┬────────┘     └────────┬────────┘
         │ 仅调业务后端           │ 调底座 Admin API
         ▼                       ▼
┌─────────────────┐     ┌─────────────────────────────┐
│ 业务后端         │────►│ ai-common-infra 后端         │
│                 │ POST│ · /api/v1/ai/invoke          │
│                 │     │ · /api/v1/admin/*            │
└─────────────────┘     └──────────────┬──────────────┘
                                       │ 仅底座内部
                                       ▼
                              外部大模型供应商 API
```

### 2.2 强制约束

| 约束 | 说明 |
|------|------|
| 禁止前端直连大模型 | 任何浏览器/移动端不得调用 OpenAI 等供应商 URL |
| 禁止业务前端直连底座 Invoke | 业务前端只调各自 `xhs-mom-helper` / `smart-job-agent` 后端；由业务后端再调 `POST /api/v1/ai/invoke` |
| 禁止业务后端直连供应商 | 必须通过底座 Invoke 接口 |
| 管理端可调 Admin API | 管理端只读查询 + 日成本查询（`GET /admin/quota/daily`）；`PUT` 同路径可选 |

---

## 四、通用约定

### 3.1 基础信息

| 项 | 约定 |
|----|------|
| 协议 | HTTPS（生产）；本地开发可用 HTTP |
| Base Path | `/api/v1` |
| 编码 | UTF-8 |
| Content-Type | `application/json` |
| 时间格式 | ISO 8601，如 `2026-05-21T10:00:00+08:00`；日期参数可用 `yyyy-MM-dd` |

### 3.2 统一响应结构

**成功：**

```json
{
  "code": 0,
  "message": "success",
  "data": { }
}
```

**失败：**

```json
{
  "code": 40001,
  "message": "业务类型未登记",
  "data": null
}
```

| 字段 | 说明 |
|------|------|
| `code` | `0` 表示成功；非 0 为业务/系统错误码（见 §九） |
| `message` | 给人可读简述 |
| `data` | 成功时为业务数据；失败时为 `null` 或补充结构 |

**说明：** AI 调用接口在**调用失败但已正常返回 HTTP 200** 时，`code` 仍为 `0`，失败语义放在 `data.status`（见 §4.1）；仅参数非法、鉴权失败等用非 0 `code`。

### 3.3 分页（列表类 Admin 接口）

**请求 Query：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `page` | int | 否 | 页码，从 1 开始，默认 1 |
| `pageSize` | int | 否 | 每页条数，默认 20，最大 100 |

**响应 `data` 结构：**

```json
{
  "list": [],
  "page": 1,
  "pageSize": 20,
  "total": 135
}
```

### 3.4 鉴权（第一阶段简化）

| 接口域 | 建议方式 |
|--------|----------|
| `POST /api/v1/ai/invoke` | 请求头 `X-Internal-Api-Key`（业务后端与底座约定密钥，环境变量注入） |
| `/api/v1/admin/*` | 同上，或管理端登录态 Token（实现阶段二选一，文档不绑定框架） |

密钥不得出现在响应体与调用日志详情中。

### 3.5 枚举取值（与 T-007 对齐）

**调用状态 `invocationStatus`：**

| 值 | 含义 |
|----|------|
| `SUCCESS` | 主模型直接成功 |
| `DEGRADED_SUCCESS` | 经降级后成功 |
| `FAILED` | 最终失败 |

**失败类型 `failureType`（节选）：**

`NETWORK_ERROR`、`TIMEOUT`、`RATE_LIMIT`、`AUTH_ERROR`、`CONTENT_SAFETY`、`PARAM_ERROR`、`MODEL_INTERNAL_ERROR`、`COST_LIMIT_EXCEEDED`、`UNKNOWN_ERROR`

---

## 五、接口清单

| 序号 | 方法 | 路径 | 用途 | 调用方 |
|------|------|------|------|--------|
| 1 | POST | `/api/v1/ai/invoke` | **统一 AI 调用** | 业务后端 |
| 2 | GET | `/api/v1/admin/invocations` | 调用日志列表 | 管理端 |
| 3 | GET | `/api/v1/admin/invocations/{requestId}` | 调用日志详情 | 管理端 |
| 4 | GET | `/api/v1/admin/stats/cost` | Token 与成本统计 | 管理端 |
| 5 | GET | `/api/v1/admin/stats/failures` | 失败原因统计 | 管理端 |
| 6 | GET | `/api/v1/admin/models` | 模型配置查询 | 管理端 |
| 7 | GET | `/api/v1/admin/routes` | 模型路由配置查询 | 管理端 |
| 8 | GET | `/api/v1/admin/quota/daily` | 日成本上限与已用占比（系统设置） | 管理端 |

**可选（第一阶段 P1）：** `PUT /api/v1/admin/quota/daily` 修改日上限（T-045）；见 §九.3。

---

## 六、接口详细设计

### 6.1 统一 AI 调用

#### 基本信息

| 项 | 内容 |
|----|------|
| **用途** | 业务系统发起大模型调用的**唯一入口**；经 Gateway 完成路由、调用、重试、降级、日志与成本计算 |
| **方法** | `POST` |
| **路径** | `/api/v1/ai/invoke` |
| **对应任务** | T-004、T-005、T-015、T-033 |
| **涉及表** | 写入 `ai_invocation_log`、`ai_invocation_attempt`；读路由与模型配置表 |

#### 请求体

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `businessType` | string | 是 | 业务类型编码，须在 `ai_business_type` 已登记且启用 |
| `projectId` | string | 是 | 项目标识，如 `xhs-mom-helper`、`smart-job-agent`、`ai-common-infra` |
| `userId` | string | 否 | 用户标识，用于统计 |
| `input` | string | 是 | 本次 AI 任务输入文本（业务侧已拼装 Prompt） |
| `params` | object | 否 | 扩展参数，底座校验后转发 |

**`params` 可选字段：**

| 字段 | 类型 | 说明 |
|------|------|------|
| `temperature` | number | 采样温度，范围由底座配置上限 |
| `maxTokens` | int | 最大输出 Token |

**请求示例：**

```json
{
  "businessType": "CONTENT_GENERATION",
  "projectId": "xhs-mom-helper",
  "userId": "user_001",
  "input": "请根据以下素材生成一篇小红书笔记正文：……",
  "params": {
    "temperature": 0.7,
    "maxTokens": 2048
  }
}
```

#### 响应体 `data`

| 字段 | 类型 | 说明 | 来源 |
|------|------|------|------|
| `requestId` | string | 全局唯一请求编号 | 系统 |
| `status` | string | `SUCCESS` / `DEGRADED_SUCCESS` / `FAILED` | 系统 |
| `output` | string | 模型生成内容；失败时为空 | 模型响应 |
| `plannedModelCode` | string | 计划模型编码 | 系统 |
| `actualModelCode` | string | 实际使用模型编码 | 系统 |
| `degraded` | boolean | 是否发生降级 | 系统 |
| `tokens` | object | Token 消耗 | 模型响应 + 系统 |
| `tokens.input` | int \| null | 输入 Token；未知为 null | 模型响应 |
| `tokens.output` | int \| null | 输出 Token | 模型响应 |
| `tokens.total` | int \| null | 合计 | 系统 |
| `tokens.unknown` | boolean | 是否未知 | 系统 |
| `estimatedCost` | number \| null | 估算成本 | 系统统计 |
| `costEstimated` | boolean | 是否为估算值 | 系统 |
| `durationMs` | int | 端到端耗时（毫秒） | 系统统计 |
| `retryCount` | int | 同模型重试次数 | 系统统计 |
| `failure` | object | 失败时填写；成功时为 null | 系统 |
| `failure.type` | string | 失败类型枚举 | 系统 |
| `failure.message` | string | 可读说明，脱敏 | 系统 |

**成功响应示例：**

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "requestId": "req_20260521103000_abc123",
    "status": "SUCCESS",
    "output": "生成的正文内容……",
    "plannedModelCode": "gpt-4o-mini",
    "actualModelCode": "gpt-4o-mini",
    "degraded": false,
    "tokens": { "input": 120, "output": 450, "total": 570, "unknown": false },
    "estimatedCost": 0.0023,
    "costEstimated": true,
    "durationMs": 1850,
    "retryCount": 0,
    "failure": null
  }
}
```

**降级成功示例：** `status` 为 `DEGRADED_SUCCESS`，`degraded` 为 `true`，`plannedModelCode` ≠ `actualModelCode`。

#### HTTP 层错误（`code` ≠ 0）

| 场景 | HTTP | code | message 示例 |
|------|------|------|----------------|
| 缺少必填字段 | 400 | 40001 | 缺少 businessType |
| 业务类型未登记 | 400 | 40002 | 业务类型未登记或已禁用 |
| 项目标识非法 | 400 | 40003 | 项目标识不合法 |
| input 为空或超长 | 400 | 40004 | 输入内容不合法 |
| 鉴权失败 | 401 | 40100 | 无效的 API Key |
| 触发底座限流 | 429 | 42901 | 请求过于频繁 |
| 日成本超限（调用前拦截） | 403 | 40301 | 已超过每日成本上限 |
| 系统内部错误 | 500 | 50000 | 系统繁忙，请稍后重试 |

**说明：** 供应商调用失败、降级后仍失败等，HTTP 仍为 **200**，`code=0`，`data.status=FAILED`，`data.failure` 有值；同时写入调用日志（AC-1）。

---

### 6.2 调用日志列表查询

#### 基本信息

| 项 | 内容 |
|----|------|
| **用途** | 管理端「调用日志」列表页；支持筛选、分页、可选当前条件汇总 |
| **方法** | `GET` |
| **路径** | `/api/v1/admin/invocations` |
| **对应任务** | T-021、T-034 |
| **涉及表** | `ai_invocation_log` |

#### 请求 Query

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `startTime` | string | 否 | 开始时间（含） |
| `endTime` | string | 否 | 结束时间（含） |
| `businessType` | string | 否 | 业务类型 |
| `projectId` | string | 否 | 项目标识 |
| `status` | string | 否 | `SUCCESS` / `DEGRADED_SUCCESS` / `FAILED` |
| `degraded` | boolean | 否 | 是否降级 |
| `failureType` | string | 否 | 失败类型 |
| `requestId` | string | 否 | 请求编号精确或前缀搜索 |
| `page` | int | 否 | 分页 |
| `pageSize` | int | 否 | 分页 |
| `withSummary` | boolean | 否 | 为 true 时返回当前筛选下汇总（调用次数、成功率等） |

#### 响应体 `data`

| 字段 | 类型 | 说明 |
|------|------|------|
| `list` | array | 日志列表项 |
| `page` | int | 当前页 |
| `pageSize` | int | 每页大小 |
| `total` | int | 总条数 |
| `summary` | object | 可选，仅 `withSummary=true` |

**`list[]` 单项字段：**

| 字段 | 类型 | 说明 |
|------|------|------|
| `requestId` | string | 请求编号 |
| `createdAt` | string | 完成时间 |
| `projectId` | string | 项目 |
| `businessType` | string | 业务类型 |
| `businessTypeName` | string | 显示名（可选） |
| `status` | string | 调用状态 |
| `actualModelCode` | string | 实际模型 |
| `durationMs` | int | 耗时 |
| `tokensTotal` | int \| null | Token 合计 |
| `estimatedCost` | number \| null | 估算成本 |
| `degraded` | boolean | 是否降级 |
| `failureType` | string \| null | 失败类型 |

**`summary` 字段（可选）：**

| 字段 | 说明 |
|------|------|
| `totalCalls` | 总调用次数 |
| `successRate` | 成功率（含降级成功） |
| `totalTokens` | Token 合计 |
| `totalEstimatedCost` | 成本合计 |

#### 错误情况

| 场景 | HTTP | code |
|------|------|------|
| 时间范围非法 | 400 | 40010 |
| 鉴权失败 | 401 | 40100 |

---

### 6.3 调用日志详情查询

#### 基本信息

| 项 | 内容 |
|----|------|
| **用途** | 按请求编号查看单次调用完整上下文（含尝试时间线） |
| **方法** | `GET` |
| **路径** | `/api/v1/admin/invocations/{requestId}` |
| **对应任务** | T-022、T-034 |
| **涉及表** | `ai_invocation_log`、`ai_invocation_attempt`、`ai_model` |

#### 路径参数

| 参数 | 说明 |
|------|------|
| `requestId` | 请求编号 |

#### 响应体 `data`

| 字段 | 类型 | 说明 |
|------|------|------|
| `requestId` | string | 请求编号 |
| `createdAt` | string | 完成时间 |
| `projectId` | string | 项目 |
| `userId` | string \| null | 用户 |
| `businessType` | string | 业务类型 |
| `status` | string | 调用状态 |
| `durationMs` | int | 总耗时 |
| `plannedModelCode` | string \| null | 计划模型 |
| `actualModelCode` | string \| null | 实际模型 |
| `degraded` | boolean | 是否降级 |
| `tokens` | object | 同 5.1 |
| `estimatedCost` | number \| null | 估算成本 |
| `retryCount` | int | 重试次数 |
| `failureType` | string \| null | 最终失败类型 |
| `failureDetail` | string \| null | 脱敏详情 |
| `blockedBy` | string \| null | 前置拦截原因 |
| `inputLength` | int | 输入长度 |
| `outputLength` | int \| null | 输出长度 |
| `contentPreview` | string \| null | 截断预览 |
| `attempts` | array | 尝试时间线 |

**`attempts[]` 单项：**

| 字段 | 说明 |
|------|------|
| `attemptNo` | 序号 |
| `modelCode` | 模型 |
| `attemptType` | `PRIMARY` / `RETRY` / `FALLBACK` |
| `attemptStatus` | `SUCCESS` / `FAILED` |
| `failureType` | 本次失败类型 |
| `failureDetail` | 脱敏详情 |
| `durationMs` | 本次耗时 |
| `startedAt` / `endedAt` | 时间 |

#### 错误情况

| 场景 | HTTP | code | message |
|------|------|------|---------|
| 记录不存在 | 404 | 40401 | 未找到该请求编号 |
| 鉴权失败 | 401 | 40100 | — |

---

### 6.4 Token 与成本统计

#### 基本信息

| 项 | 内容 |
|----|------|
| **用途** | 管理端「Token 与成本统计」页；按时间范围与维度汇总 |
| **方法** | `GET` |
| **路径** | `/api/v1/admin/stats/cost` |
| **对应任务** | T-031、T-035 |
| **涉及表** | `ai_invocation_log`（聚合） |

#### 请求 Query

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `startDate` | string | 是 | 开始日期 `yyyy-MM-dd` |
| `endDate` | string | 是 | 结束日期（含） |
| `groupBy` | string | 是 | 汇总维度：`project` / `businessType` / `model` |
| `projectId` | string | 否 | 限定某一项目时再细分 |

#### 响应体 `data`

| 字段 | 类型 | 说明 |
|------|------|------|
| `startDate` | string | 查询起始 |
| `endDate` | string | 查询结束 |
| `groupBy` | string | 维度 |
| `overview` | object | 全量汇总（在筛选条件下） |
| `breakdown` | array | 维度明细列表 |
| `dailyTrend` | array | 可选，按日趋势（第一阶段可返回，图表为增强） |

**`overview`：**

| 字段 | 说明 |
|------|------|
| `totalCalls` | 总调用次数 |
| `successCalls` | 成功次数（含降级成功） |
| `failedCalls` | 失败次数 |
| `totalTokens` | Token 合计 |
| `totalEstimatedCost` | 估算成本合计 |
| `avgCostPerCall` | 平均单次成本（成功且有成本时） |
| `avgDurationMs` | 平均耗时（成功） |

**`breakdown[]`：**

| 字段 | 说明 |
|------|------|
| `dimensionKey` | 维度键，如 `CONTENT_GENERATION`、`xhs-mom-helper`、`gpt-4o-mini` |
| `dimensionName` | 显示名 |
| `totalCalls` | 调用次数 |
| `totalTokens` | Token |
| `totalEstimatedCost` | 成本 |
| `ratio` | 占比（0～1，可选） |

**`dailyTrend[]`：**

| 字段 | 说明 |
|------|------|
| `date` | 日期 |
| `totalTokens` | 当日 Token |
| `totalEstimatedCost` | 当日成本 |
| `totalCalls` | 当日调用次数 |

#### 错误情况

| 场景 | HTTP | code |
|------|------|------|
| 日期缺失或 startDate > endDate | 400 | 40011 |
| groupBy 非法 | 400 | 40012 |
| 鉴权失败 | 401 | 40100 |

---

### 6.5 失败原因统计

#### 基本信息

| 项 | 内容 |
|----|------|
| **用途** | 管理端「失败统计」页；失败率、按类型/模型/业务类型分布 |
| **方法** | `GET` |
| **路径** | `/api/v1/admin/stats/failures` |
| **对应任务** | T-027、T-036、T-041 |
| **涉及表** | `ai_invocation_log`（`status=FAILED` 聚合） |

#### 请求 Query

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `startDate` | string | 是 | 开始日期 |
| `endDate` | string | 是 | 结束日期 |
| `projectId` | string | 否 | 限定项目 |

#### 响应体 `data`

| 字段 | 类型 | 说明 |
|------|------|------|
| `startDate` | string | — |
| `endDate` | string | — |
| `overview` | object | 失败概览 |
| `byFailureType` | array | 按失败类型分布 |
| `byModel` | array | 按模型分布 |
| `byBusinessType` | array | 按业务类型分布 |
| `recentFailures` | array | 最近 N 条失败（默认 N=10，可用 `recentLimit` 调整） |

**`overview`：**

| 字段 | 说明 |
|------|------|
| `totalCalls` | 总调用（含成功与失败） |
| `failedCalls` | 失败次数 |
| `failureRate` | 失败率 |

**分布项 `byFailureType[]` 等：**

| 字段 | 说明 |
|------|------|
| `key` | 类型/模型/业务类型编码 |
| `name` | 显示名 |
| `count` | 次数 |
| `ratio` | 占失败总数比例 |

**`recentFailures[]`：** 与列表接口 `list[]` 字段一致，便于跳转详情。

**可选 Query：** `recentLimit`（int，默认 10，最大 50）

#### 错误情况

同 5.4 日期校验。

---

### 6.6 模型配置查询

#### 基本信息

| 项 | 内容 |
|----|------|
| **用途** | 管理端查看已登记模型、供应商、单价（只读）；支撑路由页与系统设置中的单价参考 |
| **方法** | `GET` |
| **路径** | `/api/v1/admin/models` |
| **对应任务** | T-008、T-010、T-044 |
| **涉及表** | `ai_model`、`ai_model_provider`、`ai_model_pricing` |

#### 请求 Query

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `enabledOnly` | boolean | 否 | 默认 true，仅返回启用模型 |

#### 响应体 `data`

| 字段 | 类型 | 说明 |
|------|------|------|
| `list` | array | 模型列表 |

**`list[]` 单项：**

| 字段 | 说明 |
|------|------|
| `modelCode` | 模型编码 |
| `modelName` | 显示名 |
| `providerCode` | 供应商编码 |
| `providerName` | 供应商显示名 |
| `enabled` | 是否启用 |
| `defaultTimeoutMs` | 默认超时 |
| `capabilityTags` | 能力标签 |
| `pricing` | 单价对象 |
| `pricing.inputPricePer1k` | 输入单价 |
| `pricing.outputPricePer1k` | 输出单价 |
| `pricing.currency` | 币种 |
| `pricing.fallbackPricePerCall` | 按次估算（可空） |

**不返回：** 供应商 API Key、完整 endpoint 密钥信息。

#### 错误情况

| 场景 | HTTP | code |
|------|------|------|
| 鉴权失败 | 401 | 40100 |

---

### 6.7 模型路由配置查询

#### 基本信息

| 项 | 内容 |
|----|------|
| **用途** | 管理端「模型路由与状态」页；展示业务类型 → 主模型 → 备用模型及策略 |
| **方法** | `GET` |
| **路径** | `/api/v1/admin/routes` |
| **对应任务** | T-009、T-023、T-044 |
| **涉及表** | `ai_route_rule`、`ai_route_fallback`、`ai_business_type`、`ai_model` |

#### 请求 Query

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `enabledOnly` | boolean | 否 | 默认 true |

#### 响应体 `data`

| 字段 | 类型 | 说明 |
|------|------|------|
| `list` | array | 路由规则列表 |

**`list[]` 单项：**

| 字段 | 说明 |
|------|------|
| `businessType` | 业务类型编码 |
| `businessTypeName` | 显示名 |
| `strategy` | 路由策略 |
| `allowHighCost` | 是否允许高成本模型 |
| `enabled` | 是否启用 |
| `primaryModel` | 主模型 `{ modelCode, modelName }` |
| `fallbackModels` | 备用模型数组，含 `order`、`modelCode`、`modelName` |
| `health` | 可选运行时摘要（第一阶段可简化） |
| `health.recentSuccessRate` | 近期成功率 |
| `health.avgDurationMs` | 近期平均耗时 |
| `health.circuitOpen` | 是否熔断中 |

**说明：** `health` 依赖运行时统计与熔断状态（T-029）；首版可返回 null 或占位，页面以只读配置为主。

#### 错误情况

| 场景 | HTTP | code |
|------|------|------|
| 鉴权失败 | 401 | 40100 |

---

## 七、需求与架构追溯

> **完整映射表（含产品页面、表、技术栈）：** [ai-common-infra-module-mapping.md](../02-product/ai-common-infra-module-mapping.md)。

| 接口 | 需求 | 架构模块 | 主要表 |
|------|------|----------|--------|
| `POST /ai/invoke` | §4.1～4.9 | AI Gateway 编排链 | `ai_invocation_log`、`ai_invocation_attempt`、路由/配额表 |
| `GET /admin/invocations*` | §4.7、§4.10 | Invocation Logger、Admin | `ai_invocation_log` |
| `GET /admin/stats/cost` | §4.6、§4.10 | Token & Cost | `ai_invocation_log` 聚合 |
| `GET /admin/stats/failures` | §4.4、§4.10 | Failure Tracker | `ai_invocation_log` 聚合 |
| `GET /admin/models` | §4.6、§4.10 | Config Registry | `ai_model*` |
| `GET /admin/routes` | §4.2、§4.3、§4.8 | Router、Circuit Breaker | `ai_route_*` |
| `GET /admin/quota/daily` | §4.9、§7.3 | Cost Guard | `ai_quota_*` |

---

## 八、接口与产品页面对照

| 产品页面 | 接口 |
|----------|------|
| 调用日志列表 | `GET /admin/invocations` |
| 调用日志详情 | `GET /admin/invocations/{requestId}` |
| Token 与成本统计 | `GET /admin/stats/cost` |
| 失败统计 | `GET /admin/stats/failures` |
| 模型路由与状态 | `GET /admin/routes`（`health` 含成功率/耗时/熔断） |
| 系统设置-单价参考 | `GET /admin/models` |
| 系统设置-日成本上限 | `GET /admin/quota/daily`；可选 `PUT` 同路径 |
| 系统设置-限流说明 | 配置文档 + `application.yml`（无独立 REST） |

**钻取：** 统计页 `breakdown[].dimensionKey` → 打开日志列表时带对应 Query（`businessType` / `projectId` / `failureType`）。

---

## 九、错误码汇总

| code | HTTP | 说明 |
|------|------|------|
| 0 | 200 | 成功 |
| 40001 | 400 | 参数缺失 |
| 40002 | 400 | 业务类型无效 |
| 40003 | 400 | 项目标识无效 |
| 40004 | 400 | 输入内容不合法 |
| 40010 | 400 | 查询时间范围无效 |
| 40011 | 400 | 统计日期无效 |
| 40012 | 400 | 统计维度 groupBy 无效 |
| 40100 | 401 | 鉴权失败 |
| 40301 | 403 | 日成本超限 |
| 40401 | 404 | 请求记录不存在 |
| 42901 | 429 | 限流 |
| 50000 | 500 | 系统内部错误 |

**Invoke 业务失败：** 使用 `data.status=FAILED` + `data.failure`，不占用上表业务错误码（除前置拦截 40301/42901）。

---

## 十、第一阶段范围说明

### 10.1 本阶段包含

- 上述 **8** 个核心接口的语义与字段（含 `GET /admin/quota/daily`）
- 业务后端 → Invoke 的唯一 AI 通路
- 管理端只读查询（模型、路由、日志、统计）

### 10.2 本阶段不包含（后续扩展）

| 接口 | 说明 |
|------|------|
| `POST /admin/routes` 等写接口 | 路由仍通过配置文件维护，管理端只读 |
| `PUT /admin/quota/daily-limit` | 日成本上限可配置文件 + 简单 PUT（T-045，可选附录） |
| `GET /admin/circuit-breakers` | 熔断独立列表，可合并进 routes.health |
| `POST /ai/invoke:stream` | 流式输出 |
| 批量调用、异步任务查询 | Agent 长任务 |
| 报表导出 | PDF/Excel |

### 10.3 附录：`PUT` 日成本上限（可选，T-045）

若管理端需在线修改日成本上限，可增补：

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/v1/admin/quota/daily` | 查询当前日上限与已用占比 |
| PUT | `/api/v1/admin/quota/daily` | 修改 `ai_quota_policy` 中日成本策略 |

第一阶段**不强制**实现 PUT；配置文件即可验收时，可仅实现 GET。

---

## 十一、与开发任务映射

| 任务 | 接口 |
|------|------|
| T-004、T-005 | §6.1 请求/响应字段 |
| T-015、T-033 | §6.1 |
| T-021 | §6.2 |
| T-022 | §6.3 |
| T-031、T-035 | §6.4 |
| T-027、T-036、T-041 | §6.5 |
| T-008、T-010 | §6.6 |
| T-009、T-023、T-044 | §6.7 |
| T-039、T-045 | §清单 #8、`§10.3` PUT（可选） |
| T-034～T-046 | Admin 各接口对接管理端 |

---

## 十二、关联文档

| 文档 | 路径 |
|------|------|
| 数据库设计 | [ai-common-infra-database.md](../05-database/ai-common-infra-database.md) |
| 架构设计 | [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) |
| 开发计划 | [ai-common-infra-development-plan.md](../04-development/ai-common-infra-development-plan.md) |
| 全链路映射表 | [ai-common-infra-module-mapping.md](../02-product/ai-common-infra-module-mapping.md) |

---

## 十三、文档版本记录

| 版本 | 日期 | 说明 |
|------|------|------|
| 2026.v1 | 2026-05-21 | 初版：7 核心接口 + 通用约定与错误码 |
| 2026.v1.1 | 2026-05-21 | 新增 §二 技术实现与接口风格；章节重编号；对齐 technology-stack |
| 2026.v1.2 | 2026-05-21 | 专项一致性：8 接口含 quota/daily；§七 追溯矩阵；产品/需求对照补全 |
