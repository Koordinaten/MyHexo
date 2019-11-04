---
title: Ubuntu16.04换源
comments: true
categories: Ubuntu
tags: [笔记,换源]
abbrlink: 4f16dedc
date: 2019-11-04 13:36:38
---

备份源列表
	```
	sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
	```
修改sources.list文件
	```
	sudo gedit /etc/apt/sources.list
	```
编辑 /etc/apt/sources.list 文件，更改为阿里云镜像源
	```
	#  阿里源
	deb-src http://archive.ubuntu.com/ubuntu xenial main restricted #Added by software-properties
	deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted
	deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties
	deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted
	deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties
	deb http://mirrors.aliyun.com/ubuntu/ xenial universe
	deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
	deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse
	deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse
	deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
	deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties
	deb http://archive.canonical.com/ubuntu xenial partner
	deb-src http://archive.canonical.com/ubuntu xenial partner
	deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted
	deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties
	deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
	deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse
	```
更新并升级
	```
	sudo apt-get update && sudo apt-get upgrade
	```
其他源

	```
	# 清华源
	deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
	deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
	deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
	deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
	deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
	deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
	deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
	deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
	```

	```
	#  中科大源
	deb https://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse
	deb-src https://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse
	deb https://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
	deb-src https://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
	deb https://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
	deb-src https://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
	deb https://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
	deb-src https://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
	deb https://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
	deb-src https://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
	```