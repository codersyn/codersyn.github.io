---
title: 用curl实现网页自动签到
date: 2022-05-12 19:10:27
tags:
---

> 最近在网上看到一个免费分享iOS已购应用账号的网站，但是虽说免费，实则需要用积分来兑换，而积分通过充值或者签到来获取，作为资深白嫖党充值肯定是不会充值的，当然选择签到白嫖，不过签到讲究的就是一个日积月累，虽说动动手指就能完成的事，但是长期下来也是一件很累人的事，何况时不时还会忘记，所以这里直接利用curl挂个脚本每天自动定时签到。

1. 先进入需要自动签到的网站：[考拉爱分享 (kolagame.xyz)](http://www.kolagame.xyz/)

2. 正常登录网站，勾选“记住我”选项

<img src="https://s1.ax1x.com/2022/05/20/OL8B4A.png" title="" alt="" data-align="center">

3. 按F12打开开发者工具，切换到“网络”选项卡

<img src="https://s1.ax1x.com/2022/05/20/OLGm8I.png" title="" alt="" data-align="center">

4. 清除下面这些用不到的信息

<img src="https://s1.ax1x.com/2022/05/20/OLJBlt.png" title="" alt="" data-align="center">

5. 筛选器里选全部，然后点击签到按钮，会看到控制台弹出一些信息

<img src="https://s1.ax1x.com/2022/05/20/OLYiAe.png" title="" alt="" data-align="center">

6. 点击下面那个网页

<img src="https://s1.ax1x.com/2022/05/20/OLtXsx.png" title="" alt="" data-align="center">

可看到返回了一个json，msg后面的值是Unicode编码，转换成中文其实就是签到成功，获得50积分，那这个url就是我们需要的

7. 右键，复制curl

<img title="" src="https://s1.ax1x.com/2022/05/20/OLUygs.png" alt="" data-align="left">

此时如果新建一个记事本，粘贴进去，可看到

```bash
curl 'http://www.kolagame.xyz/wp-admin/admin-ajax.php' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6' \
  -H 'Connection: keep-alive' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  -H 'Cookie: wordpress_b31798e9c4af3742c**********9f864=W163843958869%7C1654226980%7C1m8GCqULeMKFksD1xfW*******6weDHRQuZDyo7QIWi7%7C9732d0bba8e6c70a*******09e456368c1458c95ad6ca6c67ad481348863f3b6c; pps_cookie_431=3_days; wordpress_logged_in_b31798e9c4af3742c2******64=W163843958869%7C1654226980%7C1m8GCqULeMKFksD1xfW******eDHRQuZDyo7QIWi7%7C5d4485412bcb7edb8dad16902bef84d9******e4a1d145b9209827aa153b' \
  -H 'Origin: http://www.kolagame.xyz' \
  -H 'Referer: http://www.kolagame.xyz/' \
  -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.64 Safari/537.36 Edg/101.0.1210.47' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'action=daily_sign' \
  --compressed \
  --insecure
```

如果现在把这串代码粘贴进服务器终端就代表点了一次签到，我们把它放在脚本里，然后服务器添加一个定时任务，到时间就自动执行一遍脚本，这样就实现了每天自动签到

*注意：服务器如果为CentOS 7且版本较低的话不支持`--data-raw`选项，需要改为`--data`*

8. 创建一个日志文件夹存放json反馈，然后写个脚本并创建定时任务
+ 创建存放日志的文件夹

```bash
mkdir qiandao
```

注意此处是直接用root用户创建的文件夹，所以`qiandao`文件夹是在`root`目录下。

+ 编写脚本

```bash
cd qiandao
vi qiandao.sh
```

然后脚本里写入：

```bash
echo "-------------------" >> /root/qiandao/qiandao.log
echo $(date +%F%n%T) >> /root/qiandao/qiandao.log
echo $(curl 'http://www.kolagame.xyz/wp-admin/admin-ajax.php' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  -H 'Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6' \
  -H 'Connection: keep-alive' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  -H 'Cookie: wordpress_b31798e9c4af3742c**********9f864=W163843958869%7C1654226980%7C1m8GCqULeMKFksD1xfW*******6weDHRQuZDyo7QIWi7%7C9732d0bba8e6c70a*******09e456368c1458c95ad6ca6c67ad481348863f3b6c; pps_cookie_431=3_days; wordpress_logged_in_b31798e9c4af3742c2******64=W163843958869%7C1654226980%7C1m8GCqULeMKFksD1xfW******eDHRQuZDyo7QIWi7%7C5d4485412bcb7edb8dad16902bef84d9******e4a1d145b9209827aa153b' \
  -H 'Origin: http://www.kolagame.xyz' \
  -H 'Referer: http://www.kolagame.xyz/' \
  -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.64 Safari/537.36 Edg/101.0.1210.47' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'action=daily_sign' \
  --compressed \
  --insecure) >> /root/qiandao/qiandao.log
```

给脚本赋予执行权限：

```bash
chmod +x qiandao.sh
```

这样执行脚本之后结果就会反馈到qiandao文件夹下的qiandao.log日志里，注意把括号里curl的值换成你自己的

+ 添加定时任务

`crontab -e`编辑crontab文件，写入定时任务

```bash
00 12 * * * /root/qiandao/qiandao.sh
```

这样就会每天中午12点自动执行签到脚本，并把结果反馈到日志文件里

9. 查看反馈结果

```bash
cat /root/qiandao/qiandao.log
```

可在终端看到反馈信息：

<img title="" src="https://s1.ax1x.com/2022/11/25/zY5H29.png" alt="" data-align="left">

这里Unicode转换为中文为“您今天已经签到了，明天再来吧！”，证明脚本生效。这样就解放双手不必每天手动签到了。

其实原理很简单，通过浏览器F12开发者工具抓包签到按钮，然后利用curl和crontab每天帮我们自动访问即可达到每天自动签到的目的。
