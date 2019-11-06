---
title: 安装Ubuntu18.04后做的事
comments: true
categories: 笔记
tags: Ubuntu
abbrlink: e139eb9d
date: 2019-11-06 09:12:17
---

---

## 删除不需要的链接

```
sudo apt-get remove libreoffice-common

sudo apt-get remove thunderbird totem rhythmbox empathy brasero simple-scan gnome-mahjongg aisleriot gnome-mines cheese transmission-common gnome-orca webbrowser-app gnome-sudoku  landscape-client-ui-install

sudo apt-get remove onboard deja-dup

sudo apt-get remove unity-webapps-common

sudo apt autoremove
```

不过Ubuntu18.04上我总是不能删除Amazon链接，需要从软件中心卸载。

## 换源

可以参考下面的文章：
    [Ubuntu18.04换源](https://ddcoder.club/posts/8f147ee5.html)
    [Ubuntu16.04换源](https://ddcoder.club/posts/4f16dedc.html)

```
sudo apt-get update && sudo apt-get upgrade
```

## 安装chrome浏览器

```
sudo wget https://repo.fdzh.org/chrome/google-chrome.list -P /etc/apt/sources.list.d/

wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -

sudo apt update

sudo apt install google-chrome-stable
```

通常update时会出现一些错误，删除就没事了。

## 升级firefox浏览器为Firefox Quantum

```
sudo add-apt-repository ppa:mozillateam/firefox-next

sudo apt update && sudo apt upgrade
```

## 安装python

可以看这篇文章
[Ubuntu18.04安装python2.7和python3.6共存](https://ddcoder.club/posts/e6efc55d.html)

## 换终端-zsh

我是不经意间才发现竟有如此神器，从此爱上她而一发不可收拾。

### 首先我们需要安装一些前置软件

```
sudo apt-get install -y vim curl wget git
```

### 安装zsh

```
sudo apt-get install zsh
```

### 把默认的Shell改成zsh

```
chsh -s /bin/zsh
```

### 配置密码文件，解决chsh: PAM认证失败的问题

```
sudo vim /etc/passwd
```
> 把第一行的/bin/bash改成/bin/zsh，这个是root用户的。

### 安装oh-my-zsh

自动安装
```
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

手动安装
```
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```
> 登出再登录后，检查当前的shell 是否配置正确

## 安装插件

### 启用语法高亮显示

```
sudo apt install zsh-syntax-highlighting

echo "source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/.zshrc
```

### 安装zsh-autosuggestions语法历史记录插件

```
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

> 配置:
>
>``vim ~/.zshrc``
>
>在plugins 下加上
>
>``setopt no_nomatch`` 如果不加的话就不能使用*
>
> 在最后一行 添加
>
>``source $ZSH_CUSTOM/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh``

### 主题

我用的主题是 ``maran``

### 插件

我用的插件
```
plugins=(
  git z zsh-autosuggestions
)
```
