# AI 指令模板库

## 一、文档定位

本文档用于存放项目开发过程中反复使用的 AI 指令模板。

使用目标：

1. 降低每次重新说明项目背景的成本
2. 让 AI 每次开发前先恢复上下文
3. 让 AI 每次只推进一个明确的小任务
4. 避免 AI 跳过当前阶段或扩展无关功能
5. 保证每次开发结束后更新进度、决策、交接和任务状态
6. 让项目在多次会话、多次中断后仍然可以连续推进

建议路径：

```text
docs/04-development/ai-command-templates.md
```

使用方式：

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“会话恢复模板”执行。
```

---

## 二、通用原则

所有模板都必须遵守以下原则：

1. 先阅读文档，再恢复上下文
2. 先输出计划，再开始执行
3. 每次只推进一个最小任务
4. 不扩展无关功能
5. 不跳过当前阶段
6. 不实现高风险自动化能力
7. 所有 AI 调用必须经过通用 AI 技术底座
8. 前端不能直接调用大模型
9. 后端业务模块不能直接绑定具体模型供应商
10. 每次完成后必须更新开发过程文档

---

## 三、会话恢复模板

### 适用场景

用于以下情况：

1. 新开 AI 会话
2. 上一次会话中断
3. 更换 AI 工具
4. 隔天继续开发
5. 不确定当前项目进度

### 固定指令

```text
请先阅读以下文档，恢复当前项目上下文：

1. README.md
2. docs/00-project-guideline.md
3. docs/04-development/development-plan.md
4. docs/04-development/progress.md
5. docs/04-development/decision-log.md
6. docs/04-development/ai-handoff.md
7. docs/04-development/task-board.md

如果当前任务涉及具体模块，还需要阅读对应模块的需求文档、产品文档、架构设计文档、数据库设计文档和接口设计文档。

阅读后请先输出：

1. 当前项目阶段
2. 当前正在推进的模块
3. 已完成内容
4. 待处理任务
5. 当前不应该做的内容
6. 本次最适合继续推进的一个最小任务
7. 本次任务的验收标准

先不要写代码，等我确认后再开始。
```

---

## 四、通用开发前模板

### 适用场景

用于已经恢复上下文后，准备开始某个任务之前。

### 固定指令

```text
现在准备开始本次任务。

请先不要直接写代码。

请根据当前文档和任务看板，先输出本次任务计划：

1. 本次任务目标
2. 本次任务范围
3. 本次不做哪些内容
4. 涉及哪些目录或文件
5. 是否需要新增文档
6. 是否需要修改已有文档
7. 是否涉及前端
8. 是否涉及后端
9. 是否涉及数据库
10. 是否涉及接口
11. 是否涉及 AI 调用
12. 验收标准

要求：

1. 本次只推进一个最小任务
2. 不扩展无关功能
3. 不跳过当前阶段
4. 不实现高风险自动化功能
5. 如果涉及 AI 调用，必须经过通用 AI 技术底座
6. 如果任务边界不清晰，先提出问题，不要直接实现

等我确认后，再开始执行。
```

---

## 五、通用开发后更新模板

### 适用场景

用于每次开发、文档修改、设计调整完成后。

### 固定指令

```text
请根据本次完成内容，更新以下文档：

1. docs/04-development/progress.md
2. docs/04-development/decision-log.md
3. docs/04-development/ai-handoff.md
4. docs/04-development/task-board.md

要求：

1. progress.md 记录当前进度、已完成内容、正在进行内容、下一步任务和当前问题
2. decision-log.md 只记录重要决策和原因，不记录普通修改
3. ai-handoff.md 记录下一次会话继续开发所需上下文，包括当前阶段、当前模块、已完成内容、下一步建议和必须阅读的文档
4. task-board.md 更新任务状态，将已完成任务标记为完成，将下一步任务放入待处理
5. 如果本次修改影响了需求、产品、架构、数据库或接口文档，请同步说明需要更新哪些文档
6. 最后输出下一次最适合继续推进的一个最小任务
```

---

## 六、文档生成模板

### 适用场景

用于生成或补充以下文档：

1. 需求文档
2. 产品文档
3. 架构设计文档
4. 数据库设计文档
5. 接口设计文档
6. 测试方案文档
7. 部署文档
8. 开发计划文档

### 固定指令

```text
现在只生成文档，不写代码。

请先阅读：

1. README.md
2. docs/00-project-guideline.md
3. docs/04-development/development-plan.md
4. docs/04-development/progress.md
5. docs/04-development/ai-handoff.md
6. docs/04-development/task-board.md

如果本次文档属于具体模块，还需要阅读该模块已有的上游文档。

请先输出：

1. 本次要生成的文档名称
2. 该文档的作用
3. 该文档依赖哪些上游文档
4. 文档目录结构
5. 本次文档写到什么粒度
6. 哪些内容本次不写
7. 生成后需要同步更新哪些开发过程文档

要求：

1. 只写文档，不写代码
2. 文档内容要和已有文档保持一致
3. 不提前设计未进入当前阶段的复杂能力
4. 明确第一阶段做什么、不做什么
5. 如果发现已有文档冲突，先指出冲突，不要直接覆盖
6. 文档完成后更新 progress、decision-log、ai-handoff、task-board
```

---

## 七、架构设计模板

### 适用场景

用于生成或修改架构设计文档。

### 固定指令

```text
现在只做架构设计，不写代码。

请根据当前模块的需求文档和产品文档，生成或更新架构设计文档。

请先阅读：

1. README.md
2. docs/00-project-guideline.md
3. 当前模块需求文档
4. 当前模块产品文档
5. docs/04-development/development-plan.md
6. docs/04-development/progress.md
7. docs/04-development/ai-handoff.md

请先输出：

1. 当前架构文档名称
2. 当前模块定位
3. 架构设计范围
4. 本次不设计哪些内容
5. 依赖哪些上游文档

文档要求：

1. 只写架构设计，不写代码
2. 不写具体类实现
3. 不写完整数据库建表语句
4. 不写接口实现细节
5. 需要说明模块边界
6. 需要说明主要调用链路
7. 需要说明前端、后端、公共能力之间的关系
8. 如果涉及 AI 调用，必须说明如何经过通用 AI 技术底座
9. 需要明确第一阶段做什么、不做什么
10. 完成后更新 progress、decision-log、ai-handoff、task-board
```

---

## 八、数据库设计模板

### 适用场景

用于生成或修改数据库设计文档。

### 固定指令

```text
现在只做数据库设计，不写代码。

请基于当前模块的需求文档、产品文档和架构设计文档，生成或更新数据库设计文档。

请先阅读：

1. 当前模块需求文档
2. 当前模块产品文档
3. 当前模块架构设计文档
4. docs/04-development/progress.md
5. docs/04-development/ai-handoff.md
6. docs/04-development/task-board.md

请先输出：

1. 当前数据库设计文档名称
2. 本次数据库设计范围
3. 第一阶段必须设计哪些表
4. 哪些表后续扩展
5. 本次不写哪些内容

文档要求：

1. 只做数据库设计，不写业务代码
2. 可以说明表名、表用途、核心字段、字段含义、主要关系、索引建议
3. 不需要写完整建表 SQL，除非我明确要求
4. 需要区分第一阶段必需表和后续扩展表
5. 需要说明哪些数据来自用户输入，哪些数据来自 AI 生成，哪些数据来自系统统计
6. 如果影响接口设计，需要说明后续需要补充哪些接口
7. 完成后更新 progress、decision-log、ai-handoff、task-board
```

---

## 九、接口设计模板

### 适用场景

用于生成或修改接口设计文档。

### 固定指令

```text
现在只做接口设计，不写代码。

请基于当前模块的需求文档、产品文档、架构设计文档和数据库设计文档，生成或更新接口设计文档。

请先阅读：

1. 当前模块需求文档
2. 当前模块产品文档
3. 当前模块架构设计文档
4. 当前模块数据库设计文档
5. docs/04-development/progress.md
6. docs/04-development/ai-handoff.md
7. docs/04-development/task-board.md

请先输出：

1. 当前接口设计文档名称
2. 本次接口设计范围
3. 涉及哪些页面或后端能力
4. 涉及哪些数据表
5. 是否涉及 AI 调用
6. 本次不设计哪些接口

文档要求：

1. 只做接口设计，不写代码
2. 说明接口用途、请求方式、路径、主要参数、响应结构、错误情况
3. 不写具体实现代码
4. 不写过度复杂的接口
5. 所有 AI 相关接口必须经过后端和通用 AI 技术底座
6. 前端不能直接调用大模型
7. 完成后更新 progress、decision-log、ai-handoff、task-board
```

---


## 十、公共模块开发模板

### 适用范围

适用于：

1. ai-common-infra
2. 统一 AI 网关
3. 模型路由
4. 模型降级
5. AI 调用日志
6. Token 成本统计
7. 失败追踪
8. 重试机制
9. 限流熔断
10. 公共异常
11. 公共返回结构

### 开发前必须阅读

```text
1. README.md
2. docs/00-project-guideline.md
3. docs/01-requirements/ai-common-infra-requirements.md
4. docs/02-product/ai-common-infra-product.md
5. docs/03-architecture/ai-common-infra-architecture.md
6. docs/05-database/ai-common-infra-database.md
7. docs/06-api/ai-common-infra-api.md
8. docs/04-development/progress.md
9. docs/04-development/ai-handoff.md
10. docs/04-development/task-board.md
```

### 固定指令

```text
请按照公共模块开发模板执行。

当前只开发通用 AI 技术底座，不开发小红书业务，不开发智能求职 Agent 业务。

请先阅读公共模块相关文档，然后输出：

1. 当前公共模块任务
2. 本次任务目标
3. 本次任务范围
4. 本次不做哪些内容
5. 涉及哪些目录或文件
6. 是否涉及数据库
7. 是否涉及接口
8. 是否涉及 AI 调用
9. 是否需要更新文档
10. 验收标准

固定要求：

1. 不开发小红书业务
2. 不开发智能求职 Agent 业务
3. 不开发具体业务页面
4. 不直接接入复杂业务逻辑
5. 所有能力要考虑被两个业务系统复用
6. 每次只完成一个公共能力
7. 完成后必须更新 progress、decision-log、ai-handoff、task-board

等我确认后，再开始执行。
```

### 推荐任务顺序

```text
1. 公共工程基础初始化
2. 统一返回结构
3. 统一异常处理
4. TraceId 请求追踪
5. AI Gateway 基础入口
6. 业务类型定义
7. 模型路由基础结构
8. 模型降级基础流程
9. AI 调用日志
10. Token 成本统计
11. 失败分类
12. 重试策略
13. 公共部分最小闭环验证
```

---

## 十一、后端开发模板

### 适用范围

适用于：

1. ai-common-infra/backend
2. xhs-mom-helper/backend
3. smart-job-agent/backend

### 固定指令

```text
请按照后端开发模板执行。

请先确认当前属于哪个后端模块：

1. ai-common-infra/backend
2. xhs-mom-helper/backend
3. smart-job-agent/backend

请先阅读：

1. README.md
2. docs/00-project-guideline.md
3. 当前模块需求文档
4. 当前模块架构设计文档
5. 当前模块数据库设计文档
6. 当前模块接口设计文档
7. docs/04-development/progress.md
8. docs/04-development/ai-handoff.md
9. docs/04-development/task-board.md

开发前请先输出：

1. 当前后端模块
2. 本次任务目标
3. 涉及的后端分层
4. 涉及的数据库表
5. 涉及的接口
6. 是否涉及 AI 调用
7. 是否需要更新接口文档
8. 是否需要更新数据库文档
9. 本次不做哪些内容
10. 验收标准

固定要求：

1. Controller 不写复杂业务逻辑
2. Service 负责业务编排
3. Repository / Mapper 负责数据访问
4. 所有返回使用统一返回结构
5. 所有异常走统一异常处理
6. 所有请求需要具备 TraceId
7. 所有 AI 调用必须经过通用 AI 技术底座
8. 不硬编码 API Key、账号密码等敏感信息
9. 完成后说明如何验证
10. 完成后更新 progress、decision-log、ai-handoff、task-board

等我确认后，再开始执行。
```

---

## 十二、前端开发模板

### 适用范围

适用于：

1. xhs-mom-helper/frontend
2. smart-job-agent/frontend
3. 后续可能拆出的管理控制台前端

### 固定指令

```text
请按照前端开发模板执行。

请先确认当前属于哪个前端模块：

1. xhs-mom-helper/frontend
2. smart-job-agent/frontend

请先阅读：

1. README.md
2. docs/00-project-guideline.md
3. 当前模块产品文档
4. 当前模块需求文档
5. 当前模块接口设计文档
6. 当前模块架构设计文档
7. docs/04-development/progress.md
8. docs/04-development/ai-handoff.md
9. docs/04-development/task-board.md

前端开发前请先输出：

1. 当前页面名称
2. 页面目标
3. 页面主要区域
4. 页面操作流程
5. 页面数据来源
6. 需要调用哪些接口
7. 需要哪些页面状态
8. 空状态、加载状态、错误状态如何处理
9. 是否涉及 AI 生成结果
10. 验收标准

固定要求：

1. 本次只开发一个页面或一个页面内的明确功能
2. 前端不能直接调用大模型
3. 前端只能通过后端接口使用 AI 能力
4. AI 生成结果需要支持复制、编辑或保存
5. 页面需要有清晰的空状态、加载状态和错误状态
6. 页面完成后需要说明如何操作验证
7. 完成后更新 progress、decision-log、ai-handoff、task-board

等我确认后，再开始执行。
```

---

## 十三、小红书内容运营工作台开发模板

### 开发模式

该项目采用 Vibe Coding 模式。

重点：

1. 页面体验
2. 用户流程
3. 业务闭环
4. 前后端联动
5. AI 能力嵌入业务流程

### 开发前必须阅读

```text
1. README.md
2. docs/00-project-guideline.md
3. docs/01-requirements/xhs-mom-helper-requirements.md
4. docs/02-product/xhs-mom-helper-product.md
5. docs/03-architecture/xhs-mom-helper-architecture.md
6. docs/05-database/xhs-mom-helper-database.md
7. docs/06-api/xhs-mom-helper-api.md
8. docs/04-development/progress.md
9. docs/04-development/ai-handoff.md
10. docs/04-development/task-board.md
```

### 固定指令

```text
请按照小红书内容运营工作台开发模板执行。

当前项目采用 Vibe Coding 模式，重点是页面、用户流程、业务闭环和前后端联动。

请先输出：

1. 当前业务模块
2. 用户操作流程
3. 涉及页面
4. 涉及后端接口
5. 涉及数据表
6. 是否调用 AI
7. 如何经过通用 AI 技术底座
8. 本次不做哪些内容
9. 验收标准

固定要求：

1. 不做小红书自动发布
2. 不做小红书自动登录
3. 不做小红书自动抓取
4. 不做自动评论
5. 不做自动私信
6. 发布动作由用户人工完成
7. 所有 AI 内容生成必须经过通用 AI 技术底座
8. 每次只推进一个业务模块
9. 每个模块要形成可操作流程
10. 完成后更新 progress、decision-log、ai-handoff、task-board

等我确认后，再开始执行。
```

### 推荐开发顺序

```text
1. 用户与账号基础能力
2. 素材库
3. AI 内容生成
4. 草稿箱
5. 内容日历
6. 发布包导出
7. 发布数据复盘
8. AI 调用日志与成本查看
```

---

## 十四、智能求职 Agent 工作台开发模板

### 开发模式

该项目采用 Agent 工程化模式。

重点：

1. 任务拆解
2. Agent 分工
3. RAG 检索
4. 工具调用
5. 任务状态
6. 人工确认
7. 失败恢复
8. 执行链路追踪

### 开发前必须阅读

```text
1. README.md
2. docs/00-project-guideline.md
3. docs/01-requirements/smart-job-agent-requirements.md
4. docs/02-product/smart-job-agent-product.md
5. docs/03-architecture/smart-job-agent-architecture.md
6. docs/05-database/smart-job-agent-database.md
7. docs/06-api/smart-job-agent-api.md
8. docs/04-development/progress.md
9. docs/04-development/ai-handoff.md
10. docs/04-development/task-board.md
```

### 固定指令

```text
请按照智能求职 Agent 工作台开发模板执行。

当前项目采用 Agent 工程化模式，重点是任务流、Agent 分工、RAG、状态管理、人工确认和失败恢复。

请先输出：

1. 当前 Agent 或任务节点
2. 输入是什么
3. 输出是什么
4. 由哪个 Agent 执行
5. 是否需要 RAG
6. 是否需要工具调用
7. 任务状态如何变化
8. 是否需要人工确认
9. 失败如何处理
10. 如何记录执行链路
11. 验收标准

固定要求：

1. 不做招聘网站自动登录
2. 不做自动投递
3. 不做自动回复 HR
4. 不做自动抓取招聘平台数据
5. 岗位信息由用户手动导入
6. 关键节点需要人工确认
7. 所有 AI 调用必须经过通用 AI 技术底座
8. 不要把系统做成普通 CRUD 后台
9. 不要只做一个聊天框
10. 每次只推进一个 Agent 能力或一个任务节点
11. 完成后更新 progress、decision-log、ai-handoff、task-board

等我确认后，再开始执行。
```

### 推荐开发顺序

```text
1. 个人资料管理
2. 个人知识库
3. 岗位导入
4. JD 分析 Agent
5. 个人经历匹配 Agent
6. 主控 Agent 工作流
7. Agent 任务看板
8. 面试准备 Agent
9. 求职跟踪 Agent
10. 投递状态管理
```

---


## 十五、文档一致性检查模板

### 适用场景

用于正式开发前，检查文档是否冲突。

### 固定指令

```text
现在不要写代码。

请检查以下文档之间是否一致：

1. README.md
2. docs/00-project-guideline.md
3. 当前模块需求文档
4. 当前模块产品文档
5. 当前模块架构设计文档
6. 当前模块数据库设计文档
7. 当前模块接口设计文档
8. docs/04-development/development-plan.md
9. docs/04-development/progress.md
10. docs/04-development/task-board.md

请重点检查：

1. 模块名称是否一致
2. 阶段顺序是否一致
3. 第一阶段范围是否一致
4. 做什么、不做什么是否一致
5. 是否出现提前开发其他模块的内容
6. 是否存在过度设计
7. 是否缺失关键能力
8. 前端、后端、数据库、接口是否能对应起来
9. AI 调用是否都经过通用 AI 技术底座
10. 是否存在高风险自动化内容

只输出检查结果和修改建议，不写代码。
```

---

## 十六、Bug 修复模板

### 适用场景

用于修复运行错误、接口错误、页面错误、编译错误等问题。

### 固定指令

```text
请按照 Bug 修复模板执行。

先不要直接修改代码。

请先输出：

1. 问题现象
2. 可能原因
3. 涉及模块
4. 涉及文件
5. 修复方案
6. 是否影响已有设计文档
7. 是否影响数据库
8. 是否影响接口
9. 验证方式
10. 是否需要更新开发过程文档

固定要求：

1. 不要顺手重构无关代码
2. 不要扩大修改范围
3. 不要改变既有模块边界
4. 修复后说明如何验证
5. 如果是重要问题，需要更新 decision-log 或 progress
6. 如果影响下一次继续开发，需要更新 ai-handoff
7. 修复完成后更新 task-board

等我确认后，再开始修复。
```

---

## 十七、重构模板

### 适用场景

用于结构优化、命名调整、模块边界优化等。

### 固定指令

```text
请按照重构模板执行。

先不要直接修改代码。

请先输出：

1. 为什么需要重构
2. 当前问题是什么
3. 重构目标是什么
4. 涉及哪些模块
5. 涉及哪些文件
6. 是否影响接口
7. 是否影响数据库
8. 是否影响前端
9. 是否影响后端
10. 是否影响已有文档
11. 重构后的验收标准

固定要求：

1. 不改变业务行为
2. 不新增无关功能
3. 不扩大重构范围
4. 先保证现有功能可用
5. 重构完成后说明验证方式
6. 如影响架构或模块边界，需要更新架构文档
7. 完成后更新 progress、decision-log、ai-handoff、task-board

等我确认后，再开始重构。
```

---

## 十八、测试验证模板

### 适用场景

用于每个模块完成后进行验证。

### 固定指令

```text
请按照测试验证模板执行。

请基于当前已完成内容，输出测试验证方案。

需要包括：

1. 本次验证的模块
2. 本次验证的功能点
3. 正常流程怎么验证
4. 异常流程怎么验证
5. 边界情况怎么验证
6. 是否需要 Mock AI 调用
7. 是否需要测试数据库
8. 是否需要测试前端页面
9. 是否需要测试接口
10. 验收通过标准

要求：

1. 不写新的业务功能
2. 不扩大测试范围
3. 如果发现缺少测试文档，需要指出
4. 如果发现需要补充测试用例，需要列出
5. 验证完成后更新 progress 和 task-board
```

---

## 十九、每次会话推荐操作流程

每次开始：

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“会话恢复模板”执行。
```

确定当前任务后：

```text
请按照“通用开发前模板”输出本次任务计划，先不要执行。
```

确认后：

```text
确认，按本次计划执行。本次只推进这个最小任务。
```

完成后：

```text
请按照“通用开发后更新模板”更新开发过程文档。
```

---

## 二十、不同任务的推荐入口

### 20.1 新会话继续开发

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“会话恢复模板”执行。
```

### 20.2 开发公共模块

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“公共模块开发模板”执行。本次只推进一个最小任务。
```

### 20.3 开发后端

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“后端开发模板”执行。本次只开发一个后端能力。
```

### 20.4 开发前端

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“前端开发模板”执行。本次只开发一个页面或一个页面内的明确功能。
```

### 20.5 开发小红书内容运营工作台

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“小红书内容运营工作台开发模板”执行。本次只推进一个业务模块。
```

### 20.6 开发智能求职 Agent 工作台

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“智能求职 Agent 工作台开发模板”执行。本次只推进一个 Agent 能力或一个任务节点。
```

### 20.7 生成文档

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“文档生成模板”执行。本次只生成指定文档，不写代码。
```

### 20.8 修复 Bug

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“Bug 修复模板”执行。先分析问题，不要直接修改代码。
```

### 20.9 重构

```text
请阅读 docs/04-development/ai-command-templates.md，并按照“重构模板”执行。先说明重构原因和影响范围，不要直接修改代码。
```

---

## 二十一、禁止行为提醒

无论使用哪个模板，都禁止以下行为：

1. 未恢复上下文就直接写代码
2. 未确认任务边界就开始实现
3. 一次性开发多个模块
4. 跳过公共 AI 技术底座
5. 业务模块直接调用具体大模型
6. 前端直接调用大模型
7. 实现小红书自动发布
8. 实现小红书自动登录
9. 实现小红书自动抓取
10. 实现招聘网站自动登录
11. 实现招聘网站自动投递
12. 实现自动回复外部用户
13. 绕过验证码、风控或平台限制
14. 不更新开发过程文档
15. 修改无关文件
16. 生成无法验证的伪功能

---

## 二十二、推荐长期习惯

每次开发都遵循以下节奏：

```text
读文档
↓
恢复上下文
↓
输出计划
↓
确认任务
↓
执行一个最小任务
↓
验证结果
↓
更新进度文档
↓
更新交接文档
↓
进入下一次任务
```

核心原则：

```text
先读文档恢复上下文，先输出计划，不要直接写代码；本次只做一个最小任务，完成后更新进度和交接文档。
```
