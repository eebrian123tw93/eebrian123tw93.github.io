---
title: 使用WebCodecs API對H265解碼
tags:
  - H265
  - WebCodecsAPI
categories:
  - web
date: '2024-01-25 10:43:00'
updated: '2025-02-19 08:51:01'
description: ''
abbrlink: 49fdb147
keywords:
  - H265
  - WebCodecsAPI
---
# 前言
這邊接續{% post_link  "使用WebCodecs API對H264解碼" %}，現在處理H265格式，這裡的只是將程式碼有一點小改動

# 差異
## 建立 VideoDecoder
```javascript
function handleFrame(frame) {
	// render by canvas
}

const init = {
	output: handleFrame,
	error: (e) => {
	  console.log(e.message);
	},
};

const config = {
	codec: "hev1.1.6.L123.B0",
	codedWidth: 1920,
	codedHeight: 1080,
};

const decoder = new VideoDecoder(init);
decoder.configure(config);
```
這裡的不同的是  H264 和 H265 使用的 codec 不同
> 使用 `await VideoDecoder.isConfigSupported(config)`
 <!-- more -->
 > 檢查`config` 是否支援

H265目前在chrome上只支援硬解，可以參考底下的連結看看硬體是否支援，
如果硬體不行也可以下載支援H265軟硬解的 Chromium 試試看
附上採坑日常 {% post_link  "20240123小日常" %}
## 參考
- [enable-chromium-hevc-hardware-decoding](https://github.com/StaZhu/enable-chromium-hevc-hardware-decoding)
- [解决chrome浏览器不能播放H265....）DXVA Checker、GPU-Z](https://blog.csdn.net/Dontla/article/details/132752490?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1-132752490-blog-135105844.235^v40^pc_relevant_anti_vip&spm=1001.2101.3001.4242.2&utm_relevant_index=4)
- [HEVC Video Test](https://codepen.io/danm8675123/pen/abNavqG)
- [DXVAChecker](https://bluesky-soft.com/en/DXVAChecker.html)

## 將 H265NALU轉換成`EncodedVideoChunk`
``` javascript
const init = {
  type: unit.type === 19 ? 'key' : 'delta',
  data: h26XBuffer,
  timestamp: timestamp,
};
chunk = new EncodedVideoChunk(init);
decoder.decode(chunk);
```
`EncodedVideoChunk` 初始化要分辨這一幀是 `key` 或 `delta` (使用NALU的unit_type)
這裡的不同的是 NALU的 unit_type H264 和 H265 代表的不一樣，算出unit_type的方式也有所不同，
大家要非常的小心

# 總結
H265 和 H264 使用 WebCodecs 的流程大致上沒有太大的區別，需要注意的是H265對於硬體的要求比較高，當今天硬體不支援時，該如何判斷，該如何處理，還有如何解析出H264 H265 unit_type，這也需要工程師閱讀資料得知了- 可以參考[HEVC/H265幀類型判斷及NALU TYPE介紹](https://www.twblogs.net/a/5c6d2817bd9eee5c86dcca4a)
