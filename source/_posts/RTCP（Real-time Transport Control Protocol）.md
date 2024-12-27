---
title: RTCP（Real-time Transport Control Protocol）
categories: []
date: '2024-12-24 11:12:00'
updated: '2024-12-27 02:31:02'
description: ''
abbrlink: ad213933
tags:
  - WebRTC
---
### RTCP (RTP Control Protocol) 介紹

RTCP（RTP Control Protocol）是與 RTP（Real-Time Transport Protocol）一起使用的協議，用於管理和監控多媒體數據的傳輸。它主要負責提供反饋機制，讓參與者能夠監控網絡性能，並協調實時應用程序的行為。

#### **主要功能**

1. **提供統計資訊**  
    RTCP 為參與者提供傳輸統計資訊（如丟包率、延遲和抖動等），幫助檢測網絡問題並評估性能。
 <!-- more -->
     
2. **同步多媒體流**  
    RTCP 可以幫助同步來自不同來源的多媒體流，例如音頻和視頻流，確保它們在接收端正確對齊。
    
3. **標識參與者**  
    RTCP 包含 CNAME（Canonical Name），用於標識會話中的參與者，方便處理多個流之間的對應關係。
    
4. **帶寬控制**  
    RTCP 使用一定比例的帶寬（通常是 RTP 數據流的 5%），以確保其不會過度佔用網絡資源。
    

#### **RTCP 包結構**

RTCP 使用多種類型的報告包來實現其功能，主要包括：

1. **SR（Sender Report）**
    
    - 由主動發送數據的參與者生成。
    - 包括發送的數據包數量、字節數量和時間戳等資訊。
2. **RR（Receiver Report）**
    
    - 由接收數據的參與者生成。
    - 提供接收端的反饋信息，例如丟包率、抖動、延遲等。
3. **SDES（Source Description Items）**
    
    - 用於標識參與者的元數據，如 CNAME（Canonical Name）、姓名和電子郵件等。
4. **BYE（Goodbye）**
    
    - 用於通知其他參與者某個來源即將離開會話。
5. **APP（Application-Specific Messages）**
    
    - 提供應用程序自定義的控制信息。

#### **RTCP 的工作流程**

1. RTP 負責傳輸實時數據流，如音頻或視頻數據。
2. RTCP 定期發送控制消息，讓參與者了解網絡狀況和其他流的情況。
3. 根據 RTCP 的統計和反饋，應用可以調整編碼參數、優化網絡使用或重新同步流。

#### **RTCP 的應用場景**

- **WebRTC**：在 WebRTC 中，RTCP 為實時音視頻會話提供網絡性能監控和同步支持。
- **IPTV**：在互聯網視頻廣播中，RTCP 協助監控網絡質量，保證用戶體驗。
- **視頻會議系統**：RTCP 用於多點視頻會議中的性能反饋和同步。

#### **RTCP 與 RTP 的區別**

|特性|RTP|RTCP|
|---|---|---|
|功能|傳輸實時多媒體數據|管理和監控 RTP 傳輸|
|包類型|音視頻數據包|控制和反饋報告包|
|帶寬使用|主要佔用網絡帶寬|佔用 RTP 帶寬的 5%|
|例子|音頻數據、視頻數據|丟包率、延遲、同步信息|