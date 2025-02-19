---
title: SDP（Session Description Protocol, 会话描述协议）
categories: []
date: '2024-12-23 15:45:00'
updated: '2025-02-19 08:51:03'
description: ''
abbrlink: 739a46ba
tags:
  - WebRTC
keywords:
  - WebRTC
---
会话描述协议（SDP，Session Description Protocol）是一种用于多媒体通信的文本格式协议。它通常用于描述多媒体会话的参数，例如会议、语音或视频通话、ICE 服务器信息、音视频编码信息，以便参与方可以协商和建立通信。

SDP 本身并不传输媒体数据，而是作为描述协议，与其他传输协议（如 RTP、RTCP 或 SIP）结合使用，以建立和管理会话。以下是 SDP 的一些关键点：
 <!-- more -->
 
---

### **核心功能**

1. **会话描述：** 描述会话的基本信息（例如名称、时间、地点）。
2. **媒体信息：** 指定会话中涉及的媒体类型（音频、视频等）、传输协议（如 RTP）和编码格式（如 H.264、Opus）。
3. **网络地址：** 提供媒体传输所需的 IP 地址和端口。
4. **能力协商：** 描述参与方的媒体能力，便于协商。

---

### **SDP 的结构**

SDP 的结构由多行键值对组成，每行的格式为：
```php
<键>=<值><CR><LF>
```
<键>=<值>大小寫敏感 且=之間不能有空白
- `<键>` : 為單一小寫單字，代表特定屬性
- `<值>` : 一串與屬性有關的結構化文字描述，UTF-8編碼。
#### **常见字段**
整個SDP內容主要會由`Session description`、`Time description`、`Media description` 這三大類組成，  
每個信息可能會包含多個`Time description`、`Media description`資訊。
##### Session description
```
 v=  (protocol version number, currently only 0)
    o=  (originator and session identifier : username, id, version number, network address)
    s=  (session name : mandatory with at least one UTF-8-encoded character)
    i=* (session title or short information)
    u=* (URI of description)
    e=* (zero or more email address with optional name of contacts)
    p=* (zero or more phone number with optional name of contacts)
    c=* (connection information—not required if included in all media)
    b=* (zero or more bandwidth information lines)
    _One or more **time descriptions** ("t=" and "r=" lines; see [below](https://en.wikipedia.org/wiki/Session_Description_Protocol#Time_description))_
    z=* (time zone adjustments)
    k=* (encryption key)
    a=* (zero or more session attribute lines)
    _Zero or more **Media descriptions** (each one starting by an "m=" line; see [below](https://en.wikipedia.org/wiki/Session_Description_Protocol#Media_description))_
```
##### Time description(mandatory 強制)
```
t=  (time the session is active)
r=* (zero or more repeat times)
```
##### Media description (optional)
```
m=  (media name and transport address)
i=* (media title or information field)
c=* (connection information — optional if included at session level)
b=* (zero or more bandwidth information lines)
k=* (encryption key)
a=* (zero or more media attribute lines — overriding the Session attribute lines)
```
#### **示例 SDP 文件**

以下是一个简单的 SDP 示例：
```
v=0
o=- 2890844526 2890842807 IN IP4 192.168.1.10
s=Example
c=IN IP4 203.0.113.1
t=0 0
m=audio 49170 RTP/AVP 0
a=rtpmap:0 PCMU/8000
m=video 51372 RTP/AVP 96
a=rtpmap:96 H264/90000
```
### **SDP 的用途**
1. **SIP 会话：** 在 VoIP 系统中，SDP 与 SIP 配合，传递会话描述信息。
2. **WebRTC：** 用于浏览器之间的实时通信，协商音视频参数。
3. **多媒体广播：** 描述视频会议或流媒体会话。

---
### **扩展**

虽然 SDP 是一种标准化协议，但不同应用可能需要扩展一些属性。例如，WebRTC 定义了额外的 SDP 属性用于描述数据通道和 NAT 穿越技术。

# 總結
SDP 就是規範描述多媒體通信的格式，使用key,value作為結構
只是單單的格式定義 不包含媒體傳輸