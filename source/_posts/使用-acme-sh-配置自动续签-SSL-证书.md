---
title: 使用 acme.sh 配置自动续签 SSL 证书
date: 2022-06-24 17:37:12
tags:
---

### 介绍

*TLS*或传输层安全性及其前身*SSL*（代表安全套接字层）是用于将正常流量包装在受保护的加密包装器中的Web协议。

使用此技术，服务器可以在服务器和客户端之间安全地发送流量，而不会使消息被外部方拦截。证书系统还帮助用户验证他们正在连接的站点的身份。

在本指南中，我们将向你展示如何配置自动续签SSL证书，以便在Debian 10服务器上与Nginx Web服务器一起使用。

## 先决条件

- 一台 Debian 10 服务器、一个具有管理权限的非 root 用户和一个活动的防火墙。要设置这些东西，请遵循 [Debian 10 教程的初始服务器设置](https://sysy.su/2022/05/21/%E5%A6%82%E4%BD%95%E7%A7%91%E5%AD%A6%E7%9A%84%E4%BD%BF%E7%94%A8Debian/)。
- Nginx 安装在你的服务器上，遵循[如何在 Debian 10 上安装 Nginx](https://sysy.su/2022/05/25/%E5%A6%82%E4%BD%95%E5%9C%A8Debian%E4%B8%8A%E5%AE%89%E8%A3%85Nginx/)。

## 步骤 1 — 安装 [acme.sh](http://acme.sh/)

[acme.sh](https://acme.sh/) 是一个集成了 ACME 客户端协议的 Bash 脚本，作者是 [@neilpangxa](https://twitter.com/neilpangxa)，按照[官方文档](https://github.com/acmesh-official/acme.sh)说明，我们直接在 Linux 下安装。

安装acme前置依赖：

```bash
sudo apt install curl socat
```

安装acme：

```bash
curl https://get.acme.sh | sh -s email=username@example.com
```

请注意替换 `username@example.com` 为你自己的邮箱，避免无法收到上游证书的邮件通知，比如 Let's Encrypt 偶尔会错发证书，然后就会邮件通知你，这时候就需要重新签发一次证书了。

安装完成后重新加载 Bash：

```bash
source ~/.bashrc
```

然后也可以开启自动更新：

```bash
acme.sh --upgrade --auto-upgrade
```

## 步骤 2 — 选择默认 CA

目前 [acme.sh](http://acme.sh/) 支持四个正式环境 CA，分别是 Let's Encrypt、Buypass、ZeroSSL 和 [SSL.com](http://ssl.com/)，默认使用 ZeroSSL，如果需要更换可以使用如下命令：

切换 Let's Encrypt

```bash
acme.sh --set-default-ca --server letsencrypt
```

切换 Buypass

```bash
acme.sh --set-default-ca --server buypass
```

切换 ZeroSSL

```bash
acme.sh --set-default-ca --server zerossl
```

切换 [SSL.com](http://ssl.com/)

```bash
acme.sh --set-default-ca --server ssl.com
```

切换 Google Public CA

```bash
acme.sh --set-default-ca --server google
```

## 步骤 3 — 使用 DNS API 验证签发证书

[acme.sh](http://acme.sh/)提供了三种验证方式：HTTP 方式、手动 DNS 方式和 DNS API 方式。**推荐使用 DNS API 方式**。

这里以 Cloudflare 为例，登录 Cloudflare Dash 后在 [API Token](https://dash.cloudflare.com/profile/api-tokens) 菜单里查看你的**Global API Key**，复制备用。

接着在终端运行

```bash
export CF_Key="复制的Global API Key"
export CF_Email="你的cloudflare邮箱"
```

然后开启 [acme.sh](http://acme.sh/) 的 DNS API 模式申请证书

```bash
acme.sh --issue --dns dns_cf -d example.com -d *.example.com
```

请把`example.com`替换为你自己的域名。

## 步骤 4 — 安装证书

把证书安装到`/etc/ssl`目录下需要先给用户分配权限：

```bash
sudo chown -R $USER:$USER /etc/ssl
```

请把`example.com`替换为你自己的域名。

```bash
acme.sh --install-cert -d example.com \
--key-file       /etc/ssl/example.com.key  \
--fullchain-file /etc/ssl/example.com.crt \
--ca-file        /etc/ssl/example.com.ca.crt \
--reloadcmd     "sudo systemctl restart nginx"
```

对应的 Nginx 配置指定证书文件

```bash
ssl_certificate /etc/ssl/example.com.crt;
ssl_certificate_key /etc/ssl/example.com.key;
ssl_trusted_certificate /etc/ssl/example.com.ca.crt;
```

## 步骤 5 — 配置 Nginx 以使用 SSL

我们已经在`/etc/ssl`目录下创建了密钥和证书文件。现在我们只需要修改我们的Nginx配置就能利用这些证书了。

我们将对配置进行一些调整。

1. 我们将创建一个包含 SSL 密钥和证书文件位置的配置代码段。
2. 我们将创建一个包含强 SSL 设置的配置代码段，这些设置将来可用于任何证书。
3. 我们将调整我们的Nginx服务器块来处理SSL请求，并使用上面的两个代码段。

这种配置Nginx的方法将使我们能够保持干净的服务器块，并将常见的配置段放入可重用的模块中。

### 创建指向 SSL 密钥和证书的配置代码段

首先，让我们在`/etc/nginx/snippets`目录中创建一个新的Nginx配置代码段。

为了正确区分此文件的用途，我们称之为：`cer-local.conf`

```bash
sudo nano /etc/nginx/snippets/cer-local.conf
```

在此文件中，我们将证书配置文件的存放地址放到里面。将以下行添加到文件中：

```bash
ssl_certificate /etc/ssl/example.com.crt;
ssl_certificate_key /etc/ssl/example.com.key;
ssl_trusted_certificate /etc/ssl/example.com.ca.crt;
```

添加这些行后，保存并关闭文件。

### 使用强加密设置创建配置代码段

接下来，我们将创建另一个代码段，用于定义一些 SSL 设置。这将为Nginx设置一个强大的SSL密码套件，并启用一些高级功能，这将有助于保持我们的服务器安全。

我们将设置的参数可以在将来的Nginx配置中重用，因此我们将为文件指定一个通用名称：

```bash
sudo nano /etc/nginx/snippets/ssl-params.conf
```

为了安全地设置Nginx SSL，我们将使用Remy van Elst在 [**Cipherli.st**](https://cipherli.st/) 网站上的建议。此站点旨在为流行软件提供易于使用的加密设置。

**注意：****[Cipherli.st](http://cipherli.st/)** 站点上的建议设置提供了强大的安全性。有时，这是以提高客户端兼容性为代价的。如果您需要支持较旧的客户端，可以通过单击页面上标记为“**是，给我一个适用于旧版/旧版软件的密码套件**”的链接来访问该列表。该列表可以替换为以下项目。

使用哪种配置的选择很大程度上取决于你需要支持的内容。它们都将提供出色的安全性。

出于我们的目的，我们可以完整地复制提供的设置。我们只需要做一些小的修改。

首先，我们将为上游请求添加首选的 DNS 解析程序，本指南将使用谷歌DNS作为DNS 解析程序。

其次，我们将注释掉设置严格传输安全标头的行。在取消注释此行之前，你应该花点时间阅读[HTTP严格传输安全或HSTS](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security)，特别是其[“预加载”功能](https://hstspreload.org/)。预加载 HSTS 可提高安全性，但如果意外启用或错误启用，可能会产生深远的影响。

将以下内容复制到代码段文件中：

```bash
ssl_protocols TLSv1.2;
ssl_prefer_server_ciphers on;
ssl_dhparam /etc/nginx/dhparam.pem;
ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384;
ssl_ecdh_curve secp384r1; # Requires nginx >= 1.1.0
ssl_session_timeout  10m;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off; # Requires nginx >= 1.5.9
ssl_stapling on; # Requires nginx >= 1.3.7
ssl_stapling_verify on; # Requires nginx => 1.3.7
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;
# Disable strict transport security for now. You can uncomment the following
# line if you understand the implications.
# add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";
```

### 生成 dhparam.pem

OpenSSL 的 dhparam 用于生成和管理 dh 文件。dh(Diffie-Hellman) 是著名的密钥交换协议，它可以保证通信双方安全地交换密钥。

使用如下命令生成一个 `dhparam.pem` 文件：

```bash
sudo openssl dhparam -out /etc/nginx/dhparam.pem 2048
```

### 调整Nginx配置以使用SSL

现在我们有了代码段，我们可以调整Nginx配置以启用SSL。

本指南假设你正在`/etc/nginx/sites-available/your_domain`目录中使用自定义服务器块配置文件，如安装Nginx的先决条件教程中[步骤5](https://sysy.su/2022/05/25/%E5%A6%82%E4%BD%95%E5%9C%A8Debian%E4%B8%8A%E5%AE%89%E8%A3%85Nginx/#%E6%AD%A5%E9%AA%A4-5-%E2%80%93-%E8%AE%BE%E7%BD%AE%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%9D%97)所述。

现在，打开配置文件进行调整：

```bash
sudo nano /etc/nginx/sites-available/your_domain
```

如果遵循先决条件，则服务器块将如下所示：

```bash
server {
    listen 80;
    listen [::]:80;

    root /var/www/your_domain/html;
    index index.html index.htm index.nginx-debian.html;

    server_name your_domain www.your_domain;

    location / {
            try_files $uri $uri/ =404;
    }

}
```

你文件的顺序可能不同，并且你可能具有一些其他自定义配置语句，这没关系，因为我们只需要更新SSL代码段就行。我们将新增一个监听443端口的服务器块以提供 SSL 流量，然后修改之前的服务器块自动将流量重定向到443端口 。

**注意：**我们将使用302重定向，直到我们验证一切正常之后，可以将其更改为永久的301重定向。

在现有配置文件中，新建服务器块监听443端口以使用SSL，并用`include`导入我们之前创建的两个代码段：

```bash
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    include snippets/cer-local.conf;
    include snippets/ssl-params.conf;

    root /var/www/your_domain/html;
    index index.html index.htm index.nginx-debian.html;

    server_name your_domain www.your_domain;

    . . .
}
```

接下来，将旧的服务器块修改如下：

```bash
. . .
server {
    listen 80;
    listen [::]:80;

    server_name your_domain www.your_domain;

    return 302 https://$server_name$request_uri;
}
```

这是一个基本配置，用于侦听80端口并执行重定向到 HTTPS，完成编辑后保存并关闭文件。

一个完整的配置文件可能如下所示：

```bash
server {
    listen 80;
    listen [::]:80;

    server_name your_domain www.your_domain;

    return 301 https://$server_name$request_uri;
}


server {
    listen 443 ssl;
    listen [::]:443 ssl;
    include snippets/cer-local.conf;
    include snippets/ssl-params.conf;

    root /var/www/your_domain/html;
    index index.html index.htm index.nginx-debian.html;

    server_name your_domain www.your_domain;

    location / {
            try_files $uri $uri/ =404;
    }
}
```

## 步骤 6 — 调整防火墙

如果按照先决条件指南的建议启用了防火墙，则需要调整设置以允许 SSL 流量。幸运的是，Nginx在安装时注册了一些配置文件。

我们可以通过键入以下内容来查看可用的配置文件：

```bash
sudo ufw app list
```

你应该会看到如下列表：

```bash
OutputAvailable applications:
. . .
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
. . .
```

你可以通过键入以下内容来查看当前设置：

```bash
sudo ufw status
```

如果你遵循了先决条件，它将如下所示，这意味着只允许将 HTTP 流量发送到 Web 服务器：

```bash
OutputStatus: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
Nginx HTTP                 ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

为了另外允许HTTPS流量进入，我们可以允许“Nginx Full”配置文件，然后删除冗余的“Nginx HTTP”配置文件允许：

```bash
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
```

你的状态现在应如下所示：

```bash
sudo ufw status
```

```bash
OutputStatus: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
Nginx Full                 ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
Nginx Full (v6)            ALLOW       Anywhere (v6)
```

正确配置防火墙后，我们可以继续测试Nginx配置。

## 步骤 7 - 启用Nginx中的更改

现在我们已经进行了更改并调整了防火墙，我们可以重新启动Nginx来实现我们的新更改。

首先，我们应该检查以确保我们的文件中没有语法错误。我们可以通过键入以下内容来执行此操作：

```bash
sudo nginx -t
```

如果一切成功，你将获得如下所示的结果：

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

如果输出与上述内容匹配，则配置文件没有语法错误。我们可以安全地重新启动Nginx来实现我们的更改：

```bash
sudo systemctl restart nginx
```

通过测试我们的Nginx配置，我们可以继续测试我们的设置。

## 步骤 8 — 测试加密

现在，我们已准备好测试 SSL 服务器。

打开 Web 浏览器，然后在地址栏中键入`https://`+服务器的域名或 IP：

```bash
https://your_domain_or_server_IP
```

如果不出意外查看浏览器地址栏，你将会看到一个安全的小锁。在这种情况下，意味着SSL证书正在加密你的连接。

![Nginx 自签名证书警告](https://s1.ax1x.com/2022/06/24/jF1Dk6.png)

如果你为Nginx配置了自动将HTTP内容重定向到HTTPS的服务器块，你还可以检查重定向功能是否正确：

```bash
http://server_domain_or_IP
```

如果地址栏的`http://`自动变成了`https://`，则表示你的重定向工作正常。

## 完结

现在你的网站已将 Nginx 服务器配置为对客户端连接使用强加密。这将允许你安全地为请求提供服务，并防止外部各方读取你的流量。
