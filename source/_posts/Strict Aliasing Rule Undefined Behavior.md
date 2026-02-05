---
title: Strict Aliasing Rule Undefined Behavior
tags:
  - OptimizingCompiler
  - cplusplus
date: '2026-01-28 12:36:00'
updated: '2026-02-05 05:46:52'
categories: []
description: ''
keywords:
  - OptimizingCompiler
  - cplusplus
abbrlink: 4ccde5ec
Aliases:
---
## 一、問題核心回顧

> **Strict aliasing rule 規定：不同型別的 pointer / reference 不得指向同一物件。**

違反此規則：
- 屬於 **未定義行為（UB）**
- `-O2 / -O3` 會合法地產生錯誤行為
- `-O0` 正常只是「剛好沒被最佳化」
> 「這段程式碼永遠不會出現」，因此優化器可能 **不會像你預期那樣正確讀/寫記憶體**。
---
 <!-- more -->
 ## 二、什麼情況一定要「解」
只要出現以下任一狀況，就**必須處理**：
- `(T&)obj`，且 `T ≠ obj 的實際型別`
- pointer cast 做 type-punning
- `reinterpret_cast<T&>`
- `-O0` 正常、`-O2` 錯亂
- 加 `volatile` 才「正常」
---

## 三、Strict Aliasing 的正確解法總覽

|解法|是否推薦|說明|
|---|---|---|
|型別一致|⭐⭐⭐⭐⭐|最佳、零 UB|
|不回傳 reference|⭐⭐⭐⭐⭐|安全、好維護|
|`memcpy` type-punning|⭐⭐⭐⭐|合法轉型|
|`union`（有限）|⭐⭐|需小心 active member|
|`-fno-strict-aliasing`|⭐|不推薦，僅過渡|

---

## 四、解法 1：型別一致（最正確）

### 問題寫法
```cpp
time_base_seconds& secs() {
  return (time_base_seconds&)tv.tv_sec;
}
```
正確寫法
```cpp
time_t& secs() {
  return tv.tv_sec;
}

```
### 原則
> **用什麼型別宣告，就用什麼型別存取**

## 五、解法 2：不要回傳 reference（強烈推薦）

### 問題點
- reference 綁定錯誤型別
- 容易隱含 UB
### 安全設計
```cpp
time_base_seconds secs() const {
  return tv.tv_sec;
}

void set_secs(time_base_seconds v) {
  tv.tv_sec = v;
}
```
### 優點
- 無 aliasing 問題
- 最佳化友善
- API 清楚
## 六、解法 3：使用 `memcpy`（合法 type-punning）

### 適用情境
- 需要轉換位元表示
- 不能改結構定義
```cpp
time_base_seconds dst; 
std::memcpy(&dst, &tv.tv_sec, sizeof(dst));
```
### 說明
- C++ 標準明確允許
- 編譯器會最佳化成單一 load
七、解法 4：`union`（有限且需非常小心）
```cpp
union U {
  int i;
  float f;
};
```
限制：
- 只能存取 **目前 active member**
- 跨型別讀取在 C++ 中仍有限制
- **不推薦在新程式使用**
八、解法 5：關閉 strict aliasing（最後手段）
```bash
-fno-strict-aliasing
```
### 為什麼不推薦
- 全專案效能下降
- 掩蓋設計錯誤
- 不可攜
- 新 compiler / LTO 仍可能出問題

九、哪些東西「不能解」strict aliasing

|方法|原因|
|---|---|
|`volatile`|不影響 alias 規則|
|`reinterpret_cast`|明確寫出 UB|
|指標大小相同|規範不看大小|
|x86 強排序|與 alias 無關|

## 十、重構時的安全準則（工程版）
1. **永遠不要回傳 cast 過的 reference**
2. **API 以值為界線，內部再轉型**
3. **型別不一致 → 用 `memcpy`**
4. **`reinterpret_cast` 視為紅色警告**
5. **`-O2` 是基本測試，不是選項**
---

## 十一、Code Review 檢查清單

-  是否有 `(T&)obj`？
-  是否用 pointer cast 存取不同型別？
-  accessor 是否回傳錯誤型別 reference？
-  是否依賴 `-fno-strict-aliasing`？
-  `-O2` 行為是否與 `-O0` 一致？

---

## 十二、總結
- Strict aliasing 是 **效能與正確性的交換前提**
- 違反規則的程式：
    - 行為不被保證
    - `-O2` 出問題是正常
- 唯一根本解法：  
    **修正型別設計，使存取方式合法**

> **不要和編譯器鬥智，讓型別正確即可。**