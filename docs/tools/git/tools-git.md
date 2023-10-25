---
title: Git
date: 2023-10-02
author: Shuaike Shen
tags: ['Tools','Git']
categories: 
- Tools
- Git
---

## Git工作流程

克隆 Git 资源作为工作目录——在克隆的资源上添加或修改文件——如果其他人修改了，你可以更新资源——在提交前查看修改——提交修改——在修改完成后，如果发现错误，可以撤回提交并再次修改并提交。

## 基本概念

- **工作区：**就是你在电脑里能看到的目录。
- **暂存区：**英文叫 stage 或 index。一般存放在 **.git** 目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
- **版本库：**工作区有一个隐藏目录 **.git**，这个不算工作区，而是 Git 的版本库。

## Git创建仓库&克隆仓库&配置

```bash
git init
```

在当前目录初始化仓库

```bash
git add *.c
git add README
git commit -m 'commit message'
```

把修改commit，’’单引号中放commit信息

```bash
git clone
```

使用git clone命令从远程仓库clone一个

git 的设置使用 **git config** 命令。

```bash
git config --global user.name "Eurekashen"
git config --global user.email shenshuaike256@gmail.com
```

这是设置提交代码的时候的用户信息，如果去掉global的话就是只对当前的仓库有效

## 提交和修改

| 命令                                       | 说明                                     |
| ------------------------------------------ | ---------------------------------------- |
| https://www.runoob.com/git/git-add.html    | 添加文件到暂存区                         |
| https://www.runoob.com/git/git-status.html | 查看仓库当前的状态，显示有变更的文件。   |
| https://www.runoob.com/git/git-diff.html   | 比较文件的不同，即暂存区和工作区的差异。 |
| https://www.runoob.com/git/git-commit.html | 提交暂存区到本地仓库。                   |
| https://www.runoob.com/git/git-reset.html  | 回退版本。                               |
| https://www.runoob.com/git/git-rm.html     | 将文件从暂存区和工作区中删除。           |
| https://www.runoob.com/git/git-mv.html     | 移动或重命名工作区文件。                 |

### **提交日志**

| 命令                                                         | 说明                                 |
| ------------------------------------------------------------ | ------------------------------------ |
| https://www.runoob.com/git/git-commit-history.html#git-log   | 查看历史提交记录                     |
| https://www.runoob.com/git/git-commit-history.html#git-blame | 以列表形式查看指定文件的历史修改记录 |

### **远程操作**

| 命令                                       | 说明               |
| ------------------------------------------ | ------------------ |
| https://www.runoob.com/git/git-remote.html | 远程仓库操作       |
| https://www.runoob.com/git/git-fetch.html  | 从远程获取代码库   |
| https://www.runoob.com/git/git-pull.html   | 下载远程代码并合并 |
| https://www.runoob.com/git/git-push.html   | 上传远程代码并合并 |