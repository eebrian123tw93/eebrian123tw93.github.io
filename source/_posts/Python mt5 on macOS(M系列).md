---
title: Python mt5 on macOS(M系列)
tags:
  - MT5
  - mac
  - python
  - rpyc
  - vnc
  - wine
categories:
  - python
date: '2024-01-30 12:15:00'
updated: '2024-12-27 02:19:13'
description: ''
abbrlink: 3099daac
---
# 前言
接續之前{% post_link  "Python mt5 on linux" %}已經可以在docker順利啟動mt5．前面這篇通常是部署在Server上，但是開發的時候又是拿Mac開發，決定要嘗試看看，現在要來在Mac M1 上面啟動，因為如果按造{% post_link  "Python mt5 on linux" %}的話，會在docker起 vnc ，但好像沒有arm的版本，所以這次就不在docker上啟動了
 <!-- more -->
 
如果是linux 請參考{% post_link  "Python mt5 on linux" %}

# 需求
- python3.9 
- MacOS M系列

# 開始吧
## 上腳本
感謝 [MetaTrader5-Docker-Image](https://github.com/gmag11/MetaTrader5-Docker-Image/blob/main/Metatrader/start.sh)腳本幫助
``` bash
brew tap gcenx/wine
brew install --cask --no-quarantine wine-crossover
if [ ! -e ~/.wine ]; then
  wineboot --init
fi

# Configuration variables
mt5file=~/.wine/drive_c/"Program Files/MetaTrader 5/terminal64.exe"
wine_executable="wine"
metatrader_version="5.0.36"
mt5server_port="8001"
mono_url="https://dl.winehq.org/wine/wine-mono/8.0.0/wine-mono-8.0.0-x86.msi"
python_url="https://www.python.org/ftp/python/3.9.0/python-3.9.0.exe"
mt5setup_url="https://download.mql5.com/cdn/web/metaquotes.software.corp/mt5/mt5setup.exe"

# Function to display a graphical message
show_message() {
    echo $1
}

# Function to check if a dependency is installed
check_dependency() {
    if ! command -v $1 &> /dev/null; then
        echo "$1 is not installed. Please install it to continue."
        exit 1
    fi
}

# Function to check if a Python package is installed
is_python_package_installed() {
    python3 -c "import pkg_resources; exit(not pkg_resources.require('$1'))" 2>/dev/null
    return $?
}

# Function to check if a Python package is installed in Wine
is_wine_python_package_installed() {
    $wine_executable python -c "import pkg_resources; exit(not pkg_resources.require('$1'))" 2>/dev/null
    return $?
}

# Check for necessary dependencies
check_dependency "curl"
check_dependency "$wine_executable"

# Install Mono if not present
if [ ! -e ~/.wine/drive_c/windows/mono ]; then
    show_message "[1/7] Downloading and installing Mono..."
    if [ ! -f ~/.wine/drive_c/mono.msi ]
    then
      wget -O ~/.wine/drive_c/mono.msi  $mono_url
    fi
    WINEDLLOVERRIDES=mscoree=d $wine_executable msiexec /i ~/.wine/drive_c/mono.msi /qn
    show_message "[1/7] Mono installed."
else
    show_message "[1/7] Mono is already installed."
fi


if [ -e "$mt5file" ]; then
    show_message "[2/7] File $mt5file already exists."
else
    show_message "[2/7] File $mt5file is not installed. Installing..."

    # Set Windows 10 mode in Wine and download and install MT5
    $wine_executable reg add "HKEY_CURRENT_USER\\Software\\Wine" /v Version /t REG_SZ /d "win10" /f
    show_message "[3/7] Downloading MT5 installer..."
    if [ ! -e ~/.wine/drive_c/mt5setup.exe ]; then
      wget -O ~/.wine/drive_c/mt5setup.exe $mt5setup_url
    fi
    show_message "[3/7] Installing MetaTrader 5..."
    $wine_executable ~/.wine/drive_c/mt5setup.exe "/auto"

fi

# Recheck if MetaTrader 5 is installed
if [ -e "$mt5file" ]; then
    show_message "[4/7] File $mt5file is installed. Running MT5..."
    $wine_executable "$mt5file" &
else
    show_message "[4/7] File $mt5file is not installed. MT5 cannot be run."
fi


# Install Python in Wine if not present
if ! $wine_executable python --version 2>/dev/null; then
    show_message "[5/7] Installing Python in Wine..."
    if [ ! -f ~/.wine/drive_c/python-3.9.0.exe ]; then
      wget -O ~/.wine/drive_c/python-3.9.0.exe $python_url
    fi
    $wine_executable ~/.wine/drive_c/python-3.9.0.exe /quiet InstallAllUsers=1 PrependPath=1

    show_message "[5/7] Python installed in Wine."
else
    show_message "[5/7] Python is already installed in Wine."
fi

# Upgrade pip and install required packages
show_message "[6/7] Installing Python libraries"
$wine_executable python -m pip install --upgrade --no-cache-dir pip
# Install MetaTrader5 library in Windows if not installed
show_message "[6/7] Installing MetaTrader5 library in Windows"
if ! is_wine_python_package_installed "MetaTrader5==$metatrader_version"; then
    $wine_executable python -m pip install --no-cache-dir MetaTrader5==$metatrader_version
fi
# Install mt5linux library in Windows if not installed
show_message "[6/7] Checking and installing mt5linux library in Windows if necessary"
if ! is_wine_python_package_installed "mt5linux"; then
    $wine_executable python -m pip install --no-cache-dir mt5linux
fi

# Install mt5linux library in Linux if not installed
show_message "[6/7] Checking and installing mt5linux library in Linux if necessary"
if ! is_python_package_installed "mt5linux"; then
    pip install --upgrade --no-cache-dir mt5linux
fi

# Install pyxdg library in Linux if not installed
show_message "[6/7] Checking and installing pyxdg library in Linux if necessary"
if ! is_python_package_installed "pyxdg"; then
    pip install --upgrade --no-cache-dir pyxdg
fi

# Start the MT5 server on Linux
show_message "[7/7] Starting the mt5linux server..."
python3 -m mt5linux --host 0.0.0.0 -p $mt5server_port -w $wine_executable python.exe &

# Give the server some time to start
sleep 5

# Check if the server is running
if  lsof -n -i:$mt5server_port | grep LISTEN > /dev/null; then
    show_message "[7/7] The mt5linux server is running on port $mt5server_port."
else
    show_message "[7/7] Failed to start the mt5linux server on port $mt5server_port."
fi


```
這個腳本會判斷是否已經安裝，第一次安裝會比較久一點
## 建立腳本
大家可以建立一個`start.sh` 的腳本 將上面的腳本程式複製進去
``` bash
# 建立腳本
touch start.sh
# 編輯腳本 把上面的整坨貼進去
vim start.sh
# 給 start.sh 執行權限
chmod +x start.sh
# 執行腳本
bash start.sh
```
## 腳本會自動安裝下面的套件 (根據設備能力不同時間也有所不同第一次啟動會花比較久時間 )
- wine (用來模擬window的環境)
- mone (.NET框架)
- python (在wine裡面安裝python)
- mt5 (在wine裡面安裝mt5)
- python packages (所需套件)
## 安裝完後 會直接開啟 MT5
![](/images/20240131121021.png)
## 在自己的python上面安裝 mt5linux
``` bash
python -m pip install mt5linux==0.1.9
```
## 測試連線
``` python
from mt5linux import MetaTrader5
mt5 = MetaTrader5(host='localhost', port=8001)
mt5.initialize()
print(mt5.version())
```
# 附上流程
![](/images/20240131120931.png)
- GUI 顯示 wine 裡面的 MT5
- 從自己python 經過8001port 透過 rpyc 跟 wine 的 python 溝通

# 結論
感謝[MetaTrader5-Docker-Image](https://github.com/gmag11/MetaTrader5-Docker-Image/blob/main/Metatrader/start.sh)腳本幫助，雖然沒用docker啟動，有可能會污染環境，但是在M系列上面順利執行，這樣開發也可以順利開發了。

# 參考
- [MetaTrader5-Docker-Image](https://github.com/gmag11/MetaTrader5-Docker-Image)
- [python_metatrader5](https://www.mql5.com/zh/docs/python_metatrader5)
- [rpyc](https://rpyc.readthedocs.io/en/latest/)
- [mt5linux](https://pypi.org/project/mt5linux/)
- [Mac M1 安装 wine 过程](https://blog.csdn.net/rjc_lihui/article/details/124053424)