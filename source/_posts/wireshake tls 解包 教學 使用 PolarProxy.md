---
title: wireshake tls 解包 教學 使用 PolarProxy
tags:
  - tls
date: '2026-01-26 17:01:00'
updated: '2026-02-05 05:46:52'
categories: []
description: ''
keywords:
  - tls
abbrlink: '40217706'
Aliases:
---
## 一、原理說明（為什麼 PolarProxy 能解）
### 核心概念：**主動式 MITM TLS Proxy**
PolarProxy 並不是像 `SSLKEYLOGFILE` 那樣「被動取得 session key」，而是：
1. **客戶端 ↔ PolarProxy ↔ 伺服器**
2. PolarProxy：
    - 與「伺服器」建立一條真正的 TLS
    - 與「客戶端」建立另一條 TLS
 <!-- more -->
 3. 因為 PolarProxy **本身就是 TLS 端點**
    - 它「知道所有明文資料」
    - 可以把「已解密後的流量」輸出成 pcap
4. Wireshark 只看到 **明文 TCP / RTSP / HTTP**，不需要任何金鑰

👉 **關鍵差異**

|方法|原理|限制|
|---|---|---|
|SSLKEYLOGFILE|被動解密|需 client 支援|
|PolarProxy|主動 MITM|需信任 CA|

---

## 二、適用範圍（非常重要）

### ✅ 適合
- RTSPS（RTSP over TLS）
- HTTPS
- MQTT over TLS
- **第三方 NVR / Camera（無法改程式）**
- openRTSP、VLC、客戶端黑盒
### ❌ 不適合 / 無法
- **Certificate Pinning**
- 硬體內建不可替換 CA
- Mutual TLS（client cert 驗證）
- TLS over UDP（DTLS）

---

## 三、整體架構圖（文字版）

```bash
openRTSP / NVR
      │
      │  TLS (假憑證)
      ▼
  PolarProxy
      │
      │  TLS (真憑證)
      ▼
   真實 RTSP Server
```

PolarProxy 同時：
- 對 client 偽裝 server
- 對 server 偽裝 client
---

## 四、實作步驟（一步一步）

### Step 1：下載執行檔
- [官網](https://www.netresec.com/?page=PolarProxy)
- tar -zxvf PolarProxy_1-0-2_linux-x64.tar.gz
---
### Step 2：啟動 PolarProxy 讓「客戶端信任 PolarProxy CA」

```bash
# 啟動服務 
# 監聽 443 port 
# 流量轉到 10.81.81.4
# tcp dump 到 decrypted.pcap
# cert port 10080 訪問 http://localhost:10080/polarproxy.cer
# -d debug
sudo ./PolarProxy -p 0.0.0.0,443,443 --connect 10.81.81.4  -w decrypted.pcap --certhttp 10080 -d  --nosni Topview --leafcert sign

# linux
wget http://localhost:10080/polarproxy.cer 
sudo mv polarproxy.cer  /usr/local/share/ca-certificates/polarproxy.crt 
sudo update-ca-certificates
```

---
### Step 3：修改 client 連線目標
- 打開瀏覽器訪問 https://localhost
---
### Step 4：用 Wireshark 開啟解密後封包
``` bash
wireshark decrypted.pcap
```
![](/images/20260126173632.png)
![](/images/20260126173728.png)
❗**Wireshark 不需要設定 TLS Key**

---
## 六、常見問題整理

### Q1：為何 PolarProxy 沒反應？
- client 沒連到 proxy
- port 綁錯 IP
- 防火牆
- CA 未被信任
### Q2：看到 TLS Alert / Handshake Failure？
- client 不信任 CA
- TLS version / cipher 不相容
- Certificate Pinning
### Q3：可以解 UDP RTP 嗎？
- ❌ 不行  
- PolarProxy 只處理 TLS/TCP

---
## 七、什麼情境「一定要用 PolarProxy」
- 第三方 NVR
- 無法改 client
- SSLKEYLOGFILE 不支援
- 想直接看到 RTSP / HTTP 明文