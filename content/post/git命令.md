---
title: "git 常用命令"           # 文章标题
author: "烂柯"              # 文章作者
description: "git 常用命令" # 文章描述信息
date: 2023-08-16                   # 文章编写日期
lastmod: 2025-09-29                # 文章修改日期

tags: [                    # 文章所属标签
    "版本管理",
    "开发工具"
]
categories: [              # 文章所属分类
    "环境配置"
]
keywords: [                # 文章关键词
    "git",
    "版本管理",
    "开发环境"
]
---

- ‌**feat**‌：表示新增功能（feature）
- ‌**fix**‌：表示修复bug
- ‌**docs**‌：表示文档更新
- ‌**style**‌：表示代码格式调整（不影响代码逻辑）
- ‌**refactor**‌：表示代码重构
- ‌**test**‌：表示测试相关更改
- ‌**chore**‌：表示构建过程或辅助工具的变动

git init               # 初始化仓库
git stash            暂存当前修改

git stash               # 暂存本地修改
git pull --rebase origin develop  # 变基式拉取最新代码
git stash pop           # 恢复本地修改

git fetch --all	#刷新获取远程分支

git branch -vv	#当前追踪的远程分支

## 第一次提交
提交本地修改
git add .
git commit -m "feat: add wxz feature"

创建并切换分支
git checkout -b feature/wxz

推送到远程
git push -u origin feature/wxz

若之前暂存了修改
git stash pop


git checkout -b feature/wxz	#直接基于当前分支创建并切换到 feature/wxz‌，无需先切换（因为分支不存在），可直接创建并切换：

#提交代码到新分支
git add .                      # 添加修改
git commit -m "提交描述"        # 提交到本地分支
git push -u origin feature/wxz # 推送到远程仓库

git checkout feature/3.10.7.C123  # 切换回原分支
git merge feature/wxz             # 合并新分支的修改
git push origin feature/3.10.7.C123 # 推送合并结果

分支操作决策树

graph TD     A[当前状态] --> B{需继续开发新功能?}     B -->|是| C[保持在feature/wxz分支修改]     B -->|否| D[返回feature/3.10.7.C123处理其他任务]     C --> E[定期rebase原分支]     D --> F[通过MR将wxz变更合并到主开发线]

若需继续开发wxz相关功能：

git checkout feature/wxz  # 返回特性分支 git add . && git commit -m "feat: 完善XX功能"  # 持续提交


若需处理3.10.7.C123其他任务：

git checkout feature/3.10.7.C123 git pull origin feature/3.10.7.C123  # 同步最新代码

跨分支同步策略

从主开发分支获取更新（每周至少执行一次） git checkout feature/wxz git rebase feature/3.10.7.C123  # 解决可能出现的冲突 git push -f origin feature/wxz  # 需要强制推送


**代码提交流程

sequenceDiagram     开发者->>+feature/wxz: 本地提交     feature/wxz->>+GitLab: 推送变更     GitLab->>+MR系统: 创建合并请求     评审者->>MR系统: 代码审核     MR系统->>feature/3.10.7.C123: 合并通过后自动同步

（AI生成）

## 统计近三十天代码行的脚本，保存为.ps1后缀文件
```powershell
$add = $subs = 0
git log --since="30 days ago" --pretty=tformat: --numstat --author="$(git config user.email)" |
ForEach-Object {
    $arr = $_ -split "\t"
    if ($arr[0] -match "^\d+$" -and $arr[1] -match "^\d+$") {
        $add += [int]$arr[0]
        $subs += [int]$arr[1]
    }
}

[PSCustomObject]@{
    '新增行数' = $add
    '删除行数' = $subs
    '净增行数' = ($add - $subs)
} | Format-List

```

## 统计代码行的脚本，保存为.ps1后缀文件
默认统计近七天 指定Days参数统计对应天数
示例： C:\script\CodestatisticsXday.ps1 -Days 7
```powershell
param(
    [int]$Days = 7
)

# 获取当前git用户邮箱
$userEmail = git config user.email

if (-not $userEmail) {
    Write-Error "无法获取git用户邮箱，请先配置git config user.email"
    exit 1
}

$add = $subs = 0

# 构建git命令
$sinceParam = "$Days days ago"
git log --since="$sinceParam" --pretty=tformat: --numstat --author="$userEmail" |
ForEach-Object {
    $arr = $_ -split "\t"
    if ($arr[0] -match "^\d+$" -and $arr[1] -match "^\d+$") {
        $add += [int]$arr[0]
        $subs += [int]$arr[1]
    }
}

# 输出结果
[PSCustomObject]@{
    '统计天数' = $Days
    '用户邮箱' = $userEmail
    '新增行数' = $add
    '删除行数' = $subs
    '净增行数' = ($add - $subs)
} | Format-List
```


git init

git remote add origin https://gitee.com/your_gitee_username/your_repo_name.git

git add .
git commit -m "Initial commit"  # 提交信息可以根据您的需要修改

git push -u origin master

git pull origin master

git add .
git commit -m "Merge remote changes"
	
git push origin master
查看改变的文件列表
git status




这个错误提示表明您尝试将两个没有共同祖先的分支进行合并，因此Git拒绝了此次合并。出现这种情况的原因可能是因为您的本地仓库和远程仓库在历史上没有共同的提交点。这通常在以下情况下发生：

本地仓库是新创建的，并且没有进行任何提交。
远程仓库是新创建的，并且没有进行任何提交。
本地仓库和远程仓库是独立的两个不同项目，没有共同的提交历史。
为了解决这个问题，您可以采取以下方法之一：

方法一：在推送前先拉取远程仓库的内容并进行合并：

在终端中执行以下命令，拉取远程仓库的内容并将其合并到本地分支中：
git pull origin master --allow-unrelated-histories
或者，如果您在Gitee上使用的是默认分支名为main，请将上述命令中的master替换为main：

git pull origin main --allow-unrelated-histories
方法二：创建新的分支并推送代码：

首先，将本地仓库创建为一个新的分支：

git checkout -b new_branch
然后将新分支关联到远程仓库：

git remote add origin https://gitee.com/w-x-z/vue.git
然后推送代码到远程仓库：

git push -u origin new_branch
通过执行上述方法之一，您应该能够成功将代码推送到Gitee远程仓库。请注意，第一种方法会将远程仓库的内容合并到当前分支中，而第二种方法会创建一个新的分支并将代码推送到该分支。选择哪种方法取决于您的需求和当前仓库的状态。