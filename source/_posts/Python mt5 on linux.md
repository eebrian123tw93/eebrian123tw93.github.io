---
title: Python mt5 on linux
tags:
  - MT5
  - docker
  - linux
  - python
  - rpyc
  - vnc
  - wine
categories:
  - python
date: '2024-01-26 12:15:00'
updated: '2024-01-30 16:27:51'
description: ''
---
# 前言
因為客戶有需求，所以需要用python去控制mt5，但大多伺服器大多都是linux，起一台window的費用比起一台linux貴的很多，所以想要在linux跑mt5，今天介紹一個一個 github 專案[MetaTrader5-Docker-Image](https://github.com/gmag11/MetaTrader5-Docker-Image)

如果是Mac M系列 請參考{% post_link  "Python mt5 on macOS(M系列)" %}
 <!-- more -->
 

# 需求
- python3.9
- intelx86/amd64  
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
- 從瀏覽器訪問的時候是經過3000port 開啟 vnc 連到 mt5
- 從自己python 經過8001port 透過 rpyc 跟 wine 的 python 溝通

# 結論
使用這個專案非常地輕鬆，設定好帳號密碼後只要將docker compose 拉起來，既不用污染環境也非常的快速，真的是非常好用，但是這適用於Intelx86或amd64，如果是Mac M1晶片無法起動成功，參考{% post_link  "Python mt5 on macOS(M系列)" %}．
# 參考
- [MetaTrader5-Docker-Image](https://github.com/gmag11/MetaTrader5-Docker-Image)
- [python_metatrader5](https://www.mql5.com/zh/docs/python_metatrader5)
- [rpyc](https://rpyc.readthedocs.io/en/latest/)
- [mt5linux](https://pypi.org/project/mt5linux/)








