---
title: 編譯器最佳化導致問題（UB 筆記）
tags:
  - OptimizingCompiler
  - cplusplus
date: '2026-01-28 11:37:00'
updated: '2026-02-05 05:46:52'
categories: []
description: ''
keywords:
  - OptimizingCompiler
  - cplusplus
abbrlink: f331a109
Aliases:
---
# 編譯器最佳化導致問題筆記（Undefined Behavior）
## 一、核心觀念

> **編譯器最佳化不是「照程式碼跑」，而是「照語言規範跑」。**

在 C / C++ 中：

- **未定義行為（UB）代表語言不保證任何結果**
- 編譯器可以假設 **UB 不存在**
- 因此在 `-O2` / `-O3` 下，編譯器可以：
    - 重排指令
    - 刪除看似「不可能發生」的程式碼
 <!-- more -->
     - 快取變數、不再重讀
---

## 二、為什麼 `-O2` 會導致問題

### 關鍵假設 ***程式沒有 UB***

基於此假設，編譯器可以合法地做出以下行為：
- 如果某條件在「無 UB 前提下永遠為真 / 假」→ 直接刪除分支
- 如果某變數理論上不會被改變 → 永遠使用快取值
- 如果某狀態在規範下不可能出現 → 不做防禦檢查

👉 結果就是：
- `-O0` 看似正常
- `-O2` 行為錯亂或邏輯消失

## 三、常見會被 `-O2` 放大的 UB 類型

### 1️⃣ 使用未初始化變數
```cpp
int x;
if (x > 0) { ... }   // ❌ UB
```
- `x` 的值未定義
- 編譯器可以假設：
    `這段程式永遠不會走到這裡`
- 分支可能被整段刪除
### 2️⃣陣列越界
```cpp
int a[10];
a[10] = 1;   // ❌ UB
```
- 編譯器假設所有存取都在合法範圍內
- 越界後的行為不被保證
- `-O2` 下可能：
    - 覆寫不相關變數
    - 產生不可預期結果
### 3️⃣ 解引用 NULL / 野指標
```cpp
int* p = nullptr;
*p = 1;   // ❌ UB
```
- 編譯器可以假設：`指標永遠不是 NULL`
- 因此：
    - NULL 檢查可能被移除
    - 導致 crash 或邏輯錯誤
### 4️⃣ signed overflow（非常常見）
```cpp
int x = INT_MAX;
x += 1;   // ❌ UB
```
- **signed overflow 是 UB**
- 編譯器可假設：`overflow 不會發生`
- 因此：
    - `if (x < 0)` 可能被刪除
    - 數學邏輯被重排
### 5️⃣ 資料競態（Data Race）
```cpp
int flag = 0;
// thread A
flag = 1;
// thread B
while (flag == 0) {}
```
- 無同步的多執行緒存取
- 屬於 UB
- `-O2` 下：
    - `flag` 可能被快取
    - 迴圈變成無限迴圈
- 解決辦法: {% post_link  "資料競態（Data Race） Undefined Behavior" %}
### 6️⃣ `memcpy` 用在重疊記憶體
```cpp
memcpy(buf + 1, buf, n);   // ❌ UB
```
- `memcpy` 明確要求 **來源與目的不可重疊**
- 編譯器可以：
    - 使用向量化
    - 重排 copy 順序
- 導致資料毀損
正確做法：
```cpp
memmove(buf + 1, buf, n);  // ✅
```

### 7️⃣ {% post_link  "Strict Aliasing Rule Undefined Behavior" %}

## 四、`-O2` 下的典型錯誤現象
- 條件判斷被「消失」
- 看似必要的程式碼被刪除
- 變數值突然不更新
- `volatile` 加了才「正常」
- debug / release 行為完全不同
---
## 五、為什麼 `volatile` 只能暫時「救火」
- `volatile` 只影響：
    - 是否允許快取
    - 是否可消除存取
- **不能修正 UB**
- 換編譯器 / 換版本 / 換平台 → 仍可能出錯
## 六、實務除錯與預防建議

### ✅ 使用 Sanitizer（最有效）
```bash
-fsanitize=address,undefined
-fsanitize=thread
```
### ✅ 比較不同最佳化等級
```cpp
-O0 / -O1 / -O2
```

### ✅ 避免依賴 UB 的「剛好行為」
- 不用未初始化值
- 嚴格檢查邊界
- 使用正確同步原語
- 使用正確 API（`memmove` vs `memcpy`）

## 七、工程判斷準則（重點）
> **只要 `-O0` 正常、`-O2` 出錯  
> 幾乎可以直接判定：程式中存在 UB**

## 八、總結
- 編譯器最佳化不會製造 bug
- **UB 才是問題來源**
- `-O2` 只是揭露原本就不被保證的行為
- 唯一根本解法：  
    **修正程式邏輯，使其符合語言規範**