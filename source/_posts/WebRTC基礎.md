---
title: WebRTC基礎
tags:
  - WebRTC
date: '2024-12-23 18:03:00'
updated: '2024-12-27 02:33:32'
categories: []
description: ''
abbrlink: 345ae512
---
### 1. **理解 WebRTC 基本概念**

- {% post_link  "NAT（Network Address Translation，網路位址轉換）" %}
- {% post_link  "P2P 打洞（Peer-to-Peer NAT Traversal）" %}：WebRTC 通常基於 P2P 進行通信，因此需要處理 NAT（網絡地址轉換）問題。打洞技術幫助穿越 NAT 和防火牆，使 P2P 連接可能。
 <!-- more -->
 - {% post_link  "STUN（ Session Traversal Utilities for NAT，NAT 穿越會話工具）" %}：STUN 是一個協議，用於幫助 WebRTC 客戶端發現其公共 IP 地址和端口。這對於 P2P 連接至關重要。
- {% post_link  "TURN 協議（Traversal Using Relays around NAT）" %}：當 P2P 連接無法建立時，TURN 服務器用作中繼來轉發數據，保證通信不會中斷。
- {% post_link  "ICE Interactive Connectivity Establishment，交互式连接建立" %}：ICE 是 WebRTC 中的連接建立過程，結合 STUN 和 TURN 來選擇最佳的連接路徑。
- {% post_link  "SDP（Session Description Protocol, 会话描述协议）" %}]
- {% post_link  "信令服務器" %}

### 2. **了解傳輸協議**

- {% post_link  "RTP（Real-time Transport Protocol）" %}：WebRTC 使用 RTP 來傳輸語音和視頻流。它為實時應用提供了一個適合的數據包格式。
- **{% post_link  "RTCP（Real-time Transport Control Protocol）" %}**：RTCP 是用於監控 RTP 流量質量的協議，它會定期回報傳輸情況。

### 3. **學習{% post_link  "信令服務器" %}**

- WebRTC 本身並不定義信令過程。信令服務器用來協調 P2P 連接的建立，通常包括交換候選地址（ICE candidates）、會議協議、媒體協商等。
- {% post_link  "PeerConnection 連線過程" %}
- {% post_link  "ICE連線排序規則" %}
- 常見的信令方式有 WebSocket 或 HTTP 請求。需要學習如何建立一個信令服務器，來實現 WebRTC 客戶端之間的通信協議。
- {% post_link  "搭建webrtc服务器" %}
- {% post_link  "編譯程式碼webrtc" %}

### 4. **實作步驟**

- **建立 WebRTC 連接**：理解如何通過 JavaScript API（如 `RTCPeerConnection`、`RTCDataChannel`）創建 P2P 連接。
- **處理 ICE 候選**：學會如何使用 STUN 和 TURN 服務器來發現和協商連接。
- **處理媒體流**：學會如何處理音頻和視頻流的捕捉、編碼、解碼，以及如何發送和接收這些流。

### 5. **工具與資源**

- **WebRTC 教程**：網上有許多 WebRTC 實作教程和範例，幫助你從簡單的 P2P 連接開始學習。
- **WebRTC 網頁應用範例**：從實際應用入手，例如開發視頻聊天應用，來了解如何將 STUN、TURN、ICE 和 RTP 整合進去。

### 6. {% post_link  "Webrtc視訊流" %}
### 7.{% post_link  "WebRTC 使用了哪些協定" %}

### 8. 多人會議
[视频会议服务器SFU、MCU、Mesh三种类型的区别](https://blog.csdn.net/zhizhuodewo6/article/details/104797267)
# 參考
- [WebRTC 学习指南](https://webrtc.mthli.com/)
- [WebRTC Native 源码导读](https://blog.piasy.com/2017/07/24/WebRTC-Android-Camera-Capture/index.html)
- 