---
paths:
  - "*"
---

# Git 操作规范

## 分支策略

| 分支 | 命名 | 来源 | 合并目标 | 说明 |
|------|------|------|----------|------|
| `master` | `master` | — | — | 生产环境 |
| `develop` | `develop` | — | — | 集成分支 |
| Feature | `feature/<MES-编号>-<描述>` | `develop` | `develop` | 功能开发 |
| Release | `release/<版本>` | `develop` | `master` + `develop` | 发布准备 |
| Hotfix | `hotfix/<MES-编号>-<描述>` | `master` | `master` + `develop` | 紧急修复 |

**Feature/Hotfix 分支名必须包含 Multica Issue 编号**（如 `feature/MES-42-xxx`），Multica GitHub App 通过扫描分支名实现 PR ↔ Issue 自动关联。

## 提交信息规范

采用约定式提交（Conventional Commits）：

```
<类型>(<范围>): <简短描述>

<详细描述>

Closes <Issue编号>
```

| 类型 | 说明 |
|------|------|
| `feat` | 新功能 |
| `fix` | Bug 修复 |
| `docs` | 文档 |
| `refactor` | 重构 |
| `test` | 测试 |
| `chore` | 杂项 |
| `style` | 格式 |
| `perf` | 性能 |
| `build` | 构建 |
| `ci` | CI |

**注意：** Commit message 中的 Issue 编号不会被 Multica GitHub App 自动关联。关联方式：分支名 / PR 标题 / PR 正文。

## PR 基本规则

- **流向**: `feature/*` → `develop`；`release/*` `hotfix/*` → `master`
- **标题**: `<类型>(<范围>): <描述>`
- **单一职责**: 一个 PR 只做一个变更
- **大小**: 不超过 400 行代码变更
- **Draft**: 开发中 PR 用 Draft 模式
- **清理**: 合并后自动删除源分支

## 合并策略

| 流向 | 策略 |
|------|------|
| `feature/*` → `develop` | Squash and Merge |
| `release/*` → `master` | Merge Commit |
| `release/*` → `develop` | Merge Commit |
| `hotfix/*` → `master` | Merge Commit |
| `hotfix/*` → `develop` | Merge Commit |
