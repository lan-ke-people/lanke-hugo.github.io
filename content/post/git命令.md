---
title: "git 常用命令"           # 文章标题
author: "烂柯"              # 文章作者
description: "git 常用命令" # 文章描述信息
date: 2023-08-16                   # 文章编写日期
lastmod: 2023-08-16                # 文章修改日期

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