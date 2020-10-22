---
title: centos7安装php7.2并安装php-redis扩展
date: 2020-08-25 19:32:27
categories: 
- 编码
tags: 
- centos
- php
---
首先更新源
`yum install epel-release`
`rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm`
`rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm`
```bash
yum install -y php72w php72w-fpm php72w-opcache php72w-xml php72w-mcrypt php72w-gd php72w-devel php72w-mysql php72w-intl php72w-mbstring  php72w-cli php72w-common php72w-devel php72w-intl php72w-pecl-redis
```

遇到错误，类似如下错误：
`Transaction check error:
    file /etc/httpd/conf.d/php.conf from install of mod_php72w-7.2.14-1.w7.x86_64  ...
错误概要`

解决方法：`yum -y remove 上面提示有冲突的包`


如果报 没有可用软件包 php72w-mcrypt
`yum  install epel-release`
`yum install libmcrypt libmcrypt-devel mcrypt mhash`

在执行：`php -v` 应该已经更新到7.2版本了。

如果缺少其他扩展，如redis,执行命令即可：
`sudo yum install php72-php-redis`

其他常用命令:
`sudo systemctl status php72-php-fpm.service`
`sudo systemctl restart php72-php-fpm.service`
`systemctl start php-fpm.service`