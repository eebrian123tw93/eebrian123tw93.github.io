---
title: git常用指令
tags:
  - git
categories:
  - 軟體工程
date: '2025-06-22 15:42:00'
updated: '2025-07-21 02:07:25'
description: ''
keywords:
  - git
---
# config

```bash
# 解决 git 中文路径显示 unicode 代码的问题
git config --global core.quotepath false
git config --global user.name username
git config --global user.email user_email
git config --local user.name username
git config --local user.email user_email
git config --list
git config --global core.editor "vim"
```
# branch
``` bash
git branch
git branch feature-branch
git checkout feature-branch
git checkout -b feature-branch

# 合併
git merge feature-branch
git merge --no-ff feature-branch
# 刪除
git branch -d feature-branch
git branch -D feature-branch
```
# remote
```bash
git remote -v
git remote add origin remote-url
git remote set-url origin remote-url
git push origin feature-branch
# 从远程仓库拉取并合并分支
git pull origin feature-branch
# 从远程仓库获取更新，但不合并。
git fetch origin
# 基於遠端的 `origin/main` 分支，建立 main 分支
git checkout -b main origin/main
```
# sync remote to local
```bash
# 兩種方式擇一
# 1.重置本地 `main` 分支 強制重置本地分支，清除暫存區和工作區的所有變更。
git fetch origin 
git reset --hard origin/main
# 2.git pull：自動執行 `fetch` 和 `merge`，將遠端變更合併到本地分支。
git pull origin main
```
# log
```bash
git log --graph --all --oneline
```
# 常用
 <!-- more -->
 ``` bash
git add ./
git status
git commit -m "update"
git push origin main
```
# stash
```bash
git stash list
git stash
git stash pop
git stash apply stash@{0}
```
# tag
```bash
git tag
git tag tag-name
git push origin tag-name
#删除
git tag -d tag-name
```
# reset 
```bash
# git reset [選項] [提交ID]
git reset --hard <提交ID>
```
- **`--soft`**：撤銷提交，保留暫存區。
- **`--mixed`**：撤銷提交，清空暫存區，保留工作區。
- **`--hard`**：撤銷提交，清空暫存區與工作區。