---
title:  實作 Nginx 負載平衡 load balance
created: 星期一 17日 四月 2023 22:33
Last modified:  NaN
Aliases: 
Tags:  #DevOps 
---
# 實作 Nginx 負載平衡 load balance

# Summery

## 三種 load balancing 方法
1. round-robin：預設值，會將請留輪流平均分配到每台伺服器上
2. lest-connected：會將請求分配到目前連接數最少的伺服器上
3. ip-hash：利用 hash-function 來決定使用者要被分配到的伺服器，此方法可以達到同一個使用者 (IP address) 每次連結的伺服器都是相同的

## 設定不同付載平衡模式
```
http {
	upstream myapp {
		ip_hash; #付載平衡模式 round-robin(default 不寫), lest_conn , ip-hash
		server srv1.example.com;
		server srv2.example.com;
		server srv3.example.com;
	}

	server {
		listen 80;

			location / {
				proxy_pass	 http://myapp;
			}
	}

}
```
## Round-robin 分配權重 weight
```
http {
	upstream myapp {
		server srv1.example.com weight=3;
		server srv2.example.com weight=2;
		server srv3.example.com;
	}

	server {
		listen 80;

			location / {
				proxy_pass	 http://myapp;
			}
	}

}
```
## 備份 backup backup 代表，所有伺服器都掛掉之後，此伺服器才會生效
```
http {
	upstream myapp {
		server srv1.example.com weight=3;
		server srv2.example.com backup;
		server srv3.example.com;
	}

	server {
		listen 80;

			location / {
				proxy_pass	 http://myapp;
			}
	}

}
```

---
# Outer Link
- [maxlist.xyz/2020/06/18/flask-nginx/](maxlist.xyz/2020/06/18/flask-nginx/)
