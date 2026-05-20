# 后端开发指南

版本：2026.v1（占位）  
状态：**待补充**

---

## 文档说明

本文档将约定全项目后端工程的通用规范，包括但不限于：

1. 技术栈与版本（与 [00-project-guideline.md](../00-project-guideline.md) 对齐）
2. 模块与包结构约定
3. 统一返回结构、异常处理、请求追踪（底座 T-002 产出可对齐写入）
4. 配置、日志、本地启动方式
5. 与 `ai-common-infra` 的集成方式（业务系统调用网关）

当前阶段优先完成底座；本指南在 T-001～T-003 推进过程中逐步充实。

---

## 模块后端目录

| 模块 | 后端目录 |
|------|----------|
| 通用 AI 技术底座 | `ai-common-infra/backend/` |
| 小红书内容运营工作台 | `xhs-mom-helper/backend/` |
| 智能求职 Agent 工作台 | `smart-job-agent/backend/` |

---

## 关联文档

- [ai-common-infra-development-plan.md](./ai-common-infra-development-plan.md)
- [development-plan.md](./development-plan.md)
