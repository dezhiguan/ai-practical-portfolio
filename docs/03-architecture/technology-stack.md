# 技术栈说明

## 一、文档定位

本文档用于统一说明 `ai-practical-portfolio` 项目的整体技术栈选型。

本文档作为后续架构设计、数据库设计、接口设计、前后端开发和部署运维的技术基准。

适用范围：

1. 通用 AI 技术底座
2. 小红书内容运营工作台
3. 智能求职 Agent 工作台

本文档重点说明：

1. 整体技术选型
2. 后端技术栈
3. 前端技术栈
4. 数据库与缓存
5. AI Gateway 与大模型接入
6. RAG 与 Agent 技术
7. 文件存储
8. 可观测与日志
9. 部署与运维
10. 第一阶段必选技术
11. 后续扩展技术

---

## 二、技术选型原则

本项目技术选型遵循以下原则：

1. **优先选择 Java 技术栈**
    - 用户具备 Java 后端开发经验
    - 方便沉淀后端工程能力
    - 便于后续扩展为更完整的企业级 AI 应用系统

2. **先满足第一阶段落地，不做过度设计**
    - 第一阶段优先完成通用 AI 技术底座
    - 不提前引入复杂 Agent 平台、复杂工作流引擎和复杂可观测系统

3. **前后端分离**
    - 后端负责业务逻辑、AI 调用、数据存储和权限控制
    - 前端负责页面展示、用户交互和接口调用
    - 前端不能直接调用大模型

4. **所有 AI 调用统一经过通用 AI 技术底座**
    - 小红书内容运营工作台不直接调用大模型
    - 智能求职 Agent 工作台不直接调用大模型
    - 统一由 AI Gateway 完成模型路由、调用日志、Token 成本统计和失败追踪

5. **优先使用关系型数据库承载核心业务数据**
    - 第一阶段优先使用 PostgreSQL
    - 向量检索能力在智能求职 Agent 阶段再引入 pgvector 或 Milvus

6. **支持后续扩展**
    - 后续可以扩展 RAG、Agent 编排、多模型评分、Prompt 模板管理和多租户成本隔离

---

## 三、整体技术栈概览

| 分类 | 技术选型 | 使用阶段 | 说明 |
|---|---|---|---|
| 后端语言 | Java 21 | 第一阶段 | 主要后端开发语言 |
| 后端框架 | Spring Boot 3.x | 第一阶段 | 构建后端服务 |
| Web 框架 | Spring Web | 第一阶段 | 提供 REST API |
| 参数校验 | Spring Validation | 第一阶段 | 请求参数校验 |
| 权限认证 | Spring Security / JWT | 第二阶段起 | 用户登录和接口鉴权 |
| 数据访问 | MyBatis Plus | 第一阶段 | 数据访问与 CRUD |
| 主数据库 | PostgreSQL | 第一阶段 | 存储业务数据、AI 调用日志、成本统计 |
| 缓存 | Redis | 第一阶段可选，第二阶段增强 | 限流、缓存、短期状态 |
| 前端框架 | React | 第二阶段起 | 业务系统前端 |
| 前端语言 | TypeScript | 第二阶段起 | 增强前端类型约束 |
| 构建工具 | Vite | 第二阶段起 | 前端工程构建 |
| 样式方案 | Tailwind CSS | 第二阶段起 | 快速构建页面样式 |
| UI 组件 | ShadCN UI | 第二阶段起 | 管理后台和业务页面组件 |
| 图表 | Recharts | 第二阶段起 | 成本统计、数据复盘、趋势分析 |
| HTTP 客户端 | Axios / Fetch | 第二阶段起 | 前端调用后端接口 |
| 请求状态管理 | TanStack Query | 第二阶段起 | 前端接口请求、缓存、刷新 |
| AI 接入 | 统一 AI Gateway | 第一阶段 | 所有 AI 调用统一入口 |
| 大模型供应商 | OpenAI / DeepSeek / 兼容 OpenAI API 的供应商 | 第一阶段 | 通过适配器接入 |
| RAG 框架 | LangChain4j | 第三阶段 | 智能求职 Agent 知识库能力 |
| Agent 编排 | LangGraph4j / 自定义任务流 | 第三阶段 | 智能求职 Agent 工作流 |
| 向量检索 | pgvector / Milvus | 第三阶段 | 个人知识库、JD 检索、项目经历匹配 |
| 文件解析 | Apache Tika / PDF 解析组件 | 第三阶段 | 简历、文档、资料解析 |
| 文件存储 | 本地存储 / 对象存储 | 第二阶段起 | 图片、视频、简历、文档存储 |
| 容器化 | Docker | 第一阶段 | 本地和服务部署 |
| 编排 | Docker Compose | 第一阶段 | PostgreSQL、Redis、后端服务编排 |
| 反向代理 | Nginx | 部署阶段 | HTTPS、前端静态资源、后端代理 |
| 后端构建工具 | Maven | 第一阶段 | 管理 Spring Boot 后端依赖、插件和多模块构建 |
| 前端包管理工具 | pnpm | 第二阶段起 | 管理前端依赖和构建脚本 |

---

## 四、后端技术栈

### 4.1 Java 版本

选型：

```text
Java 21
```

选择原因：

1. 长期支持版本
2. 适合 Spring Boot 3.x
3. 具备较好的性能和语言特性
4. 适合中长期项目维护

---

### 4.2 Spring Boot

选型：

```text
Spring Boot 3.x
```

使用范围：

1. 通用 AI 技术底座后端
2. 小红书内容运营工作台后端
3. 智能求职 Agent 工作台后端

主要能力：

1. REST API
2. 依赖注入
3. 配置管理
4. 统一异常处理
5. 参数校验
6. 数据库访问集成
7. 日志集成

---

### 4.3 Spring Web

选型：

```text
Spring Web
```

用途：

1. 提供 REST API
2. 对接前端请求
3. 对接业务系统内部调用
4. 对外暴露 AI Gateway 调用接口和查询接口

---

### 4.4 Spring Validation

选型：

```text
Spring Validation
```

用途：

1. 请求参数校验
2. 表单字段校验
3. 接口参数合法性检查
4. 降低业务层重复判断

---

### 4.5 Spring Security / JWT

选型：

```text
Spring Security + JWT
```

使用阶段：

```text
第二阶段开始逐步引入
```

用途：

1. 用户登录
2. 接口鉴权
3. 用户身份识别
4. 后续多用户隔离

第一阶段说明：

通用 AI 技术底座第一阶段可以先预留用户标识字段，不强制完成完整权限系统。

---

### 4.6 MyBatis Plus

选型：

```text
MyBatis Plus
```

用途：

1. 数据库 CRUD
2. 调用日志查询
3. 成本统计查询
4. 模型配置查询
5. 业务数据持久化

选择原因：

1. SQL 可控
2. 适合日志、统计、配置类数据
3. 对 Java 后端开发者友好
4. 便于后续复杂查询优化

---

### 4.7 Lombok

选型：

```text
Lombok
```

用途：

1. 减少 Getter / Setter
2. 简化 DTO、VO、Entity
3. 提高代码简洁性

使用要求：

1. 不滥用复杂注解
2. 关键业务对象保留必要可读性
3. 避免过度依赖隐式行为

---

### 4.8 MapStruct

选型：

```text
MapStruct
```

使用阶段：

```text
可选，第二阶段开始引入
```

用途：

1. Entity、DTO、VO 之间转换
2. 减少手写转换代码
3. 保持类型安全

第一阶段说明：

通用 AI 技术底座第一阶段如果对象转换较少，可以先不引入，后续需要时再补充。

---

## 五、前端技术栈

### 5.1 React

选型：

```text
React
```

使用范围：

1. 小红书内容运营工作台前端
2. 智能求职 Agent 工作台前端
3. 后续可能拆出的管理控制台前端

---

### 5.2 TypeScript

选型：

```text
TypeScript
```

用途：

1. 提升前端类型安全
2. 约束接口响应结构
3. 降低页面和接口联调错误

---

### 5.3 Vite

选型：

```text
Vite
```

用途：

1. 前端项目构建
2. 本地开发服务
3. 快速热更新

---

### 5.4 Tailwind CSS

选型：

```text
Tailwind CSS
```

用途：

1. 快速构建页面样式
2. 保持页面风格一致
3. 降低手写 CSS 成本

---

### 5.5 ShadCN UI

选型：

```text
ShadCN UI
```

用途：

1. 表格
2. 表单
3. 弹窗
4. Tabs
5. Card
6. Button
7. Dialog
8. Toast

适用页面：

1. 素材库
2. 草稿箱
3. 内容日历
4. Agent 任务看板
5. 调用日志页面
6. Token 成本统计页面

---

### 5.6 React Router

选型：

```text
React Router
```

用途：

1. 前端页面路由
2. 页面跳转
3. 模块页面组织

---

### 5.7 TanStack Query

选型：

```text
TanStack Query
```

用途：

1. 接口请求状态管理
2. 缓存接口数据
3. 列表刷新
4. 加载状态
5. 错误状态处理

---

### 5.8 Axios

选型：

```text
Axios
```

用途：

1. 调用后端 REST API
2. 统一请求拦截
3. 统一响应拦截
4. 统一错误处理
5. 携带鉴权 Token

---

### 5.9 Recharts

选型：

```text
Recharts
```

用途：

1. Token 成本趋势图
2. 模型调用统计
3. 内容发布效果趋势
4. 求职投递状态统计

---

### 5.10 Markdown Renderer

选型：

```text
react-markdown 或同类组件
```

用途：

1. 展示 AI 生成的长文本
2. 展示 JD 分析报告
3. 展示匹配分析报告
4. 展示面试准备内容
5. 展示内容复盘建议

---

## 六、数据库与缓存

### 6.1 主数据库

选型：

```text
PostgreSQL
```

使用阶段：

```text
第一阶段开始使用
```

用途：

1. AI 调用日志
2. 模型配置
3. 模型路由
4. Token 成本统计
5. 失败记录
6. 小红书业务数据
7. 求职 Agent 业务数据

选择原因：

1. 稳定可靠
2. 支持复杂查询
3. 适合日志和统计类数据
4. 后续可通过 pgvector 扩展向量检索能力

---

### 6.2 缓存

选型：

```text
Redis
```

使用阶段：

```text
第一阶段可选，第二阶段增强
```

用途：

1. 请求限流
2. 短期缓存
3. 幂等控制
4. 临时状态保存
5. 后续 Agent 任务状态加速

第一阶段说明：

如果第一阶段只做最小公共底座，可以先设计 Redis 使用边界，不强制立即依赖。

---

### 6.3 向量数据库

候选方案：

```text
pgvector
Milvus
```

推荐策略：

1. 第一阶段不引入向量数据库
2. 智能求职 Agent 阶段开始引入
3. 如果希望简单落地，优先使用 pgvector
4. 如果后续知识库规模较大，再考虑 Milvus

使用场景：

1. 简历知识库
2. 项目经历检索
3. JD 语义匹配
4. 面试题库检索
5. HR 沟通记录检索

---

## 七、AI Gateway 与大模型接入

### 7.1 统一 AI Gateway

选型：

```text
自研统一 AI Gateway
```

用途：

1. 统一 AI 调用入口
2. 模型路由
3. 模型降级
4. 失败追踪
5. Token 成本统计
6. 调用日志记录
7. 业务类型隔离
8. 后续 Prompt 模板管理

所有业务系统必须通过 AI Gateway 调用大模型。

---

### 7.2 模型供应商适配

第一阶段支持方向：

```text
OpenAI
DeepSeek
其他兼容 OpenAI API 格式的模型供应商
```

设计方式：

1. 通过 ModelClient 接口抽象不同供应商
2. 每个供应商一个适配器
3. 业务代码不直接依赖具体供应商 SDK
4. 模型选择由 ModelRouter 决定

---

### 7.3 模型路由

能力：

1. 根据业务类型选择模型
2. 支持主模型
3. 支持备用模型
4. 支持低成本模型优先
5. 支持高质量模型优先
6. 支持失败后降级

业务类型示例：

1. 内容生成
2. 标题生成
3. 标签生成
4. 内容复盘
5. 岗位分析
6. 经历匹配
7. 求职话术生成
8. 面试准备
9. 文本总结
10. 文本分类

---

### 7.4 Token 成本统计

统计内容：

1. 输入 Token
2. 输出 Token
3. 总 Token
4. 估算成本
5. 调用次数
6. 平均耗时
7. 失败次数

统计维度：

1. 用户
2. 业务系统
3. 业务类型
4. 模型
5. 日期

---

### 7.5 失败追踪

失败类型：

1. 网络错误
2. 请求超时
3. 限流错误
4. 认证错误
5. 内容安全错误
6. 参数错误
7. 模型内部错误
8. 未知错误

处理原则：

1. 网络错误、超时、限流可重试
2. 认证错误、参数错误、内容安全错误不重试
3. 模型服务异常可触发降级
4. 所有失败都需要记录日志

---

## 八、RAG 与 Agent 技术栈

### 8.1 RAG 框架

候选：

```text
LangChain4j
```

使用阶段：

```text
智能求职 Agent 工作台阶段
```

用途：

1. 文档加载
2. 文本切分
3. 向量化
4. 语义检索
5. 检索增强生成

---

### 8.2 Agent 编排

候选：

```text
LangGraph4j
自定义任务流引擎
```

推荐策略：

1. 第一版可以先使用自定义任务状态流
2. 后续再根据复杂度引入 LangGraph4j
3. 不要一开始为了使用框架而过度设计

Agent 场景：

1. 主控 Agent
2. JD 分析 Agent
3. 个人经历匹配 Agent
4. 面试准备 Agent
5. 求职跟踪 Agent

---

### 8.3 Agent 状态管理

核心数据：

1. 任务编号
2. 当前步骤
3. 当前 Agent
4. 输入内容
5. 输出结果
6. 执行状态
7. 错误信息
8. Token 消耗
9. 人工确认状态

---

## 九、文件存储

### 9.1 第一阶段策略

第一阶段可以先使用：

```text
本地文件存储
```

适合：

1. 本地开发
2. 功能验证
3. 简化部署

---

### 9.2 后续扩展策略

后续可扩展为：

```text
阿里云 OSS
腾讯云 COS
MinIO
Azure Blob Storage
```

使用场景：

1. 小红书图片素材
2. 小红书视频素材
3. 简历文件
4. 项目经历文档
5. 面试记录附件
6. 发布包文件

---

## 十、可观测与日志

### 10.1 应用日志

第一阶段：

```text
Spring Boot 默认日志 + 结构化日志规范
```

日志内容：

1. 请求 TraceId
2. 请求路径
3. 调用耗时
4. 异常信息
5. AI 调用状态
6. 模型调用失败原因

---

### 10.2 AI 调用可观测

第一阶段通过数据库记录：

1. AI 调用日志
2. 失败记录
3. Token 成本统计
4. 模型使用情况

后续可扩展：

1. Prometheus
2. Grafana
3. OpenTelemetry
4. 链路追踪

第一阶段不强制引入复杂可观测平台。

---

## 十一、部署与运维

### 11.1 本地开发

推荐：

```text
Docker Compose
```

包含：

1. PostgreSQL
2. Redis
3. 后端服务
4. 前端服务

---

### 11.2 服务部署

第一阶段：

```text
Docker
Docker Compose
```

后续：

```text
Nginx
HTTPS
云服务器
对象存储
日志归档
```

---

## 十二、第一阶段必选技术

第一阶段通用 AI 技术底座优先使用：

```text
Java 21
Spring Boot 3.x
Spring Web
Spring Validation
MyBatis Plus
PostgreSQL
Lombok
Docker Compose
统一 AI Gateway
OpenAI / DeepSeek 模型适配
```

第一阶段可选但不强制：

```text
Redis
Spring Security
MapStruct
前端管理页面
Nginx
```

第一阶段不做：

```text
复杂 RAG
复杂 Agent 编排
Milvus
OpenTelemetry
Prompt A/B Testing
多租户计费
复杂权限系统
```

---

## 十三、小红书内容运营工作台技术栈

### 13.1 后端

```text
Java 21
Spring Boot 3.x
MyBatis Plus
PostgreSQL
文件存储
通用 AI Gateway
```

### 13.2 前端

```text
React
TypeScript
Vite
Tailwind CSS
ShadCN UI
React Router
TanStack Query
Axios
Recharts
Markdown Renderer
```

### 13.3 AI 使用方式

小红书内容运营工作台不直接调用大模型。

所有 AI 能力通过后端调用通用 AI 技术底座：

1. 标题生成
2. 正文生成
3. 标签生成
4. 内容复盘
5. 选题建议

---

## 十四、智能求职 Agent 工作台技术栈

### 14.1 后端

```text
Java 21
Spring Boot 3.x
MyBatis Plus
PostgreSQL
Redis
通用 AI Gateway
LangChain4j
LangGraph4j 或自定义任务流
pgvector 或 Milvus
```

### 14.2 前端

```text
React
TypeScript
Vite
Tailwind CSS
ShadCN UI
React Router
TanStack Query
Axios
Recharts
Markdown Renderer
```

### 14.3 AI 使用方式

智能求职 Agent 工作台不直接调用具体模型供应商。

所有 AI 能力通过后端调用通用 AI 技术底座：

1. JD 分析
2. 个人经历匹配
3. 求职话术生成
4. 面试准备
5. Agent 步骤执行

---

## 十五、技术栈与现有文档校准要求

由于本技术栈文档可能在部分架构、数据库、接口文档之后生成，因此后续需要进行一次文档一致性检查。

需要检查：

1. 架构设计是否与本文档技术栈一致
2. 数据库设计是否统一使用 PostgreSQL
3. 接口设计是否符合 Spring Boot REST 风格
4. 是否存在未说明的技术选型
5. 是否出现第一阶段不应引入的复杂技术
6. 是否出现前端直接调用大模型的设计
7. 是否出现业务模块直接调用具体模型供应商的设计

如果发现冲突，以本文档作为技术选型基准，但不直接推翻已有设计，应进行小范围调整。

---

## 十六、后续扩展技术

后续可根据项目发展逐步引入：

1. pgvector
2. Milvus
3. LangChain4j
4. LangGraph4j
5. OpenTelemetry
6. Prometheus
7. Grafana
8. MinIO
9. 对象存储
10. Prompt 模板管理
11. Prompt 版本管理
12. 模型效果评估
13. 多租户成本隔离
14. Agent 执行链路追踪

这些技术不属于第一阶段必须内容。
