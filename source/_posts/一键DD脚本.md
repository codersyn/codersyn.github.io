---
title: 一键DD脚本
date: 2022-06-24 14:34:47
tags:
---

备份几个常用DD脚本。

一、

```bash
wget --no-check-certificate -O AutoReinstall.sh https://git.io/AutoReinstall.sh && bash AutoReinstall.sh
```

本脚本还提供一个灵活的自动重装系统脚本，你可以在安装系统前对IP等进行相应的配置：

```bash
wget --no-check-certificate -qO AutoDD.sh 'http://git.io/autodd.sh' && bash AutoDD.sh
```

二、

```bash
#先运行:
#Debian/Ubuntu:
apt-get update
#RedHat/CentOS:
yum update
#确保安装了所需软件:
#Debian/Ubuntu:
apt-get install -y xz-utils openssl gawk file
#RedHat/CentOS:
yum install -y xz openssl gawk file
#下载及说明:
wget --no-check-certificate -qO InstallNET.sh 'https://moeclub.org/attachment/LinuxShell/InstallNET.sh' && chmod a+x InstallNET.sh
说明:
        bash InstallNET.sh      -d/--debian [dist-name]
                                -u/--ubuntu [dist-name]
                                -c/--centos [dist-version]
                                -v/--ver [32/i386|64/amd64]
                                --ip-addr/--ip-gate/--ip-mask
                                -apt/-yum/--mirror
                                -dd/--image
                                -a/-m
# dist-name: 发行版本代号
# dist-version: 发行版本号
# -apt/-yum/--mirror : 使用定义镜像
# -a/-m : 询问是否能进入VNC自行操作. -a 为不提示(一般用于全自动安装), -m 为提示.
```

三、

```bash
#下载脚本
wget --no-check-certificate -qO ~/Network-Reinstall-System-Modify.sh 'https://www.cxthhhhh.com/CXT-Library/Network-Reinstall-System-Modify/Network-Reinstall-System-Modify.sh' && chmod a+x ~/Network-Reinstall-System-Modify.sh
#进入安装选项
bash ~/Network-Reinstall-System-Modify.sh -UI_Options
#合并下载脚本+进入安装选项
wget --no-check-certificate -qO ~/Network-Reinstall-System-Modify.sh 'https://www.cxthhhhh.com/CXT-Library/Network-Reinstall-System-Modify/Network-Reinstall-System-Modify.sh' && chmod a+x ~/Network-Reinstall-System-Modify.sh && bash ~/Network-Reinstall-System-Modify.sh -UI_Options
#（高级用户使用）安装 裸机系统部署平台
bash ~/Network-Reinstall-System-Modify.sh -CXT_Bare-metal_System_Deployment_Platform
```

四、

```bash
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/installNET/master/Install.sh" && chmod +x Install.sh && ./Install.sh 
```

五、

```bash
##镜像文件在OneDrive
wget -N --no-check-certificate https://raw.githubusercontent.com/veip007/dd/master/dd-od.sh && chmod +x dd-od.sh && ./dd-od.sh
##镜像文件在GoogleDrive
wget -N --no-check-certificate https://raw.githubusercontent.com/veip007/dd/master/dd-gd.sh && chmod +x dd-gd.sh && ./dd-gd.sh
```
