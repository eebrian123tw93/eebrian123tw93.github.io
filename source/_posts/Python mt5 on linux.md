---
title: Python mt5 on linux
tags:
  - MT5
  - docker
  - linux
  - python
  - rpyc
  - vnc
categories:
  - python
date: '2024-01-26 12:15:00'
updated: '2024-01-29 14:50:11'
description: ''
---
# 前言
因為客戶有需求，所以在需要用python去控制mt5，但大多伺服器大多都是linux，起一台window的費用比起一台linux貴的很多，所以想要在linux跑mt5，今天介紹一個一個 github 專案[MetaTrader5-Docker-Image](https://github.com/gmag11/MetaTrader5-Docker-Image)

# 需求
- python3.9
 <!-- more -->
 - 64-bit x64 
- docker 

# 開始吧
## 先把專案 clone下來
``` bash
git clone https://github.com/gmag11/MetaTrader5-Docker-Image.git
```
## 進入到資料夾內
``` bash
cd MetaTrader5-Docker-Image
```
## 設定環境 可以更改帳號密碼
``` bash
cp .env.example .env
#UID=1000
#GID=1000
#CUSTOM_USER=username
#PASSWORD=securepassword
```
## docker compose 啟動
``` bash
docker compose up -d
```
## 腳本會自動安裝下面的套件 (根據設備能力不同時間也有所不同第一次啟動會花比較久時間 )
- wine (用來模擬window的環境)
- mone (.NET框架)
- python (在wine裡面安裝python)
- mt5 (在wine裡面安裝mt5)
- python packages (所需套件)
## 安裝完後 在瀏覽器輸入[localhost:3000](localhost:3000)
![](/images/20240129104101.png)
## 在自己的python上面安裝 mt5linux
``` bash
python -m pip install mt5linux==0.1.9
```
## 測試連線
``` python
from mt5linux import MetaTrader5
mt5 = MetaTrader5(host='localhost',port=8001)
mt5.initialize()
print(mt5.version())
```
# 附上流程
![](/images/20240129102552.png)
- 從瀏覽器訪穩問的時候是經過3000port 開啟 vnc 連到 mt5
- 從自己python 經過8001port 透過 rpyc 跟 wine 的 python 溝通

 # 參考
- [MetaTrader5-Docker-Image](https://github.com/gmag11/MetaTrader5-Docker-Image)
- [python_metatrader5](https://www.mql5.com/zh/docs/python_metatrader5)








