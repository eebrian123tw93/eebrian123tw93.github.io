---
title: webrtc
date: '2023-08-07 01:04:00'
updated: '2024-12-27 02:31:02'
categories: []
description: ''
abbrlink: af856b41
Aliases: null
Tags: null
tags:
  - WebRTC
---
# env
## Dockerfile
``` Dockerfile
FROM ubuntu:22.04

# 設置非互動模式，更新包管理器並安裝必要軟體
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    git curl python3 vim xz-utils lsb-release sudo file && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates && \
    update-ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# 克隆 depot_tools 並設置 PATH
RUN git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git /opt/depot_tools

# 設置環境變數
ENV PATH="/opt/depot_tools:$PATH"

WORKDIR /opt
```
## docker-compose.yml
```yml
services:
  webrtc-env:
    container_name: webrtc-env
    build:
      context: .
      dockerfile: ./Dockerfile
    platform: linux/amd64
    volumes:
      - /media/hdd/webrtc:/opt/webrtc
    restart: always
    tty: true
```
# Note

 <!-- more -->
 ```bash


mkdir webrtc-checkout
cd webrtc-checkout
fetch --nohooks webrtc
gclient sync
cd src
./build/install-build-deps.sh
gn gen out/Default
ninja -C out/Default
```
# Android
```bash
cd ../
mkdir webrtc-android 
cd webrtc-android 
fetch --nohooks webrtc_android
gclient sync
cd src
./build/install-build-deps.sh

#產出來的檔案在/src/libwebrtc.aar
./tools_webrtc/android/build_aar.py

# 20241225 不行使用
gn gen out/Debug --args='target_os="android" target_cpu="arm"'
# 參考 build_aar.py /tmp/tmp_2yg5wol/armeabi-v7a# cat args.gn
gn gen out/Debug --args='target_os="android" target_cpu="arm" enable_rust=true enable_rust_cxx=true'

ninja -C out/Debug
#只build
```
# iOS
```bash
cd ../
mkdir webrtc-ios
cd webrtc-ios
fetch --nohooks webrtc_ios
gn gen out/ios_64 --args='target_os="ios" target_cpu="arm64" target_environment="device"'
```

---

- [webrtc全平台下载编译步骤详细说明](https://www.cnblogs.com/ai-tuge/p/14489588.html)
- [WebRTC development](https://webrtc.googlesource.com/src/+/main/docs/native-code/development/README.md#getting-the-code)
- [WebRTC development android](https://webrtc.googlesource.com/src/+/main/docs/native-code/android/)
- [WebRTC development ios](https://webrtc.googlesource.com/src/+/main/docs/native-code/ios/)
- [编译源码](https://webrtc.mthli.com/basic/webrtc-compilation/)
- 