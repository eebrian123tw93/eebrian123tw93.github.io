---
title: Nessus Advanced Scan
tags:
  - Nessus
categories:
  - 資安
date: '2024-05-06 15:58:00'
updated: '2025-02-19 08:51:02'
description: ''
abbrlink: 7abecaea
keywords:
  - Nessus
---
# 前言
在使用Nessus最一般的就是用 Basic Network Scan 但這會掃描非常久時間，所以可以使用
Advanced Scan選擇想要掃的項目，但是茫茫大海要如何選擇特定的Plugin呢
# 新增掃描
![](/images/20240506131854.png)
# 這裡可以選擇要掃描的範本
![](/images/20240506131950.png)
# 選擇 Advanced Scan
 <!-- more -->
 ![](/images/20240506145217.png)
# 點擊Plugins
![](/images/20240506145310.png)
# 可以看到左邊是Plugin Famliy 右邊是Plugin
![](/images/20240506145241.png)
# 選擇 Disable All 先取消所有
![](/images/20240506145519.png)
# 假設要找的Plugin是[ICMP Domain Name Request](https://www.tenable.com/plugins/nessus/20887)
![](/images/20240506145655.png)
# 看到Family為<u>General</u>
![](/images/20240506145749.png)
# 在Advanced Scan Plugins 左側選擇 <u>General</u> 右側找到ICMP Domain Name Request 再保存就完成了
![](/images/20240506145832.png)