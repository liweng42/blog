---
title: 0014-centos升级php7.4
date: 2020-12-03 10:23:49
tags:
---
https://www.cxybj.com/?p=543

php -v

安装Remi和EPEL数据源（仓库）

rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://rpms.remirepo.net/enterprise/remi-release-7.rpm


vi /etc/yum.repos.d/remi-php74.repo
enabled=1(将0 改成 1 )


yum -y upgrade php*

php -v

systemctl status php72-php-fpm.service
systemctl stop php72-php-fpm.service
systemctl start php-fpm.service
systemctl status php-fpm.service