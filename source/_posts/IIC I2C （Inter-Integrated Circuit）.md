---
title: IIC I2C （Inter-Integrated Circuit）
tags:
  - IIC
  - I2C
date: '2023-11-19 02:38:00'
updated: '2024-01-20 18:23:10'
categories: ''
description: ''
abbrlink: d71a458d
---
# 介紹
![](/images/20231119013103.png)
I2C(IIC)唸做I-square-C 属于两线式串行总线，由飞利浦公司开发用于微控制器(MCU)和外围设备(从设备)进行通信的一种总线，属于一主多从(一个主设备(Master)，多个从设备(Slave))的总线结构，总线上的每个设备都有一个特定的设备地址，以区分同一I2C总线上的其他设备。

半雙工、同步、串行傳輸，物理上只有兩根線（SDA/SCL）；傳輸數據必須8位8位的傳輸

物理I2C接口有**两根双向线**， **串行时钟线（SCL）和串行数据线（SDA）** 组成，可用于发送和接收数据，但是通信都是由主设备发起，从设备被动响应，实现数据的传输。

# I2C主设备与从设备的一般通信过程
## 起始位 Start condition 停止位 Stop condition
![](/images/20231119013140.png)
* Start condition: SCL= high + SDA falling (SDA由高到低跳變)
* Stop condition: SCL=high + SDA raising (SDA由低到高跳變)

## 邏輯數據
![](/images/20231119013159.png)
SCL為High採集SDA數據
![](/images/20231119013218.png)
表示1010000  

## 一. 主设备给从设备发送/写入数据：
![](/images/20231119013233.png)
1. 主设备发送起始（START）信号(1 bit)
2. 主设备发送设备地址到从设备(7/8bit) + 寫(1 bit)
3. 等待从设备响应(ACK)(1 bit)
4. 主设备发送数据到从设备，一般发送的每个字节数(8bit)据后会跟着等待接收来自从设备的响应(ACK)(1 bit)
5. 数据发送完毕，主设备发送停止(STOP)信号终止传输(1 bit)

## 二. 主设备从从设备接收/读取数据
![](/images/20231119013244.png)
1. 设备发送起始（START）信号(1 bit)
2. 主设备发送设备地址到从设备(7/8bit) + 讀(1 bit)
3. 等待从设备响应(ACK)(1 bit)
4. 主设备接收来自从设备的数据，一般接收的每个字节(8bit)数据后会跟着向从设备发送一个响应(ACK)(1 bit)
5. 一般接收到最后一个数据后会发送一个无效响应(NACK)，然后主设备发送停止(STOP)信号终止传输(1 bit)

## 三. 讀寫數據幀
![](/images/20231119013257.png)

# 參考影片
* [4分钟看懂！I2C通讯协议 最简单的总线通讯！](https://www.youtube.com/watch?v=u62_Rjd5oMY)
* [I²C通訊格式介紹](https://www.youtube.com/watch?v=oPrfi_HCtjY)
* [11 IIC总线原理轻松明白](https://www.youtube.com/watch?v=FamlCPP2J4o)

# 參考
* [IIC通信协议，搞懂这篇就够了](https://zhuanlan.zhihu.com/p/503219395)
* [I2C-協定用法原理簡介-晶片溝通的橋樑](https://www.strongpilab.com/i2c-introduction/)
* [[技術筆記] I2C通訊協定操作](https://medium.com/weiting-tw/%E6%8A%80%E8%A1%93%E7%AD%86%E8%A8%98-i2c%E9%80%9A%E8%A8%8A%E5%8D%94%E5%AE%9A%E6%93%8D%E4%BD%9C-36d4dad0c9b3)
* [《Linux驱动：I2C驱动看这一篇就够了》](https://zhuanlan.zhihu.com/p/575318033)