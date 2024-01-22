---
title: git@github.com Permission denied (publickey)
created: '星期五 5日 五月 2023 12:17'
Last modified: NaN
abbrlink: 9174ad3f
date: 2023-05-05 16:48:25
Aliases:
Tags:
---
git clone permission denied(publickey)
![](/images/pasted-0.png)
原因：git clone 沒有ssh的publickey
解決：
```
cat ~/.ssh/id_rsa.pub
```

如果沒有產生過公鑰，會產下面錯誤
> cat: /root/.ssh/id_rsa.pub: No such file or directory

使用ssh-keygen產公鑰(遇到提示按Enter)
```
ssh-keygen
```

將印出來的公鑰設定去[SSH Add New Key](https://github.com/settings/ssh/new) or (GitHub >> Settings >> SSH and GPG keys >> New SSH key)
![](/images/pasted-2.png)