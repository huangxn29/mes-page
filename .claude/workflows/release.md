---
name: release
description: 执行标准发布流程，从 release 分支创建到打 tag 发布
---

# 发布流程

## Step 1: 创建 release 分支

```
git checkout develop
git pull
git checkout -b release/<版本号>
```

## Step 2: 发布准备

在 release 分支上：
- 更新版本号
- 更新 Changelog / Release Notes
- 修复发现的 Bug
- 更新文档

## Step 3: 创建 PR

创建 PR `release/<版本号>` → `master`
- PR 标题: `release(<项目>): <版本号>`
- 在描述中列出所有变更

## Step 4: Review + 合并

Code Review 通过后合并到 `master`

## Step 5: 打 Tag

```
git checkout master
git pull
git tag v<版本号>
git push origin v<版本号>
```

## Step 6: 创建 GitHub Release

基于 tag 创建 Release，填写 Release Notes。

## Step 7: 合并回 develop

确保 release 分支合并回 `develop`（如未自动合并）。

## Step 8: 清理

删除 release 分支。

## 版本号规范

遵循 [SemVer 2.0](https://semver.org/lang/zh-CN/)：`主版本.次版本.修订号`

## Release Notes 模板

```markdown
## v<版本号> - <日期>

### ✨ 新功能
- feat: ...

### 🐛 Bug 修复
- fix: ...

### 🔧 重构
- refactor: ...
```
