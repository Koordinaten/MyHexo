---
title: 在Ubuntu18.04上搭建Cuckoo Sandbox2.0.7
comments: true
categories: Ubuntu
tags:
  - 笔记
  - Cuckoo Sandbox
abbrlink: ff13853b
date: 2019-11-04 13:37:21
---

---

## 准备工作
+ Ubuntu18.04
+ windows7 32位ISO镜像文件
+ VirtualBox

本次搭建是以Ubuntu18.04为主机(Host),以在VirtualBox中创建的win7系统为客户机(Guest).

****

## 服务器(Host)安装

1.  **操作系统**

    Ubuntu18.04
2. **更换源**

    更换到国内的Ubuntu源，把pip的源也换掉，google或百度上都有相关博文。
    换源时最好将源文件做个备份。

3. **检查python版本和相关pip版本**

- cuckoo sandbox仅完全支持python2.7,不支持旧版本的python和python3版本。
    ```
    $ sudo apt-get install python2.7
    $ sudo apt install python-pip
    $ sudo apt install python3-pip
    ```
    此时用`python -V` 和 `pip -V` 查询python版本和pip版本

4. **安装基本的包**
    
    ```
    $ sudo apt-get install python python-pip python-dev libffi-dev libssl-dev
    $ sudo apt-get install python-virtualenv python-setuptools
    $ sudo apt-get install libjpeg-dev zlib1g-dev swig
    ```
5. **安装数据库**

- 为了使用基于Django的Web界面，需要使用MongoDB：
    ```
    $ sudo apt-get install mongodb
    ```

- Mysql用于储存Cuckoo运行状况：
    ```
    $ sudo apt-get install mysql-server mysql-client libmysqlclient-dev
    ```

    **注意:在最新版的mysql安装过程中，不会再提示手动设置root用户的密码,则需要用命令设置root初始密码：**
    
    ```
    $ sudo mysql_secure_installation
    ```

    然后按照提示一步一步来即可。
    **另外， root连接需要sudo命令，否则会出现以下结果**
    
    ```
    $ mysql -uroot -p
    ERROR 1698 (28000): Access denied for user 'root'@'localhost'
    ```
    解决方法：
    ```
    $ sudo mysql -u root 

    mysql> USE mysql;
    mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';
    mysql> FLUSH PRIVILEGES;
    mysql> exit;

    $ service mysql restart
    ```

6. **tcpdump**

    Host机需要可以嗅探网络数据包，需要安装tcpdump ，执行以下命令安装,并启用tcpdump 的root权限：
    
    ```
    $ sudo apt-get install tcpdump apparmor-utils
    $ sudo setcap cap_net_raw,cap_net_admin=eip /usr/sbin/tcpdump
    ```

7. **安装Cuckoo**

    推荐在虚拟环境中安装。virtualenv是一个创建隔绝的Python环境的工具，在之前的步骤中已经安装上了。
    
    ```
    $ virtualenv venv
    $ . venv/bin/activate
    (venv)$ pip install -U pip setuptools
    (venv)$ pip install -U cuckoo
    ```
    
    然后就会自动下载所需的各种包。在使用的过程中，如果提示某python模块缺失，可以再单独安装，但注意需要在虚拟环境中进行安装。

8. **安装yara**
    
    ```
    $ sudo apt-get install yara
    $ sudo apt-get install python-yara
    (venv)$ pip install yara
    ```
    安装yara时有点问题，暂时找不出解决方案，博主就没有装yara,或者说是其他的问题，然后yara背锅，因为当时安装成功时，yara并不是唯一变量，所以不确定到底是谁有问题。大家可以试一下。

9. **安装volatility**
    
    如果需要启用内存镜像分析，需要安装volatility：
    
    ```
    apt-get install volatility
    ```

10. **初始化cuckoo**

    ```
    (venv)$ cuckoo init
    ```

    初始化完成后，便会自动生成cuckoo的工作目录(Cuckoo Working Directory)，我们称之为CWD，是个隐藏文件夹。

    通常这个目录在：
    
        非root身份：/home/[username]/.cuckoo/ 
        root身份：/root/.cuckoo/
    
    以后统称为CWD 或 .cuckoo。

    **这个CWD，不运行一次Cuckoo是不会自动生成的！！！！！**

    **这简直就是个大坑，博主刚开始搭建时，是第一次接触Cuckoo，两眼一抹黑，啥也不会就只能跟着网上的教程走，但，，，网上大部分教程都好像默认来学习的人都知道CWD在哪里，或者说怎么生成 =-=，难受的亚批**

    **除了[Cuckoo Linux Subsystem: Some Love for Windows 10](https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/cuckoo-linux-subsystem-some-love-for-windows-10/)，此文章是研究怎么把win10当作主机，配置cuckoo，是个好博文不过我没学会==，win10安装和Ubuntu安装有相同之处，推荐瞅两眼**

****

## 客户机(guest)安装

1. **操作系统**

    windows7 32位

2. **虚拟机**
    
    官方文档表示Cuckoo支持VirtualBox 4.3,5.0,5.1和5.2
    
    下载[VirtualBox 5.2](https://download.virtualbox.org/virtualbox/5.2.30/virtualbox-5.2_5.2.30-130521~Ubuntu~bionic_amd64.deb)，然后在下载文件夹的终端执行以下命令：
    
    ```
    ~/Downloads$ sudo dpkg -i virtualbox-5.2_5.2.30-130521_Ubuntu_bionic_amd64.deb
    ```
3. **虚拟机安装**

    自行准备一个win7系统镜像，然后在VirtualBox中创建一个新的虚拟机，
    
    具体设置为：

        1. 虚拟机名称为cuckoo1   (在配置时会用到，也可设置为你喜欢的名称，配置时自行修改)
        2. 内存大小为2G，双处理器，硬盘32G
        3. 拖放 和 共享文件夹最好是双向
        4. 系统->启动顺序 一栏，勾选光驱和硬盘，其他不勾选
        5. 网络配置，具体看下一节
        6. 然后创建虚拟机-cuckoo1
    
    注：win7不要设置密码，直接下一步就可以了。

4. **网络配置**

    首先，需要在VirtualBox上添加一块给Host-Only虚拟网卡，默认是vboxnet0，IP为192.168.56.1，关闭DHCP服务。

    然后配置客户机的网络

        网卡1：设置为仅主机(Host-Only)网络，界面名称为vboxnet0。
        网卡2：设置为网络地址转换(NAT)。

    NAT适配器用于Internet访问时，Cuckoo使用Host-Only适配器与Guest映像内的代理进行通信。

    禁用DHCP，请执行以下步骤：
        
        1. 打开VirtualBox Manager
        2. 单击文件>首选项>网络
        3. 单击“仅主机网络”选项卡
        4. 突出显示“VirtualBox Host-Only Ethernet Adapter”并单击Edit（看起来像螺丝刀的图标）
        5. 单击“DHCP服务器”选项卡
        6. 取消选中“启用服务器”

5. **客户机配置**

    当你虚拟机的系统安装好以后，就需要对win7系统本身进行配置了，如下：

- 安装增强功能
    
        设备>安装增强功能
    
    安装增强功能以后，进入计算机你会发现你的CD盘正在工作，点击进入然后安装适合你机器的版本-32位版本，安装后重启，此时就可以在主机与客户机之间通过拖放，粘贴传送文件了，还可以调整分辨率大小。

- 配置Guest机网卡
        
        控制面板>网络和 Internet>网络和共享中心>更改适配器设置
    
    这时你会发现两个本地连接，其中一个已经连接(NAT)，另一个未识别(Host-Only)，也就是说你可以通过这个win7网上冲浪，但是却不能连接到自己的主机。
    
    这时配置Host-Only网卡，如下：
    
    ![](https://upload-images.jianshu.io/upload_images/19343988-61437531395e3b92.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240#pic_center)
    
    当设置完成后，两个网络都会显示已连接。

- 安装工具或软件
    
    - 安装python2.7，PIL，Pillow
  
    - 安装一些软件，如office、Adobe Reader、Firefox等

    - 将CWD/agent/agent.py文件拷贝到客户机里。我们需要这个文件能开机自启动，做法如下：

          1. 把agent.py文件放到系统开机启动目录中，在Win7中默认的路径是
          2. C:\Users[username]\AppData\Roaming\MicroSoft\Windows\Start\Menu\Programs\Startup\。
          3. 如果想要agent运行时没有GUI窗口，可以将后缀名改为pyw。
          4. 在任务管理器中查看进程，或在命令行中执行netstat -an查看是否有8000端口的监听，来确定agent是否在运行.

    - 关闭windows自动更新，防火墙，UAC。
   
    - 不用设置开机自动登陆，因为开始安装虚拟机系统时候就没设置开机密码哈哈哈哈，反正是自己用。

**注：对于一些工具软件的安装是很有必要的**

**Cuckoo检查网络或检测文件是否是恶意的主要过程就是跑实验，他把一个文件丢进虚拟机内运行，观察文件在操作系统中运行的具体流程和最后的结果，然后给出反馈。其中主要的一环，就是丢进去的文件可以运行。**

**但是！！PDF文件检测Cuckoo只认Adobe Reader，所以不用安装福昕阅读器，没用的，找不到，我没测试doc，所以不清楚wps是否可行，估计是同样的结果。我没试过修改配置让他适配WPS，福昕阅读器，因为对博主没必要。（其实就是懒）**

**另外，不要因为虚拟机能上网就可以在虚拟机内下载软件，因为真的是，太卡了！！我是在Ubuntu上下载以后，拖拽传进虚拟机的。**

****

## 快照设置

对于快照小白来说，快照真挺奇怪的，但是当你了解他的原理以后，真香！！    
博主对虚拟机接触不是太多，甚至是没有接触过，快照的概念理解算是我安装Cuckoo的一个难点，因为卡了一段时间，所以单独拎出来写，表示尊敬。

**快照(snapshot)** ，就是把虚拟机的某一刻所有的状态都记录下来，以便当**虚拟机系统出现问题** 或 **需要恢复原来状态**时，可以快速恢复，不必再重新安装系统、重新设置相关配置、重新安装相关软件，简单来说就是**复活点**。

那么，**什么时候进行快照**就是很神奇的问题了，自己设置自己的复活点哈哈哈有点意思。
博主是在所有软件安装完成，进行重启以后，设置的快照，当然你也可以不这样，不过一定要**运行agent.py时进行快照**，就是设置的开机自动运行的文件。不然你会每次恢复快照，都要自己手动运行一次，非常浪费时间

- 当你进行快照时，你的虚拟机应该已经设置完毕并保持启动，此时的虚拟机是运行的，agent.py也是运行的，然后在Ubuntu终端运行如下代码：
  
    ```
    # 把 虚拟机cuckoo1 此时的状态记录到 快照Snapshots 
    $ VBoxManage snapshot "cuckoo1" take "Snapshots" --pause     

    # 快照创建完成后，关闭计算机并将其用刚刚创建的快照(Snapshots)还原
    $ VBoxManage controlvm "cuckoo1" poweroff
    $ VBoxManage snapshot "cuckoo1" restorecurrent
    ```

- 如果你的快照出现了错误，或者遗漏了一个软件。你可以 **灵活运用** 以下代码进行快照设置，以及快照的快照设置，在虚拟机中，你可以设置操作系统在哪个复活点复活，还可以改复活点的名字。

    ```
    $ VBoxManage snapshot "vmname" take "snapname" [--description desc] [--live]#创建快照，有--live参数,快照创建过程中不会停止虚拟机.
    $ VBoxManage snapshot "vmname" delete "snapname" # 删除快照
    $ VBoxManage snapshot "vmname" restore "snapname" # 通过某一快照恢复
    $ VBoxManage snapshot "vmname" restorecurrent #恢复到当前快照
    $ VBoxManage snapshot "vmname" list [--details] #列出快照
    $ VBoxManage snapshot "vmname" edit "snapname" [--name <newname>] [--description <newdesc>]# 编辑快照
    ```

****

## Cuckoo配置

1. **配置文件路径**

    CWD/conf/

2. **cuckoo.conf**
 
    ```
    [database]
    connection = mysql://username:passwd@localhost:port/[数据库名称]

    [cuckoo]
    //每次启动都要检查更新很费时间，可以选择关闭
    version_check = no 

    machinery = virtualbox

    //有教程建议修改为no，这样会导致启动Cuckoo Sandbox需要多一个步骤，但会让主模块与处理报告的模块分离，大幅提升稳定性，避免各种意外发生。
    //但是多出的步骤，本博主不会
    process_results = yes 
    ```

3. **auxillary.conf**
    
    ```
    [sniffer]
    enabled = yes
    tcpdump = /usr/sbin/tcpdump 
    ```

4. **virtualbox.conf**
    
    如果虚拟机软件用的是vmware就是vmware.conf。
    
    ```
    [virtualbox]
    mode = gui //gui是有界面，headless是无界面，调试的时候可以选择
    path = /usr/bin/vboxmanage
    interface = vboxnet0
    machines = cuckoo1 //你的虚拟机名称，他默认是cuckoo1，所以开始时创建虚拟机时我推荐为cuckoo
    controlports = 5000-5050

    [cuckoo1]  //需要与machines一致
    label = cuckoo1 //你的虚拟机名称，需要与machines一致
    platform = windows
    ip = 192.168.56.101
    snapshot = Snapshots//创建的快照名称
    ```

5. **reporting.conf**

    ```
    [mongodb]
    enabled = yes  //默认是no
    host = 127.0.0.1
    port = 27017
    db = cuckoo //数据集名称
    store_memdump = yes 
    paginate = 100
    ```

6. **processing.conf**

    用于开启/关闭某些分析模块。本文没有做修改。

7. **社区签名库**
    
    可以先看 **有点问题**
    
    ```
    #虚拟环境下，运行以下命令下载Cuckoo提供的社区版行为签名库
    (venv)$ cuckoo community
    #或先在浏览器下载再导入：
    (venv)$ cuckoo community --file cuckoo_master.tar.gz
    ```

****

## 有点问题

**这个有点问题，有些是稀里糊涂解决的，所以博主只能把遇到的可能的问题 和 解决的步骤说一下，并且有些问题没写，虽然是error，但是在界限内并不会有影响，比如关于文件大小问题。**

- 你下载下来的社区签名实际上名称为"community-master.tar.gz",我把他改成了"cuckoo-master.tar.gz"然后导入签名。

- 可以在没有设置签名之前运行`（venv）$ cuckoo 或 cuckoo –d`,只是会有个warning，主要看是否会出错

- 如果出现`********int(port)`错误，莫的办法，如果尝试修改`url.py`还会出现其他错误，真是老母猪戴胸罩，一套又一套。这个博主是真的绕不过去，只能把`./cuckoo`文件删除，再重新`cuckoo init`再配置吧，签名可能还需要重新导入一下。对于这个问题，这三个步骤可以无顺序执行，总有一个顺序是对的。

- **tcpdump Permission denied**
  
    tcpdump默认是enforce模式，改成complain模式。
        
    查看模式：
    
      grep tcpdump /sys/kernel/security/apparmor/profiles
    
    更改为complain：
    
      aa-complain /usr/sbin/tcpdump

- **WARNING: Uploaded file length larger than upload_max_size, stopping upload.**

        修改cuckoo.conf
        upload_max_size = 314572800 或更大
        这个值是从客户机中传回Cuckoo的报告、截图等数据的大小。

- **Error fetching configuration file! This is a serious error. if encountered, please notify the Cuckoo developers as this error prevents analysis.**
    
        确定关闭了UAC
        确定以管理员身份运行agent.py/agent.pyw
        确定在agent运行中进行了快照
        将客户机休眠，不是关机（也许关机也是对的，但是我没有成功）
        如果不行，就关闭pythonw进程重开，或重启客户机。

****

## 启动Cuckoo

当你把一部分重要问题，或者所有问题解决时，你就可以启动cuckoo啦！

-   启动虚拟环境
    
         . venv/bin/activate

-  通常第一次启动Cuckoo，会先运行如下命令，检查是否出现错误。
    
        （venv）# cuckoo 或 cuckoo –d

-   启动web
    
         （venv）# cuckoo web
    
    然后就可以通过[http://localhost:8000](http://localhost:8000)访问了。

- **进行文件提交**

    可以在命令行：`cuckoo submit [file_1] [file_2] [file_3] ...... [file_n]`，也可以在web端提交文件。

    他们都会生成一个任务n(task #n)，当你运行`cuckoo`或`cuckoo -d`时会自动执行任务，终端中会有log提示，每次任务结束都会恢复一次快照。

-   分析结果的储存路径为：
    
     CWD/storage/analyses

****

## 总结

- **一定要看官方文档！！！**

    官方文档是最基础，最全面的教程，网上其他教程应该只是作为参考，毕竟每个人的用途，环境都不一样。

- **遇到问题一定要先看日志及推荐的解决方案。**

    不要直接复制粘贴就去网上搜Error了，Error都不读一下，**知道问题是什么才是解决问题的第一步**。

- **多做尝试，不要没弄好就重装机器，会少很多乐趣，会少学很多的东西**

- **英语不好就弄个google翻译吧，=-=**

- **完成目的，而不是解决问题**

****

## 参考

1. [cuckoo sandbox官方安装文档](https://cuckoo.sh/docs/installation/host/requirements.html#installing-python-libraries-on-ubuntu-debian-based-distributions)
2. [Cuckoo v2.0.6搭建过程](https://www.jianshu.com/p/ac009f6c2710)
3. [Cuckoo Linux Subsystem: Some Love for Windows 10](https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/cuckoo-linux-subsystem-some-love-for-windows-10/)
4. [ubuntu18.04安装python2.7和python3.6 pip 和 pip3 不同版本共存](https://blog.csdn.net/gymaisyl/article/details/86563916)
5. [ubuntu 18.04 为 mysql 设置 root 初始密码](https://www.sunzhongwei.com/set-mysql-root-password-on-ubuntu-1804)
6. [解决 MySQL 的 ERROR 1698 (28000): Access denied for user 'root'@'localhost'](https://blog.csdn.net/jlu16/article/details/82809937)
7.  [搭建Linux工作环境之VirtualBox](https://segmentfault.com/a/1190000005650099)
