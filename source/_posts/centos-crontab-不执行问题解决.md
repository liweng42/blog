---
title: centos crontab 不执行问题解决
date: 2016-01-06 10:20:09
tags: [centos, crontab]
---

最近在服务器上需要定时执行一个php脚本，需要设置crontab，写完配置后，发现并没有定时执行，后来发现是因为 crontab 并不会自动加载系统的环境变量，造成PHP找不到路径，就没有执行成功。

#### 1. 先打开crontab文件，写入定时运行配置. 
` vi /etc/crontab `

```
0  */1  *  *  * root /usr/local/php5/bin/php  a.php
```

之前是没有加PHP的执行路径，造成无法执行的问题。

#### 2. crontab 的每小时运行写法，注意是这种 */1 表示每小时运行一次。

#### 3. 修改完成后，重启服务即可

```
service crond restart
```
	

