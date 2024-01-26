---
title: python進度條tqdm
tags:
  - python
  - 裝B
  - 酷炫
categories:
  - python
date: '2024-01-24 14:17:00'
updated: '2024-01-26 02:38:09'
description: ''
abbrlink: 2ff23ae2
---
# 前言
在查網站時，無意間逛到[stackoverflow](https://stackoverflow.com/questions/276052/how-to-get-current-cpu-and-ram-usage-in-python/69511430#69511430)，有人用python的tqdm顯示記憶體和cpu的使用量，看起來有點酷炫，就來複製貼上玩一下吧，也可以用在python需要耗時的程式上，也不用一直print log看起來非常亂。
 <!-- more -->
 # tqdm
## pip 安裝 tqdm
``` bash
python3 -m pip install tqdm 
# or
pip3 install tqdm
```
## 複製程式碼
``` python
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
``` python
total = 300
for i in range(total):
    print(i)
```
假設任務有300個 ++ 就算完成 每次都print 一下
結果如下，就會非常凌亂，不夠炫炮
![](/images/20240125155025.png)
### 使用 tqdm
``` python
from tqdm import tqdm
from time import sleep
total = 300
progress = tqdm(total=total, desc='progress', position=1)

for i in range(total):
    progress.set_description(f'Processing: {i}')
    progress.update()
    sleep(0.1)

```

這裡也假設任務有300個 ++ 就算完成 每次都去 `update()` 進度條一下
`update()`就是去++ 進度條的值 因為 total 有設定 所以會自動算出完成度幾 percent
![](/images/20240125160318.gif)
套用在自己耗時的程式馬上，就不會印的很凌亂，非常直覺

##  再上範例2 多進程
![](/images/20240126102505.gif)
莫名其妙出現兩個 `ForkPoolWorker-4` 我認為是多進程的問題
> 需要考慮競爭的問題

``` python
from tqdm import tqdm
from multiprocessing import Pool, Lock
import multiprocessing
from time import sleep
def job(position):
    total = 300
    lock.acquire()
    progress = tqdm(total=total, desc='progress', position=position)
    lock.release()
    for i in range(total):
        lock.acquire()
        progress.set_description(f'Processing- {multiprocessing.current_process().name}: {i}')
        progress.update()
        lock.release()
        sleep(0.1)  

if __name__ == '__main__':
    # start 4 worker processes
    lock = Lock()
    pool = Pool(processes=4)
    pool.apply_async(job, (0,)) 
    pool.apply_async(job, (1,)) 
    pool.apply_async(job, (2,)) 
    pool.apply_async(job, (3,))  
    
    pool.close()
    pool.join()

```
![](/images/20240126102729.gif)
上一個鎖之後就沒出現，上面的情況了，大家可以把所拿掉玩玩看
## 總結
tqdm 為自己的程式看上去可以更專業，裡面還有一些參數沒有說明，大家可以去看文件了嘗試一下，另外多進程也要考慮資源共搶的情況
# 參考
- [stackoverflow](https://stackoverflow.com/questions/276052/how-to-get-current-cpu-and-ram-usage-in-python/69511430#69511430)
- [tqdm documentation](https://tqdm.github.io/)


