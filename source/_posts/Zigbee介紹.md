---
title: Zigbee介紹
tags:
  - IEEE802154
  - Zigbee
date: '2023-11-19 03:50:00'
updated: '2025-02-19 08:51:02'
categories:
  - zigbee
description: ''
abbrlink: c8085013
keywords:
  - IEEE802154
  - Zigbee
---
* [1、ZigBee 开发教程之基础篇—ZigBee简介和学习方法](https://blog.csdn.net/u011350258/article/details/113062464)
* [手把手指南—— 开启你的 Zigbee 之旅](https://zigbeealliance.org/wp-content/uploads/2019/12/Getting-Started-with-Zigbee-3.0-CN.pdf)
 <!-- more -->
 * Youtube [Zigbee](https://www.youtube.com/playlist?list=PLkc2nflOpCG1jYSnd6gOWpV8XhrlEF-2J)
* [Day 1-1 ZigBee基础知识](https://www.youtube.com/watch?v=iuhY4f_DvfI&ab_channel=TorchIoTBootCamp)
* [Zigbee快速入门第二节 – Zigbee基础介绍](https://blog.csdn.net/lexiyao/article/details/120969009)
* [zigbee-specification](https://csa-iot.org/wp-content/uploads/2023/04/05-3474-23-csg-zigbee-specification-compressed.pdf)

# ZigBee基础知识
## What is ZigBee
### Zigbee
![](/images/20231119024254.png)
右邊是覆蓋範圍大(10KM) 大多需要被管制的 由運營商主導 城市範圍的覆蓋
左邊是覆蓋範圍小(100m) 家庭 樓道用
Zigbee 短距離通訊技術 低速通訊技術 最大速度是250Kpbs
傳輸控制命令 和 數據採集
低功耗的技術 [休眠电流在uA級別](https://support.tuya.com/zh/help/_detail/K9skz5907xerr)
Mesh網路拓譜結構 網路輕鬆擴展 最大節點數 65535(wifi 50 BT 8)
2bytes=16bits=2^16=65535
zigbee：10-100m，蓝牙：2-10m，Wi-Fi：30-100m

### Zigbee網路拓譜
Star(WiFi) vs Mesh(Zigbee) 主要差別在有無中心節點，中心節點壞了就不能正常工作

Zigbee(star mesh tree)
![](/images/20231119024320.png)


### Zigbee 歷史
![](/images/20231119024339.png)
2003年 Zigbee誕生
2016年 發布Zigbee3.0(整合統一之前的標準)


[ZigBee 3.0版标准，强化低延迟与低功耗优势，并加入网际网路通讯协定(IP)支援能力](https://www.51cto.com/article/496376.html)

ZigBee完全相容Wi-Fi/IP

ZigBee 3.0完全相容IP。ZigBee设备与Wi-Fi设备类似，通常透过路由器、闸道或机上盒连接到互联网，让人可在世界其他地方利用连接互联网的个人电脑、平板或智慧手机应用程式等任何其他设备实现即时控制(图3)。由于ZigBee完全相容Wi-Fi和IP，因此没必要在手机本身插入ZigBee晶片，从而发现和控制ZigBee连接的智慧家居和物联网设备。只要通过任何联网中心均可让这一切实现，这意味着，透过Wi-Fi或蜂窝网进行联网的个人电脑和智慧手机可以作为指示板，并可毫不费力发现并与其他ZigBee设备进行通讯。

[小白也能读懂的 ZigBee 3.0 简介](https://blog.csdn.net/mengqidluffy/article/details/116061885)
###  Zigbee 聯盟
![](/images/20231119024356.png)
推广Zigbee技术的主要力量。这是一个开放的组织。任何公司都可以加入Zigbee联盟作为成员。Silicon Labs是Zigbee联盟的董事会成员。
* 为IoT（物联网）的无线设备端到设备端的通信制定开放的全球标准
* 通过我们的认证计划对产品进行认证，以帮助确保互操作性(產品和平台的認證)
* 在全球范围内推广我们的标准

![](/images/20231119024412.png)
Zigbee聯盟更名，同步推出與亞馬遜、蘋果及Google共同制定連接標準Matter
## 概述
### Zigbee協議
![](/images/20231119024438.png)
基於[IEEE802.15.4](https://baike.baidu.hk/item/IEEE%20802.15.4/6657379)標準基礎上定義
MAC層和物理層沿用IEEE802.15.4標準
網路層 APS層(Application Support Sub Layer) 應用層 是Zigbee定義的標準

網路層和APS層有加密的特性:加密 驗證 重傳保護

* 物理層 由晶片實現 無線電的收發管理 調製解調
* MAC層 一半硬體 一辦軟體實現 數據收發 確認 重傳 掃描
* 網路層 路由 設備狀狀態維護 子節點管理
* APS層 端到端重傳和確認 
* 應用層 ZDO(ZigBee Device Object 特殊的設備管理功能) User應用

灰色部分是晶片處理
綠色是平台供應商實現
橘色是應用層開發

> IEEE 802.15.4是一種技術標準，它定義了低速率無線個域網 （LR-WPAN）的協議。 它規定了LR-WPAN的物理層和媒體訪問控制 ，並由IEEE 802.15工作組維護，該工作組在2003年定義了該標準。它是Zigbee的基礎，諸如 ISA100.11a ， WirelessHART ， MiWi ， 6LoWPAN ，
> PAN 個人區域網路

### Zigbee物理層
![](/images/20231119024455.png)
#### 工作範圍在 2.4GHz 868MHz 915MHz
* 2.4GHz  全球通用 250kpbs 11-26通道 2400MHz~2600MHz 每5MHz一個通道
* 868MHz 英國電表 水表(Smart Engery) 20kpbs 0通道 
* 915MHz 北美Zigbee 40kpbs 1-10通道 每2MHz一個通道
#### 物理層功能
* 物理无线和MAC层的接口
* 無線電收發管理
* 无线电开/关控制
* 调制与解调
* 信道选择
* 链接质量估算
* 能量检测
* 功率調節

#### Zigbee 和 WIFI 共存??
![](/images/20231119024508.png)
Zigbee 和 WIFI 都使工作在2.4GHz 底下 會存在相互干擾的情況
[怎样设置Zigbee和wifi信道避免干扰？](https://www.ebyte.com/new-view-info.aspx?id=1082)
[Zigbee和Wi-Fi的干擾和共存](https://bbs.16rd.com/thread-574907-1-1.html)
[在2.4GHz共存-WLAN、蓝牙、ZigBee和Thread在2.4GHz频段共存](https://www.mwrf.net/tech/rfic/2020/27496.html)
### Zigbee MAC層

#### MAC幀
![](/images/20231119024520.png)
前面PERAMBLE SPD PHYHeader 由物理層封裝
Frame Control(2bytes):標示frame類型
* Beacon 掃描網路
* Data 上層數據
* ACK 確認用
* MAC Command 入網時候使用的命令 MAC关联过程。
CRC(最後2bytes): 驗證資料包的完整性 (空氣中傳播 受到干擾 資料會丟掉)

#### MAC 傳送確認
![](/images/20231119024532.png)
MAC层还为节点提供了一种方法，该方法可通过确认得知节点已成功接收到1跳单播传输，并且已通过验证CRC保留了所传输消息的完整性。 多跳传输应在每个跳上进行确认。节点执行CCA检查并发送消息后，它会等待MAC确认。如果未收到消息，则该节点应尝试多次重发该消息，直到其最终成功或最大的重试次数已用尽。
![](/images/20231119024545.png)
從NodeA到NodeD發送資料通常是應用層和應用層的傳送
NodeA 傳到到 NodeB 確認收到回傳MAC Ack 到 NodeA
不是NobeB接收的資料(資料最多在網路層) 往NodeC傳直到傳到正確的接收方
NodeD正確的接收要回傳APS Ack確認回NodeA

兩種不同的確認 如果沒收到Ack就會重傳
* MAC Ack 是點跟點的確認
* APS Ack 是端到端的確認


#### [CSMA/CA 避免干擾](https://zhuanlan.zhihu.com/p/51412066)
Listen Before Talk 發送資料前聽一下
判斷channel是否有人在使用
802.15.4允许多个网络位于同一通道上。因此，需要采取某种方式来避免来自不同网络的数据包在空中碰撞而造成通信错误。MAC子层使用CSMA-CA（Carrier Sense Multiple Access/Collision Avoidance）控制对无线电的访问。避免碰撞是通过CCA（Clear Channel Assessment）完成的。在发送之前，每个节点必须检查电波是否可以正常发送（RSSI低于CCA阈值）。如果是，则节点应继续进行并在进行少量随机退避后进行传输。如果CCA没有通过，则该节点应等待多个退避周期，然后才能再次尝试该过程。随机退避允许多个节点交错传输，因此它们总是可以在某个时间点找到干净的无线空间来作数据传输。尽管比特率很低，因为数据包比较小（128 bytes），即使在非常嘈杂的信道上每一个节点也可以成功地完成数据传输。

[CSMA/CD](https://www.youtube.com/watch?v=wXbcBSQDnFA) 兩種解決碰撞問題的機制
CSMA(Carrier Sense Mutiple Access) 防止碰撞機制 先偵測Hub上有無通訊 無才可通訊
CD(Collision Detection) 偵測碰撞 當偵測碰撞到碰撞時，發出JAM訊號 清空資料 再用 後退演算法錯開
CA(Collision Avoidance) 避免碰撞 
[CSMA/CA](https://wiki.mbalib.com/zh-tw/CSMA/CA)


> CSMA/CA是先聽一下附近有沒有人講話，沒有人講話再講話
> CSMA/CD是不管了直接傳，發現打斷其他人講話時就回來等一下再傳

(一)差異： 
1. CSMA/CD主要應用在有線網路，CSMA/CA主要應用在無線網路。
2. CSMA/CD是使用碰撞偵測處理(Collision Detection)，在有線網路的環境中，可以檢測碰撞，但不容易去 避免。CSMA/CA是使用避免碰撞處理(Collision Avoidance)，在無線網路的環境中，要使用CD去偵測碰 撞是困難的，所以在無線網路中採取CA的方式避免碰撞。 
3. CSMA/CD的傳輸效率較佳，但硬體成本比較高。CSMA/CA的傳輸效率較差，但硬體成本比較低。
(二)運作過程： 
1. CSMA/CD是在偵測是否有通道空閒後，在發送資料時同時進行防止Collision的檢查，如果在發送數據過 程中檢測到衝突，就立即停止發送資料，並等待一段時間，再重新發送碰撞的資料。
2. CSMA/CA在送出資料前，先送RTS封包確認或是聆聽網路上的狀態，若沒有人使用，等待一段時間後， 如果還沒有人使用，才送出資料。

### Zigbee 網路層
#### 裝置類型
![](/images/20231119024603.png)
* Coordinator 協調員
    * 一個Zigbee網路只能有一個
    * 創建網路  第一個成員 地址為0x000
    * 可以路由 添加成員
    * 不能睡眠
* Router 路由
    * 可以路由
    * 不能睡眠
* End Device/ Sleepy End Device 終端設備
    * 必須附著在 Coordinator 或 Router 上
    * 不能路由 只能接收
    * 可睡眠
#### Device Announce (加入網路時所發送的訊息)
![](/images/20231119024617.png)
Device Type(IEEE-802.15.4定义了两种设备类型)：
* FFD(Full Function Device), 全功能设备，能够执行IEEE 802.15.4标准中描述的所有职责，并且可以在网络中担任任何角色。Coordinator/Router
* RFD(Reduced Function Device), 剪裁功能设备，功能有限。 End Device/ Sleepy End Device

> RFD设备的处理能力和内存大小通常小于FFD设备。
![](/images/20231119024634.png)
Alternate PAN Coordination 標示是否為 Coordinator
![](/images/20231119024648.png)
Power Source Main 電源供電或者電池供電

#### 網路地址 Addressing PAN ID and (xPan ID)Extended PAN ID
![](/images/20231119024701.png)
* PAN ID: 短的(不唯一) 16bit 由用戶或Coordinator隨機生成 用於mac層過濾用 0xFFFF 用於廣播 
* xPan ID: 長的(唯一值) Coordinator隨機生成 只有在Beacon會傳送  用於解決PAN ID衝突

> 低速通訊技術 250kpbs 有效數據負荷占比低(mac頭部 network頭部...) 如果每次用64bit效數據負荷占比低 所以用短的網路地址

>mac層收到不是自己的PAN ID 就丟掉

![](/images/20231119024719.png)
☝️Beacon的封包

#### 設備地址 Addressing Node ID and IEEE Address
* Node ID(Short ID) 16bits加入網路時父節點分配 可能會衝突
* IEEE Address(Long ID) 64bits (MAC Address) 出廠就設定好 解決Node ID衝突
再傳送的時候用NodeID傳送
如果出現衝突就更改NodeID

### Zigbee 應用層
#### EndPoint 有點像Port的概念 範圍0-255
![](/images/20231119024733.png)

EndPoint 1-239 用戶開發用
EndPoint 0, 240-255 保留用 0(ZDO Zigbee設備管理用) 255 廣播所有EndPoint

Endpoints存在於每個節點中，以1~240的數字編號，
每個endpoint定義了一個在ZigBee節點上運行的application
(一個node可以有多個applications，也就是多個endpoints)

Endpoints的三個用途：
* 允許不同的app profiles存在於一個節點
* 允許一個節點中的不同控制點
* 允許一個節點中有不同的devices
![](/images/20231119024755.png)
通訊是 EndPoint to EndPoint 
一個當 Client 一個當 Server
通訊內容是由**Cluster**格式定義
![](/images/20231119024811.png)
Cluster: 通訊Model
Client/Server model
每一個Cluster定義一個功能(On/Off Cluster、Level Cluster) ZCL(Zigbee Cluster Library)
定義命令和屬性
![](/images/20231119024826.png)
以控制燈開關為例 開關為Client 燈為Server
On/Off Cluster:
Commend: OnOff、Toggle
Attribute: On、Off

> Cluster大多定義好了功用的 但也可以客製化定義 但這樣就不通用


## 安全特性(偽造 重傳)
### 應用層安全
![](/images/20231119024856.png)
將Applocation層的Payload加密 中間加安全頭部 然後算出Hash值 加到最後面往下傳
通過檢查Hash值(APS MIC)檢查應用層有無被篡改
### 網路層安全

![](/images/20231119024909.png)
將Network層的Payload加密 中間加安全頭部 然後算出Hash值 加到最後面往下傳
通過檢查Hash值(NWK MIC)檢查應用層有無被篡改
> 應用層網路層對稱加密: AES128 
![](/images/20231119024953.png)
> 安全頭部(APS Auxiliary HDR、NWK Auxiliary HDR)有Frame Counter防止重傳

### Frame Counter 防止重傳
![](/images/20231119025007.png)
接收端會記錄收到的 FrameCounter 如果接收到一樣就會丟掉封包 (只會紀錄周圍節點的FrameCounter)
發送端每次發送封包時會將 FrameCounter++ 達到每次FrameCounter都不一樣

### 安全特點 端到端 點到點
![](/images/20231119025021.png)
NodeA傳到NodeC 中間NodeB不會去解開APS層的資料 NodeB只會解開網路層的資料
APS層是 端到端 端跟端之間有秘鑰(Link Key)
網路層是 點到點 點跟點之間有秘鑰(NWK Key)

### NWK Key
![](/images/20231119025033.png)
* 整個網路用的NWK Key都是一樣
* 在創建網路時隨機生成 16-byte
* NWK 靠應用層加密傳輸
* Trust Center: 提供NWK Key的裝置(通常是Coordinator)
* 有兩種模式 Centralized Distrbuted

#### 傳送 NWK Key

### Link Key
* 16-byte
* Trust Center Link Key 新設備加入網路傳送NWK key
* (install code[出廠設定] + 算法) 或 (**ZigbeeAlliance09**)

#### install code 是透過人工完成的 更安全
![](/images/20231119025052.png)


## [加入網路](https://blog.csdn.net/lee_jimmy/article/details/109162896)
### 通用Link Key(ZigbeeAlliance09)
![](/images/20231119025110.png)
* Coordinator 創建網路 設定PAN ID、xPAN ID、通道、功率
* 設備可以加入網路
* 掃描網路，新設備在每個通道發起Beacon Request等待有無回應 無回應往下一個通道發Beacon Request (因為不知道所以全發)
* Coordinator收到Beacon Request回覆 一個Beacon(包含PAN ID xPAN ID...)
* 設備可能會收到很多Beacon，選擇用rssi最強當作父節點 再發送Association Request 包含(自己設備的能力...)
* 父節點發送Association Response(分配給他沒人用 Node ID)
* 等待接收NWK Key (Trust Center 會用Link Key(ZigbeeAlliance09)加密NWK Key) 用ZigbeeAlliance09解密
* router会发送一个device announce的报文（从目的地址为广播地址可以看出）通知全网，我入网了、我的设备信息都有哪些。
* 之後就可以通訊了

### 使用Install Code
![](/images/20231119025123.png)
* Coordinator 創建網路 設定PAN ID、xPAN ID、通道、功率
* 設備可以加入網路
* Coordinator 掃描 新設備Install Code建立Link Key
* 掃描網路，新設備在每個通道發起Beacon Request等待有無回應 無回應往下一個通道發Beacon Request (因為不知道所以全發)
* Coordinator收到Beacon Request回覆 一個Beacon(包含PAN ID xPAN ID...)
* 設備可能會收到很多Beacon，選擇用rssi最強當作父節點 再發送Association Request 包含(自己設備的能力...)
* 父節點發送Association Response(分配給他沒人用 Node ID)
* 等待接收NWK Key (Trust Center 會用Link Key(由掃描到的Install Code建立)加密NWK Key) 用設備上的Install Code產生Link Key解密
* router会发送一个device announce的报文（从目的地址为广播地址可以看出）通知全网，我入网了、我的设备信息都有哪些。
* 之後就可以通訊了

[Zigbee抓包工具](https://blog.csdn.net/u012993936/article/details/88225371)
* Packet Sniffer
* Ubiqua
* Wireshark 
    * [手把手教你如何通过CC2531抓取Zigbee包，并解析加密Zigbee包](https://segmentfault.com/a/1190000041469759)
    * [使用CC2531 USB Dongle 抓取小米Zigbee智能硬件數據包](https://www.twblogs.net/a/5b8f25562b717718834a0ac9)
    * [露天CC2531](https://www.ruten.com.tw/find/?q=+CC2531)
    * [如何用Wireshark對Zigbee進行抓包分析](https://www.twblogs.net/a/5c80fb1fbd9eee35fc136d05)
    * [ZigBee 无线报文的抓取与分析](https://zhuanlan.zhihu.com/p/489493842)
    * [USB Dongle CC2530 CC2531 ZigBee 协议栈 分析 抓包 工具 模块](https://item.taobao.com/item.htm?id=657969866117)
    * [利用Wireshark对Zigbee进行抓包分析](http://bbs.sunsili.com/thread-226165-1-1.html)


[认识Profile和Cluster](https://blog.csdn.net/yanwumuxi/article/details/123866005)
---------
[Zigbee Profile](https://community.nxp.com/pwmxy87654/attachments/pwmxy87654/wireless-connectivity/698/1/075367r03ZB_AFG-Home_Automation_Profile_for_Public_Download.pdf
)
![](/images/20231119025145.png)


---------
### Silicon Labs GeckoSDK
* [zigbee-app-framework-dev-guide-sdk-pdf](https://www.silabs.com/documents/public/user-guides/ug491-zigbee-app-framework-dev-guide-sdk-7x.pdf)
* [New Callback](https://community.silabs.com/s/question/0D58Y00008aZEGPSA4/hello-im-working-on-a-zigbee-minimals-project-with-the-efr32-xxx-and-i-would-like-to-ask-you-how-to-create-a-callback-to-do-an-action-every-5-minutes-thank-you?language=zh_TW)
* [Zigbee-Boot-Camp](https://github.com/SiliconLabs/IoT-Developer-Boot-Camp/wiki/Zigbee-Boot-Camp)
* [MGM240PA32VNN](https://www.silabs.com/wireless/zigbee/efr32mg24-series-2-modules/device.mgm240pa32vnn?tab=specs)
* [Zigbee应用程序框架开发指南 - 概述](https://blog.csdn.net/lexiyao/article/details/109153280)
* [【经验】EmberZnet CLI指令之绑定功能实现过程](https://www.sekorm.com/news/30245981.html)
* [Zigbee Profile](https://blog.csdn.net/yanwumuxi/article/details/123866005)
* [ZigBee 3.0 Tutorial - Light and Switch from Scratch](https://community.silabs.com/s/article/zigbee-3-0-tutorial-light-and-switch-from-scratch-step-5?language=en_US)
* [zigbee_applications example](https://github.com/SiliconLabs/zigbee_applications/tree/a90ebc1e6a06ac7dd1e158f435f58e910595a477)
* [bilibili](https://space.bilibili.com/641942011/channel/seriesdetail?sid=726018)
* [Silicon Labs EFM8 的 Hello World 程式](https://workshop.siliconforce.com/?p=862)

-----
### TI Z-Stack
* [Texas Instruments](https://www.ti.com/zh-tw/homepage.html)
* [TI Z-Stack User Guide](https://software-dl.ti.com/simplelink/esd/plugins/simplelink_zigbee_sdk_plugin/1.60.01.09/exports/docs/zigbee_user_guide/html/zigbee/introduction.html)
* [ZigBee开发-嵌入式物联网开发教程学习](https://www.bilibili.com/video/BV1d94y117kj/)
* [Zigbee学习（三）Z-Stack代码框架解析](https://blog.csdn.net/yanwumuxi/article/details/126485087)
* [Zigbee学习（四）入网流程及抓包分析](https://blog.csdn.net/yanwumuxi/article/details/131128082)
* [Zigbee驱动安装-抓包环境搭建](https://docs.wireless-tech.cn/doc/101/)
* [手把手教你如何通过CC2531抓取Zigbee包，并解析加密Zigbee包](https://blog.csdn.net/lixiaocheng1983/article/details/123090679?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-5-123090679-blog-88225371.235^v38^pc_relevant_sort_base1&spm=1001.2101.3001.4242.4&utm_relevant_index=8)


-----
### zigpy
[zigpy](https://github.com/zigpy/zigpy)
[zugpy-cli](https://github.com/zigpy/zigpy-cli)


### [ZDO](https://www.wpgdadatong.com/question/detail/15822)
提供了所有ZigBee设备共同的特殊应用，以管理已描述的各种过程。此应用程序是ZigBee设备对象或ZDO。它位于节点的应用层，并且可以使用 ZigBee设备配置文件（ZDP）和相关 cluster通过端点0与远程节点通信。它具有以下作用：

1. 定义网络设备的类型：协调器、路由器或终端设备
2. 初始化节点以允许应用程序运行。
3. 执行设备发现和服务发现过程。
4. 实现允许协调器创建网络以及路由器和终端设备加入和离开网络所需的过程。
5. 启动并响应绑定请求。
6. 启动并响应绑定请求提供允许在应用程序之间建立安全关系的安全服务。
7. 允许远程节点从节点检索信息，如路由表和绑定表，并执行节点的远程管理，如指示它离开网络。

ZDO使用堆栈内的服务来实现这些角色，并提供了一种允许用户应用程序访问堆栈服务的方法。


[ZigBeePro协议栈词汇名词解释](https://blog.csdn.net/qq_35413125/article/details/116986028)

[Thread协议介绍](https://blog.csdn.net/Msymbol/article/details/111194158)
[Thread可能取代Zigbee -ZHA並衝擊智慧家庭的通訊標準](https://iknow.stpi.narl.org.tw/Post/Read.aspx?PostID=10327)
[無線技術 Thread 是什麼？它會是智慧家庭的未來嗎？](https://www.kocpc.com.tw/archives/476397)
[Matter智慧家庭控制標準協定,用Thread整合智能居家控制系統](https://turadise.com/matter-thread/)