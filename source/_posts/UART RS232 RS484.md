---
title: UART RS232 RS484
tags:
  - UART
  - RS232
  - RS484
  - TXRX
  - TTL
date: '2023-11-19 03:31:00'
updated: '2024-01-20 18:38:51'
categories: ''
description: ''
abbrlink: f5da1a51
---
# # 介紹
UART：**全雙工、異步、串行傳輸**，收發獨立，UART總線並沒有時鐘線，所以需要提前定義好對應的波特率，傳輸速度較慢，物理上4根線（VCC/TX/RX/GND），雖然速度慢，但是可用於不同主機間的通信，通常用於debug調試非常方便；傳輸時數據位可以5、6、7、8位。
![](/images/20231119022253.png)

# 鮑率(波特率)
常見的鮑率 9600 19200 38400 115200
1 鮑率 即指每秒傳輸1個符號(bit)
9600代表每秒傳輸9600高低電平，如果不匹配會導致亂碼
每一個 bit 的時間就是 1/9600


# 數據幀 
![](/images/20231119022310.png)

## 啟始位元
表示資料封包開始， UART 的發送方會將資料傳輸線從高電壓低電壓變成低電壓

## 資料位元
可以定義為 5bit、6bit、7bit、8bit

## 同位位元
用於檢查資料正確性 同位位元有兩種模式：偶同位(even) 和 奇同位(odd)

## 停止位元
表示資料封包結束， UART 的發送方會將資料傳輸線從低電壓變成高電壓並保持1~2位元時間。

## 現況
現在 UART 很少使用同位位元來偵錯了。常見的「38400,8,N,1」這種設定，意思就是 38400 bps 的鮑率、8 個資料位元，N 則代表沒有同位位元，1 是 1 個停止位元的意思；現在其實已經很少看到後面不是「8,N,1」的設定，因為 同位位元已經幾乎不再使用，而 8 個資料位元和 1 個停止位元也幾乎成爲標準。

## 接法
![](/images/20231119022341.png)
TX接RX RX接TX 

## TTL電平 Transistor-Transistor Logic 
![](/images/20231119022404.png)
2.4v~5v 為高電平 0~0.4 為低電平
![](/images/20231119022417.png)
容易造成干擾 所以通訊距離大約1m內 改進RS232 和 RS485

## (補充) UART串口流控制（Flow control）
### 作用
是管理两个节点之间数据传输速率的过程，以防止出现接收端的数据缓冲区已满，而发送端依然继续发送数据，所导致数据丢失
### 原理
当接收端的数据缓冲区已满，无法处理数据来时，就发出 “不再接收” 的信号，发送端则停止发送，直到发送端收到 “可以继续发送” 的信号再发送数据。
计算机中常用的两种流控制分别是**硬件流控制**（RTS /CTS、DTR /DSR等）和**软件流控制**（XON /XOFF）


# RS232 
RS(Recommended Stabdard) 推薦標準
將TTL電平轉換成232電平 轉換電壓 抗干擾能力變強 還是傳輸原來的數據只是改變電壓 還是全雙工
傳輸距離到15米 2M/s
![](/images/20231119022441.png)
也是只用到 RxD TxD GND
![](/images/20231119022454.png)
將TTL電平轉換成232電平
![](/images/20231119022508.png)
進去TTL 5V 轉換成232電平 -12V (影片有錯)
![](/images/20231119022520.png)
進去TTL 0V 轉換成232電平 +12V (影片有錯)
![](/images/20231119022552.png)
正確的轉換
![](/images/20231119022615.png)
232電平 高電平為3V-15V 低電平為-3V~-15V 抗干擾能力變強

# RS485
將TTL電平轉換成485電平 轉換差分信號 抗干擾能力變強 變成半雙工
傳輸距離到1200米 50M/s
![](/images/20231119022632.png)
![](/images/20231119022701.png)
差分信號 A大於B訊號為0 B大於A訊號為1
![](/images/20231119022714.png)
受干擾時，因為是雙絞線所以基本是是同時干擾，還是差分，干擾能力強

# 結論
![](/images/20231119022730.png)
RS232 RS485 只有改變物理層

# 參考影片
* [5分钟看懂!串口RS232 RS485最本质的区别！](https://www.youtube.com/watch?v=HKQaYN5Odlk)
* [嵌入式系統(I)_洪崇文_單元四 通用非同步收發傳輸UART 與RS-232_Part 1 串列通訊介紹](https://youtu.be/ia2x1oenic0)
* [嵌入式系統(I)_洪崇文_單元四 通用非同步收發傳輸UART 與 RS-232_Part 2 UART通訊與RS232 I](https://youtu.be/xKdV7lcvy1o)
# 參考
* [UART 串列通訊](https://www.block.tw/blog/uart/)
* [UART串口流控制（Flow control）](https://blog.csdn.net/qq_42992084/article/details/104761474)
* [UART串口流控制（Flow control）](https://blog.csdn.net/qq_42992084/article/details/104761474)
* [UART 硬體流量控制 CTS及RTS](https://semhuang.pixnet.net/blog/post/141101276)
* [UART 簡介](https://silverwind1982.pixnet.net/blog/post/361701597-uart)

