---
title: WebRTC 使用了哪些協定
tags:
  - WebRTC
date: '2024-12-26 17:02:00'
updated: '2025-02-19 08:51:03'
categories: []
description: ''
abbrlink: ee154cd8
keywords:
  - WebRTC
---
## WebRTC 使用了哪些協定

我們最熟悉的網路傳輸協定TCP與UDP，作為傳輸層，兩者都有各自的優缺點，  
而在WebRTC中，需要能夠實現即時串流應用，在影音傳輸方面上，第一個想到可以搭配的協定，  
肯定是UDP。

然而UDP雖然適合影音傳輸上的應用，但在WebRTC中還需要能夠流量控制或處理擁塞等功能，這些是UDP所沒有的功能，  
為此在UDP上附加了許多輔助協定，如下圖：

 <!-- more -->
 ![](/images/20241227094202.png)

圖片擷取自[High Performance Browser Networking book](https://hpbn.co/webrtc/)

上圖展示了整個WebRTC所運用的協定架構，左邊(TCP)為Signal應用，右邊(UDP)為串流應用，  
協定堆的搭配使用，補足彼此的不足，而本章主要專注在串流應用(右邊)上的協定上，為WebRTC提供了哪些功能。

### 加密: DTLS

DTLS 與 TLS 都是為網路傳輸提供數據安全與完整性的協定，  
TLS在TCP協定上，而DTLS在UDP協定上。

雖然DTLS是建構在TLS上，但兩者的加密驗證方式是不同的，有興趣可以深入研究看看～

DTLS主要用途：會在通信雙方連接時產生密鑰，對傳輸數據進行加密再交由UDP。

### 影音數據傳輸: SRTP

SRTP(Secure Real-Time Transport Protocol): 安全即時傳輸協定。

是基於[RTP](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Intro_to_RTP)(Real-Time Transport Protocol)上的擴展，主要用途還是圍繞在核心的RTC上。

RTP的主要用途: 針對定義網路影音數據傳輸的標準格式，因此常用於多媒體應用上。

但在多媒體應用上，RTP並不能保證服務質量[QoS(quality-of-service)](https://en.wikipedia.org/wiki/Quality_of_service)，也就不能有效控制串流的品質，例如：依照頻寬調整影音數據的壓縮或是限制傳輸流量的大小等，因此常會搭配[RTCP](https://zh.wikipedia.org/wiki/%E5%AE%9E%E6%97%B6%E4%BC%A0%E8%BE%93%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE)(Real-time Transport Control Protocol)。

而SRTP從命名上就能知道，是在RTP上加上安全性(secure)加密機制，同樣與其搭檔的RTCP也有一個伴生協定SRTCP。

### 資料傳輸: SCTP

SCTP（Stream Control Transmission Protocol）：串流控制傳輸協定。

它是屬於較新訂定的協定，而主要優點是，它提供了像 TCP 一樣是可靠、有序的資料傳輸，同時也將UDP的高效做結合，也因此漸漸有許多應用層漸漸在 SCTP 的特性基礎上進行構建。

WebRTC是應用在`RTCDataChannel`中，藉由它的特性，例如多流或是可配置的無順序性配送等，並且搭配 DTLS 來加強安全性。

詳細可以參考[RFC 3286](http://www.faqs.org/rfcs/rfc3286.html)中的說明。

## 總結

本章暸解到:

- WebRTC protocol stack (所用的協定堆)
- 各個層級的協定互相搭配的用意
- DTLS,RTP/RTCP,SCTP 的功用

這邊主要明白其中的基礎概念，並沒有做深入的探討～

# 參考:
- [WebRTC 使用了哪些協定](https://ithelp.ithome.com.tw/articles/10249334)