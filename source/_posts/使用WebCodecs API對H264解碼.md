---
title: 使用WebCodecs API對H264解碼
tags:
  - WebCodecsAPI
  - H264
date: '2024-01-22 10:16:00'
updated: '2024-01-23 16:29:38'
categories: ''
description: ''
abbrlink: ef8abaf3
---
# 介紹
WebCodecs API 是可以讓開發者直接使用硬體資源，拿來處理影片或聲音，在還沒個出來之前大多的實作方法是用 webassembly 或是 原生 JavaScript 處理，這些方法需要多加載另外的資源，而且也無法使用到像是GPU的資源，使得效率低。

# 流程
![](/images/20240122092416.png)
1. `H264NALu` 轉成 `EncodedVideoChunk` 物件
2. 交給 `VideoDecoder` 做解碼 
3. 產物為 `VideoFrame` 會後交給 `Canvas` 渲染
其中 `EncodedVideoChunk` `VideoDecoder` `VideoFrame` 為  WebCodecs API 內物件
# 上程式
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
	codec: "avc1.4d401e",
	codedWidth: 1920,
	codedHeight: 1080,
};

const decoder = new VideoDecoder(init);
decoder.configure(config);
```
`handleFrame`為decoder解碼完產生`VideoFrame`的callback方法，產出的每一幀都會調用這個方法，之後就是交給 `Canvas` 渲染
`init`和`config`使用配置`VideoDecoder`的參數

> 使用 `await VideoDecoder.isConfigSupported(config)`
>  檢查`config` 是否支援
### 參考
[VideoDecoder](https://developer.mozilla.org/en-US/docs/Web/API/VideoDecoder)

## 將 H264NALU轉換成`EncodedVideoChunk`
``` javascript
const init = {
  type: unit.type == 5 ? 'key' : 'delta',
  data: h26XBuffer,
  timestamp: timestamp,
};
chunk = new EncodedVideoChunk(init);
decoder.decode(chunk);
```
`EncodedVideoChunk` 初始化要分辨這一幀是 `key` 或 `delta`
`key`:  IDR幀 關鍵幀 
`delta`: P幀 或 B幀
產生出來的 `chunk` 就可以餵到`decoder`去解碼了
### 參考
- [EncodedVideoChunk](https://developer.mozilla.org/en-US/docs/Web/API/EncodedVideoChunk/EncodedVideoChunk)
- {% post_link  "H264基礎知識" %}

## 將`Video`渲染到`Canva`上
``` javasctipt
function handleFrame(frame) {
	const bitmap = await createImageBitmap(frame);
	ctx.drawImage(bitmap, 0, 0);
	frame.close();
}
```

# 總結
上述程式帶大家大致過一下WebCodecs API的流程，具體還有很多細節沒有說明，大家再小心服用
# 參考
- [WebCodecs对音视频进行编码解码](https://chenng.cn/posts/WebCodecs%E5%AF%B9%E9%9F%B3%E8%A7%86%E9%A2%91%E8%BF%9B%E8%A1%8C%E7%BC%96%E7%A0%81%E8%A7%A3%E7%A0%81/)
- [WebCodecs API](https://developer.mozilla.org/en-US/docs/Web/API/WebCodecs_API)