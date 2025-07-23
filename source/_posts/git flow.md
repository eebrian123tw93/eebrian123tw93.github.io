---
title: git flow
tags:
  - git
categories:
  - 軟體工程
date: '2025-07-18 12:34:00'
updated: '2025-07-23 01:12:47'
description: ''
keywords:
  - git
---
## 🔧 什麼是 Git Flow？

Git Flow 是一種 Git 的分支管理模型，用來組織開發流程。它主要有以下幾種分支類型：

|分支名稱|用途|
|---|---|
|`master`|穩定的正式發佈版本|
|`develop`|開發中的主分支，準備下一個版本|
|`feature/*`|開發新功能的分支|
|`release/*`|發佈前準備的分支|
|`hotfix/*`|緊急修復生產環境的分支|
 <!-- more -->
 
```bash
#macosx
brew install git-flow-avh
#linux
sudo apt-get install git-flow

git flow init

git flow feature start my-feature
git flow feature finish my-feature

git flow release start 1.0.0
git flow release finish 1.0.0
git tag
git push origin main develop --tags

git flow hotfix start hotfix-1.0.1
git flow hotfix finish hotfix-1.0.1

#git-flow-complete
git clone https://github.com/bobthecow/git-flow-completion ~/.oh-my-zsh/custom/plugins/git-flow-completion
plugins=(git git-flow-completion) plugin添加git-flow-completion
```

## 💡 補充：Git Flow 中 MR 的用法建議

| 分支類型        | 發 MR 對象                | 說明               |
| ----------- | ---------------------- | ---------------- |
| `feature/*` | 到 `develop`            | 開發完成功能，請求合併到開發主幹 |
| `release/*` | 到 `master` + `develop` | 準備發佈，最終檢查與合併     |
| `hotfix/*`  | 到 `master` + `develop` | 緊急修復，需同步回開發主幹    |
