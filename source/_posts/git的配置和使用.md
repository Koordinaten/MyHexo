---
title: git的配置和使用
comments: true
categories: 笔记
tags: git
abbrlink: 2415892f
date: 2019-11-06 09:12:38
---

---

## 设置Git的user name和email

```
git config --global user.name Koordinaten

git config --global user.email Koordinaten@163.com
```

## 生成密钥

```
ssh-keygen -t rsa -C Koordinaten@163.com
```

连续3个回车。最后得到了两个文件：id_rsa和id_rsa.pub。

## 登录Github，添加ssh

我会，就不写了

## 测试

```
ssh -T git@github.com
```

你会看到：
```
    The authenticity of host 'github.com (207.97.227.239)' can't be established.
    RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
    Are you sure you want to continue connecting (yes/no)?
```

选择``yes``。然后你就看到你成功了。
