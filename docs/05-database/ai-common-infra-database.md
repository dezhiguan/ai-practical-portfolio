# 通用 AI 技术底座 — 数据库设计

版本：2026.v1  
模块：`ai-common-infra/`  
状态：正文已完成（第一阶段范围）

---

## 一、文档说明

### 1.1 文档定位

本文档描述通用 AI 技术底座的**逻辑数据模型**：表名、用途、核心字段、字段含义、表间关系与索引建议。

面向：

1. T-018～T-022 调用日志持久化与查询
2. T-008～T-010 模型与路由配置落地
3. T-030～T-032、T-028～T-029 成本、额度与韧性能力
4. 后续 `docs/06-api/` 接口设计

### 1.2 设计前说明（模板输出）

| 项 | 内容 |
|----|------|
| **文档名称** | `docs/05-database/ai-common-infra-database.md` |
| **设计范围** | 底座配置类表、调用事实表、尝试明细表、额度策略表；不含业务系统表 |
| **第一阶段必需表** | 见 §四（10 张） |
| **后续扩展表** | 见 §五 |
| **本次不写** | 完整建表 SQL、ORM 映射、分库分表、业务系统库表 |

### 1.3 不在本文档展开的内容

1. 完整 `CREATE TABLE` 语句（除非后续明确要求）
2. 业务系统（小红书、求职 Agent）数据库设计
3. Prompt 模板、RAG 向量、Agent 任务状态表

### 1.4 依据文档

| 文档 | 关系 |
|------|------|
| [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) | 4.7 日志字段、4.6 统计、4.8～4.9 限流与成本 |
| [ai-common-infra-product.md](../02-product/ai-common-infra-product.md) | 列表筛选、详情信息块、统计维度 |
| [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) | 持久化层定位、日志与失败追踪关系 |

---

## 二、存储选型与命名约定

### 2.1 存储选型（第一阶段）

| 项 | 决策 |
|----|------|
| **介质** | 关系型数据库（与 Java 技术栈一致，如 MySQL 8.x / PostgreSQL，具体版本在 T-001 工程骨架时确定） |
| **原因** | 调用日志需按多维度筛选分页；配置表需关联查询；AC-1～AC-7 以事务一致性写入为主 |
| **备选** | 调用量极大后的历史日志归档可走对象存储 + 索引表（后续扩展，本阶段不做） |

### 2.2 命名约定

| 约定 | 说明 |
|------|------|
| 表名前缀 | `ai_` |
| 主键 | 各表 `id`，bigint 自增或雪花（实现阶段定） |
| 业务主键 | `request_id` 为调用链全局唯一键，字符串，独立于表主键 |
| 时间字段 | `created_at`、`updated_at`，UTC 或服务器本地时区统一即可 |
| 枚举 | 库存 varchar/ smallint 编码，与 T-007 枚举文档一致 |
| 金额 | `estimated_cost` 等用 decimal，单位：元或美元在配置中统一（实现时定一种） |

---

## 三、数据来源分类

> 模板要求区分：业务请求、模型响应、系统统计。下表为全库字段级归纳。

### 3.1 来自业务请求（调用方传入）

由业务后端在调用 AI Gateway 时提交，写入日志主表快照字段（**不存完整原始 Prompt 正文**，见 §3.4）。

| 数据项 | 说明 | 主要落表 |
|--------|------|----------|
| 业务类型 `business_type` | 必填，路由与统计维度 | `ai_invocation_log` |
| 项目标识 `project_id` | 区分小红书 / 求职 Agent / 底座自测 | `ai_invocation_log` |
| 用户标识 `user_id` | 可选，按用户统计 | `ai_invocation_log` |
| 输入摘要 | 输入字符长度、哈希或截断预览（非全文） | `ai_invocation_log` |
| 扩展参数摘要 | 如 temperature、max_tokens（JSON 摘要，不含业务隐私全文） | `ai_invocation_log` |

### 3.2 来自模型响应（供应商经适配层回传）

| 数据项 | 说明 | 主要落表 |
|--------|------|----------|
| 输出摘要 | 生成结果截断预览或长度（**不存完整输出**于默认配置） | `ai_invocation_log` |
| 输入 Token `input_tokens` | 供应商返回则记录，否则 NULL + 标记未知 | `ai_invocation_log`、`ai_invocation_attempt` |
| 输出 Token `output_tokens` | 同上 | 同上 |
| 供应商原始错误码/信息 | 仅用于映射失败类型，脱敏后写入 `failure_detail` | `ai_invocation_attempt` |
| 模型侧耗时 | 可选，供应商若提供 | `ai_invocation_attempt` |

### 3.3 来自系统计算与编排（底座内部产生）

| 数据项 | 说明 | 主要落表 |
|--------|------|----------|
| 请求编号 `request_id` | Gateway 生成，全链路唯一 | `ai_invocation_log` |
| 计划模型 `planned_model_id` | Router 根据配置选出 | `ai_invocation_log` |
| 实际模型 `actual_model_id` | 含降级后最终模型 | `ai_invocation_log` |
| 调用状态 `invocation_status` | 成功 / 失败 / 降级成功等 | `ai_invocation_log` |
| 是否降级 `degraded` | 是/否 | `ai_invocation_log` |
| 估算成本 `estimated_cost` | Token × 单价或按次规则 | `ai_invocation_log` |
| 端到端耗时 `duration_ms` | Gateway 统计 | `ai_invocation_log` |
| 重试总次数 `retry_count` | 含同模型重试，不含换模型次数 | `ai_invocation_log` |
| 失败类型 `failure_type` | 最终失败时归类（T-007） | `ai_invocation_log` |
| 失败详情 `failure_detail` | 可读、脱敏 | `ai_invocation_log` |
| 单次尝试序号、尝试结果 | 每次 Model Client 调用一条 | `ai_invocation_attempt` |
| 路由/限流/成本拦截结果 | 未发起供应商调用时也写日志 | `ai_invocation_log` |

### 3.4 敏感数据存储原则

| 原则 | 说明 |
|------|------|
| 不存 API Key | 密钥仅环境变量或密钥服务，不入库 |
| 不存完整输入/输出默认 | 仅存 `input_length`、`output_length`、`content_preview`（可选，长度上限如 500 字） |
| 失败详情脱敏 | 剔除密钥片段、Authorization 头、完整堆栈 |
| 审计需要全文时 | 列为第四阶段或显式配置开关，第一阶段**默认关闭** |

---

## 四、第一阶段必需表

共 **10 张表**，覆盖：模型配置、路由、调用日志、尝试明细、失败追溯、Token/成本事实、额度限制。

### 4.1 表总览

| 序号 | 表名 | 用途 | 对应能力 / 任务 |
|------|------|------|-----------------|
| 1 | `ai_model_provider` | 模型供应商登记 | T-008 |
| 2 | `ai_model` | 逻辑模型配置 | T-008、T-012 |
| 3 | `ai_model_pricing` | 模型 Token 单价 | T-010、T-030 |
| 4 | `ai_business_type` | 业务类型字典 | T-006 |
| 5 | `ai_route_rule` | 业务类型 → 主模型路由 | T-009、T-023 |
| 6 | `ai_route_fallback` | 备用模型顺序 | T-009、T-025 |
| 7 | `ai_invocation_log` | **AI 调用日志主表** | T-018～T-022、T-030 |
| 8 | `ai_invocation_attempt` | 单次模型尝试（重试/降级链） | T-024～T-026、AC-3 |
| 9 | `ai_quota_policy` | 额度与限流、成本上限策略 | T-028、T-032 |
| 10 | `ai_quota_usage_daily` | 日成本用量快照（成本防护） | T-032、AC-6 |

**说明：** Token/成本**多维度汇总**（T-031）第一阶段优先对 `ai_invocation_log` **聚合查询**；若性能不足再启用扩展表 `ai_stats_daily`（§5.2）。

---

### 4.2 `ai_model_provider` — 模型供应商

**用途：** 登记供应商连接维度信息（不含密钥）。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `provider_code` | varchar(32) UK | 供应商标识，如 `openai`、`dashscope` | 配置 |
| `provider_name` | varchar(64) | 显示名 | 配置 |
| `base_url` | varchar(256) | API 基址（可选） | 配置 |
| `enabled` | tinyint | 是否启用 | 配置 |
| `created_at` | datetime | 创建时间 | 系统 |
| `updated_at` | datetime | 更新时间 | 系统 |

**索引建议：**

- UK：`provider_code`
- IDX：`enabled`

---

### 4.3 `ai_model` — 逻辑模型配置

**用途：** 底座内统一模型标识，供路由、日志、成本引用。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `model_code` | varchar(64) UK | 底座内模型编码，日志与响应展示用 | 配置 |
| `model_name` | varchar(128) | 显示名 | 配置 |
| `provider_id` | bigint FK | 关联 `ai_model_provider.id` | 配置 |
| `provider_model_id` | varchar(128) | 供应商侧模型 ID | 配置 |
| `default_timeout_ms` | int | 默认超时毫秒 | 配置 |
| `capability_tags` | varchar(256) | 能力标签 JSON，如高能力/低成本 | 配置 |
| `enabled` | tinyint | 是否可被路由选中 | 配置 |
| `created_at` | datetime | 创建时间 | 系统 |
| `updated_at` | datetime | 更新时间 | 系统 |

**关系：** `ai_model.provider_id` → `ai_model_provider.id`（多对一）

**索引建议：**

- UK：`model_code`
- IDX：`provider_id`, `enabled`

---

### 4.4 `ai_model_pricing` — 模型 Token 单价

**用途：** 估算成本计算；管理端「模型单价参考」只读展示。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `model_id` | bigint FK UK | 关联 `ai_model.id`，一模型一条有效单价 | 配置 |
| `input_price_per_1k` | decimal(12,6) | 每 1K 输入 Token 单价 | 配置 |
| `output_price_per_1k` | decimal(12,6) | 每 1K 输出 Token 单价 | 配置 |
| `fallback_price_per_call` | decimal(12,6) | Token 未知时按次估算单价（可空） | 配置 |
| `currency` | varchar(8) | 币种 | 配置 |
| `effective_from` | date | 生效日期 | 配置 |
| `created_at` | datetime | 创建时间 | 系统 |
| `updated_at` | datetime | 更新时间 | 系统 |

**关系：** `ai_model_pricing.model_id` → `ai_model.id`（一对一，有效单价）

**索引建议：**

- UK：`model_id`
- IDX：`effective_from`

---

### 4.5 `ai_business_type` — 业务类型字典

**用途：** 登记第一阶段业务类型；未知类型拒绝调用（T-006）。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `business_type` | varchar(64) UK | 类型编码，如 `CONTENT_GENERATION` | 配置 |
| `display_name` | varchar(128) | 中文显示名 | 配置 |
| `default_project_id` | varchar(64) | 默认所属项目（可选） | 配置 |
| `description` | varchar(256) | 说明 | 配置 |
| `enabled` | tinyint | 是否允许调用 | 配置 |
| `created_at` | datetime | 创建时间 | 系统 |
| `updated_at` | datetime | 更新时间 | 系统 |

**索引建议：**

- UK：`business_type`
- IDX：`enabled`

**预置数据方向（实现时种子数据）：** 需求 4.1.4 示例类型 + `INFRA_SELF_TEST`；项目维度在日志字段体现，不强制单独项目表。

---

### 4.6 `ai_route_rule` — 模型路由规则

**用途：** 静态路由：每个业务类型对应一个主模型及策略标记。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `business_type` | varchar(64) UK | 关联 `ai_business_type.business_type` | 配置 |
| `primary_model_id` | bigint FK | 主模型 | 配置 |
| `strategy` | varchar(32) | 路由策略：`LOW_COST_FIRST` / `HIGH_CAPABILITY_FIRST` / `DEFAULT` | 配置 |
| `allow_high_cost` | tinyint | 是否允许高成本模型（与 Cost Guard 联动） | 配置 |
| `enabled` | tinyint | 是否生效 | 配置 |
| `created_at` | datetime | 创建时间 | 系统 |
| `updated_at` | datetime | 更新时间 | 系统 |

**关系：**

- `ai_route_rule.business_type` 逻辑关联 `ai_business_type.business_type`
- `ai_route_rule.primary_model_id` → `ai_model.id`

**索引建议：**

- UK：`business_type`
- IDX：`primary_model_id`, `enabled`

**约束：** 每个已启用 `business_type` 有且仅一条有效 `ai_route_rule`（应用层或唯一约束保证）。

---

### 4.7 `ai_route_fallback` — 备用模型顺序

**用途：** 降级时按 `fallback_order` 依次尝试。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `route_rule_id` | bigint FK | 关联 `ai_route_rule.id` | 配置 |
| `fallback_model_id` | bigint FK | 备用模型 | 配置 |
| `fallback_order` | int | 顺序，从 1 递增 | 配置 |
| `enabled` | tinyint | 是否参与降级 | 配置 |
| `created_at` | datetime | 创建时间 | 系统 |

**关系：**

- `ai_route_fallback.route_rule_id` → `ai_route_rule.id`（一对多）
- `ai_route_fallback.fallback_model_id` → `ai_model.id`

**索引建议：**

- UK：`(route_rule_id, fallback_order)` 或 `(route_rule_id, fallback_model_id)`
- IDX：`route_rule_id`

---

### 4.8 `ai_invocation_log` — AI 调用日志（核心事实表）

**用途：** 每次 AI 调用（成功、失败、降级成功、校验失败、限流、成本超限）**一条主记录**；支撑 AC-1、AC-2、列表/详情/汇总查询。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `request_id` | varchar(64) UK | 全局唯一请求编号 | 系统 |
| `project_id` | varchar(64) | 项目标识 | 业务请求 |
| `user_id` | varchar(64) | 用户标识，可空 | 业务请求 |
| `business_type` | varchar(64) | 业务类型 | 业务请求 |
| `planned_model_id` | bigint FK | 计划模型，可空（校验失败时无） | 系统 |
| `actual_model_id` | bigint FK | 实际模型，可空 | 系统 |
| `invocation_status` | varchar(32) | `SUCCESS` / `DEGRADED_SUCCESS` / `FAILED` | 系统 |
| `input_token` | int | 输入 Token，NULL=未知 | 模型响应 |
| `output_token` | int | 输出 Token，NULL=未知 | 模型响应 |
| `total_token` | int | 合计，可冗余存储 | 系统统计 |
| `token_unknown` | tinyint | 是否 Token 未知 | 系统统计 |
| `estimated_cost` | decimal(12,6) | 估算成本，NULL=未计算 | 系统统计 |
| `cost_estimated_flag` | tinyint | 是否估算值 | 系统统计 |
| `duration_ms` | int | 端到端耗时 | 系统统计 |
| `retry_count` | int | 同模型重试总次数 | 系统统计 |
| `degraded` | tinyint | 是否发生降级 | 系统统计 |
| `failure_type` | varchar(32) | 最终失败类型，成功为空 | 系统 |
| `failure_detail` | varchar(1024) | 脱敏失败详情 | 系统 |
| `input_length` | int | 输入长度 | 业务请求 |
| `output_length` | int | 输出长度 | 模型响应 |
| `content_preview` | varchar(512) | 输入+输出截断预览，可空 | 可选 |
| `ext_params_snapshot` | varchar(512) | 扩展参数 JSON 摘要 | 业务请求 |
| `blocked_by` | varchar(32) | 前置拦截：`RATE_LIMIT` / `COST_LIMIT` / `VALIDATION` 等，可空 | 系统 |
| `created_at` | datetime | 调用完成时间 | 系统 |

**关系：**

- `planned_model_id`、`actual_model_id` → `ai_model.id`
- `business_type` 逻辑关联 `ai_business_type.business_type`

**索引建议（满足产品筛选与 AC）：**

| 索引 | 字段 | 用途 |
|------|------|------|
| UK | `request_id` | 详情查询 AC-1 |
| IDX | `created_at` DESC | 列表默认排序 |
| IDX | `(project_id, created_at)` | 按项目 AC-7 |
| IDX | `(business_type, created_at)` | 按业务类型筛选、汇总 |
| IDX | `(invocation_status, created_at)` | 状态筛选 |
| IDX | `(failure_type, created_at)` | 失败统计 AC-4 |
| IDX | `(actual_model_id, created_at)` | 按模型统计 |
| IDX | `(degraded, created_at)` | 降级记录筛选 |
| IDX | `(user_id, created_at)` | 按用户（预留） |

**与失败追踪：** 失败调用必有一条 `invocation_status=FAILED` 的主记录；`failure_type` + `failure_detail` 满足需求 4.4.3。无需单独「失败表」即可支撑 AC-4 列表筛选；聚合统计可对主表 `WHERE invocation_status='FAILED'` 分组。

---

### 4.9 `ai_invocation_attempt` — 调用尝试明细

**用途：** 记录每次对某一模型的调用尝试（含重试、降级换模）；支撑 AC-3 降级链路、产品详情「时间线」。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `request_id` | varchar(64) | 关联主日志 | 系统 |
| `attempt_no` | int | 全局尝试序号，从 1 递增 | 系统 |
| `model_id` | bigint FK | 本次尝试的模型 | 系统 |
| `attempt_type` | varchar(32) | `PRIMARY` / `RETRY` / `FALLBACK` | 系统 |
| `attempt_status` | varchar(32) | `SUCCESS` / `FAILED` | 系统 |
| `failure_type` | varchar(32) | 本次失败类型，成功为空 | 系统 |
| `failure_detail` | varchar(1024) | 脱敏详情 | 系统 |
| `input_token` | int | 本次 Token | 模型响应 |
| `output_token` | int | 本次 Token | 模型响应 |
| `duration_ms` | int | 本次调用耗时 | 系统统计 |
| `started_at` | datetime | 开始时间 | 系统 |
| `ended_at` | datetime | 结束时间 | 系统 |

**关系：**

- `request_id` 逻辑关联 `ai_invocation_log.request_id`（一对多）
- `model_id` → `ai_model.id`

**索引建议：**

- IDX：`(request_id, attempt_no)` — 详情页时间线
- IDX：`(model_id, started_at)` — 按模型查近期失败
- IDX：`(failure_type, started_at)` — 失败分析

**写入规则：**

1. 每次 Model Client 调用结束写一条（成功或失败）。
2. 主模型重试：`attempt_type=RETRY`，`model_id` 相同。
3. 降级换模：`attempt_type=FALLBACK`，`model_id` 为备用模型。
4. 校验失败、限流、成本超限**未调用供应商**时可不写 attempt，或写 0 条（仅主表 `blocked_by`）。

---

### 4.10 `ai_quota_policy` — 额度与限流策略

**用途：** 配置全局限流、按业务类型限流、日成本上限等（T-028、T-032）；管理端「系统设置」可改项落库。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `policy_code` | varchar(64) UK | 策略编码 | 配置 |
| `policy_type` | varchar(32) | 见下表 | 配置 |
| `scope_key` | varchar(64) | 作用域：全局 `GLOBAL`、业务类型码、项目 ID | 配置 |
| `threshold_value` | decimal(18,6) | 阈值：QPS、日成本上限等 | 配置 |
| `threshold_unit` | varchar(16) | `QPS` / `REQUESTS_PER_MIN` / `CURRENCY_PER_DAY` 等 | 配置 |
| `window_type` | varchar(16) | `SECOND` / `MINUTE` / `DAY` | 配置 |
| `action_on_exceed` | varchar(32) | `REJECT` / `LOG_ONLY`（第一阶段均 REJECT） | 配置 |
| `enabled` | tinyint | 是否启用 | 配置 |
| `remark` | varchar(256) | 说明 | 配置 |
| `created_at` | datetime | 创建时间 | 系统 |
| `updated_at` | datetime | 更新时间 | 系统 |

**policy_type 枚举（第一阶段）：**

| policy_type | 说明 | 对应需求 |
|-------------|------|----------|
| `GLOBAL_RATE_LIMIT` | 全局限流 | 4.8.1 |
| `BIZ_TYPE_RATE_LIMIT` | 按业务类型限流 | 4.8.1 |
| `DAILY_COST_LIMIT` | 日成本上限 | 4.9、7.3 |
| `USER_RATE_LIMIT` | 按用户限流 | 4.8.1（可选，待决策） |

**索引建议：**

- UK：`policy_code`
- IDX：`(policy_type, scope_key, enabled)`

---

### 4.11 `ai_quota_usage_daily` — 日成本用量

**用途：** 支撑 Cost Guard 快速判断当日是否超限；避免每次调用全表 SUM。

| 字段 | 类型建议 | 含义 | 数据来源 |
|------|----------|------|----------|
| `id` | bigint PK | 主键 | 系统 |
| `usage_date` | date | 统计日 | 系统 |
| `scope_type` | varchar(16) | `GLOBAL` / `PROJECT` | 系统 |
| `scope_key` | varchar(64) | 全局填 `GLOBAL`，分项目填 project_id | 系统 |
| `total_estimated_cost` | decimal(14,6) | 当日已累计估算成本 | 系统统计 |
| `total_invocations` | int | 当日调用次数 | 系统统计 |
| `total_success` | int | 成功次数 | 系统统计 |
| `total_failed` | int | 失败次数 | 系统统计 |
| `updated_at` | datetime | 最后累加时间 | 系统 |

**关系：** 与 `ai_invocation_log` 为汇总关系，非 FK；每次成功写入日志后异步或同事务累加。

**索引建议：**

- UK：`(usage_date, scope_type, scope_key)`
- IDX：`usage_date`

**第一阶段：** 至少维护 `scope_type=GLOBAL` 一行；分项目行可选，满足 AC-7 分项目日成本时可增加 `PROJECT` 行。

---

## 五、后续扩展表

| 表名 | 用途 | 引入时机 |
|------|------|----------|
| `ai_stats_daily` | 按日+维度预聚合 Token/成本/失败率，加速 T-031 查询 | 日志量增大或统计查询变慢时 |
| `ai_stats_hourly` | 小时粒度趋势 | 产品趋势图增强 |
| `ai_circuit_breaker_state` | 熔断状态持久化，多实例共享 | T-029 多实例部署时；单实例可内存 |
| `ai_monthly_quota` | 月成本上限 | 需求 4.9 完整 UI 阶段 |
| `ai_user_cost_quota` | 单用户成本上限 | 第二阶段初 |
| `ai_invocation_log_archive` | 历史日志归档 | 保留周期策略上线后 |
| `ai_audit_config` | 是否记录全文输入输出 | 显式审计需求时 |

---

## 六、主要实体关系

```text
ai_model_provider 1 ── N ai_model 1 ── 1 ai_model_pricing
                              │
                              │ primary_model_id
ai_business_type 1 ── 1 ai_route_rule 1 ── N ai_route_fallback
         │                    │
         │ business_type      └──► ai_model (fallback_model_id)
         │
         └──────────────────────► ai_invocation_log ◄── request_id ── N ai_invocation_attempt
                                      │ planned_model_id / actual_model_id
                                      └──► ai_model

ai_quota_policy (独立配置)
ai_quota_usage_daily (按日汇总，逻辑依赖 invocation_log)
```

---

## 七、核心场景与表协作

### 6.1 一次成功调用（无降级）

1. 读 `ai_route_rule` + `ai_model` 得 `planned_model_id`
2. 写 `ai_invocation_attempt`（PRIMARY，成功）
3. 写 `ai_invocation_log`（SUCCESS，Token/成本/耗时）
4. 累加 `ai_quota_usage_daily`

### 6.2 降级成功（AC-3）

1. 主模型：`ai_invocation_attempt` PRIMARY/RETRY → FAILED
2. 备用模型：`ai_invocation_attempt` FALLBACK → SUCCESS
3. `ai_invocation_log`：`DEGRADED_SUCCESS`，`degraded=1`，`planned_model_id`≠`actual_model_id`
4. 主模型失败信息保留在 attempt 的 `failure_type`/`failure_detail`

### 6.3 限流 / 日成本超限（AC-6）

1. 不写或少量写 `ai_invocation_attempt`
2. `ai_invocation_log`：`FAILED`，`failure_type=RATE_LIMIT` 或成本超限类，`blocked_by` 标明
3. 超限不累加成功成本（失败不计入 success 成本，是否计入 attempted 由产品约定：建议仅计数不调模型）

### 6.4 Token 与成本多维度汇总（AC-5、AC-7）

**第一阶段：** 对 `ai_invocation_log` 按 `DATE(created_at)` + `project_id` / `business_type` / `actual_model_id` 聚合：

- `SUM(total_token)`、`SUM(estimated_cost)`、`COUNT(*)`、`AVG(duration_ms)`（成功）
- `COUNT(*) WHERE invocation_status='FAILED'` 失败次数

**钻取：** 统计页点击维度 → 日志列表带预填筛选（产品 4.3）。

---

## 八、与开发任务映射

| 任务 | 涉及表 |
|------|--------|
| T-006 | `ai_business_type` |
| T-008 | `ai_model_provider`、`ai_model` |
| T-009 | `ai_route_rule`、`ai_route_fallback` |
| T-010 | `ai_model_pricing` |
| T-018 | 本文档定稿；字段与需求 4.7.2 对齐 |
| T-019～T-020 | `ai_invocation_log`、`ai_invocation_attempt` |
| T-021～T-022 | `ai_invocation_log` 索引与查询 |
| T-024～T-026 | `ai_invocation_attempt` |
| T-027、T-036 | `ai_invocation_log` 失败聚合 |
| T-028 | `ai_quota_policy` |
| T-030 | `ai_invocation_log` Token/成本字段 |
| T-031 | `ai_invocation_log` 聚合（或扩展 `ai_stats_daily`） |
| T-032 | `ai_quota_policy`、`ai_quota_usage_daily` |
| T-029 | 扩展 `ai_circuit_breaker_state` 或内存 |

---

## 九、后续需补充的接口文档（`docs/06-api/`）

数据库设计完成后，接口文档建议至少覆盖：

| 接口域 | 依赖表 | 说明 |
|--------|--------|------|
| AI 调用（对业务） | 不写库表暴露，写 `ai_invocation_log` | 统一 invoke |
| 调用日志列表/详情 | `ai_invocation_log`、`ai_invocation_attempt` | T-021、T-022、T-034 |
| Token/成本汇总 | `ai_invocation_log` | T-031、T-035 |
| 失败统计 | `ai_invocation_log` | T-036、T-041 |
| 路由配置查询 | `ai_route_rule`、`ai_route_fallback`、`ai_model` | T-044 |
| 额度/成本设置 | `ai_quota_policy`、`ai_quota_usage_daily` | T-045 |
| 熔断状态查询 | 内存或 `ai_circuit_breaker_state` | T-044 |

---

## 十、第一阶段表清单速查

| 分类 | 表 |
|------|-----|
| 模型配置 | `ai_model_provider`、`ai_model`、`ai_model_pricing` |
| 路由 | `ai_business_type`、`ai_route_rule`、`ai_route_fallback` |
| 调用日志 | `ai_invocation_log`、`ai_invocation_attempt` |
| 额度限制 | `ai_quota_policy`、`ai_quota_usage_daily` |

---

## 十一、关联文档

| 文档 | 路径 |
|------|------|
| 架构设计 | [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) |
| 需求说明 | [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) |
| 开发计划 | [ai-common-infra-development-plan.md](../04-development/ai-common-infra-development-plan.md) |

---

## 十二、文档版本记录

| 版本 | 日期 | 说明 |
|------|------|------|
| 2026.v1 | 2026-05-21 | 初版：10 张第一阶段表、数据来源分类、索引与任务映射 |
