# AI 会话交接文档

## 一、一句话状态（2026-05-21）

```text
第一阶段 · 通用 AI 技术底座
✅ 需求/产品/开发计划/目录骨架/底座架构正文
⏳ T-001 编码（工程骨架）
❌ 未写任何业务系统代码
```

---

## 二、项目与阶段

- **仓库：** `ai-practical-portfolio`
- **当前：** 全项目第一阶段 = 通用 AI 技术底座
- **工程路径：** `ai-common-infra/backend/`（编码从这里开始）

---

## 三、本次会话已完成

1. **底座架构设计正文** — [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md)  
   - 架构目标、四层逻辑划分、模块边界  
   - AI Gateway / Model Router / Model Client / 日志 / Token 成本 / 失败追踪 / 重试 / 降级职责  
   - 主路径与失败-重试-降级调用链  
   - 小红书、求职 Agent 复用方式  
   - 第一阶段做什么/不做什么、T-xxx 映射  
2. **过程文档** 更新：progress、decision-log、task-board、本文件

此前已完成（勿重复做）：6 份需求/产品、开发计划、目录骨架、文档占位、一致性检查。

---

## 四、底座第一阶段边界

| 做 | 不做 |
|----|------|
| 网关、路由、降级、日志、Token/成本、失败追踪、重试、限流、熔断、管理端可观测、日成本上限 | Prompt、动态路由、Agent 编排、RAG、多租户计费、业务功能 |

验收：需求文档 **AC-1～AC-7**。

---

## 五、下一次会话：建议做什么

**推荐：执行 T-001**

| 项 | 说明 |
|----|------|
| 任务 | T-001 模块工程骨架初始化 |
| 路径 | `ai-common-infra/backend/` |
| 输入 | [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md) §八、[ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) T-001 |
| 产出 | 可构建、可空启动的工程骨架；子目录与架构逻辑模块可对应 |
| 规则 | 一次会话只做一个 T-xxx；完成后更新 progress、task-board、本文件 |

可选并行：T-002～T-003 约定写入 `backend-development-guide.md`。

---

## 六、恢复上下文：阅读顺序

1. 本文件 → [progress.md](./progress.md) → [task-board.md](./task-board.md)  
2. [ai-common-infra-architecture.md](../03-architecture/ai-common-infra-architecture.md)（**架构单一事实来源**）  
3. [ai-common-infra-requirements.md](../01-requirements/ai-common-infra-requirements.md) §七、§八  
4. [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md) — 即将执行的 T-xxx  
5. [decision-log.md](./decision-log.md)

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
docs/03-architecture/        ← 底座架构正文已完成
docs/05-database/            ← T-018 后补充
docs/06-api/                 ← 网关语义稳定后补充
```

---

## 九、待决策（非阻塞）

1. 是否增加 `ai-common-infra/frontend/`（管理端）— T-040 前确认  
2. 首个模型供应商（T-008、T-012）  
3. 第一阶段是否做按用户限流（T-028 现为全局 + 业务类型）  
4. Java 版本与构建工具链  
5. 业务调用底座：同进程模块 vs HTTP（T-014 接口设计时定）

---

## 十、最近更新时间

2026-05-21
