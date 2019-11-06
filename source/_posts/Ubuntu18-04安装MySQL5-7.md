---
title: Ubuntu18.04安装MySQL5.7
comments: true
categories: 笔记
tags:
  - Ubuntu
  - MySQL
abbrlink: a958b182
date: 2019-11-04 13:36:00
---

---

## 前言

MySQL 5.7版本采用了全新安装机制，改善了MySQL的安全性，所以和以前安装不太一样。比如：root密码设置放置到了安装完成之后的安全设置中来等。但是经过后来测试，这篇笔记是有问题的，在密码设置的步骤，但是我现在暂时不会布置MySql（用不到），所以暂时还不修改错误。Ubuntu16.04是没有这个错误的。

## 安装MySQL

```
$ sudo apt update
$ sudo apt install mysql-server
```
在安装过程中并不会提示设置root密码和一些其他设置，这与Ubuntu16.04不相同。

## 配置MySQL

运行安全脚本：

```
$ sudo mysql_secure_installation
```
然后只需要根据提示设置root用户的密码。成功设置密码以后还会有一些安全设置，可以一路Y/Enter，也可以按个人需求。

## 验证MySQL

```
$ systemctl status mysql.service
```
## 登陆MySQL 

```
$ sudo mysql -uroot -p
```
**注意，登陆root用户应该使用sudo来执行，不然会报错`ERROR 1698 (28000): Access denied for user 'root'@'localhost'`，这是权限不足导致的**

## 设置远程连接

设置 mysql 数据库支持远程访问，需要把绑定 IP 地址改为 0.0.0.0，或者不写IP地址。因此编辑配置文件如下：

```
$ sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
修改绑定地址为 0.0.0.0。把原来默认绑定 127.0.0.1 注释。

```
bind-address = 0.0.0.0
# bind-address = 127.0.0.1
```
另外需要进入 MySQL 程序修改 root 账户的远程访问的权限。如果这一步不执行，则远程用 Navicat 访问时，会报 1130 错误。

```
$ sudo mysql -u root -p //登陆root账户

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION; //修改权限
flush privileges;	//同时刷新权限
systemctl restart mysql.service	//重启服务器
```
至此，完成了 MySQL 的安装与远程访问设置。

## 参考

1. [ubuntu 18.04 安装 mysql-server 5.7](https://wangxin1248.github.io/linux/2018/07/ubuntu18.04-install-mysqlserver.html)