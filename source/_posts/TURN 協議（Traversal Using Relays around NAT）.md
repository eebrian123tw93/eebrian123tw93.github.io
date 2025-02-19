---
title: TURN 協議（Traversal Using Relays around NAT）
date: '2024-12-23 12:09:00'
updated: '2025-02-19 08:51:03'
categories: []
description: ''
abbrlink: 46d03228
tags:
  - WebRTC
keywords:
  - WebRTC
---
**TURN（Traversal Using Relays around NAT）** 是一種用於穿越 NAT（Network Address Translation）和防火牆的網絡協議，通常在實時通信應用中使用，例如視頻通話、即時消息和其他需要點對點連接的應用。通過**中繼服務器轉發流量**來解決這些問題

---

### **核心概念**

1. **背景：**
    
    - NAT 和防火牆可能會阻止直接的點對點通信。
 <!-- more -->
     - {% post_link  "STUN（ Session Traversal Utilities for NAT，NAT 穿越會話工具）" %}有助於獲取公共 IP 和端口，但在某些情況下無法建立直接連接，例如雙重 NAT 或嚴格防火牆環境。
2. **解決方案：**
    
    - TURN 通過**中繼服務器轉發流量**來解決這些問題，從而在無法建立直接連接時提供通信路徑。

---

### **工作原理**

1. **客戶端和 TURN 服務器交互：**
    
    - 客戶端向 TURN 服務器請求一個中繼地址（Relay Address）。
    - TURN 服務器分配一個臨時的公共 IP 和端口，充當中繼節點。
2. **中繼通信：**
    
    - 之後的流量通過 TURN 服務器轉發。通信的一方將數據發送到 TURN 服務器，然後由 TURN 服務器轉發給另一方。
3. **協議依賴：**
    
    - TURN 使用 UDP 為主，但也支持 TCP 和 TLS（加密通道）。
    - TURN 協議由 RFC 5766 定義。

---

### **應用場景**

1. **WebRTC（Web Real-Time Communication）：**
    
    - TURN 是 WebRTC 中必須的技術之一，與 STUN 配合使用，確保音視頻通信的可靠性。
2. **高防火牆場景：**
    
    - 在企業網絡或高度限制的環境中，TURN 是唯一的解決方案。
3. **NAT 穿越：**
    
    - 當 STUN 無法建立直接通信時，TURN 是備選方案。

---

### **TURN 的限制**

1. **性能開銷：**
    
    - TURN 流量經過服務器中繼，會帶來額外的延遲和服務器負載。
2. **帶寬需求：**
    
    - TURN 服務器需要足夠的帶寬來處理大量的數據流量。
3. **部署成本：**
    
    - 運行和維護 TURN 服務器需要額外的基礎設施資源。

---

### **TURN 和 STUN 的區別**

| 特性       | STUN          | TURN        |
| -------- | ------------- | ----------- |
| **用途**   | 發現公共 IP 和端口   | 提供中繼服務器轉發流量 |
| **性能開銷** | 較低            | 較高          |
| **直連能力** | 適用於大多數 NAT 環境 | 適用於無法直連的情況  |

如果您想部署 TURN 服務器，常見的開源軟件包括 [Coturn](https://github.com/coturn/coturn)。