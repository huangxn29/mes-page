# MES-Page

MES 项目前端仓库。

## 快速导航

- 📖 [Git & GitHub 操作规范](GITHUB_OPERATIONS.md) — 分支策略、提交规范、PR 流程、Code Review 指南
- 🤝 [贡献指南](CONTRIBUTING.md) — 如何参与项目开发

## 分支策略

本项目采用 **Git Flow** 分支模型：

| 分支 | 用途 |
|------|------|
| `master` | 生产环境，只接受 `release/*` 或 `hotfix/*` 合并 |
| `develop` | 集成分支，功能开发的基础分支 |
| `feature/*` | 功能开发分支，基于 `develop` |
| `release/*` | 发布准备分支 |
| `hotfix/*` | 生产环境紧急修复分支 |

详见 [Git & GitHub 操作规范](GITHUB_OPERATIONS.md)。
