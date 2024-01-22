---
title: DMA (Direct Memory Access)
tags:
  - DMA
date: '2023-11-19 03:48:00'
updated: '2024-01-20 18:27:13'
categories: ''
description: ''
abbrlink: e891e3a3
---
# 介紹
![](/images/20231119023846.png)
直接記憶體存取（Direct Memory Access，DMA） 獨立地直接讀寫系統記憶體，而不需中央處理器介入處理，減輕處理器負擔，減輕內核工作量

簡單來說，就是搬資料，不需要用到CPU的指令。換言之，搬資料時，CPU不用知道。要怎麼做可以確保搬的資料不會錯。

1. 要知道原始資料的位置。
2. 要知道目地的位置。
3. 要知道要搬多少資料長度。

# 配置DMA(以SRAM到外部設備)
* 從哪裡複製(要複製的地址) SRAM Address
* 複製的地址(複製目的地的地址) 外部設備 Address
* DMA方向 從 SRAM 到 外部設備
* 傳輸資料大小
* SRAM 地址自增 On or Off 
* 外部設備 地址自增  On or Off 


# 參考影片
* [10行代码，就能让你真正理解DMA！你用的可能很少，但是在单片机中非常重要！](https://www.youtube.com/watch?v=2_X9qD12_pw)

# 參考
* [什麼是DMA ?](https://sharing-icdesign-experience.blogspot.com/2014/05/dma.html)
* [【STM32】 DMA原理，步骤超细详解，一文看懂DMA](https://blog.csdn.net/as480133937/article/details/104927922)