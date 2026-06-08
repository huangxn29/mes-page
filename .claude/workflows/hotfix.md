---
name: hotfix
description: 执行生产环境紧急修复流程
---

# 热修复流程

## Step 1: 创建 hotfix 分支

```
git checkout master
git pull
git checkout -b hotfix/<MES-编号>-<描述>
```

## Step 2: 修复

在 hotfix 分支上修复问题。
版本号递增修订号：`1.1.0` → `1.1.1`

## Step 3: 创建 PR

创建 PR `hotfix/*` → `master`
- PR 标题: `fix: <修复内容>`
- 标记为紧急，尽快安排 Review

## Step 4: Review + 合并到 master

## Step 5: 打 Tag

```
git checkout master
git pull
git tag v<新版本号>
git push origin v<新版本号>
```

## Step 6: 创建 GitHub Release

## Step 7: 同步到 develop

```
git checkout develop
git pull
git cherry-pick <hotfix-commit-hash>
git push
```
或创建 PR `hotfix/*` → `develop`

## Step 8: 清理

删除 hotfix 分支。
