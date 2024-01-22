---
title: Hello World 使用Github pages 靜態網站
tags:
  - github
  - ''
categories: []
abbrlink: 526efc4a
date: 2023-05-05 10:03:00
---

建立一個新的倉庫 命名為 **帳戶名稱.github.io**
![建立一個新的倉庫](/images/image.png)

好了之後在自己的電腦上clone專案下來
```
git clone git@github.com:eebrian123tw60/eebrian123tw60.github.io.git
```
進入專案 新增一個index.html 內容是Hello World
```
cd eebrian123tw60.github.io
echo "Hello World" > index.html
```
推到遠端的倉庫
```
git add --all
git commit -m "Initial commit"
git push -u origin main
```
等待幾分鐘 訪問https://eebrian123tw60.github.io/
![](/images/image2.png)
結果就出來拉！！

---
## 參考連結
- https://pages.github.com/
- [從零開始: 用github pages 上傳靜態網站](https://medium.com/%E9%80%B2%E6%93%8A%E7%9A%84-git-git-git/%E5%BE%9E%E9%9B%B6%E9%96%8B%E5%A7%8B-%E7%94%A8github-pages-%E4%B8%8A%E5%82%B3%E9%9D%9C%E6%85%8B%E7%B6%B2%E7%AB%99-fa2ae83e6276)