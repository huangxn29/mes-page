# Git 与 GitHub 操作规范

> 基于行业最佳实践及 Multica 平台规范制定
> 适用于 MES 及 MES-Page 项目

---

## 目录

1. [分支策略](#1-分支策略)
2. [分支命名规范](#2-分支命名规范)
3. [提交信息规范](#3-提交信息规范)
4. [Pull Request 规范](#4-pull-request-规范)
5. [Code Review 规范](#5-code-review-规范)
6. [合并策略](#6-合并策略)
7. [分支保护规则](#7-分支保护规则)
8. [发布流程](#8-发布流程)
9. [热修复流程](#9-热修复流程)
10. [Multica 平台集成](#10-multica-平台集成)
11. [Multica + GitHub 端到端工作流](#11-multica--github-端到端工作流)
12. [CI/CD 推荐配置](#12-cicd-推荐配置)
13. [Git 本地配置建议](#13-git-本地配置建议)
14. [常用命令速查](#14-常用命令速查)

---

## 1. 分支策略

本项目采用 **Git Flow** 分支模型，符合行业标准实践。

| 分支类型 | 命名 | 来源 | 合并目标 | 生命周期 | 说明 |
|----------|------|------|----------|----------|------|
| **`master`** | `master` | — | — | 永久 | 生产环境分支。只接受 `release/*` 和 `hotfix/*` 的合并。每个合并到 `master` 的提交对应一次发布。 |
| **`main`** | `main` | — | — | 永久 | 与 `master` 保持同步，两者互为镜像（可选）。推荐统一使用 `master` 作为生产分支。 |
| **`develop`** | `develop` | — | — | 永久 | 集成分支，包含下一个版本的全部已完成功能。`feature/*` 分支合并到此分支。 |
| **Feature** | `feature/<描述>` | `develop` | `develop` | 临时 | 功能开发分支。功能完成后通过 Pull Request 合并回 `develop`。 |
| **Release** | `release/<版本号>` | `develop` | `master` + `develop` | 临时 | 发布准备分支。用于 Bug 修复、文档更新、版本号升级。准备就绪后合并到 `master` 和 `develop`。 |
| **Hotfix** | `hotfix/<描述>` | `master` | `master` + `develop` | 临时 | 生产环境紧急修复分支。修复完成后合并到 `master` 和 `develop`。 |

### 分支策略图示

```
master  ───────●────────────────────●──────────→
               \                    /
develop  ──●────●────●────●────●──●────●────→
            \        /    \    /        \    /
feature/*    ●──●──●      ●──●          ●──●

release/*               ●──●──●
                              \
hotfix/*           ●──●───────●
                  /
master  ─────────●────────────────────────────→
```

---

## 2. 分支命名规范

### 2.1 Feature 分支

```
feature/<项目>-<功能简短描述>
```

- 使用 `kebab-case`（中划线分隔的小写单词）
- 描述应简短明确，体现功能核心
- 长度建议不超过 50 个字符

**示例：**
- `feature/mes-add-work-order-module`
- `feature/mes-page-dashboard-refresh`
- `feature/mes-api-export-endpoint`

### 2.2 Release 分支

```
release/<语义化版本号>
```

- 遵循 [SemVer 2.0](https://semver.org/) 规范
- 格式：`主版本.次版本.修订号`

**示例：**
- `release/1.0.0`
- `release/1.2.0`
- `release/2.0.0-beta.1`

### 2.3 Hotfix 分支

```
hotfix/<问题简述>
```

- 使用 `kebab-case`
- 描述应反映修复内容

**示例：**
- `hotfix/login-npe-fix`
- `hotfix/prod-db-connection-leak`
- `hotfix/null-pointer-on-report`

---

## 3. 提交信息规范

采用 **约定式提交（Conventional Commits）** 标准，这是社区广泛采纳的提交规范。

### 3.1 提交信息格式

```
<类型>(<范围>): <简短描述>

<详细描述（可选）>

<关闭的 Issue（可选）>
```

### 3.2 提交类型

| 类型 | 说明 | 版本影响 |
|------|------|----------|
| `feat` | 新功能 | 次版本+1 |
| `fix` | Bug 修复 | 修订号+1 |
| `docs` | 仅文档更改 | 无 |
| `style` | 格式调整（空格、格式化等，不影响代码运行） | 无 |
| `refactor` | 代码重构（既不修复 Bug 也不增加功能） | 无 |
| `perf` | 性能优化 | 无 |
| `test` | 添加或修改测试 | 无 |
| `build` | 构建系统或外部依赖变更 | 无 |
| `ci` | CI 配置或脚本变更 | 无 |
| `chore` | 杂项（构建过程或辅助工具变更） | 无 |

### 3.3 示例

```
feat(mes-page): add work order detail page

Implement work order detail view with status timeline.
Includes print preview and export to PDF.

Closes MES-45
```

```
fix(mes-page): resolve null pointer on dashboard load

The dashboard component crashed when user had no assigned orders.
Added null check before rendering order list.

Fixes MES-55
```

```
docs(readme): update deployment instructions

Add Docker Compose setup guide and environment variable reference.
```

### 3.4 提交信息规范细则

1. **简短描述**：不超过 72 个字符，首字母小写，结尾不加句号。
2. **祈使语气**：使用 "Add"、"Fix"、"Update" 而非 "Added"、"Fixed"、"Updated"。
3. **范围可选**：`feat(mes-page)` 有助于定位变更模块。
4. **关联 Issue**：在正文中使用 `Closes #123` 或 `Fixes MES-45` 自动关联。

---

## 4. Pull Request 规范

### 4.1 PR 创建流程

```
feature/*  →  Pull Request  →  develop
release/*  →  Pull Request  →  master  (同时反向合并到 develop)
hotfix/*   →  Pull Request  →  master  (同时反向合并到 develop)
```

### 4.2 PR 标题规范

```
<类型>(<范围>): <简短描述>
```

与提交信息格式一致。

**示例：**
- `feat(mes-page): add work order detail page`
- `fix(mes-page): fix dashboard not refreshing on data change`
- `release(mes-page): 1.2.0`

### 4.3 PR 描述模板

每个 Pull Request 应包含：

```markdown
## 变更内容
<!-- 描述此 PR 做了什么，为什么做 -->

## 关联 Issue
<!-- Closes #123 或 Fixes MES-42 -->

## 测试步骤
<!-- 如何验证变更正确 -->

## 截图（UI 变更时必填）
<!-- 如果有 UI 变更，请附上前后对比截图 -->

## 检查清单
- [ ] 代码通过本地构建
- [ ] 新增/修改的代码有对应的单元测试
- [ ] 自测通过
- [ ] 文档已更新（如适用）
- [ ] 无引入新的告警或错误日志
```

### 4.4 PR 基本规则

| 规则 | 说明 |
|------|------|
| **单一职责** | 一个 PR 只做一个变更，不要混合多个不相关的功能或修复 |
| **PR 大小** | 建议每个 PR 不超过 400 行代码变更。大功能应拆分为多个 PR |
| **Draft PR** | 开发中的 PR 使用 Draft 模式，准备好审查后再标记为 Ready for Review |
| **自动关闭** | 合并分支后，对应的 feature/hotfix 分支由创建者负责删除（GitHub 可设置自动删除源分支） |
| **Chain PR** | 避免链式 PR（B 依赖 A 未合并的变更），应使用基于 develop 的独立分支 |

---

## 5. Code Review 规范

### 5.1 Review 流程

1. **开发者**创建 PR → 指派 Reviewers → 标记为 "Ready for Review"
2. **Reviewer** 收到通知 → 在 24 小时内开始审查
3. **Reviewer** 提出意见 → 开发者回应并修改
4. **至少 1 人 Approve** 后方可合并

### 5.2 Review 检查维度

| 维度 | 检查要点 |
|------|----------|
| **正确性** | 逻辑是否正确？边界条件是否处理？是否存在潜在的 NPE？ |
| **安全性** | 输入是否校验？是否有 SQL 注入/XSS 风险？权限检查是否到位？ |
| **性能** | 是否存在 N+1 查询？循环中的无效操作？缓存使用是否合理？ |
| **可维护性** | 代码是否清晰易懂？是否有适当的注释？是否遵循现有代码风格？ |
| **测试覆盖** | 是否有充分的单元测试？是否覆盖了边界情况和异常路径？ |
| **一致性** | 是否遵循了项目的架构模式？API 设计是否与现有接口风格一致？ |

### 5.3 Review 评论分级

| 级别 | 标记 | 含义 |
|------|------|------|
| **必须修复** | ❌ **REQUIRED** | Bug、安全问题、严重逻辑错误。未修复不得合并。 |
| **建议修改** | ⚠️ **SUGGESTION** | 可改进的地方，但不是必须修复。由开发者判断是否采纳。 |
| **问题/疑问** | ❓ **QUESTION** | 对代码有疑问，需要开发者解释。 |
| **表扬** | 👍 **NICE** | 对好的实现给予肯定。保持团队积极性。 |

### 5.4 Review 最佳实践

- **Reviewer：** 关注代码逻辑和架构，而非代码风格（代码风格应由 Linter 和 Formatter 自动保证）
- **Reviewer：** 友善且建设性的评论。问问题而非下结论："这个方法在这里是否可能返回 null？"
- **开发者：** 对每条评论予以回复（"已修复"、"有道理，已采纳"、"这里是因为..."）
- **开发者：** 不强制要求 Reviewer 二次审查。微小修改可直接 resolve
- **速度优先：** 小 PR 优先审查，避免积压阻塞下游工作

---

## 6. 合并策略

### 6.1 合并策略选择

| 分支流向 | 推荐策略 | 说明 |
|----------|----------|------|
| `feature/*` → `develop` | **Squash and Merge** | 将 feature 分支上所有提交压缩为一个提交，保持 `develop` 历史整洁 |
| `release/*` → `master` | **Merge Commit** | 保留完整的发布历史，便于回滚 |
| `release/*` → `develop` | **Merge Commit** | 保留修复记录 |
| `hotfix/*` → `master` | **Merge Commit** | 保留紧急修复记录 |
| `hotfix/*` → `develop` | **Merge Commit** | 保留修复记录 |

### 6.2 Squash and Merge 规范

当使用 Squash and Merge 将 `feature/*` 合并到 `develop` 时：

- **提交信息**：使用 PR 标题作为 squash commit 的标题
- **提交正文**：保留 PR 描述中的关键信息
- 压缩后的提交应描述完整的功能变更，而非零散的开发过程

### 6.3 删除源分支

合并后，源分支应被删除：
- GitHub 支持合并时勾选 "Delete source branch"
- 定期清理本地已合并的分支：`git fetch -p && git branch -d $(git branch --merged | grep feature/)`

---

## 7. 分支保护规则

以下规则应在 GitHub 仓库 Settings → Branches → Branch protection rules 中配置。

### 7.1 `master` 分支保护

```yaml
# 推荐的分支保护规则 - master
- Require a pull request before merging: ✅
  - Require approvals: 1
  - Dismiss stale pull request approvals when new commits are pushed: ✅
  - Require review from Code Owners: ✅
- Require status checks to pass before merging: ✅
  - Require branches to be up to date: ✅
- Require conversation resolution before merging: ✅
- Include administrators: ✅
- Allow force pushes: ❌
- Allow deletions: ❌
- Lock branch: ❌
```

### 7.2 `develop` 分支保护

```yaml
# 推荐的分支保护规则 - develop
- Require a pull request before merging: ✅
  - Require approvals: 1
  - Dismiss stale pull request approvals: ✅
- Require status checks to pass before merging: ✅
  - Require branches to be up to date: ✅
- Require conversation resolution before merging: ✅
- Include administrators: ✅
- Allow force pushes: ❌
- Allow deletions: ❌
```

### 7.3 `release/*` 和 `hotfix/*` 分支保护

```yaml
# 推荐的分支保护规则 - release/* 和 hotfix/*
- Require a pull request before merging: ✅
  - Require approvals: 1
- Require status checks to pass before merging: ✅
- Include administrators: ✅
- Allow force pushes: ❌
```

### 7.4 必需的状态检查

建议将以下 CI 检查设为 Required：

| 检查 | 说明 |
|------|------|
| Build / Compile | 项目编译通过 |
| Lint | 代码风格检查通过 |
| Unit Tests | 单元测试全部通过 |
| Integration Tests | 集成测试全部通过（可选） |

---

## 8. 发布流程

### 8.1 标准发布流程

```
1. 从 develop 创建 release 分支
   git checkout develop
   git checkout -b release/1.2.0

2. 在 release 分支上：
   - 更新版本号
   - 更新 Changelog / Release Notes
   - 修复发现的 Bug
   - 更新文档

3. 创建 PR release/1.2.0 → master
   - PR 标题: "release(mes-page): 1.2.0"
   - 在描述中列出所有变更

4. Code Review + 测试验证通过后合并到 master

5. 在 master 上打 Tag
   git checkout master
   git pull
   git tag v1.2.0
   git push origin v1.2.0

6. 创建 GitHub Release
   - 基于 tag v1.2.0 创建 Release
   - 填写 Release Notes

7. 将 release 分支合并回 develop（如未自动合并）

8. 删除 release 分支
```

### 8.2 版本号规范

遵循 [语义化版本 2.0.0](https://semver.org/lang/zh-CN/)：

- **主版本号**：做了不兼容的 API 修改时递增
- **次版本号**：新增了向下兼容的功能时递增
- **修订号**：做了向下兼容的问题修正时递增
- **预发布版本**：`1.0.0-alpha.1`、`1.0.0-beta.1`、`1.0.0-rc.1`

### 8.3 Release Notes 模板

```markdown
## v1.2.0 - 2026-06-05

### ✨ 新功能
- feat(mes-page): add work order detail page (MES-45)
- feat(mes-page): add production dashboard (MES-43)

### 🐛 Bug 修复
- fix(mes-page): resolve NPE on empty order list (MES-55)
- fix(mes-page): date picker not initializing correctly (MES-57)

### 🔧 重构
- refactor(mes-page): extract order list component

### 📝 文档
- docs: update component documentation

### ⬆️ 依赖更新
- build: upgrade react to 18.3.0
```

---

## 9. 热修复流程

### 9.1 紧急修复流程

```
1. 从 master 创建 hotfix 分支
   git checkout master
   git checkout -b hotfix/prod-login-npe

2. 在 hotfix 分支上修复问题

3. 创建 PR hotfix/* → master
   - PR 标题: "fix: resolve login NPE on production"
   - 标记为紧急，尽快安排 Review

4. Review 通过后合并到 master

5. 在 master 上打 Tag
   git checkout master
   git tag v1.1.1
   git push origin v1.1.1

6. 创建 GitHub Release（包含修补说明）

7. 创建 PR hotfix/* → develop（确保修复同步到开发分支）

8. 删除 hotfix 分支
```

### 9.2 热修复分支的版本号

- 热修复递增修订号：`1.1.0` → `1.1.1`
- 版本号应在 hotfix 分支上更新

---

## 10. Multica 平台集成

> 参考：[Multica 官方文档](https://github.com/multica-ai/multica) · [CLI 命令参考](https://github.com/multica-ai/multica/blob/main/CLI_AND_DAEMON.md)

Multica 是开源托管 Agent 平台，将 AI Agent 变成真正的团队成员。以下内容基于 Multica 官方 GitHub App 集成及 CLI 规范制定。

### 10.1 官方 GitHub App 集成（PR ↔ Issue 自动关联）

Multica 提供 **GitHub App** 实现 PR 与 Issue 的双向自动关联。这是 Multica 官方的标准集成方式，无需手动维护关联。

**工作原理：** 安装 Multica GitHub App 并授权仓库后，App 会自动扫描 PR 的以下位置，检测 Multica Issue 编号：

| 扫描位置 | 示例 | 自动关联 |
|----------|------|---------|
| **PR 头部分支名** | `fix-MES-42` 或 `feature/MES-42-work-order` | ✅ |
| **PR 标题** | `fix: MES-42 修复工单创建` | ✅ |
| **PR 正文** | `Closes MES-42` 或 `Fixes MES-42` | ✅ |
| **Commit message** | `feat: add work order (MES-42)` | ❌ 不支持 |
| **PR 评论** | `MES-42 待处理` | ❌ 不支持 |

**关键规则（官方）：**
- Issue 编号前缀自动限定本工作区（如 `MES-`），不同工作区的 Issue 不会误关联
- 一个 PR 可以关联**多个** Issue（在 PR 正文中列出多个编号）
- 一个 Issue 也可被**多个** PR 关联

**自动流转：**
- **仅当一个 Issue 关联的所有 PR 都已合并**后，Issue 才会自动转为 **Done** 状态（官方 `fix: only auto-close issue after all linked PRs resolve`）
- 无需手动运行 `multica issue status MES-42 done`

### 10.2 官方 Issue → Agent → GitHub 工作流

Multica 官方的核心工作流是：

```
创建 Issue → 分配给 Agent → Agent 自主工作 → 交付代码 → 创建 PR → PR 关联 Issue → 合并后自动关闭
```

根据官方 README：Agent 是 **"一级团队成员"**，会被分配 Issue，自主执行以下操作：
- "pick up the work（接手工作）"
- "write code（编写代码）"
- "report blockers（报告阻塞）"
- "update statuses（更新状态）"

### 10.3 Multica CLI 官方命令参考

根据 Multica CLI 官方文档，开发全流程涉及的官方命令：

| 阶段 | 官方命令 | 说明 |
|------|----------|------|
| 安装工具 | `multica setup` | 一键配置、认证、启动守护进程 |
| 查看任务 | `multica issue get <issue-id> --output json` | 获取 Issue 详情 |
| 指派任务 | `multica issue assign <id> --to "Agent Name"` | 将 Issue 指派给 Agent |
| 切换状态 | `multica issue status <id> <status>` | 有效状态：`backlog` → `todo` → `in_progress` → `in_review` → `done` → `blocked` → `cancelled` |
| 查看讨论 | `multica issue comment list <id> --thread <id> --tail 30` | 按线程查看评论 |
| 参与讨论 | `multica issue comment add <id> --parent <id> --content-file <path>` | 回复特定评论 |
| 元数据跟踪 | `multica issue metadata set <id> --key <k> --value <v>` | 写入 KV 元数据 |
| 创建子任务 | `multica issue create --parent <id> --title "..." --status todo` | 创建子 Issue |

### 10.4 官方 Autopilot 自动化

Multica 提供 **Autopilot** 机制实现定期自动化任务：

| 触发方式 | 说明 | 适用场景 |
|----------|------|----------|
| **Cron 定时** | 按 cron 表达式定期触发 | 日报、周报、定期巡检 |
| **Webhook 触发** | 外部事件触发 | GitHub 事件联动 |
| **手动触发** | 人工调用 `multica autopilot trigger <id>` | 按需执行 |

Autopilot 会自动创建 Issue 并分配给指定 Agent，Agent 按照预设指令执行任务。

### 10.5 官方 Squad 与 Skills 机制

**Squad（小队）：** 官方支持多个 Agent 组成由 Leader Agent 带队的小队。任务分配给小队后，Leader 判断 "谁最适合接手"。通过 `@前端组` 风格引用，而非逐个 @成员。

**Skills（技能）：** Multica 官方技能系统支持将解决方案沉淀为团队可复用的技能。典型技能包括：
- 部署流程
- 数据库迁移
- **代码审查（Code Review）**—— 官方文档明确列出此场景
- **创建 PR** —— Agent 可自动创建 PR 并推送

---

## 11. Multica + GitHub 官方端到端工作流

### 11.1 官方推荐的开发流程

```
┌──────────────────────────────────────────────────────────┐
│                    Multica 平台                            │
│                                                           │
│  1. 创建 Issue ──→ 2. 分配给 Agent ──→ 3. Agent 自主执行  │
│     (描述需求)       (multica issue assign)   (写代码、    │
│                                               提PR、汇报)  │
│         ↑                                        │        │
│         │  GitHub App 检测到 PR 关联 Issue       │        │
│         │  ↕                                      ↓        │
│  5. Issue 自动 Done ── 4. PR 合并到 develop ── PR 创建    │
│     (所有关联PR合并后)    (Code Review通过)   (关联Issue)  │
└──────────────────────────────────────────────────────────┘
```

### 11.2 分步操作

#### Step 1：在 Multica 中创建并分配 Issue

```bash
# 创建 Issue（或在 Web 看板上创建）
multica issue create \
  --title "Add work order detail page" \
  --description "需要实现工单详情页面..." \
  --priority high \
  --status todo

# 指派给 Agent
multica issue assign MES-42 --to "Agent Name"

# Agent 会自动接手任务并开始工作
```

#### Step 2：Agent 开始工作

Agent 被分配 Issue 后会自动：
1. 通过 `multica issue get MES-42 --output json` 查看详情
2. 通过 `multica repo checkout <repo-url>` 检出仓库
3. 创建 feature 分支
4. 编写代码、提交、推送

#### Step 3：创建 PR（GitHub App 自动关联）

Agent 创建 PR 时，GitHub App **自动扫描**以下位置建立关联：

```bash
# 推荐的关联方式 1：分支名包含 Issue 编号
git checkout -b feature/MES-42-work-order-page   # ← GitHub App 自动识别

# 推荐的关联方式 2：PR 标题包含 Issue 编号
# PR Title: "feat(mes-page): add work order detail page - MES-42"  ← GitHub App 自动识别

# 推荐的关联方式 3：PR 正文包含 Closes 关键字
# PR Body: "Closes MES-42"  ← GitHub App 自动识别
```

> **注意：** 根据 Multica 官方规范，Commit Message 中的 Issue 编号**不会**被自动关联。请确保在分支名、PR 标题或 PR 正文中引用 Issue 编号。

#### Step 4：Code Review 与合并

```bash
# 在 GitHub 上完成 Code Review

# Agent 也可以执行 Code Review（Multica Skills 机制）
# Reviewer 通过后合并 PR

# PR 合并后，GitHub App 自动检测并流转 Issue
```

#### Step 5：Issue 自动关闭

Multica 官方规则：**当关联一个 Issue 的所有 PR 都已合并**，该 Issue 自动转入 **Done** 状态。无需手动运行 `multica issue status MES-42 done`。

### 11.3 官方状态映射

| 开发阶段 | Multica Issue 状态 | GitHub 状态 | 自动机制 |
|----------|-------------------|-------------|----------|
| 待处理 | `backlog` / `todo` | — | — |
| 正在进行 | `in_progress` | feature 分支已推送 | Agent 自动切换状态 |
| 等待审查 | `in_review` | PR 已创建（关联 Issue） | PR 创建时 Agent 可切换 |
| 已合并 | `done` ✅ | PR 已合并 | **GitHub App 自动**（所有 PR 合并后） |
| 已部署 | `done` ✅ | Tag 已推送 | — |
| 阻塞 | `blocked` | — | Agent 可报告阻塞 |

### 11.4 补充场景：Autopilot 定期任务

对于周期性任务（如每日巡检、周报生成）：

```bash
# 创建 Autopilot
multica autopilot create \
  --title "Daily codebase health check" \
  --description "每日检查代码库健康状况" \
  --agent "Agent Name" \
  --mode create_issue

# 添加 Cron 触发器（每天早上 9 点）
multica autopilot trigger-add <autopilot-id> \
  --cron "0 9 * * *" \
  --timezone "Asia/Shanghai"
```

Autopilot 会在指定时间自动创建 Issue 并分配给 Agent，Agent 执行完毕后更新结果。

---

## 12. CI/CD 推荐配置

### 12.1 推荐的 GitHub Actions 工作流

#### PR 验证（Pull Request 触发）

```yaml
# .github/workflows/pr-check.yml
name: PR Check

on:
  pull_request:
    types: [opened, synchronize, reopened]
    branches: [develop, master, main, release/*, hotfix/*]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build & Test
        run: |
          # 根据项目语言调整
          # npm run build && npm test
      - name: Lint Check
        run: |
          # npm run lint

  commitlint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: wagoid/commitlint-github-action@v5
```

#### 发布自动化

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build
        run: |
          # 构建产物
      - name: Create Release
        uses: softprops/action-gh-release@v2
        with:
          generate_release_notes: true
```

### 12.2 Commitlint 配置

```javascript
// commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      ['feat', 'fix', 'docs', 'style', 'refactor', 'perf', 'test', 'build', 'ci', 'chore']
    ],
    'scope-empty': [0],
    'subject-case': [2, 'always', 'lower-case'],
    'subject-max-length': [2, 'always', 72],
  }
};
```

---

## 13. Git 本地配置建议

### 13.1 推荐全局配置

```bash
# 用户信息
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 默认分支名
git config --global init.defaultBranch master

# 行尾处理（Windows）
git config --global core.autocrlf true

# 大小写敏感
git config --global core.ignorecase false

# 编辑器（按需设置）
git config --global core.editor "code --wait"

# 别名
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD"
git config --global alias.lg "log --graph --oneline --all --decorate"

# 推送设置
git config --global push.default simple
git config --global pull.rebase true
git config --global rebase.autoStash true
```

### 13.2 提交信息模板（可选）

将以下内容保存为 `~/.gitmessage`：

```
<类型>(<范围>): <简短描述>

<详细描述>

Closes #
```

配置：`git config --global commit.template ~/.gitmessage`

### 13.3 全局 .gitignore 建议

```bash
# 设置全局 gitignore
git config --global core.excludesFile ~/.gitignore_global
```

`~/.gitignore_global` 内容：

```
# IDE
.idea/
.vscode/
*.iml
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Build
target/
build/
dist/
*.log

# Environment
.env.local
.env.*.local
```

---

## 14. 常用命令速查

### 14.1 新功能开发

```bash
# 从最新的 develop 创建 feature 分支
git checkout develop
git pull
git checkout -b feature/mes-page-add-work-order-page

# 开发过程中保持同步
git fetch origin
git rebase origin/develop

# 提交
git add .
git commit -m "feat(mes-page): add work order detail page"

# 推送
git push -u origin feature/mes-page-add-work-order-page

# → 在 GitHub 上创建 PR
```

### 14.2 发布

```bash
# 创建 release 分支
git checkout develop
git pull
git checkout -b release/1.2.0

# 更新版本号、Changelog
# ...

# 提交发布准备
git add .
git commit -m "chore(release): prepare release 1.2.0"
git push -u origin release/1.2.0

# → 创建 PR release/1.2.0 → master
# → Review + 合并

# 在 master 上打 tag
git checkout master
git pull
git tag v1.2.0
git push origin v1.2.0

# → 创建 GitHub Release
```

### 14.3 热修复

```bash
# 从 master 创建 hotfix 分支
git checkout master
git pull
git checkout -b hotfix/login-npe

# 修复 + 提交
git add .
git commit -m "fix: resolve null pointer on login page"

# 推送
git push -u origin hotfix/login-npe

# → 创建 PR hotfix/login-npe → master
# → Review + 合并到 master
# → 打 tag

# 同步到 develop
git checkout develop
git pull
git cherry-pick <hotfix-commit-hash>
git push

# 或创建 PR hotfix/login-npe → develop
```

### 14.4 同步与清理

```bash
# 更新本地分支信息
git fetch -p

# 删除本地已合并的 feature 分支
git branch -d feature/completed-feature

# 删除远程已合并的分支
git push origin --delete feature/completed-feature

# 查看分支拓扑
git log --graph --oneline --all --decorate
```

---

## 附录 A：项目配置文件索引

本项目推荐在根目录下包含以下配置文件：

| 文件 | 说明 |
|------|------|
| `.github/PULL_REQUEST_TEMPLATE.md` | PR 模板 |
| `commitlint.config.js` | 提交信息检查规则 |
| `.github/workflows/pr-check.yml` | PR 自动化检查 |
| `.github/workflows/release.yml` | 发布自动化 |
| `.gitignore` | Git 忽略规则 |
| `CONTRIBUTING.md` | 贡献指南（本文档的精简版） |

---

## 附录 B：参考资源

### Multica 官方文档
- [Multica 官方 GitHub 仓库](https://github.com/multica-ai/multica) — 开源托管 Agent 平台
- [Multica 中文 README](https://github.com/multica-ai/multica/blob/main/README.zh-CN.md) — 官方中文使用指南
- [Multica CLI 与 Daemon 命令参考](https://github.com/multica-ai/multica/blob/main/CLI_AND_DAEMON.md) — 完整 CLI 命令列表
- [Multica 云服务](https://multica.ai) — 快速开始使用

### 行业标准
- [Git Flow 原始论文 — Vincent Driessen](https://nvie.com/posts/a-successful-git-branching-model/)
- [语义化版本 2.0.0](https://semver.org/lang/zh-CN/)
- [约定式提交 1.0.0](https://www.conventionalcommits.org/zh-hans/)
- [GitHub 官方文档 — 管理分支保护规则](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches)

---

> **维护者：** 本项目维护者应定期审查并更新此规范文档。
> **版本：** v1.0.0
> **最后更新：** 2026-06-05
