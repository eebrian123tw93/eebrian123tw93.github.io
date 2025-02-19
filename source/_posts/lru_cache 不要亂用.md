---
title: lru_cache 不要亂用
tags:
  - cache
  - memory_leak
  - python
categories:
  - 小日常
date: '2025-02-19 16:15:00'
updated: '2025-02-19 08:51:03'
description: ''
abbrlink: 6a4c0b66
keywords:
  - cache
  - memory_leak
  - python
---
 今天發現跑的一支python程式裡面，我的記憶體不斷上漲，
 使用好幾套監測套件
 - tracemalloc
 - objgraph
 找來找去都找不到哪裡記憶體增加
 已知大概範圍，但不管如何 `del`，`gc` 也呼叫了記憶體還是蹭蹭蹭往上漲，
 我以為是我這邊的問題，因為我大部分都是互叫別人的套件，徹底檢查我的程式碼也沒有看到哪裡有洩漏可能，我這支程式是去Influxdb拿資料，還一步步的去追到套件的程式碼也沒看出哪裡有問題，甚至印出所有物件，看是否有循環引用的問題，分析半天也沒看出來
 <!-- more -->
 
最後去查套件的每一行code，無意間在[query.py](https://github.com/Javidjms/influxable/blob/develop/influxable/db/query.py)瞟到 cache ，@lru_cache(maxsize=None)， 查了一下[官方文檔](https://docs.python.org/zh-tw/3/library/functools.html#functools.lru_cache)這樣寫記憶體無線增長阿!
終於找出真正原因

但這個寫在裝飾器上沒辦法帶參數些改，最後好險可以透過繼承的手法覆蓋原本的方法，要不然我真的要換套件了，顆顆!!

我開了一個issus [Memory may grow indefinitely](https://github.com/Javidjms/influxable/issues/31)再看開發者如何回復
# 參考
+ [Python｜functools｜lru_cache](https://medium.com/@jepersyne/python-functools-lru-cache-d5cb632df710)
+ [官方文檔](https://docs.python.org/zh-tw/3/library/functools.html#functools.lru_cache)