# 前端开发指南

版本：2026.v1（占位）  
状态：**待补充**

---

## 文档说明

本文档将约定业务系统与管理端前端的通用规范，包括但不限于：

1. 技术栈与版本
2. 目录与路由约定
3. 与后端 API 的协作方式（接口文档见 `docs/06-api/`，待补充）
4. 本地开发与联调方式

说明：**通用 AI 技术底座**第一阶段以可观测管理界面为主，是否独立 `ai-common-infra/frontend/` 由架构文档确定；当前目标结构中底座仅列 `backend/`，管理端可后续在底座或统一门户中落地。

---

## 模块前端目录

| 模块 | 前端目录 | 阶段 |
|------|----------|------|
| 小红书内容运营工作台 | `xhs-mom-helper/frontend/` | 全项目第二阶段 |
| 智能求职 Agent 工作台 | `smart-job-agent/frontend/` | 全项目第三阶段 |

---

## 关联文档

- [ai-common-infra-product.md](../02-product/ai-common-infra-product.md)（底座管理端页面）
- [development-plan.md](./development-plan.md)
