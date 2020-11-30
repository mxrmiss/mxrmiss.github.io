---
title: Git & Github
date: 2020/5/3 9:08:28
comments: ture
categories:
- Git
tags:
- Git
---

# Git & Github



- Git 初始化

  ```
  # 设置github昵称
  git config --global user.name 'mxrmiss'
  # 设置github邮箱
  git config --global user.email 'heroli520@outlook.com'
  ```

- git status 无法显示中文

  ```
  git config --global core.quotepath false
  ```
  <!--more-->

- git 无法推送新文件到github仓库原因

  1. 可能仓库内已经有东西了，而且使用的推送命令不对
  2. 或是原来的仓库内的东西是通过网页进行上传的， 而不还是通过命令行进行上传的
  3. 强制使用命令  “git commit 文件 +mster” 可能会使原仓库内的东西丢失

- 查看git简单配置

  ```
  git config --list
  ```

- 是git无密向github传输文件

  1. 终端输入ssh-keygen, 之后不断按回车
  2. 会产生一对公密和私密，存储在家目录下的 .ssh 目录中
  3. cat ～/.ssh/id_rsa.pub ，复制输出的公密内容至 github个人主页setting中的ssh中，并保存
  4. 以后clone仓库地址时就可以直接使用
  
- 向仓库更新文件基本命令

```
git status
git add *
git commit -m "内容描述"
git push
```



- 无法读取远程仓库

  删除blog文件夹下的隐藏文件 .deploy_git

```
fatal: 无法读取远程仓库。

请确认您有正确的访问权限并且仓库存在。
```