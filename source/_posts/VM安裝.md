---
title: VM安裝
date: 2024-01-20 13:16:07
updated: {{ date }}
tags:
  - vm
  - VirtualBox
  - samba
  - ssh-server
categories:
description:
---
## Install Guest Additions 
`sudo apt-get install gcc make perl -y`
![](/images/20231129152725.png)
插入 Guest Additions CD 映像
![](/images/20231129152840.png)
點擊Run
![](/images/20231129152907.png)
輸入密碼
![](/images/20231129152934.png)
運行中
![](/images/20231129153043.png)
無報錯 安裝成功後 reboot 可以縮放 剪貼簿 公用資料夾
### 參考
- [简单解决Please install the gcc make perl packages from your distribution问题](https://blog.csdn.net/bell_love/article/details/105401852)

## 共用資料夾
![](/images/20231129154247.png)
點擊共用資料夾設定
![](/images/20231129154432.png)
點擊加號
![](/images/20231129154530.png)
輸入要掛載的資料夾 和 掛載點 點擊確認
![](/images/20231129154609.png)
成功會出現在掛載點
### 參考
- [[Linux / Windows] VirtualBox 共用資料夾與雙向剪貼簿之權限設定](https://www.jinnsblog.com/2021/05/virtualbox-shared-folder-permission-setting.html)

## 加入憑證

```bash
sudo cp my-ca-certificate.crt /usr/local/share/ca-certificates/
cd /usr/local/share/ca-certificates/
sudo chmod 644 my-ca-certificate.crt
sudo  update-ca-certificates
```
### 參考
- [如何為LINUX, WINDOWS容器加入憑證?](https://blog.kkbruce.net/2020/01/linux-windows-container-add-cert.html)

## Samba 
![](/images/20231129163310.png)
新增網路介面卡2 僅限主機 介面卡
```
apt-get install samba -y
smbpasswd -a root #設定root 密碼
vim /etc/samba/smb.conf 
```
smb.conf 加入底下的設定 使用root 登入 /media/new_drive 需要存在
```
[brian]
   comment = new_drive
   public = yes
   path = /media/new_drive
   guest ok = yes
   read only = no
   browseable = yes
   create mask = 777
   directory mask = 777
   writable = yes
   valid users = root
```
![](/images/20231129160349.png)
`samba restart` or `/etc/init.d/smbd restart`
![](/images/20231129160659.png)
本機右鍵 連線網路磁碟機
![](/images/20231129160740.png)
輸入上面資訊 `\\192.168.56.108\brian` brian 為設定檔`[brian]`  
之後 按完成 需要輸入密碼 

 ### 參考
- [virtualbox虛擬機ubuntu使用samba圖文教學](https://hollyqood.wordpress.com/2016/12/08/virtualbox%e8%99%9b%e6%93%ac%e6%a9%9fubuntu%e4%bd%bf%e7%94%a8samba%e5%9c%96%e6%96%87%e6%95%99%e5%ad%b8/)

## SSH Server
```
apt-get install ssh -y
vim /etc/ssh/sshd_config
```
編輯這些參數
```
Port 22
PermitRootLogin yes
PasswordAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile	.ssh/authorized_keys
```
(可選)如果用root登入需要設定root密碼
`sudo passwd`
重啟ssh
`/etc/init.d/ssh restart`
### 參考
- [Ubuntu 安裝與設定 ssh server](https://www.ewdna.com/2012/06/ubuntu-ssh-server.html)
- [What is the password for ``ssh root@localhost``?](https://askubuntu.com/questions/171521/what-is-the-password-for-ssh-rootlocalhost)

## zsh + oh my zsh
``` bash
apt install zsh -y
sudo sh -c "echo $(which zsh) >> /etc/shells"
chsh -s /bin/zsh
apt install git curl -y

#oh my zsh
curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh | sh -s

# plugin zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

vim ~/.zshrc
# plugins=(git zsh-autosuggestions) plugin添加zsh-autosuggestions

source ~/.zshrc

# plugin zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

vim ~/.zshrc
# plugins=(git zsh-syntax-highlighting) plugin添加zsh-syntax-highlighting
source ~/.zshrc
```
### 參考
- [如何在Linux作業系統上使用 Z Shell (Zsh)？](https://magiclen.org/zsh/)
- [zsh 命令自动补全插件 zsh-autosuggestions 安装和配置](https://www.jianshu.com/p/43c1b6e40c69)


`apt-get iputils-ping net-tools traceroute vim` 

``` bash
echo "{"bip":"172.15.0.1/16"}" > /etc/docker/daemon.json
systemctl restart docker.service
```