---
title: 在Windows上使用Ubuntu共享的打印机
date: 2017-09-07 21:39:58
author: wangduo
cover: /assets/images/.jpg
tags: [Windows, win10, Ubuntu, Ubuntu 16.04, 打印机, printer]
categories: Deep learning
---

Ubuntu下使用cups共享打印机， 是一种简单易用的方法。CUPS(Common UNIX Printing System，通用Unix打印系统)是Fedora Core3中支持的打印系统，它主要是使用IPP(Internet Printing Protocol)来管理打印工作及队列，但同时也支持"LPD"(Line Printer Daemon)和"SMB"(Server Message Block)以及AppSocket等通信协议。(From baike.baidu.com)

### CUPS － 打印服务器
Ubuntu 印刷和打印服务的主要机制是 CUPS (Common UNIX Printing System，通用 UNIX 打印系统)。这个打印系统是一个免费可用的、可移植的打印虚拟层，并已成为大多数 Linux 发行版的新打印标准。

CUPS 管理打印作业和队列，并使用标准的 Internet 打印协议 (IPP) 提供网络打印，该协议提供最大范围的打印机支持，从点阵打印机到激光打印机以及位于两者之间的许多打印机。CUPS 也支持 PostScript Printer Description (PPD) 和网络打印机的自动检测，以及提供基于 Web 的简单配置和管理工具。

> 安装
> 配置
> Web Interface
> 参考资料

#### 安装
To install CUPS on your Ubuntu computer, simply use sudo with the apt command and give the packages to install as the first parameter. A complete CUPS install has many package dependencies, but they may all be specified on the same command line. Enter the following at a terminal prompt to install CUPS:

`sudo apt install cups`

Upon authenticating with your user password, the packages should be downloaded and installed without error. Upon the conclusion of installation, the CUPS server will be started automatically.

For troubleshooting purposes, you can access CUPS server errors via the error log file at: /var/log/cups/error_log. If the error log does not show enough information to troubleshoot any problems you encounter, the verbosity of the CUPS log can be increased by changing the LogLevel directive in the configuration file (discussed below) to "debug" or even "debug2", which logs everything, from the default of "info". If you make this change, remember to change it back once you've solved your problem, to prevent the log file from becoming overly large.

#### 配置
可以通过 /etc/cups/cupsd.conf 文件中的指令来配置通用 UNIX 打印系统服务器的行为的。CUPS 配置文件与 Apache HTTP 服务器的主配置文件语法相同，因此熟悉编辑 Apache 配置文件的用户在编辑 CUPS 配置文件时会感到相当容易。在这里将显示一些您可能想要改变初始值的设置范例。

> 在编辑配置文件之前，您应该将原始文件做个副本并将其写保护，以便您可以将原始文件作为参考并在必要时重用它。
> 拷贝 /etc/cups/cupsd.conf 文件并对其写保护，可以在终端提示符后执行以下命令：

```
sudo cp /etc/cups/cupsd.conf /etc/cups/cupsd.conf.original
sudo chmod a-w /etc/cups/cupsd.conf.original
```

1. **ServerAdmin**: To configure the email address of the designated administrator of the CUPS server, simply edit the /etc/cups/cupsd.conf configuration file with your preferred text editor, and add or modify the ServerAdmin line accordingly. For example, if you are the Administrator for the CUPS server, and your e-mail address is 'bjoy@somebigco.com', then you would modify the ServerAdmin line to appear as such:
`ServerAdmin bjoy@somebigco.com`

2. **Listen**: By default on Ubuntu, the CUPS server installation listens only on the loopback interface at IP address 127.0.0.1. In order to instruct the CUPS server to listen on an actual network adapter's IP address, you must specify either a hostname, the IP address, or optionally, an IP address/port pairing via the addition of a Listen directive. For example, if your CUPS server resides on a local network at the IP address 192.168.10.250 and you'd like to make it accessible to the other systems on this subnetwork, you would edit the /etc/cups/cupsd.conf and add a Listen directive, as such:
```
Listen 127.0.0.1:631 # existing loopback Listen
Listen /var/run/cups/cups.sock # existing socket Listen
Listen 192.168.10.250:631 # Listen on the LAN interface, Port 631 (IPP)
```
在上面的例子里，如果您不想 cupsd 监听环回地址 (127.0.0.1) ，您可能注释或删除了相关语句。但最好保留它以监听局域网 (LAN) 的以太网接口。为了能监听一个特定主机名所绑定的所有的网络接口，您可以为 socrates 主机名创建一个 Listen 条目，如下所示：
`Listen socrates:631 # Listen on all interfaces for the hostname 'socrates'`
或者忽略 Listen 语句并使用 Port 来代替，如：
`Port 631 # Listen on port 631 on all interfaces`

关于 CUPS 服务器配置文件中配置语句的更多范例，通过在终端提示符后输入以下命令可以查阅相关的系统手册页：

`man cupsd.conf`

> 无论您在什么时间修改了 /etc/cups/cupsd.conf 配置文件，您都需要重启 CUPS 服务，在终端提示符后键入以下命令：
> `sudo systemctl restart cups.service`

#### Web Interface
> CUPS can be configured and monitored using a web interface, which by default is available at http://localhost:631/admin. The web interface can be used to perform all printer management tasks.

In order to perform administrative tasks via the web interface, you must either have the root account enabled on your server, or authenticate as a user in the lpadmin group. For security reasons, CUPS won't authenticate a user that doesn't have a password.

To add a user to the lpadmin group, run at the terminal prompt:

`sudo usermod -aG lpadmin username`

Further documentation is available in the Documentation/Help tab of the web interface.

在http://localhost:631/admin页面中找到Server Settings，选择"Share printers connected to this system"及其子项"Allow printing from the Internet"，点击"Change Setting"按钮保存设置。

进入http://localhost:631/printers/页面点击自己打印机的名字，复制跳转到的页面的URL，即打印机的地址。然后，就可以在Windows上添加使用Ubuntu共享的打印机了。

#### 参考资料
[CUPS 网络](http://www.cups.org/)
[Debian Open-iSCSI page](http://wiki.debian.org/SAN/iSCSI/open-iscsi)

#### 参考文献
1.http://www.cnblogs.com/yuxc/archive/2012/03/01/2375846.html
2.http://www.360doc.com/content/10/0705/12/254935_37021593.shtml
3.https://help.ubuntu.com/lts/serverguide/cups.html
