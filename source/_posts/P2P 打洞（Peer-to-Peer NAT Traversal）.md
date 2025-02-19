---
title: P2P 打洞（Peer-to-Peer NAT Traversal）
date: '2024-12-19 18:28:00'
updated: '2025-02-19 08:51:03'
categories: []
description: ''
abbrlink: 464c14bb
tags:
  - WebRTC
  - p2p
keywords:
  - WebRTC
  - p2p
---
# 兩個裝置分別在NAT後面 
![](/images/20241219171958.png)
# 這時需要一台Server當作溝通橋梁 傳送訊號
![](/images/20241219172742.png)
# 裝置A向Sever註冊自己外網  IP 和 port
![](/images/20241219172424.png)
在 A 裝置啟一個 3000 UDP服務 並將自己的 外網 ip 和 port 向 server 註冊
 <!-- more -->
 # 裝置B向Sever註冊自己外網  IP 和 port
![](/images/20241219172021.png)
在 B 裝置啟一個 3000 UDP服務 並將自己的 外網 ip 和 port 向 server 註冊
# Sever 向A B 裝置 傳送對方的 IP 和 port

![](/images/20241219172034.png)
Server向A傳送B的 ip 和 port
Server向B傳送A的 ip 和 port
# A 向 B 發送 UDP 封包
![](/images/20241219172123.png)
A 向 B 發送 UDP 封包 在這個過程中 A 的源端口為 3000 並且在穿過 NAT A 時，
留下紀錄 192.168.1.1:3000 <-> 203.0.113.1:1234
封包到達NAT B時，因為不知如何轉發，所以將此封包拋棄
# B 向 A 發送 UDP 封包
![](/images/20241219172134.png)
B 向 A 發送 UDP 封包 在這個過程中 B 的源端口為 3000 並且在穿過 NAT B 時，
留下紀錄 192.168.30.1:3000 <-> 192.51.100.1:1233
封包到達NAT A時，因為先前已有紀錄轉發，所以將此封包轉到 A
# 兩邊可以通過UDP 做 P2P連線
![](/images/20241219172144.png)
# 總結
  上述流程為簡易的流程，如
  - 如何知道自己公網 IP 以及 {% post_link  "NAT（Network Address Translation，網路位址轉換）" %}#NAT（网络地址转换）分为以下四种类型 需要用 {% post_link  "STUN（ Session Traversal Utilities for NAT，NAT 穿越會話工具）" %}
  - 如果NAT類型為 非對稱NAT  需要用 {% post_link  "TURN 協議（Traversal Using Relays around NAT）" %} 
  如果遇到不同的設備，成功率會不同，上述流程需要一台Server做幫忙，且NAT類型為 非對稱NAT，仰賴NAT映射關係及UDP特性
# 參考
 - [P2P 打洞原理](https://webrtc.mthli.com/basic/p2p-hole-punching/)