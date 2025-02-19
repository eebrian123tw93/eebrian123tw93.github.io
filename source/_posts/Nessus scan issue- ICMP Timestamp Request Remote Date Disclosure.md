---
title: Nessus scan issue- ICMP Timestamp Request Remote Date Disclosure
tags:
  - Nessus
  - chatgpt
categories:
  - web
date: '2025-02-18 12:38:00'
updated: '2025-02-19 08:51:03'
description: ''
abbrlink: 8d2c5c93
keywords:
  - Nessus
  - chatgpt
---
**ICMP Timestamp Request Remote Date Disclosure** 是一種資訊洩露漏洞，主要與 ICMP（Internet Control Message Protocol）中的 **Timestamp Request（類型 13）** 和 **Timestamp Reply（類型 14）** 相關。
### **可能的問題與風險**
1. **系統時間洩露（時間同步攻擊）**
 <!-- more -->
     - 攻擊者可以透過 ICMP Timestamp Request 回應的時間資訊，獲取目標系統的當前時間。
    - 這可能有助於進行「時序攻擊」（Timing Attack），例如分析時間同步問題、推測隨機數生成器的狀態等。
2. **協助進行網路探測（Network Reconnaissance）**
    - 攻擊者可以掃描大量 IP 來確定哪些系統回應 ICMP Timestamp Request，進一步縮小攻擊範圍。
    - 此外，透過不同設備的回應時間，可以推測目標設備的地理位置或時區。
3. **協助執行 NTP 攻擊（Network Time Protocol Attack）**
    
    - 若攻擊者發現目標設備的時間與標準時間不同，可能嘗試對 NTP 服務進行攻擊，例如 **NTP 反射攻擊（NTP Amplification Attack）** 或 **時間重置攻擊（Time Shift Attack）**，導致系統錯誤。
4. **幫助識別作業系統與設備類型**
    - 不同作業系統（Windows、Linux、BSD 等）對 ICMP Timestamp Request 的回應方式可能不同，因此攻擊者可以利用這些資訊進行指紋識別（Fingerprinting）。
    - 例如，某些設備的時鐘可能是從開機時間開始計算的，而非標準 UTC 時間。
5. **協助分佈式攻擊（Distributed Attack）**
    - 在分散式攻擊中，攻擊者可以利用這些時間資訊來同步惡意行為，例如 DDoS 攻擊中的「時間協調」（Coordinated Timing Attack）。

---
### **如何修復與緩解**
1. **封鎖 ICMP Timestamp Request（類型 13）**
- 在防火牆（如 `iptables` 或 `pf`）或網路設備上封鎖 ICMP 類型 13 封包：
```bash
iptables -A INPUT -p icmp --icmp-type timestamp-request -j DROP
```
- 若使用 `pf`（如 OpenBSD / FreeBSD）：
```bash
block in quick proto icmp icmp-type timestamp
```
2. **在網路設備上停用 ICMP Timestamp 回應**
    - 部分設備（如 Cisco、Juniper）可以透過 CLI 停用 ICMP Timestamp：
```bash
no ip icmp timestamp-reply
```
1. **針對伺服器或終端設備進行系統加固**
    - 在 Linux 系統中，透過 sysctl 限制 ICMP：
```bash
echo "net.ipv4.icmp_echo_ignore_all = 1" >> /etc/sysctl.conf sysctl -p
```
4. **使用 NTP 進行時間同步，避免時鐘偏移**
    - 確保系統時間同步到可信任的時間伺服器（如 `time.google.com`、`ntp.org`）。
    - 若設備時間偏差過大，可能會影響 TLS、Kerberos 認證等安全機制。

---

### **結論**
ICMP Timestamp Request Remote Date Disclosure 本身不是一個直接的攻擊向量，但它會洩露系統時間資訊，可能被攻擊者利用來進一步分析和計劃攻擊。建議在不影響正常業務的情況下，封鎖 ICMP Timestamp Request，並確保系統時間正確同步，以降低潛在風險。

### 以上是ChatGPT得回覆

## 驗證法一  
使用 nping(安裝wireshark完應該有) 指令測試驗證 icmp type 13 為 timestamp request  
```bash
nping --icmp --icmp-type 13 192.168.57.3 
``` 
![](/images/20250218113332.png)
看到 RCVD有收到來自192.168.57.3的回覆 這是原本的  
![](/images/20250218113344.png)
沒有收到來自192.168.57.3的回覆 這是修改後的

## 驗證法二  
使用Nessus的Advance Scan  
Plugins 只選擇 General 裡面的 ICMP Timestamp Request Remote Date Disclosure  
做掃描
![](/images/20250218113509.png)
原本會掃描出黃色Low 的 ICMP Timestamp Request 弱點
![](/images/20250218113518.png)
修改後 就不會出現
