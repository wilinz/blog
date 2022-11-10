---
title: "校园网账号密码认证之802.1X"
date: "2022-05-14"
---

## 原文链接

[https://itw01.com/25K74E4.html](https://itw01.com/25K74E4.html)

说起802.1X，可能很多高校的学子对它没有好感，因为某捷、某三公司利用它限制了很多高校学生的网路使用。实际上，802.1X在使用者认证方面是挺优秀的。相比传统路由中的WEP、WPA-PSK认证、甚至是Portal认证(即WEB认证)都有很多独有的特点。

手机搜索到的通过802.1X EAP进行保护的WIFI![](images/5657_CY6AfT_25K74E4.png!r1024x0.jpg)

使用802.1X协议的主要优点有：

1. 加密方式安全多样，可以使用PAP(明文不加密)、EAP、CHAP、MSCHAPv2等加密方式。
2. 接入使用者的使用者名称和密码的管理十分科学。
3. 相对于Portal认证，802.1X的计费方式更加实时高效。

但是用802.1X协议建立WIFI有如下限制：

1. 需架设RADIUSSERVER，RADIUS伺服器软体是一个认证计费伺服器，由于RADIUS协议简单明确，可扩充，因此得到了广泛应用，包括普通电话上网、ADSL上网、小区宽频上网、IP电话、行动电话预付费等业务的计费。RADIUSSERVER储存着接入使用者的使用者名称和密码，这些使用者名称和密码可以储存在档案里，也可以通过RADIUSSERVER拓展储存于SQL资料库或LDAP伺服器中。当然，RADIUS SERVER也可以是OpenWRT路由器本身。

详细关于802.1X和RADIUS的介绍和架设可以参阅英文网站：

[http://tldp.org/HOWTO/html\_single/8021X-HOWTO/](http://tldp.org/HOWTO/html_single/8021X-HOWTO/)

经过查阅大量的OpenWrt官方的档案资料，发现Openwrt实际上是支援建立通过802.1X协议保护的WiFi的。OpenWRT有很多WIFI套件，详见//wiki.openwrt.org/doc/howto/wireless.utilities，支援802.1X的套件是wpad和hostapd，OpenWRT原生携带了wapd-mini包，这个包精简了对802.1X的支援，只需安装完整版wpad即可，当然，利用hostapd也可以实现，不过这个还有待研究。

安装方法：   

#先解除安装wpad-mini

```
opkg remove wpad-mini
opkg updateopkg install wpad
```

安装完成之后，直接通过LUCI即可建立通过802.1X协议保护的WiFi。

![](images/5659_nklGRC_25K74E4.bmp!r1024x0.jpg)

![](images/5700_FQn7EC_25K74E4.bmp!r1024x0.jpg)

接下来就是建立RADIUS SERVER，上图中的Radius认证伺服器需要填写自己架设的RadiusServer的IP地址或域名，如果RadiusServer是OpenWRT本身，填写127.0.0.1.Radius认证埠预设1812即可，Radius认证金钥可以自己定义。Radius计费伺服器如果没有计费需求，可以忽略。

RadiusServer我选择在外部Linux伺服器上利用freeradius软体建立，OpenWRT上也有提供freeradius2包，可以以相同的方法在OpenWRT上建立。

安装freeradius，可以利用Linux发行版的包管理器安装：

在RHEL/CengOS下：sudo yum install freeradius

在Ubuntu/Debain下：sudo apt-get install freeradius

当然，也可以到//www.freeradius.org/下载原始码编译安装，不过编译时间会比较长。

安装完成后，使用命令：radiusd-X尝试执行freeradius，-X表示在前台执行，并输出除错资讯，如果没有错误资讯，证明正常执行。按Ctrl+C结束它。

/etc/raddb/radiusd.conf是freeradius的配置档案，一般保持预设即可，如有需要，根据里面的英文提示修改即可。

/etc/raddb/client.conf是列出允许连线freeradius的客户端的档案，编辑个档案，在末尾加入「client ip{ secret your\_secret shortname localhost}

其中，ip更改为你的路由器的IP，secret即再上图中填写的Radius认证金钥，shortname可以自定义。

/etc/raddb/users储存了接入使用者的使用者名称和密码。

编辑它，在末尾加入行your\_username Cleartext-Password:=」your\_password」.

your\_username、your\_password根据需要自己定义，可以定义多个使用者名称和密码，不同使用者通过不同使用者名称和密码登陆。

利用Radius，还可以把使用者的使用者名称和密码储存于SQL资料库或LDAP伺服器当中，可以对接入使用者的接入时长进行统计、控制、甚至计费，可以使单个使用者名称和密码仅可以在一个装置上登陆，这有待于你的发掘。
