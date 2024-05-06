---
title: Nessus
tags:
  - Nessus
  - docker
categories:
  - 資安
date: '2024-05-06 10:44:00'
updated: '2024-05-06 05:55:42'
description: ''
abbrlink: 613b46cc
---
# 介紹
Nessus是一個弱點掃描工具，業界廣泛使用的漏洞與系統設定評估產品
# 免費版啟用代碼
## 到[官網](https://zh-tw.tenable.com/products/nessus/activation-code)選擇最右邊的免費版(Nessus Essentials)
![](/images/20240506093731.png)
## 填入相關資訊
![](/images/20240506093802.png)
 <!-- more -->
 ## 到Email收信，裡面會有啟用碼
![](/images/20240506093524.png)
# 用Docker容器啟動nessus

## 法一 docker run
``` bash
docker run -d --name "Nessus"  -p 666:8834 -e ACTIVATION_CODE=啟動代碼 -e USERNAME=admin -e  PASSWORD=123456 tenableofficial/nessus
```

## 法二 docker compose
``` bash
services:
  nessus:
    container_name: Nessus
    ports:
      - 666:8834
    environment:
      - ACTIVATION_CODE=啟動代碼
      - USERNAME=admin
      - PASSWORD=123456
    image: tenableofficial/nessus
```

## 啟動後需要3-5分鐘 才可以正常訪問
網址打 https://192.168.56.111:666/#/
![](/images/20240506131509.png)
輸入docker設定的帳號(admin)密碼(123456)
![](/images/20240506131731.png)
登入後的頁面
# 掃描
## 新增掃描
![](/images/20240506131854.png)
## 這裡可以選擇要掃描的範本
![](/images/20240506131950.png)
## 選擇 Basic Network Scan 
![](/images/20240506132153.png)
## Settings 裡面 Name 和 Targets 必填
![](/images/20240506132304.png)
- Name: 隨便填
- Targets: 要掃描機器的IP或域名
## Plugins 裡面就可以看到要掃描的項目
![](/images/20240506132708.png)
## 點擊 Save 保存

## 在My Scans 底下就會有剛剛建立起來的掃描項目
![](/images/20240506132844.png)
## 點進來再點擊 Launch 就可以開始掃描了
![](/images/20240506132911.png)
## 掃描中...
![](/images/20240506133234.png)
## 掃描過程中可以看到有哪些弱點(vulnerability)
![](/images/20240506133326.png)
Basic Network Scan 掃描要花非常久的時間，建議用Advanced Scan可以選擇想要掃的項目

# 問題
## New Scan 無法點擊
需要等待Plugins編譯完或者參考[nessus 启动后 Compiling plugins...卡在这里故障处理](https://www.cnblogs.com/netsa/p/15597136.html)

# 參考
- [系統弱點掃描工具-Tenable Nessus(上)](https://ithelp.ithome.com.tw/articles/10268209)
- [Docker搭建Nessus漏洞扫描器](https://cloud.tencent.com/developer/article/1927126)
- [nessus 启动后 Compiling plugins...卡在这里故障处理](https://www.cnblogs.com/netsa/p/15597136.html)
- [Nessus已激活，New Scan按钮不可点击](https://blog.csdn.net/SwTesting/article/details/133899949)
