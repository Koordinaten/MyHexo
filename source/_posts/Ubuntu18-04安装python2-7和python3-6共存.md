---
title: Ubuntu18.04安装python2.7和python3.6共存
comments: true
abbrlink: e6efc55d
date: 2019-11-04 13:36:20
categories: 笔记
tags: [Ubuntu ,python]
---

---

## 安装python2.7

```
sudo apt install python2.7
```

## 安装pip和pip3

```
sudo apt install python-pip
sudo apt install python3-pip
```

## 查看python版本和pip版本

```
python -V
pip -V
```
## 查看python路径和pip路径

```
whereis python
whereis pip
```
## 升级pip到对新版本

```
python -m pip install --upgrade pip
```