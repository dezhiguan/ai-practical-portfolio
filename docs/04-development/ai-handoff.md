# AI 会话交接文档

## 一、一句话状态（2026-05-20）

```text
第一阶段 · 通用 AI 技术底座
✅ 需求/产品/开发计划/目录骨架/文档占位
⏳ 架构正文、开发指南正文、T-001 编码
❌ 未写任何业务系统代码
```

---

## 二、项目与阶段

- **仓库：** `ai-practical-portfolio`
- **当前：** 全项目第一阶段 = 通用 AI 技术底座
- **工程路径：** `ai-common-infra/backend/`（编码从这里开始）

---

## 三、本次会话已完成

1. **目录结构对齐**（见 [README.md](../../README.md) 第三节）  
   - `ai-common-infra/backend/`  
   - `xhs-mom-helper/{backend,frontend}/`、`smart-job-agent/{backend,frontend}/` 占位  
2. **文档占位**  
   - `docs/03-architecture/*-architecture.md`（3 份）  
   - `docs/04-development/{frontend,backend}-development-guide.md`  
3. **过程文档** 更新（本文件、progress、decision-log、task-board）

此前已完成（勿重复做）：6 份需求/产品、`ai-common-infra-development-plan.md`、文档一致性检查。

---

## 四、底座第一阶段边界

| 做 | 不做 |
|----|------|
| 网关、路由、降级、日志、Token/成本、失败追踪、重试、限流、熔断、管理端可观测、日成本上限 | Prompt、动态路由、Agent 编排、RAG、多租户计费、业务功能 |

验收：需求文档 **AC-1～AC-7**。

---

## 五、下一次会话：建议做什么

**推荐顺序（二选一入口）：**

| 优先级 | 动作 | 产出 |
|--------|------|------|
| 1 | 充实 `docs/03-architecture/ai-common-infra-architecture.md` | 模块图、调用链、与 T-xxx 映射 |
| 2 | 执行 **T-001** | `ai-common-infra/backend/` 可构建空工程 |

规则：**一次会话只做一个 T-xxx**；完成后更新 progress、task-board、本文件。

T-001 输入见 [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) 任务表。

---

## 六、恢复上下文：阅读顺序

1. 本文件 → [progress.md](./progress.md) → [task-board.md](./task-board.md)  
2. [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) §七、§八  
3. [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) §二总览 + 即将执行的 T-xxx  
4. 若写架构：[ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md)（当前为占位）  
5. [decision-log.md](./decision-log.md)（目录布局、架构优先等决策）

---

## 七、关键约束（必守）

- 所有 AI 调用经统一网关；业务不得直连模型 API  
- 不在底座阶段开发小红书 / 求职 Agent 功能  
- `T-006` 登记的业务类型、项目 ID 仅为路由/统计**预留**  
- 不做自动发布、投递、抓取、回复 HR  

---

## 八、工程目录速查

```text
ai-common-infra/backend/     ← T-001 起（当前唯一活跃工程目录）
xhs-mom-helper/              ← 占位，第二阶段
smart-job-agent/             ← 占位，第三阶段
docs/03-architecture/        ← 底座架构正文优先
docs/04-development/         ← ai-common-infra-development-plan.md 为任务主清单
```

---

## 九、待决策（非阻塞）

1. 是否增加 `ai-common-infra/frontend/`（管理端）  
2. 首个模型供应商（T-008、T-012）  
3. 第一阶段是否做按用户限流（T-028 现为全局 + 业务类型）  
4. Java 版本与构建工具链  

---

## 十、最近更新时间

2026-05-20
