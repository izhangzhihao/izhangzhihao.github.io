---
layout: post
title: 试用DNS Over Https
categories: 
description: 最近要在家办公，所以就想折腾一下家里的网络
keywords: DNS-Over-Https, DOH, NextDns, dnsmasq, PandoraBox, OpenWRT, Padavan, AdGuardHome
---

# 试用DNS Over Https

## 最原始方式：hosts

我一直在我的mac/windows设备上通过改hosts的方式过滤一些广告，加上浏览器的chrome插件。一直感觉也还可以。可是还有几点觉得不太方便：

* 需要隔段时间更新下hosts文件，还要避免和以前增加的自定义hosts冲突
* 安卓支持尚且可以，毕竟可以root。但是iphone支持就有点难办了
* 解决不了DNS劫持的问题（可以通过这个网站来判断是不是劫持了https://www.dnsleaktest.com）
* ...

## 去广告的DNS？

做了一番搜索之后，发现NextDNS貌似还可以的样子。于是开始试用：

在路由器上设置了我的NextDns的自定义ip之后发现，DNS劫持仍然存在，比如：我把baidu.com 和 zhihu.com都加入了黑名单，发现在使用`nslookup baidu.com`的时候会被运营商的DNS强(抢)奸(答)。然后试用了它提供的客户端，发现在iphone上可以很顺利的去除爱奇艺的广告，但是对腾讯视频无效。而我不想给所有设备都装上客户端，感觉挺麻烦的，爸妈也不会操作。

## Dns Over Https可以一战否？

其实NextDNS是支持Dns Over Https的，在它路由器设置的界面推荐使用UNIX Client/dnsmasq/Unbound/Stubby/pfSense的方式来搞。

## 搞起

首先给路由器刷了Breed（请自行搜索）。然后刷了PandoraBox（一个OpenWRT的衍生版），折腾一番后发现：

* 整个路由器的速度比官方固件要更慢，且不太稳定。
* 可以自定义的地方还是不太多，我用到的就是改改hosts，dnsmasq。
* 在PandoraBox上折腾DNS Over Https有点费劲，没有什么可用的安装包。UNIX Client/dnsmasq/Unbound/Stubby/pfSense/AdGuardHome都没装上...

换个方式，搜索一番发现貌似Padavan还不错，再试试：

首先装了官方固件：发现总体功能很多，但是有很多我用不到。并且路由器磁盘比较紧张。又看到了一个利用github action来自定义编译固件的项目（https://github.com/chongshengB/Padavan-build），太赞了。搞起！
Fork了之后，进行了一番修改，在自定义插件里面只留下了AdGuardHome。安装完成之后，**将NextDns作为AdGuardHome的上游DNS，启用Dns Over Https, 再把AdGuardHome作为dnsmasq的上游**
让我们来看看结果：


```
nslookup baidu.com
Server:		192.168.123.1
Address:	192.168.123.1#53

Non-authoritative answer:
Name:	baidu.com
Address: 0.0.0.0

nslookup zhihu.com
Server:		192.168.123.1
Address:	192.168.123.1#53

Non-authoritative answer:
Name:	zhihu.com
Address: 0.0.0.0
```

另外DNS劫持的检查（https://www.dnsleaktest.com）：

```
Test complete
Query round	Progress...	Servers found
1		......		1
2		......		1
3		......		1
4		......		1
5		......		1
6		......		1
IP	Hostname	ISP	Country
155.138.207.154	dns.nextdns.io.	Choopa, LLC	Atlanta, United States 
```

还有测速了一下，发现速度提升了一倍不止。。。稳定性更是非常棒！


完美！