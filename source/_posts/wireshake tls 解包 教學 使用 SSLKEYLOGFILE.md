---
title: wireshake tls 解包 教學 使用 SSLKEYLOGFILE
tags:
  - tls
date: '2026-01-26 17:01:00'
updated: '2026-02-05 05:46:52'
categories: []
description: ''
keywords:
  - tls
abbrlink: a136f8fa
Aliases:
---
## 一、原理

TLS/SSL 是一種端到端加密協議，資料在網路上傳輸時都是加密的。Wireshark 無法直接解讀加密的 TLS 封包內容（Application Data），除非你提供 **解密所需的對稱 session key**。

`SSLKEYLOGFILE` 是一個環境變數，它允許某些 TLS/SSL 客戶端（如 Chrome、Firefox、OpenSSL 或某些 Python 套件）在啟動時把 **每個 TLS 連線的 session key（pre-master / master secret）** 寫入一個檔案。Wireshark 讀取這個檔案後，就能用對稱加密金鑰解密封包，還原明文資料。
 <!-- more -->
 
流程概念如下：
1. 瀏覽器/程式端啟用 `SSLKEYLOGFILE`，每次 TLS 握手成功後會將對應的 session key 寫入檔案。
2. Wireshark 讀取這個 key log 檔案，匹配封包中的 `ClientHello`、`ServerHello` 等訊息。
3. Wireshark 使用這些 session key 解密封包，顯示原始明文資料（HTTP、WebSocket、gRPC 等）。

---

## 二、適用範圍

|項目|說明|
|---|---|
|支援協議|TLS 1.0 / 1.1 / 1.2 / 1.3（部分舊版或實驗性 TLS 1.3 cipher 可能無法解）|
|支援軟體|Firefox、Chrome、Edge、OpenSSL-based 程式（curl, Python `requests` with OpenSSL）|
|不適用情況|非 OpenSSL/TLS 客戶端產生的 key，或者使用硬體加密（如 HSM、某些硬體 VPN）|
|注意|TLS 1.3 中，Wireshark 只能解密使用 key log 的加密資料，**early data / 0-RTT** 可能無法解密。|

---

## 三、一步步教學

假設你要抓瀏覽器的 HTTPS 流量：
### 步驟 1：設定 SSLKEYLOGFILE 環境變數

- Windows（PowerShell）：
```bash
#windows (PowerShell)
$env:SSLKEYLOGFILE="C:\temp\sslkeys.log"
# Linux or MacOS
`export SSLKEYLOGFILE=/tmp/sslkeys.log`
```
> 注意：路徑需確保程式有寫入權限。

---
### 步驟 2：在 Wireshark 設定 TLS key log
1. 在 Wireshark 選單：**Edit → Preferences → Protocols → TLS**
2. 找到 **(Pre)-Master-Secret log filename**，填入 `sslkeys.log` 的路徑。
3. 確定勾選 **“Attempt to decrypt TLS”**。
 ![](/images/20260126163156.png)
 ![](/images/20260126163334.png)
---
### 步驟 3：啟動 TLS 客戶端
- 例如 Chrome 或 Firefox：
```bash
google-chrome
```
- 任何新的 HTTPS 連線都會把 session key 寫入 `sslkeys.log`。
---

### 步驟 4：抓取封包
1. 打開 Wireshark，選擇你的網路介面抓包。
2. 針對你要解密的 TLS 流量開始抓包（或用已存在的 pcap 檔）。
### 步驟 5：解密封包
1. 重新載入封包或重新抓包。
2. Wireshark 會自動使用 key log 解密 TLS 封包。
3. 可以看到 `Application Data` 被解析成明文，例如 HTTP/2、WebSocket。
---
### 步驟 6：驗證
- 在 Wireshark 中，找到某個 TLS 封包 → 展開 `Secure Sockets Layer` → `Decrypted TLS Application Data`。
- 若顯示明文 HTTP/JSON/其他資料，表示解密成功。
![](/images/20260126164348.png)
![](/images/20260126164708.png)