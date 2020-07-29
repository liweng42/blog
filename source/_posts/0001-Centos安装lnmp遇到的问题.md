---
title: Centos7.8安装lnmp 1.7遇到的问题
date: 2020-07-29 13:29:21
tags:
---
## 背景
Centos 7.8 重新安装后，照例要初始化服务器，安装各种环境。lnmp是必不可少的，直接从[官方lnmp站点](https://lnmp.org)下载安装，具体过程参考lnmp官方站点文档。
## 遇到的问题
* lnmp1.7 安装完成后，检查服务器的nginx/php/mysql都正常，在添加一台vhost，在配置上let's encrenpt 但是在安装laravel开发的网站时报错
    > PHP Fatal error:  require(): Failed opening required '/home/wwwroot/xxx/public/../vendor/autoload.php' (include_path='.:/usr/local/php/lib/php') in /home/wwwroot/xxx/public/index.php on line 24"
* 这个错误的原因是lnmp不允许跨目录访问，需要将这个设置去掉。在网站目录下面有一个.user.ini文件， 直接将这个文件删除掉就可以了，删除后可以在重启一下lnmp即可。
    > cd 网站目录 & rm .user.ini
* 在访问laravel开发的网站就正常了
