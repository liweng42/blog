---
title: shadowsocks 科学上网利器
date: 2015-12-06
tags: shadowsocks
---

### 服务器端配置
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
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
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

### 客户端设置
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