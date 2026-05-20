# AI 会话交接文档

## 一、当前项目

```text
ai-practical-portfolio
```

---

## 二、当前阶段与进度一句话

```text
第一阶段：通用 AI 技术底座
文档与 0→1 开发计划已就绪 → 缺架构专文 → 下一步：写架构或执行 T-001
编码：未开始
```

---

## 三、项目组成（不变）

1. 通用 AI 技术底座（**当前**）
2. 小红书内容运营工作台（仅文档，未开发）
3. 智能求职 Agent 工作台（仅文档，未开发）

---

## 四、已完成（本次及近期会话）

| 类别 | 内容 |
|------|------|
| 需求 | 底座 + 两业务轻量需求（`docs/01-requirements/`） |
| 产品 | 底座 + 两业务轻量产品（`docs/02-product/`） |
| 开发计划 | [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md)（T-001～T-050） |
| 检查 | 7 份文档一致性比对（结论：整体一致，架构文件缺失） |
| 过程文档 | progress / decision-log / task-board / 本文件 持续更新 |

**尚未完成：**

- `docs/03-architecture/ai-common-infra-architecture.md`
- 任意 T-xxx 编码任务

---

## 五、底座第一阶段边界（必记）

**做：** 统一网关、路由、降级、调用日志、Token/成本统计、失败追踪、重试、限流、熔断、管理端可观测（日志/成本/失败/设置）、日成本上限 subset。

**不做：** Prompt 管理、动态路由、Agent 编排、RAG、多租户计费、业务系统功能、复杂告警导出。

**验收：** 需求文档 AC-1～AC-7。

---

## 六、开发计划执行入口

主文档：[ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md)

推荐下一编号（二选一）：

| 选项 | 任务 | 说明 |
|------|------|------|
| A（建议） | 先写架构文档 | 再 T-001，减少返工 |
| B | **T-001** | 模块工程骨架初始化，不依赖架构全文但需后续补齐 |

依赖链摘要：

```text
T-001 → T-002 → T-003 → T-004～010 → T-011～013 → T-014～017
→ T-018～022 → T-023 → T-024～026 → T-027 / T-030～031
→ T-028、029、032 → T-033 → T-034～036 → T-040～046 → T-050
```

---

## 七、关键约束

- 所有 AI 调用经统一网关，业务不得直连模型 API  
- 不做自动发布/投递/抓取/回复 HR  
- 模式：`AI 辅助 + 人工确认`  
- 业务类型/项目 ID 在 T-006 登记仅为路由与统计**预留**，不等于开发小红书/求职 Agent  

---

## 八、下一次会话建议步骤

1. 读本文 + [progress.md](./progress.md) + [task-board.md](./task-board.md)  
2. 读 [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) 第七章、第八章  
3. 读 [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) 第二节总览与即将执行的 T-xxx  
4. 若写架构：对照需求第 4 章与开发计划「逻辑模块」表  
5. 若写代码：只认领 **一个** T-xxx，完成后更新 progress、task-board、本文件  

---

## 九、优先阅读清单

### 必读（恢复上下文）

1. [ai-handoff.md](./ai-handoff.md)（本文件）  
2. [progress.md](./progress.md)  
3. [task-board.md](./task-board.md)  

### 必读（底座开发）

4. [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md)  
5. [ai-common-infra-product.md](../02-product/ai-common-infra-product.md)  
6. [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md)  

### 按需

7. [development-plan.md](./development-plan.md)（全项目阶段）  
8. [decision-log.md](./decision-log.md)  
9. [README.md](../../README.md)、[00-project-guideline.md](../00-project-guideline.md)  

### 待编写

- `docs/03-architecture/ai-common-infra-architecture.md`  

---

## 十、待用户/后续决策（非阻塞可先默认）

1. 第一阶段是否做「按用户限流」（T-028 当前仅全局+业务类型）  
2. 首个大模型供应商选型（影响 T-008、T-012）  
3. 技术栈细节（guideline 写 Java，实施前确认版本）  

---

## 十一、最近更新时间

2026-05-20
