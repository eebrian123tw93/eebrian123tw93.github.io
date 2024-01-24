---
title: SparkFun Thing Plus Matter - MGM240P
tags:
  - Zigbee
  - MCU
  - SOC
  - SparkFun
  - MGM240P
  - simplicity-studio
  - mgm240pb32vna
  - silabs
categories:
  - zigbee
date: '2023-11-19 02:46:00'
updated: '2024-01-24 02:17:05'
description: ''
abbrlink: 2b622287
---
# [SparkFun Thing Plus Matter - MGM240P](https://www.sparkfun.com/products/20270)
# 安裝 Simplicity Studio 5
先下載[simplicity-studio](https://www.silabs.com/developers/simplicity-studio)並安裝(防毒軟體先關掉)

![](/images/rJPmOAx7T.png)
 <!-- more -->
 ![](/images/rkeBu0em6.png)
![](/images/rJMUOAgmT.png)
![](/images/H19PO0x7a.png)
一直下一步，安裝到預設路徑即可 安裝完會自動打開
![](/images/H1kFFAxXp.png)
同意並繼續
![](/images/r1NhtAxQ6.png)
登入silabs的帳戶，免費辦一個
登入後會安裝驅動 輸入Admin帳號密碼安裝
![](/images/HkqDc0xX6.png)
點擊Intall 點擊Intall by Connecting devices
![](/images/Hy4oqAlQ6.png)
現在沒有裝置 接入USB
![](/images/B1vJiAlXa.png)
接入USB 會需要安裝驅動板子套件按下Yes
![](/images/HyS8sRxm6.png)
選擇板子 Next
![](/images/BJROsCxmp.png)
選擇Auto Next
![](/images/H1T3oCeQ6.png)
同意安裝套件 Next 
![](/images/ByHS2Aema.png)
會裝一些無線套件(geoko sdk)之類的(大約跑30分鐘)
![](/images/HJYMgkbmp.png)
重開 Simplicity Studio
![](/images/Byv9xyZmp.png)

> ** 系統會提示更新 Adapter FW
> 不要更新保持這個1v4p9b113版本
> (1.4.12.18刷進去會失敗，辯識會有問題，會變磚)**


# 更新版子 Adapter Firmware (跳過) 
更新版子參考 [How to update the Firmware on your Starter Kit board](https://community.silabs.com/s/article/how-to-update-the-firmware-on-your-starter-kit-board)
更新版子變磚參考 [Recover EFM32 or EFM8 kit that was bricked during firmware upgrade](https://community.silabs.com/s/article/recover-efm32-or-efm8-kit-that-was-bricked-during-firmware-upgrade)
>  SparkFun Thing Plus Matter 曾經升到1.4.12.18，但失敗變磚，要退版本但是進入boot mode 的方法上面試不出來，最後是不知道怎麼又可以辨識出來(有可能是用手壓住後面的腳位接入USB，陰錯陽差進到 boot mode)，然後降版


# 簡單的範例 [Blink Example](https://learn.sparkfun.com/tutorials/sparkfun-thing-plus-matter---mgm240p-hookup-guide/all#blink-example) 
這個範例是一個會閃的燈 
![](/images/SJeZ176.png)
File >> New >> Silicon Labs Project Wizard 
Target Boards: Sparkfun Thing Plus MGM240P (BRD2704A Rev A00)
Target Device: mgm240pb32vna
SDK: Deoko SDK Suite
IDE/Toolchain: Simplicity IDE
點擊 Next
![](/images/Hy9YGyZXp.png)
搜尋 blink 點擊 Platform - Blink Bare-mental
點擊 Next
![](/images/HkO1XyZQa.png)
點擊 Finish
![](/images/BkGX1b7T.png)
![](/images/rJHA71WQT.png)
編譯並寫入板子
![](/images/HkiX4ybX6.png)
編譯無錯誤 正常寫入板子後可看到燈會不斷閃爍
![](/images/SyC7ByZQa.jpg)
![](/images/S1vES1bmT.jpg)
可以到blink.c裡面的 TOOGLE_DELAY_MS 更改閃爍時間
## 參考影片
* [Get Started with Simplicity Studio and the Thing Plus Matter Board](https://www.youtube.com/watch?v=POVRRuVCr-Q)

# NCP 模式 
![](/images/S1O4zC76.png)

> 這裡Demo會要創立三個專案，目的是要創建Zigbee網路
## 創建 Bootloader - NCP UART XMODEM 專案
![](/images/SJeZ176.png)
File >> New >> Silicon Labs Project Wizard 
Target Boards: Sparkfun Thing Plus MGM240P (BRD2704A Rev A00)
Target Device: mgm240pb32vna
SDK: Deoko SDK Suite
IDE/Toolchain: Simplicity IDE
點擊 Next
![](/images/By9Mw1mp.png)
點擊 Bootloader - NCP UART XMODEM
點擊 Next
![](/images/BkaUwkb7T.png)
點擊 Finish
![](/images/rJlydyXa.png)

## 創建 Zigbee - NCP ncp-uart-hw 專案
![](/images/SJeZ176.png)
File >> New >> Silicon Labs Project Wizard 
Target Boards: Sparkfun Thing Plus MGM240P (BRD2704A Rev A00)
Target Device: mgm240pb32vna
SDK: Deoko SDK Suite
IDE/Toolchain: Simplicity IDE
點擊 Next
![](/images/ry3LJbX6.png)
點擊 Zigbee - NCP ncp-uart-hw
點擊 Next
![](/images/Sy39kW7a.png)
點擊 Finish

## 創建 Zigbee - Host Gatway 專案 (在Linux上執行)
![](/images/ByLntyWX6.png)
File >> New >> Silicon Labs Project Wizard 
Target Boards: Custom Board (可能會有點難點)
Target Device: Linux 64 bit
SDK: Deoko SDK Suite
IDE/Toolchain: Makefile IDE
點擊 Next
![](/images/ryakcyZmT.png)
點擊 Zigbee - Host Gateway
點擊 Next
![](/images/HyKZ9J7p.png)
點擊 Finish

## 燒入 Bootloader - NCP UART XMODEM
燒入 Bootloader - NCP UART XMODEM 和 NCP ncp-uart-hw 要刷進板子裡面
![](/images/Hyxny7a.png)
點擊專案 點擊槌子 進行編譯
![](/images/Hy8f2yZma.png)
編譯成功完後 Binaries 出現 檔名為bootloader-uart-xmodem 的各種image
進行寫入動作
![](/images/ryDL31WXp.png)
點擊Tools
![](/images/HJt3kZQ6.png)
點擊Simplicity Commander 點擊ok
![](/images/r1Wnn1WQp.png)
選擇Binary File `v5_workspace/bootloader-uart-xmodem/GNU ARM v10.3.1 - Default/bootloader-uart-xmodem.s37` 點擊 Flash
![](/images/BJGlkeZXp.png)
log 顯示 Successfully
![](/images/HJhC31Q6.png)
在Device Info 裡面的 MemoryMap 裡的 Flash Map 可以看到 Bootloader 占用兩個綠色區塊
![](/images/SJaSJxZXa.png)
右鍵 Debug Adapter 選擇Launch Console 
![](/images/SkJsJxWm6.png)
Seial 1 會出現 
```
Gecko Bootloader v2.03.02
1. upload gbl
2. run
3. ebl info
BL > 
```
代表進入 Gecko Bootloader mode 
> 在還沒刷入 Application 會進入到 Gecko Bootloader mode 
> 如果已經刷了 Application 則此處不會顯示任何東西


## 燒入 NCP ncp-uart-hw
![](/images/Hy0kxCQ6.png)
點擊ncp-uart-hw.slcp
![](/images/BkgQlCWm6.png)
點擊Software Components
![](/images/HJsvlRXa.png)
找到Service >> IO Stream >> Driver >> IO Stream: USART >> vcom
點擊 Congiure
![](/images/HJY3e0Zm6.png)
將 Flow Control 改成 Software Flow control
![](/images/HyScEaWm6.png)
點擊專案 點擊槌子 進行編譯
![](/images/B107BT7p.png)
編譯成功完後 Binaries 出現 檔名為ncp-uart-hw 的各種image
進行寫入動作
![](/images/ryDL31WXp.png)
點擊Tools
![](/images/HJt3kZQ6.png)
點擊Simplicity Commander 點擊ok
![](/images/Byb5rabmT.png)
選擇Binary File `v5_workspace/ncp-uart-hw/GNU ARM v10.3.1 - Default/ncp-uart-hw.s37` 點擊 Flash
![](/images/S1CsrTZXp.png)
log 顯示 Successfully
![](/images/S1mLITXa.png)
在Device Info 裡面的 MemoryMap 裡的 Flash Map 可以看到 Bootloader + ncp-uart-hw 的分布
Bootloader 從0x8000000開始
ncp-uart-hw 從0x8006000開始
![](/images/SJaSJxZXa.png)
右鍵 Debug Adapter 選擇Launch Console 
![](/images/HJCWDabQ6.png)
Seial 1 不會顯示任何東西
代表進入 Application mode 


## 編譯 Zigbee - Host Gateway(要用 Linux 編譯運行在 Linux 上)
![](/images/B11Aupb76.png)
cd 到專案底下
![](/images/SJSjdpWXa.png)
`gcc -v`
![](/images/r11lFaZmT.png)
`make -f Z3Gateway.Makefile`
![](/images/HyhsKTZmT.png)
編譯完成
![](/images/SyIW9pbXa.png)

## 透過 Host Gateway 控制 MCU 建立Zigbee 網路
![](/images/SJ0OqpZXp.png)
這裡選用Jlink OB 這也是帶有Serial Com Port 功能 
![](/images/H1Dhnama.png)
cd 到 程式所在地
![](/images/HkPfpabQ6.png)
`./Z3Gateway -f x -b 115200 -p  /dev/ttyACM0`
![](/images/S1aSTT76.png)
直接輸入info
![](/images/rJXOpTWQp.png)
可以看到當前狀態 nodeId PanId xpan .... 接下來就是建立網路
![](/images/HJPg067a.png)
`network leave` 先離開網路
![](/images/r1P5A6WQT.png)
`plugin network-creator start 1` 成功會看到 EMBER_NETWORK_UP 0x0000 的字眼
![](/images/SyBRAT7T.png)
再輸入info 可以看到當前狀態 nodeId PanId xpan .... 網路已經建立好了
![](/images/Sy8Iy0Wma.png)
`plugin network-creator-security open-network` 讓其他設備加入

## 參考
* [UG235.09: Developing NCP Applications with Silicon Labs Connect v2.x](https://www.silabs.com/documents/public/user-guides/ug235-09-developing-ncp-apps-with-connect.pdf)
* [Building a Customized NCP Application with Zigbee EmberZNet 7.x](https://www.silabs.com/documents/public/application-notes/an1320-customized-ncp-zigbee7.pdf)
* [EZSP-UART Host Interfacing Guide](https://www.silabs.com/documents/public/application-notes/an706-ezsp-uart-host-interfacing-guide.pdf)
* [基于芯科Host-NCP解决方案的Zigbee 3.0 Gateway技术研究（-）-Z3GatewayHost应用](https://blog.csdn.net/lshddd/article/details/115458007)
* [在Cygwin环境下构建和使用EmberZNet PRO Zigbee Host应用程序](https://blog.csdn.net/qq_42860989/article/details/125909875)
* [managed-zigbee-gateway/README.md](https://github.com/SiliconLabs/managed-zigbee-gateway/blob/master/README.md)
* [UART串口流控制（Flow control）](https://blog.csdn.net/qq_42992084/article/details/104761474)
* [How to Verify the Normal Operation of NCP？](https://community.silabs.com/s/question/0D51M00007xeQUSSA2/how-to-verify-the-normal-operation-of-ncp?language=zh_TW)
## 參考影片
* [Day1-4-EmberZnet SDK的Bootloader和OTA配置详解](https://www.bilibili.com/video/BV1nM4y1g755/)
* [Day 1-2_EmberZnet协议栈介绍及开发工具AppBuilder介绍 (1)](https://www.bilibili.com/video/BV1fv411y7cq/)
* [Day 3-1 Zigbee 3.0 RTOS网关介绍](https://www.bilibili.com/video/BV1bT4y1w7EJ)
* [10-01 何謂bootloader](https://www.youtube.com/watch?v=EP96d_ps4vg&ab_channel=CUSTCourses)
* [ARM Bootloader开发-1.bootloader概述1](https://www.youtube.com/watch?v=njFxwNoPbkc)


# Soc 模式 (以Zigbee - Soc Switch 為例 將裝置修改為Coordinator並且建立Zigbee網路)
## 創建 Bootloader - Soc Internal Storage 專案
![](/images/SJeZ176.png)
File >> New >> Silicon Labs Project Wizard 
Target Boards: Sparkfun Thing Plus MGM240P (BRD2704A Rev A00)
Target Device: mgm240pb32vna
SDK: Deoko SDK Suite
IDE/Toolchain: Simplicity IDE
點擊 Next
![](/images/HyWccAWX6.png)
點擊 Bootloader - Soc Internal Storage (1536kB)
點擊 Next
![](/images/r1xiqRbX6.png)
點擊 Finish


## 創建 Zigbee - Soc Switch 專案
![](/images/SJeZ176.png)
File >> New >> Silicon Labs Project Wizard 
Target Boards: Sparkfun Thing Plus MGM240P (BRD2704A Rev A00)
Target Device: mgm240pb32vna
SDK: Deoko SDK Suite
IDE/Toolchain: Simplicity IDE
點擊 Next
![](/images/HJKFCXT.png)
點擊 Zigbee - Soc Switch
點擊 Next
![](/images/B1vXqRZXp.png)
點擊 Finish


## 修改 Zigbee - Soc Switch 專案設定 Part1
![](/images/B17oiRWma.png)
點擊 Z3Switch.slcp
![](/images/BkgQlCWm6.png)
點擊Software Components
![](/images/r120sCZm6.png)
找到Platform >> Driver >> Button >> Simple Button >> bt1
點擊 Congiure
![](/images/HyIm2C7p.png)
修改 SL_SIMPLE_BUTTON_BTN1 先隨便選一個

再修改
找到Service >> IO Stream >> Driver >> IO Stream: USART >> vcom
將 Flow Control 改成 Software Flow control


## 分別燒入 Bootloader - Soc Internal Storage 和 Zigbee - Soc Switch
參考上面燒入
![](/images/rkD2zJMQT.png)
打開console seral 1  可以看到 > Z3Switch 輸入info 看資訊
此時不能建立網路 因為現在裝置類型是End Device 而且 創建網路需要增加plugin

## 修改 Zigbee - Soc Switch 專案設定 Part2
修改
找到 Zigbee >> Utility >> Zigbee Device Config
將 Primary Network Device Type 改成 Coordonator or Router

找到 Zigbee >> Stack >> Pro Stack >> Pro Leaf Stack 
將他 Uninstall

找到 Zigbee >> Stack >> Pro Stack >> Pro Stack 
將他 install

找到 Zigbee >> Utility >> End Device Support
將他 Uninstal

找到 Zigbee >> Zigbee3.0 >> Network Creator
將他 install
## 燒入 Zigbee - Soc Switch Part2
參考上面燒入
![](/images/r1aTByf7p.png)
`network leave`
![](/images/B1xGUkGma.png)
`plugin network-creator start 1`
![](/images/SyBNIyfQa.png)
`info` 可以看到當前狀態 nodeId PanId xpan .... 網路已經建立好了
之後再輸入`plugin network-creator-security open-network` 讓其他設備加入



# 用 Bootloader - NCP UART XMODEM 刷 Application
![](/images/H1RwPNGXT.png)

## 製作專門給 gecko bootloader 用的 gbl檔案
![](/images/Hk5bbfM76.png)\
`.\commander.exe gbl create ncp-uart-hw.gbl  --app ncp-uart-hw.s37` 

## 進入 geoko bootloader 模式
![](/images/B1glHMGma.jpg)
讓板子進入geoko bootloader 模式 預設是PC00接地，接上電源
如果沒有Application是直接進入到bootloader模式

![](/images/Bkpk8fGQp.png)
用Tera Term 看 會出現 gecko bootloader v2.03.02 字眼 

## 上傳 gbl 檔案
![](/images/ry6nUMzXa.png)
此時Memory Map 裡面只有 bootloader

![](/images/B14lwMGXp.png)
按1進入上傳模式 這裡會持續60s 如果沒有上傳檔案會恢復到Menu狀態

![](/images/HkUmvzzXT.png)
點擊 File >> Transfer >> XMODEM >> Send...


![](/images/Sy9owMzmT.png)
上傳中

![](/images/Hk66PffX6.png)
上傳完顯示 Serial upload complete 這時還是在 bootloader 模式

![](/images/SJwZMzQ6.png)
此時Memory Map 裡面只有 bootloader 和 Application

![](/images/Hy0SdGzQT.png)
按下2 啟動Application 會出現一點亂碼

![](/images/Syw9Mfm6.png)
啟動Application 後的 Memory Map 尾部會出現資料

## 參考
* [UG489: Silicon Labs Gecko Bootloader User’s Guide for GSDK 4.0 and Higher](https://www.silabs.com/documents/public/user-guides/ug489-gecko-bootloader-user-guide-gsdk-4.pdf)
* [UG162: Simplicity Commander Reference Guide](https://www.silabs.com/documents/public/user-guides/ug162-simplicity-commander-reference-guide.pdf)
* [Bootloading Fundamentals](https://www.silabs.com/documents/public/user-guides/ug103-06-fundamentals-bootloading.pdf)
* [中文翻譯Bootloading Fundamentals](https://github.com/dsyx/emberznet-doc/blob/master/UG103.6/UG103.6.md)
* [UG266: Silicon Labs Gecko Bootloader User's Guide (Rev. 1.5)](https://github.com/dsyx/docs.silabs.com_zh/blob/master/docs/UG266%20Silicon%20Labs%20Gecko%20Bootloader%20User's%20Guide.md)

# 用 Bootloader - NCP UART XMODEM 更新 Bootloader
![](/images/H1Wv54f7a.png)
> 這裡更新Bootloader版本只能往上更新
> 編輯Bootloader版本設定  Bootloader Version Main Customer >> Bootloader Core
## 製作專門給 gecko bootloader 用的 gbl檔案
![](/images/HkXtcNz7T.png)
`.\commander.exe gbl create bootloader-uart-xmodem.gbl --bootloader bootloader-uart-xmodem.s37`

## 進入 geoko bootloader 模式
![](/images/B1glHMGma.jpg)
讓板子進入geoko bootloader 模式 預設是PC00接地，接上電源
如果沒有Application是直接進入到bootloader模式

![](/images/Bkpk8fGQp.png)
用Tera Term 看 會出現 gecko bootloader v2.03.02 字眼 

## 上傳 gbl 檔案
![](/images/B14lwMGXp.png)
按1進入上傳模式 這裡會持續60s 如果沒有上傳檔案會恢復到Menu狀態

![](/images/HkUmvzzXT.png)
點擊 File >> Transfer >> XMODEM >> Send...

![](/images/S199i4MXa.png)
上傳完顯示 Serial upload complete 這時還是在 bootloader 模式


![](/images/rkN3sVzmp.png)
按 2 重新啟動 會出現 gecko bootloader v2.03.03 字眼 

## 參考
* [UG489: Silicon Labs Gecko Bootloader User’s Guide for GSDK 4.0 and Higher](https://www.silabs.com/documents/public/user-guides/ug489-gecko-bootloader-user-guide-gsdk-4.pdf)
* [UG162: Simplicity Commander Reference Guide](https://www.silabs.com/documents/public/user-guides/ug162-simplicity-commander-reference-guide.pdf)
* [Bootloading Fundamentals](https://www.silabs.com/documents/public/user-guides/ug103-06-fundamentals-bootloading.pdf)
* [中文翻譯Bootloading Fundamentals](https://github.com/dsyx/emberznet-doc/blob/master/UG103.6/UG103.6.md)
* [UG266: Silicon Labs Gecko Bootloader User's Guide (Rev. 1.5)](https://github.com/dsyx/docs.silabs.com_zh/blob/master/docs/UG266%20Silicon%20Labs%20Gecko%20Bootloader%20User's%20Guide.md)


# 外接 UART 接法
如果要走自己拉的 UTN401A + Ambar 
最左邊要High 最右邊是Low 中間左到右分別是RX TX 
`H|_|RX|TX|_|G`
![](/images/rJpMMU7a.jpg)
* Zigbee - NCP ncp-uart-hw 專案
    Service >> IO Stream >> Driver >> IO Stream: USART >> vcom 
    SL_IOSTREAM_USART_VCOM 變更 RX TX 腳位輸入輸出設定

* Bootloader - NCP UART XMODEM 專案
    * Platform >> Bootloader >> Drivers >> Bootloader UART Driver 
    * SL_SERIAL_UART 變更 RX TX 腳位輸入輸出設定