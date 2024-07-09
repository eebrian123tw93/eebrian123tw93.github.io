---
title: LDAP
tags:
  - openldap
  - phpldapadmin
date: '2024-04-16 15:45:00'
updated: '2024-05-06 07:27:02'
categories: []
description: ''
abbrlink: 51c3235
---
# LDAP
LDAP 為身份識別伺服器與內部檔案系統伺服器
其他服務如(gitlab、ipcamera、Samba....) 可以做綁定，
透過LDAP server 做驗證，將帳號密碼做整合

![](/images/20240416160514.png)
在驗證時，服務會向LDAP做驗證，權限控制還使由服務設定，
LDAP server 只會存資料而已
![](/images/20240416160738.png)
 <!-- more -->
 比如在ipcamera網頁登入的是一般操作者，LDAP 指會存這個這個user的等級
ipcamera 拿到這個user 的等級，回應相對應的權限服務 
# 系統說明
LDAP 基本上就是個樹狀目錄系統，可以根據需求增加節點，例如公司叫「example」，下面有兩個部門人資「People」及IT「Servers」，人資下面有個員工，如下圖示意：
![](/images/20240416161136.png)
值得注意的是，LDAP表示方式**都是由小到大**，下面登入的帳號，就是先說他是誰，後面才描述其他屬性。
# openldap + phpldapadmin
## docker_compose.yml
``` yaml
version: '3'

services:
  openldap:
    container_name: openldap
    image: osixia/openldap:latest
    ports:
      - "8389:389"
      - "8636:636"
    volumes:
      - ~/ldap/backup:/data/backup
      - ~/ldap/data:/var/lib/openldap
      - ~/ldap/config:/etc/openldap/slapd.d
      - ~/ldap/certs:/assets/slapd/certs
    command: [--copy-service,  --loglevel, debug]
    environment:
      - LDAP_DOMAIN=ipcamera.com
      - LDAP_BASE_DN=dc=ipcamera,dc=com
      - LDAP_ADMIN_PASSWORD=admin
  phpldapadmin:
    container_name: phpldapadmin
    image: osixia/phpldapadmin:latest
    ports:
      - "8081:80"
    environment:
      - PHPLDAPADMIN_HTTPS="false"
      - PHPLDAPADMIN_LDAP_HOSTS=openldap
    links:
      - openldap
    depends_on:
      - openldap

```
## 啟動服務
```bash
docker compose up -d
```
## 查看服務log
``` bash
docker logs openldap -f
```
# [window tcp 轉發](https://blog.csdn.net/cw_hello1/article/details/105968359)
因為在ldap server 在不同網段，所以需要轉發
``` bash
# 新增轉發規則
netsh interface portproxy add v4tov4 listenport=389 listenaddress=192.168.57.2 connectaddress=192.168.56.111 connectport=8389
netsh interface portproxy add v4tov4 listenport=8081 listenaddress=192.168.57.2 connectaddress=192.168.56.111 connectport=8081

# 顯示規則
netsh interface portproxy show all

# 重開機需要刪除後新增
netsh interface portproxy delete v4tov4 listenport=389 listenaddress=192.168.57.2
```

# phpldapadmin
## 訪問 http://192.168.56.111:8081/
![](/images/20240416144938.png)

## 登入
docker compose預設: 帳號cn=admin,dc=example,dc=org 密碼 admin
帳號: cn=admin,dc=ipcamera,dc=com
密碼: admin
![](/images/20240416145153.png)
## 建立entry
![](/images/20240416145244.png)
新增節點非常簡單，只要在要新增節點的上一層，點擊下面的 Create a child entry，就可以新增節點。
![](/images/20240416145259.png)
常用的是Generic 這些類別
![](/images/20240416145425.png)
簡單的權限管理為以下樹狀結構
```
groups(ou)
	admin(cn)
	operator(cn)
	user(cn)
users(ou)
	Brian Lu(cn)
	Vivian Chen(cn)
	...
```
![](/images/20240416145706.png)
![](/images/20240416152235.png)
# 指令集
## 登入 server
``` bash
docker exec -ti openldap bash
```
# 指令
``` bash
# 查詢數據庫訊息
ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b cn=config dn

# 使用admin查詢
ldapsearch -x -D "cn=admin,dc=ipcamera,dc=com" -w admin -b "dc=ipcamera,dc=com" "(objectClass=*)"

# 使用Brian Lu查詢
ldapsearch -x -D "cn=Brian Lu,ou=users,dc=ipcamera,dc=com" -w admin -b "dc=ipcamera,dc=com" "(objectClass=*)"

# 變更olc權限，使得Brian Lu可以查詢 docker compose預設密碼為config 
# {1}mdb為使用的database使用上面查詢數據庫訊息查詢
ldapmodify -x -D "cn=admin,cn=config" -w config <<EOF
dn: olcDatabase={1}mdb,cn=config
changetype: modify
add: olcAccess
olcAccess: {2}to dn.subtree="dc=ipcamera,dc=com"
  by dn="cn=Brian Lu,ou=users,dc=ipcamera,dc=com" read
  by dn="cn=Brian Lin,ou=users,dc=ipcamera,dc=com" read
  by * none
EOF

```

# docker openldap memberOf欄位
## 前提
建立 ou=users,dc=ipcamera,dc=com 和 ou=groups,dc=ipcamera,dc=com
docker 裡面 /container/service/slapd/assets/config/bootstrap/ldif/03-memberOf.ldif 為 groupOfUniqueNames
## add_user.ldif
``` bash
dn: cn=Brian Lin,ou=users,dc=ipcamera,dc=com
cn: Brian Lin
givenName: Brian
sn: Lin
uid: blin
uidNumber: 5000
gidNumber: 1000
userPassword: {SHA}M6XDJwA47cNw9gm5kXV1uTQuMoY=
homeDirectory: /home/john
mail: john.doe@example.com
objectClass: top
objectClass: posixAccount
objectClass: inetOrgPerson
```
##  add_unique_group.ldif
``` bash
dn: cn=admin,ou=groups,dc=ipcamera,dc=com
objectClass: top
objectClass: groupOfUniqueNames
cn: admin
uniqueMember: cn=Brian Lin,ou=users,dc=ipcamera,dc=com


dn: cn=oprator,ou=groups,dc=ipcamera,dc=com
objectClass: top
objectClass: groupOfUniqueNames
cn: oprator
uniqueMember: 


dn: cn=user,ou=groups,dc=ipcamera,dc=com
objectClass: top
objectClass: groupOfUniqueNames
cn: user
uniqueMember: 

```
## 下指令
``` bash
# 新增user和groups
ldapadd -x -D cn=admin,dc=ipcamera,dc=com -W -f add_user.ldif 
ldapadd -x -D cn=admin,dc=ipcamera,dc=com -W -f add_unique_group.ldif 

# 查詢 要帶上查詢的attribute=memberOf
ldapsearch -x -D "cn=Brian Lin,ou=users,dc=ipcamera,dc=com" -w admin -b "dc=ipcamera,dc=com" "cn=Brian Lin" memberOf 
```
# 參考
- [LDAP 入門](https://linux.vbird.org/somepaper/20050817-ldap-1.pdf)
- [使用 Docker 建置 LDAP 系統](https://chrislee0728.medium.com/%E4%BD%BF%E7%94%A8-docker-%E5%BB%BA%E7%BD%AE-ldap-%E7%B3%BB%E7%B5%B1-82370c53bc9f)
- [OpenLDAP 添加groupOfUniqueNames属性](https://chenzhonzhou.github.io/2020/11/04/openldap-tian-jia-groupofuniquenames-shu-xing/)
- [启用OpenLDAP的memberOf特性](https://www.linuxprobe.com/enable-memberof-openldap.html)
- [LDAP添加 memberOf 模块](https://blog.csdn.net/u011607971/article/details/119037796)
- [如何启用 OpenLDAP 的 memberOf 特性](https://mayanbin.com/posts/enable-memberof-in-openldap)
- [OpenLDAP 系列7 --- memberof & refint](https://www.cnblogs.com/eagle6688/p/16990393.html)
- 