---
title: PeerConnection 連線過程
date: '2024-12-24 14:30:00'
updated: '2024-12-27 02:31:02'
categories: []
description: ''
abbrlink: 40453b0a
tags:
  - WebRTC
---
# 上圖
![](/images/20241224132201.png)
## 備註
1. 上圖不完全是API的呼叫流程，讀者仍需要參考WebRTC的文件或原始碼註解。
2. 先進入房間的使用者是發起方（ClientA），後進入房間的使用者是參與者（ClientB）。伺服器可以將它們與ICE伺服器位址一起傳回給參與者。
3. add streams 不是連接流程中的關鍵步驟，也可以在ICE流程之後再執行。
 <!-- more -->
 4. 通話雙方均與指定的ICE伺服器連線成功後，即可開始相互推流。
5. 在多人會議服務端架構中，一般由SFU伺服器同時擔當ICE伺服器的角色。

# 參考
- [PeerConnection 连接流程](https://webrtc.mthli.com/connection/peer-connection/)
- [WebRTC >RTCPeerConnection-建立连接的全过程](https://juejin.cn/post/6844904079102050311)
- [WebRTC系列-WebRTC基础(四)连接建立时序图及运行机制](https://blog.csdn.net/lym594887256/article/details/124081420)
- [WebRTC PeerConnection 建立连接过程介绍](https://blog.csdn.net/aggresss/article/details/106832965)
- 