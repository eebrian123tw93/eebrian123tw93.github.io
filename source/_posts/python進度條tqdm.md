---
title: python進度條tqdm
tags:
  - python
  - 裝B
  - 酷炫
categories:
  - python
date: '2024-01-24 14:17:00'
updated: '2024-01-25 02:07:38'
description: ''
abbrlink: 2ff23ae2
---
# 前言
在查網站時，無意間逛到[stackoverflow](https://stackoverflow.com/questions/276052/how-to-get-current-cpu-and-ram-usage-in-python/69511430#69511430)，有人用python的tqdm顯示記憶體和cpu的使用量，看起來有點酷炫，就來複製貼上玩一下吧，也可以用在python需要耗時的程式上，也不用一直print log看起來非常亂。
 <!-- more -->
 # tqdm
## pip 安裝 tqdm
```
python3 -m pip install tqdm 
# or
pip3 install tqdm
```
## 複製程式碼
```
from tqdm import tqdm
from time import sleep
import psutil

with tqdm(total=100, desc='cpu%', position=1) as cpubar, tqdm(total=100, desc='ram%', position=0) as rambar:
    while True:
        rambar.n=psutil.virtual_memory().percent
        cpubar.n=psutil.cpu_percent()
        rambar.refresh()
        cpubar.refresh()
        sleep(0.5)
```
## 看看成果
![](/images/20240124113531.gif)
是不是非常酷炫 
`tqdm(total=100, desc='cpu%', position=1) as cpubar` 
`tqdm(total=100, desc='ram%', position=0) as rambar`
個別宣告兩個進度條 `position`是顯示在第幾條位置
然後用`.n`更新值，再用 `.refresh()`去刷新terminal

## 再上範例1 單進程
```
from tqdm import tqdm
total = 300
for i in range(total):
    print(i)


from tqdm import tqdm
from time import sleep
total = 300
progress = tqdm(total=total, desc='progress', position=1)

for i in range(total):
    progress.set_description(f'Processing: {i}')
    progress.update()
    sleep(0.1)

```
##  再上範例2 多進程
> 需要考慮競爭的問題


# 參考
[stackoverflow](https://stackoverflow.com/questions/276052/how-to-get-current-cpu-and-ram-usage-in-python/69511430#69511430)



