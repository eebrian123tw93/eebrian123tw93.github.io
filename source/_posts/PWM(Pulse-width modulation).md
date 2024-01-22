---
title: PWM(Pulse-width modulation)
tags:
  - PWM
  - WS2812B
date: '2023-11-19 03:46:00'
updated: '2024-01-20 18:18:30'
categories: ''
description: ''
abbrlink: 68d2e719
---
# 介紹
![](/images/20231119023727.png)
是將脈波轉為類比信號的“一種技術”,利用在頻率不變的狀態下, 改變占空比大小, 使整體平均電壓值上升或下降, 藉此間歇性電壓及功率切換以節省能源及控制等效果.
# Period 週期
通常週期固定然後調整占空比

# Duty Circle(占空比)
100%代表有1個clock有百分之100是高電平 平均電壓為 5V*1 = 5V
75%代表有1個clock有百分之75是高電平 平均電壓為 5V*0.75 = 3.75V
25%代表有1個clock有百分之25是高電平 平均電壓為 3V*0.25 = 2.25V
0%代表有1個clock有百分之0是高電平 平均電壓為 3V*0 = 0V
藉由控制占空比可以控制平均電壓 控制功率 可以控制燈的亮度 馬達轉速


# 範例 WS2812B
## WS2812B PWM 如果是單純用PWM用IO的方法控制duty_cycle無法控制到每個bit
period = 800Khz 1/800000 = 1250 ns 
duty cycle 33% `1250*0.333 = 412.5` 0 碼
duty cycle 67% `1250*0.666 = 825` 1 碼
ws2812 复位信号为一个 不低于 50us

### 注册该pwm通道
```
cat /sys/class/pwm/pwmchip0/npwm
echo 0 > /sys/class/pwm/pwmchip0/export
```
### 設定週期(ns)
```
echo 1250 > /sys/class/pwm/pwmchip0/pwm0/period
```
### 設定duty cycle(ns)
```
echo 825 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
```

### 開燈
```
echo 825 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable
echo 0 > /sys/class/pwm/pwmchip0/pwm0/enable
```
### 關燈
```
echo 412 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable
echo 0 > /sys/class/pwm/pwmchip0/pwm0/enable
```



# 參考影片
* [2.1.1脈衝寬度(PWM)基本介紹](https://www.youtube.com/watch?v=LCejYewAgwA)
* [[Arduino 教學課程] 什麼！用pwm就能控制LED亮度？那個像是蚯蚓符號的東西原來有其他用意？來實作看看呼吸燈ㄅ 從零開始學Arduino #11](https://www.youtube.com/watch?v=27GkMk8ct0s)
