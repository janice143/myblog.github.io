---
title: 【Github】配置连接github
date: 2021-08-20 15:48:00
tags:
---


### 复制代码
配置身份，不正确会影响贡献度统计

```
git config --global user.email "A175A174@gmail.com"
git config --global user.name "A175A174"
```

### 配置 ssh 链接，一路回车，打开提示目录下 id_rsa.pub
复制到 https://github.com/settings/keys 点击 New sshKey，Title 可以不写
```
ssh-keygen -t rsa -C "A175A174@email.com"
```

### 验证 ssh 是否成功
Hi A175A174! You've successfully authenticated, but GitHub does not provide shell access
```
ssh -T git@github.com
```