---
title: "nvm 常用命令"           # 文章标题
author: "烂柯"              # 文章作者
description: "Node Version Manager (nvm) 常用命令" # 文章描述信息
date: 2024-06-28                   # 文章编写日期
lastmod: 2024-06-28                # 文章修改日期

tags: [                    # 文章所属标签
    "Node.js",
    "版本管理",
    "开发工具"
]
categories: [              # 文章所属分类
    "前端开发",
    "环境配置"
]
keywords: [                # 文章关键词
    "nvm",
    "Node.js",
    "版本切换",
    "开发环境"
]
---

# nvm 常用命令

### 显示可以安装的所有node.js的版本
```bash
nvm list available
```

### 安装指定版本的Node.js
```bash
nvm install 16.13.1
```

### 查看已安装的node.js版本
```bash
nvm list
```

### 切换到指定的nodejs版本
```bash
nvm use 16.13.1
```

### 查看本电脑上所有的node版本
```bash
nvm list
```

### 查看已经安装的版本
```bash
nvm list installed
```

### 查看网络可以安装的版本
```bash
nvm list available
```

### 安装最新版本Node.js
```bash
nvm install
```

### 切换使用指定的版本node
```bash
nvm use <version>
```

### 列出所有版本
```bash
nvm ls
```

### 显示当前版本
```bash
nvm current
```

### 给不同的版本号添加别名
```bash
nvm alias <name> <version>
```

### 删除已定义的别名
```bash
nvm unalias <name>
```

### 重新全局安装指定版本号的npm包
```bash
nvm reinstall-packages <version>
```

### 打开nodejs控制
```bash
nvm on
```

### 关闭nodejs控制
```bash
nvm off
```

### 查看设置与代理
```bash
nvm proxy
```

### 设置或查看node_mirror
```bash
nvm node_mirror [url]
```

### 设置或查看npm_mirror
```bash
nvm npm_mirror [url]
```

### 卸载制定的版本
```bash
nvm uninstall <version>
```

### 切换制定的node版本和位数
```bash
nvm use [version] [arch]
```

### 设置和查看root路径
```bash
nvm root [path]
```

### 查看当前的nvm版本
```bash
nvm version
```