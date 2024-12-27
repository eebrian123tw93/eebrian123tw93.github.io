---
title: ICE Interactive Connectivity Establishment，交互式连接建立
tags:
  - WebRTC
categories:
  - web
date: '2024-12-23 16:30:00'
updated: '2024-12-27 02:31:02'
description: ''
abbrlink: 58bf8773
---
**ICE（Interactive Connectivity Establishment，交互式连接建立）** 是一種協議框架，旨在幫助網絡應用程式在 NAT（網絡地址轉換）和防火牆環境下進行點對點（P2P）連接。ICE 綜合了多種 NAT 穿越技術，包括 STUN 和 TURN，用於建立可靠的通信通道。
![](/images/20241223152120.png)
**STUN/TURN+NAT穿越流=ICE流程**
 <!-- more -->
 ICE 是框架，STUN 和 TURN 是技術支持。


ICE用于NAT穿透，确定媒体流传输地址。这是音视频互通的第一步。包括如下四个流程
1. 收集候选地址
2. 产生offer/answer sdp，将候选地址携带在sdp中(通过Signal Server单独传输)
3. 通过sdp中携带的候选者地址进行连通性测试
三个步骤是串行的，完成上一步骤才进行下一步

#### 收集候选地址(Candiate)

互通的两端所在的网络拓扑可能包括如下几种：

1. 在内网中(同一网段，或是跨网段)，通过内网地址互通
2. 在NAT之后，通过本机NAT映射后的外网的IP和Port互通
3. 需要通过relay服务器来互通

所以收集地址时，分别对应三种类型的Candiate地址

- host 类型,即本机内网的 IP 和端口
- srflx 类型,即本机 NAT 映射后的外网的 IP 和端口
- relay 类型,即中继服务器的 IP 和端口

3种类型的Candiate优先级依次是host,srflx,relay。按这种优先级进行连通测试。
#### ICE流程
![](/images/20241223152415.png)
1.收集candidates
- 获取本机host address
- 从STUN服务器获取srvflx address
- 从TURN服务器获取relay address
2.交换candidates 发起offer的一方称为主叫方，Web A为主叫方。在A获取到candidates后，会在offer携带。被叫方Web B收到offer，开始收集candiates，收集完后，在answer消息中携带。(sdp中的`a=candidate`属性行携带本端的candidates地址)
3.按优先级进行连通检查

### **核心概念**
1. **目標：**
    - 促進點對點通信，即使通信雙方在不同的網絡環境（如 NAT 背後）中。
2. **主要任務：**
    - 探測雙方的網絡可達性。
    - 尋找最佳的網絡路徑。
    - 處理候選連接，最終確定最有效的通信方式。

---
### **ICE 的組成部分**
1. **候選（Candidates）：**
    - ICE 嘗試多種方式來建立通信，並將每種方式表示為一個「候選地址」。
    - **候選類型：**
        - **主機候選（Host Candidate）：** 本地網絡接口的地址。
        - **反射候選（Server Reflexive Candidate）：** 通過 STUN 服務器獲得的公共 IP 和端口。
        - **中繼候選（Relay Candidate）：** 通過 TURN 服務器中繼流量的地址。
2. **STUN（Session Traversal Utilities for NAT）：**
    - 幫助獲取公共 IP 和端口。
    - 用於檢測候選地址的可達性。
3. **TURN（Traversal Using Relays around NAT）：**
    - 提供中繼服務器，以確保在嚴格 NAT 或防火牆環境中也能建立連接。
4. **優先級排序：**
    - 每個候選地址根據特定算法分配優先級，確保選擇性能最佳的連接。

---

### **ICE 的工作流程**
1. **候選地址收集（Gathering Candidates）：**
    - 雙方應用程序通過主機地址、STUN、TURN 等方式獲取一組候選地址。
2. **候選地址交換（Exchanging Candidates）：**
    - 雙方通過信令（如 SIP 或 WebRTC 的 SDP）交換候選地址。
3. **連接檢測（Connectivity Checks）：**
    - 使用 STUN 請求測試候選地址的可達性。
4. **選擇最佳連接（Candidate Pair Selection）：**
    - 基於檢測結果和優先級，選擇一對候選地址作為最終通信通道。
5. **連接建立（Establish Connection）：**
    - 通過選定的候選對進行數據交換。

---

### **應用場景**

1. **WebRTC：**
    
    - ICE 是 WebRTC 實現點對點音視頻通信的基礎。
2. **VoIP：**
    
    - ICE 支持 SIP 協議進行語音通信。
3. **點對點文件傳輸：**
    
    - 在 NAT 和防火牆環境下提供穩定的文件共享。

---

### **ICE 與 STUN、TURN 的關係**

- **ICE 是框架，STUN 和 TURN 是技術支持。**
    - STUN 和 TURN 負責獲取候選地址和中繼流量。
    - ICE 組織候選地址的收集、交換和選擇，並進行連接檢測。
  
# 參考
- [浅述webrtc中的ICE流程](https://juejin.cn/post/7131010868115931143)
- 