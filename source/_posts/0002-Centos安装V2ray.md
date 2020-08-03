---
title: Centos7.8安装V2ray，科学上网指南
date: 2020-07-29 15:19:11
categories: 
- 编码
tags: 
- v2ray
- 科学上网
---
![](https://img.liweng42.com/upload/image/202007/096a3d9e78f59bc2458410773406c433.jpg)
## 前言
* Linode是一款国外比较好的VPS，性能一直比较稳定，价格也很稳定（不便宜）。我从15年开始一直用最低配的1核/1G/25G/2000G流量，每个月5美刀，搭建博客、科学上网都可以，总体还不错，想买的[戳这里Linode](https://www.linode.com/?r=5983e9477f163113d02c5777227c1683d576e351)，从链接进入有优惠哦。
* 作为一名IT人士，如果不会科学上网，就是一个局域网程序猿了，百度的质量太差了，而且基本英文的东西搜不出来，必须使用google才能更好的找东西。
## 正题，如何用Linode的VPS来科学上网。
* 前几年科学上网都使用shadowsocks，不过因为后来作者被喝茶，GFW也破解了shadowsocks的协议，所以我现在一般不用这个了。
* 现在科学上网比较稳的是v2ray，下面介绍v2ray的在Centos7.8上的安装方法。
## Centos7.8安装v2ray
* 在服务器上执行命令，一路回车就可以：
    > bash <(curl -s -L https://git.io/v2ray.sh)
* 在上面命令的最后会有提示，接下来输入：
    > v2ray url 或者 v2ray qrcode，就可以复制链接了。
* 记住界面上提示的防火墙端口号，然后执行：
    > firewall-cmd --add-port=xxxxx/tcp --permanent
## Centos7.8一键安装锐速BBR方法
* 这种方法是把内核升级到了4.X版本
    > wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh

### 附录，Centos防火墙基本操作命令
* firewall-cmd --add-port=XXXXX/tcp --permanent #永久添加例外XXXXX端口(全局)
* service firewalld start
* service firewalld restart
* service firewalld stop
* firewall-cmd --list-all 
* firewall-cmd --query-port=8080/tcp
* firewall-cmd --permanent --add-port=80/tcp
* firewall-cmd --permanent --remove-port=8080/tcp
* firewall-cmd --reload