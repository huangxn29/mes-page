# MES-Page

> ⚠️ **Git Flow 强制规范 — 每次操作前必须阅读**
> 本仓库所有分支创建、提交、推送、合并操作，**必须严格遵循** `.claude/rules/git-workflow.md` 中的规范。
>
> **Agent 必须执行的红线：**
> 1. 分支名 **必须包含 Issue 编号**：`feature/MES-编号-描述` / `hotfix/MES-编号-描述`
> 2. 提交信息必须使用 **约定式提交**：`<type>(<scope>): <desc>`
> 3. **禁止直接 push** 到 `master` / `develop` / `main`，必须走 Pull Request
> 4. 合并策略：feature→develop 用 Squash Merge；release/hotfix→master 用 Merge Commit
> 5. 本仓库已配置 `.claude/hooks/` git 钩子，违规操作会被自动拦截
>
> 使用 `/git-workflow` 查看完整规范。

前端项目（React / TypeScript）。

## 命令
- Dev: `npm run dev`
- Build: `npm run build`
- Test: `npm test`
- Lint: `npm run lint`

## Git 规范（精简版）
- **分支模型**: Git Flow — `master` / `develop` / `feature/*` / `release/*` / `hotfix/*`
- **分支命名**: `feature/<MES-编号>-<描述>` / `hotfix/<MES-编号>-<描述>` （**必须包含 Issue 编号**，Multica GitHub App 通过分支名自动关联 Issue）
- **提交格式**: 约定式提交 — `feat:` `fix:` `docs:` `refactor:` `chore:` `test:`
- **合并策略**: feature→develop 用 Squash Merge；release/hotfix→master 用 Merge Commit
- **PR 前**: 运行 lint + build，通过后再提交
- **红线**: 永远不要直接 push 到 `master` 或 `develop`

## 资源
- [完整 Git 规范](./.claude/rules/git-workflow.md)
- [PR 模板](./.github/PULL_REQUEST_TEMPLATE.md)
- 分支/提交规范: `/git-workflow` (slash command)
- 发布流程: `/release`
- 热修复流程: `/hotfix`
- Code Review: `/review`
