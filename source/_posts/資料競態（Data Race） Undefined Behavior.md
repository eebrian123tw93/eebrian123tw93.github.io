---
title: 資料競態（Data Race） Undefined Behavior
tags:
  - OptimizingCompiler
  - cplusplus
date: '2026-01-28 12:26:00'
updated: '2026-02-05 05:59:13'
categories: []
description: ''
keywords:
  - OptimizingCompiler
  - cplusplus
abbrlink: ed926797
Aliases:
---
# 資料競態（Data Race）如何解 — C++ 工程筆記
## 一、什麼是 Data Race（重點）

> **兩個以上執行緒，同時存取同一個記憶體位置，且至少一個是寫入，又沒有同步機制**  
> → **未定義行為（UB）**

在 C++：
- Data race **不是「邏輯錯誤」**
- 是 **語言層級的 UB**
- 編譯器可以假設「它不會發生」
## 二、為什麼 `-O2` 會放大 Data Race 問題
 <!-- more -->
 
在沒有同步的情況下，編譯器可以合法地：
- 將變數快取在暫存器
- 假設變數不會被其他執行緒修改
- 移除重複讀取
- 重排讀寫指令
結果：
```cpp
while (flag == 0) {}
```
在 `-O2` 下可能變成 **無限迴圈**。

## 三、錯誤範例（典型踩雷）

### ❌ 無同步的旗標
```cpp
int ready = 0;

// thread A
ready = 1;

// thread B
while (ready == 0) {}
```
問題：
- 無 mutex
- 無 atomic
- 無 memory order  
    → **Data race（UB）**
## 四、正確解法總覽（先看表）

|情境|正確解法|
|---|---|
|單一 flag / 計數|`std::atomic`|
|多個共享資料|`std::mutex`|
|生產者 / 消費者|`condition_variable`|
|只需一次初始化|`std::call_once`|
|高效能 lock-free|atomic + memory order|
## 五、解法 1：使用 `std::atomic`（最常見）

### 適用情境
- 單一變數
- 狀態旗標
- 計數器
### 範例
```cpp
#include <atomic>
std::atomic<bool> ready{false};
// thread A
ready.store(true, std::memory_order_release);
// thread B
while (!ready.load(std::memory_order_acquire)) {}
```
### 為什麼有效
- 保證：
    - 不會被快取
    - 不會被重排
    - 無 data race
## 六、解法 2：使用 `std::mutex`
### 適用情境
- 多個共享變數
- 複雜狀態
- 需要一致性
### 範例
```cpp
std::mutex m;
int data;

// thread A
{
  std::lock_guard<std::mutex> lock(m);
  data = 42;
}

// thread B
{
  std::lock_guard<std::mutex> lock(m);
  use(data);
}

```
### 優點
- 語意清楚
- 不需理解 memory order
- 安全性最高

## 七、解法 3：`condition_variable`（避免 busy wait）

### 適用情境
- 等待事件
- 生產者 / 消費者
```cpp
std::mutex m;
std::condition_variable cv;
bool ready = false;

// thread A
{
  std::lock_guard<std::mutex> lock(m);
  ready = true;
}
cv.notify_one();

// thread B
{
  std::unique_lock<std::mutex> lock(m);
  cv.wait(lock, [] { return ready; });
}
```

## 八、為什麼 `volatile` 不能解 Data Race（重要）
❌ **錯誤觀念**
```cpp
volatile int flag;
```
事實：
- `volatile`：
    - 只保證「每次都真的讀 / 寫」
    - 不提供同步
    - 不提供 memory order
- **不能防止 data race**
- 在 C++ 多執行緒下仍是 UB
👉 `volatile` 只適合：
- MMIO
- signal handler（有限情境）
## 九、常見錯誤修法（不要用）

|錯誤方式|問題|
|---|---|
|`volatile`|無同步、仍 UB|
|`sleep()` 等待|timing hack|
|假設 x86 strong ordering|不可攜|
|`if` + 無鎖|無 happens-before|
## 十、如何檢測 Data Race

### ✅ ThreadSanitizer（強烈建議）
`-fsanitize=thread`
- 幾乎能直接指出：
    - 哪個變數
    - 哪些 thread
    - 哪行 code
---
## 十一、工程判斷口訣
> **只要多執行緒共享資料  
> → 不是 atomic 就是 mutex  
> → 沒第三條路**

---
## 十二、總結
- Data race 是 **C++ 語言層級 UB**
- `-O2` 只會讓問題更明顯
- `volatile` 不是解法
- 正確工具：
    - `std::atomic`
    - `std::mutex`
    - `condition_variable`
- 唯一正確原則：  
    **所有共享資料都必須有同步語意**