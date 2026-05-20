# 任务看板

## 一、状态说明

| 状态 | 含义 |
|------|------|
| 待处理 | 未开始 |
| 进行中 | 当前会话执行中 |
| 已完成 | 已交付或已验收 |
| 暂缓 | 延后至后续全项目阶段 |

---

## 二、进行中

无。

---

## 三、待处理（按优先级）

### P0 文档

| 任务 | 备注 |
|------|------|
| [ ] 充实 `backend-development-guide.md` 正文 | 可与 T-002～T-003 同步 |

### P0 工程

| 任务 | 开发计划 | 路径 |
|------|----------|------|
| [ ] 底座后端工程骨架 | **T-001** | `ai-common-infra/backend/` |

### P1 文档（架构后）

| 任务 | 备注 |
|------|------|
| [ ] `docs/07-testing/` 测试说明 | T-033 前后 |
| [ ] 可选：README / guideline 第一阶段清单对齐 | 一致性检查建议 |

### P2 底座开发（T-001 之后，勿跳项）

见 [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md)：`T-002` → … → `T-050`。

| 阶段 | 编号 | 摘要 |
|------|------|------|
| A | T-002～T-003 | 公共约定、配置约定 |
| B | T-004～T-010 | 请求/响应、枚举、模型与路由配置 |
| C | T-011～T-013 | 供应商适配 |
| D～N | T-014～T-050 | 网关、日志、路由、重试降级、成本、限流熔断、管理端、验收 |

---

## 四、已完成

### 项目与文档体系

- [x] 三部分组成、建设顺序、红线约束
- [x] `README.md`、`00-project-guideline.md`
- [x] `development-plan.md`
- [x] 过程文档四件套（含本看板）

### 需求与产品（2026.v1）

- [x] `ai-common-infra-requirements.md` / `product.md`
- [x] `xhs-mom-helper-requirements.md` / `product.md`
- [x] `smart-job-agent-requirements.md` / `product.md`

### 底座计划与质量

- [x] `ai-common-infra-development-plan.md`（T-001～T-050）
- [x] 文档一致性检查（架构正文除外）

### 目录结构（本次）

- [x] `ai-common-infra/backend/` 目录 + README
- [x] `xhs-mom-helper/{backend,frontend}/` 占位
- [x] `smart-job-agent/{backend,frontend}/` 占位
- [x] 三份 `docs/03-architecture/*-architecture.md` 占位
- [x] `ai-common-infra-architecture.md` 正文（模块边界、调用链、组件职责、阶段范围、T-xxx 映射）
- [x] `ai-common-infra-database.md` 正文（10 张第一阶段表、数据来源、索引、T-018 对齐）
- [x] `ai-common-infra-api.md` 正文（Invoke + 6 类 Admin 查询、错误码、调用边界）
- [x] `frontend-development-guide.md`、`backend-development-guide.md` 占位
- [x] README 第三节目录树更新

---

## 五、暂缓

- [ ] 小红书 / 求职 Agent 需求加深、架构正文、编码（第二、三阶段）
- [ ] 两业务 `05-database` / `06-api` 详设
- [ ] 第四阶段增强（Prompt、混合检索等）
- [ ] T-001～T-050 **编码执行**（整体尚未开始，仅 T-001 目录级占位）

---

## 六、下一步 Top 3

1. 执行 **T-001**（`ai-common-infra/backend/` 工程骨架）  
2. 可选：`backend-development-guide.md`（T-002～T-003）  
3. 完成后更新 progress、ai-handoff、本看板

---

## 七、最近更新时间

2026-05-21
