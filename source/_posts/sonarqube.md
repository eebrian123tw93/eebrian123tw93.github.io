---
title: sonarqube
categories: []
date: '2024-09-05 14:50:00'
updated: '2025-02-19 08:51:03'
description: ''
abbrlink: 5675f0f1
tags: []
keywords: []
---
# Sonarqube 服務
## docker run
``` bash
docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube
```
## docker compose`
```yaml
version: "3"
services:
  sonarqube:
    image: sonarqube:community
    depends_on:
      - db
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    ports:
      - "9000:9000"
  db:
    image: postgres:12
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:
```

# [SonarScanner](https://docs.sonarsource.com/sonarqube/9.9/analyzing-source-code/scanners/sonarscanner/)啟動器 
 <!-- more -->
 ``` bash
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-6.1.0.4477-linux-x64.zip
unzip sonar-scanner-cli-6.1.0.4477-linux-x64.zip
export PATH="$(pwd)/sonar-scanner-6.1.0.4477-linux-x64/bin:$PATH"
```

# 訪問 [localhost:9000](http://localhost:9000)
## 預設帳密 admin/admin 
![](/images/20240905135340.png)
## 建立專案 
點擊[create local project](http://localhost:9000/tutorials?id=Test&selectedTutorial=local)
![](/images/20240905135413.png)
輸入專案名稱 點擊Next
![](/images/20240905135503.png)
點擊Use the glogal setting
![](/images/20240905135545.png)
## 建立分析
點擊Locally
![](/images/20240905135622.png)
建立分析token
![](/images/20240905135659.png)
點擊 continue
![](/images/20240905135735.png)
複製 command
![](/images/20240905143201.png)
# 將資料夾內送上去服務分析
``` bash
cd "project path"
sonar-scanner \
  -Dsonar.projectKey=Test \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=sqp_21fd7ffbfd319d6ef13a7afa8b1598b164c5c2c7
```

![](/images/20240905144822.png)
看到 EXECUTION SUCEESS 表示成功
點選 http://localhost:9000/dashboard?id=Test 查看分析結果

# 參考
- [如何使用 Docker 安裝 SonarQube？](https://old-oomusou.goodjack.tw/sonarqube/docker/)
-  [Day 8: 快快樂樂地用SonarQube進行檢測吧!](https://ithelp.ithome.com.tw/articles/10294624)