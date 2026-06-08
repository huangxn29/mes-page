# 贡献指南

欢迎参与 MES-Page 项目的开发！请遵循以下规范。

## 快速开始

1. Fork 本仓库
2. 基于 `develop` 创建包含 Issue 编号的功能分支：`git checkout -b feature/MES-<issue-number>-<your-feature> develop`
3. 提交代码（遵循[约定式提交](.claude/rules/git-workflow.md)规范）
4. 创建 Pull Request 到 `develop` 分支

## 开发规范

| 内容 | 位置 | 加载方式 |
|------|------|---------|
| 核心精简规则 | [CLAUDE.md](CLAUDE.md) | 自动加载 |
| 分支命名 / 提交 / PR / 合并策略 | `.claude/rules/git-workflow.md` | 按需加载 |
| Code Review 标准 | `/review` | slash command |
| 发布流程 | `/release` | slash command |
| 热修复流程 | `/hotfix` | slash command |
| 完整参考文档 | [GITHUB_OPERATIONS.md](GITHUB_OPERATIONS.md) | 手动查阅 |

## 关键红线

- **分支名必须包含 Multica Issue 编号**（如 `feature/MES-42-xxx`）
- 永远不要直接 push 到 `master` 或 `develop`
- 提交前运行 `npm run lint && npm run build`
- 至少 1 人 Approve 后方可合并 PR
