---
title: shadowsocks 科学上网利器
date: 2015-12-06
tags: shadowsocks
---

### 服务器端配置，以ubuntu为例
* 安装Shadowsocks
``` bash
apt-get update
apt-get install python-pip
pip install shadowsocks
vi /etc/shadowsocks.json
```
* 将配置COPY进去
``` bash
{
    "server":"ip",
    "server_port":9601,
    "local_address": "127.0.0.1",
    "local_port":1086,
    "password":"password",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```
* 服务器设置启动
```
vi /etc/rc.local
/usr/bin/python /usr/bin/ssserver -c /etc/shadowsocks.json  -start #在exit 0 前加入
```

### MAC客户端设置
* MACBOOK 安装ShadowsocksX 客户端，链接上服务器
* 浏览器chrome与firefox配置好翻墙代理，测试OK
* MACBOOK 安装privoxy 客户端，配置好listen 地址为 0.0.0.0:8118，forward-socks5 / 127.0.0.1:1080
* MACBOOK 启动privoxy，本地命令行 curl 测试IP地址为翻墙出去的服务器IP，OK
* vagrant 当中命令行配置http代理，http_proxy
* 可能会用到的命令
``` bash 
export http_proxy=http://127.0.0.1:8118 && export https_proxy=http://127.0.0.1:8118
sudo /Applications/Privoxy/startPrivoxy.sh
```
* 取消命令行代理
``` bash
unset http_proxy
unset https_proxy
```

### Windows 客户端设置
* 下载shadowsocks_win客户端软件。（该软件需要 .net 4.0以上运行环境请自行安装），将上面的服务器IP与端口填进去，密码也输入进去，并设置为开启状态。[shadowsocks_win下载](http://www.liweng42.com/Shadowsocks-4.0.8.zip)
* 下载chrome的翻墙代理软件switchsharp并解压缩，switchsharp是chrome的一个扩展插件，下载完成后直接在浏览器地址输入 chrome://extensions/，然后把switchsharp直接拖拽进去完成安装。[switchsharp下载](http://www.liweng42.com/switchsharp-1.9.48.zip)
* switchsharp 安装完成后，会在chrome地址栏右侧出现一个“地球”小图标，点击后选择“选项”，新增一个情景模式，在socks代理 输入127.0.0.1 端口为上方配置的local_port的端口，注意选择 socks v5，保存
* 然后在chrome浏览器访问 www.google.com 看看是否能科学上网了