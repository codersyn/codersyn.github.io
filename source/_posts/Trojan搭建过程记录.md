---
title: Trojan搭建过程记录
date: 2021-09-24 18:04:21
tags:
typora-root-url: ./
---

> 因为最近做梯子的服务器到期了，所以重新搭一个，以前一直用的SSR，但是由于GFW的原因越来越不稳定了，所以这次采用Trojan，顺便做个记录。

## 前置条件

+ 国外VPS一枚
+ SSH工具
+ 域名一个
+ SSL证书
+ Trojan项目地址：[https://github.com/trojan-gfw/trojan](https://github.com/trojan-gfw/trojan)

## 准备工作

### 申请域名

如果你已经有域名这步可跳过，因为我拿到的服务器是一年的，而我之前买的域名不足一年了，为了钱包的安全，我决定去申请一个免费域名。

免费申请域名的网站：[https://www.freenom.com/](https://www.freenom.com/)，这个应该大家都熟悉，此处注意一个点就是freenom似乎禁止了中国玩家的申请，可用的申请办法是直接挂代理申请，如果遇到技术错误导致域名未注册成功的话去百度找一个外国人信息修改你的资料就行了，因为挂代理它会检测你的ip地址，找ip地址对应的国家外国人信息填上就行了。

*NOTE*：①如果输入域名点击**检查**按钮无反应把语言调为英文再试试；②确定想要的域名后点击**获取/Git it now**按钮出现**不可用/x**有两种解决办法：1.在域名后面加后缀再点击**检查**按钮，如你想要synblog.ga这个域名那就输入整个synblog.ga再点击**检查**按钮，不要只输入synblog；2.先注册登录后再申请域名，关于找不到注册入口的可依次点击首页顶部的**合作伙伴/Partners**—**开发者/Developers**选项，把页面拉到最下面点击**Get a Random Domains Account today**按钮即可进入到注册页面；

注册过程无难点，就不再赘述，按照要求填入信息就好，注册成功后先放一边，然后进行下一步操作。

### 把域名放到DNSPod解析

成功拿到域名后就打开[DNSpod](https://www.dnspod.cn/)，注册一个账号并且实名认证。然后依次在**控制管理台**—**DNS解析**—**添加域名**处输入刚才在freenom申请到的域名，然后根据指示复制DNSPod提供给你的两个DNS服务器：

![image-20210926154237563](/../images/Trojan%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B%E8%AE%B0%E5%BD%95/image-20210926154237563.png)

在freenom里点击**Manage Domain**：

![image-20210926154156139](/../images/Trojan%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B%E8%AE%B0%E5%BD%95/image-20210926154156139.png)

**Management Tools**—**Nameservers**：

![image-20210926155150193](/../images/Trojan%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B%E8%AE%B0%E5%BD%95/image-20210926155150193.png)

选择**Use custom nameservers**，并在下面粘贴刚才复制过来的DNS服务器：

![image-20210926155553613](/../images/Trojan%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B%E8%AE%B0%E5%BD%95/image-20210926155553613.png)

保存即可。

然后返回DNSPod把域名解析到你的服务器IP就行。至于为什么不使用Cloudflare，原因是CF不支持申请freenom域名的SSL证书。如果非freenom的域名可以用CF，用CF的话下面acme申请证书把DP的指令换成CF的就行。

最后创建并查看api密钥复制备用，点击右上角的账号中心，选择**API密钥**：

![image-20210926191840888](/../images/Trojan%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B%E8%AE%B0%E5%BD%95/image-20210926191840888.png)

选择**DNSPod Token**：

![image-20210926191932287](/../images/Trojan%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B%E8%AE%B0%E5%BD%95/image-20210926191932287.png)

### 杂项

+ 更新系统

```bash
yum update #centos
apt update && apt upgrade -y #debian/ubuntu
```

+ 关闭防火墙**或者**开放80和443端口（此处二选一即可）#centos

```bash
systemctl stop firewalld.service  #关闭防火墙
```

```bash
firewall-cmd --zone=public --add-port=80/tcp --permanent  #开放80端口
firewall-cmd --zone=public --add-port=443/tcp --permanent  #开放443端口
firewall-cmd --reload  #如果是选择开放端口那需要重载防火墙才能生效
```

+ 安装acme.sh依赖

```bash
yum install -y socat cronie curl #centos
apt install -y socat cron curl #debian/ubuntu
```

+ 安装trojan依赖

```bash
yum install -y xz #centos
apt install -y libcap2-bin xz-utils nano #debian/ubuntu
```

+ 启动crontab

```bash
systemctl start crond #centos
systemctl enable crond #centos
systemctl start cron #debian/ubuntu
systemctl enable cron #debian/ubuntu
```

+ 创建存放证书的文件夹

```bash
mkdir /usr/local/etc/certfiles
```

+ 允许httpd联网

```bash
setsebool -P httpd_can_network_connect 1
```

CentOS反向代理需要配置SELinux允许httpd模块可以联网，否则服务器会返回502错误，如果出现SELinux is disabled错误，说明SELinux已经被彻底的关闭了，那可以直接跳过。

## 安装并配置nginx

### 安装nginx

+ 安装epel #centos

```bash
yum install epel-release
```

+ 安装nginx

```bash
yum install -y nginx #centos
apt install -y nginx #debian/ubuntu
```

### 配置nginx

+ 编辑confing

```bash
vi /etc/nginx/nginx.conf #centos
vi /etc/nginx/sites-available/default #debian/ubuntu
```

+ 删除原有的server代码块

![image-20210926185617676](/../images/Trojan%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B%E8%AE%B0%E5%BD%95/image-20210926185617676-16327033247461.png)

+ 替换server代码块为下面这个：

```bash
server {
    listen 127.0.0.1:80 default_server;
    server_name your_domain;

    location / {
        proxy_pass https://www.baidu.com;
    }
}

server {
    listen 127.0.0.1:80;
    server_name your_server_ip;
    return 301 https://your_domain$request_uri;
}

server {
    listen 0.0.0.0:80;
    listen [::]:80;
    server_name _;
    return 301 https://$host$request_uri;
}
```

请注意替换上面的`your_domain`和`your_server_ip`为你自己的域名和IP，`proxy_pass`后面的网址为你想反向代理的网址，你可以替换为任意没有敏感信息的网站。
解释一下这些虚拟主机的一些细节：第一个server接收来自Trojan的流量，第二个server也是接收来自Trojan的流量，但是这个流量尝试使用IP而不是域名访问服务器，所以将其认为是异常流量，并重定向到域名，第三个server接收除127.0.0.1:80外的所有80端口的流量并重定向到443端口，这样便开启了全站https，可有效的防止恶意探测。

如果不想反代而是设置伪装站点，或者本身有网站，那把第一个server修改下就行：

```bash
server {
    listen 80 default_server;
    server_name your_domain;
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

然后把你的网站文件放在对应的目录下就行了，一般centos的默认html文件在`/usr/share/nginx/html`下，debian/ubuntu的默认html文件在`/var/www/html`下，根据你的系统选择并更改上面的`root`项即可，当然你想放在其他位置也行，这样写只是为了方便快捷一些。

### 启动nginx

+ 启动nginx

```bash
systemctl restart nginx
```

如果报错多半是SELinux在搞事情，可以彻底关闭SELinux。

```bash
vi /etc/selinux/config
```

找到`SELINUX=xxx`，改为`SELINUX=disabled`并`reboot`重启服务器即可。

+ 让nginx开机自启

```bash
systemctl enable nginx
```

## 申请证书

### 安装证书

这里使用acme.sh自动签发证书。

```bash
curl  https://get.acme.sh | sh -s email=my@example.com
```

`my@example.com`改为你的邮箱。

安装完成后重新加载 Bash：

```bash
source ~/.bashrc
```

### 添加API密钥

+ DNSpod

```bash
export DP_Id="1234"  #1234改为之前复制的dnspod的id
export DP_Key="token"  #token改为之前复制的dnspod的token
```

+ Cloudflare

```bash
export CF_Key="<你的 Global API Key>"
export CF_Email="<你的cloudflare邮箱>"
```

### 申请证书

+ DNSpod

```bash
acme.sh --issue --dns dns_dp -d example.com #example.com改为你的域名
```

+ Cloudflare

```bash
acme.sh --issue --dns dns_cf -d example.com #example.com改为你的域名
```

如果要申请通配证书则如下：

+ DNSpod

```bash
acme.sh --issue --dns dns_dp -d example.com -d *.example.com #example.com改为你的域名
```

+ Cloudflare

```bash
acme.sh --issue --dns dns_cf -d example.com -d *.example.com #example.com改为你的域名
```

等待一会，出现这四个证书文件则为申请成功：

![image-20210926194858419](/../images/Trojan%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B%E8%AE%B0%E5%BD%95/image-20210926194858419.png)

如果没有出现上图而是出现一串红色的信息，未申请成功的话，大概率是因为zerossl抽风，可以更换默认CA为Let's Encrypt：

```bash
acme.sh --set-default-ca --server letsencrypt
```

之后再重新执行一遍申请证书的指令即可。

### 安装证书

```bash
acme.sh --install-cert -d example.com --key-file /usr/local/etc/certfiles/private.key --fullchain-file /usr/local/etc/certfiles/certificate.crt
```

使用acme.sh将证书安装到certfiles目录，这样acme.sh更新证书的时候会自动将新的证书安装到这里，注意把`example.com`替换为你的域名

### 配置acme.sh自动更新

```bash
acme.sh  --upgrade  --auto-upgrade
```

到这里不出意外就能访问你带https的域名了，如果你反向代理了百度的话，会进入百度的页面

## 安装并配置trojan

### 安装trojan

```bash
sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/trojan-gfw/trojan-quickstart/master/trojan-quickstart.sh)"
```

### 编辑trojan配置

```bash
vi /usr/local/etc/trojan/config.json
```

修改**password**为你喜好的密码，修改**cert**的地址为证书所在地址：`/usr/local/etc/certfiles/certificate.crt`，修改**key**地址为证书所在地址：`/usr/local/etc/certfiles/private.key`，保存退出即可。

### 启动trojan

+ 启动trojan

```bash
systemctl restart trojan
```

+ 添加开机自启

```bash
systemctl enable trojan
```

### 配置trojan自动加载更新后的证书

+ 编辑crontab文件

```bash
crontab -e
```

+ 写入定时任务

```bash
0 0 1 * * killall -s SIGUSR1 trojan
```

保存退出即可。

## 安装bbr加速（可选）

```bash
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```

放个一键脚本自己按要求操作吧，我实在写不动了...

## 安装客户端

+ Windows版本
  
  1. [Trojan-Qt5](https://drive.sysy.su/d/%E8%BD%AF%E4%BB%B6/Trojan-Qt5-Windows-1.4.0.7z)
  
  2. [V2rayN](https://github.com/2dust/v2rayN/releases)
  
  两者皆可，自行下载。

+ Android版本
  
  1. [igniter](https://github.com/trojan-gfw/igniter/releases)

+ iOS版本
  
  自行在App Store下载shadowrocket

## 常用命令

+ 重载nginx配置

```bash
nginx -s reload
```

+ trojan运行状态

```bash
systemctl status trojan
```

+ 关闭trojan

```bash
systemctl stop trojan
```

+ 跟踪trojan日志

```bash
journalctl -u trojan -f
```

# ---END---