---
title: 20250214小日常-WebSocket IDR Frame delay
tags:
  - H264
  - H265
  - IDR幀
categories:
  - 小日常
date: '2025-02-14 18:22:00'
updated: '2025-02-19 01:21:13'
description: ''
---
今天在除錯時，發現有時兩路websocket LiveView 開下去，就造成卡頓，到websocket的流程一看原來裡面有一個機制，
是會去檢查當前拿到的Frame Number 和 encode 最新的 Frame Number ，如果差距太大（例如: 兩個fps之類的），
代表使用者看到的畫面是好幾秒前的，這時會再下一幀強制插 IDR  Frame ，讓使用者看到最新的，這個機制設計沒有什麼問題，
 <!-- more -->
 但今天遇到的websocket 是單線程，且如果 IDR  Frame 拿的比較慢，會造成大家都在拿 IDR  Frame，形成 hungry。

原本的解法是，多加一點條件限制拿 IDR  Frame 的條件
1. 如果當前是 IDR  Frame ，下一幀就不拿 IDR  Frame
2. 大家在一秒內，總共只能一次 IDR  Frame，其餘的就忽略
3. 拉大兩個 Frame Number 的距離 (3~4倍fps)

再來就是不要 插 IDR  Frame 直接取最近的 I Frame

重點來了！！上面的解法都只是解決表面而已
感謝前輩的幫忙與教導 其實是要找到真正的根本原因 (Root Cause)
1. 是否是多線程干擾
2. 打印出 Frame Number 和 拿 frame 的結果
最後排出多線程，並且打印打印出 Frame Number 和 拿 frame 的結果
發現拿 IDR  Frame 都拿超時，但這不應該，因為 IDR  Frame 速度應該都蠻快的
最後發現底層強制 拿 IDR  Frame的索引錯了，導致沒有人去拿 IDR  Frame，自然而然都超時，

中間一度想往網路方向去解，回頭想想才兩路 websocket 根本不可以網路送不動，算一算根本還有很大的頻寬可以用。
下次如過要解這類問題還要是先搞懂，整體是單線程還是多線程，打印 Frame Number 和 thread Id 還有 result
相信會越解越快，更快找出根本原因 (Root Cause)。










