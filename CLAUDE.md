# CLAUDE.md

此文件为 Claude Code (claude.ai/code) 在此仓库中工作时提供指导。

## 项目概述

mes-page 是制造执行系统（MES）的前端页面项目。

- **GitHub**: `https://github.com/huangxn29/mes-page.git`
- **当前分支**: `develop`（主开发分支）

> 当前仓库处于初始阶段，尚未设置源代码、构建系统或依赖项。

---

## Git Flow 工作流

本项目采用 **Git Flow** 分支模型进行版本控制。

### 分支策略

| 分支 | 命名 | 来源 | 合并目标 | 说明 |
|------|------|------|----------|------|
| `master` | `master` | — | — | 生产环境，只接受 `release` / `hotfix` 合并 |
| `develop` | `develop` | — | — | 集成分支，包含下一个版本全部功能 |
| Feature | `feature/<描述>` | `develop` | `develop` | 功能开发 |
| Release | `release/<版本>` | `develop` | `master` + `develop` | 发布准备、Bug 修复、文档更新 |
| Hotfix | `hotfix/<描述>` | `master` | `master` + `develop` | 生产环境紧急修复 |

### 常用操作

```bash
# 开发新功能
git checkout develop && git checkout -b feature/xxx
# ... 开发完成后
git checkout develop && git merge --no-ff feature/xxx && git branch -d feature/xxx

# 发布版本
git checkout develop && git checkout -b release/v1.0.0
# ... 修复 Bug、更新版本号
git checkout master && git merge --no-ff release/v1.0.0 && git tag -a v1.0.0
git checkout develop && git merge --no-ff release/v1.0.0 && git branch -d release/v1.0.0

# 紧急修复
git checkout master && git checkout -b hotfix/xxx
# ... 修复完成
git checkout master && git merge --no-ff hotfix/xxx && git tag -a v1.0.1
git checkout develop && git merge --no-ff hotfix/xxx && git branch -d hotfix/xxx
```

### 提交信息格式

```
<type>(<scope>): <subject>

<body>
```

- **type**: `feat` | `fix` | `docs` | `refactor` | `test` | `chore`
- **scope**: 影响范围（可选）
- **subject**: 不超过 50 字

### 版本号规范

遵循 [SemVer](https://semver.org/)：`v主版本.次版本.修订号`

---

## 当前状态

- 项目正在初始化，尚未选型技术栈
- **待办**: 选定前端框架、构建工具、测试框架后，补充对应的开发命令
