---
title: centos 6.5 安装配置nginx+mysql+php
date: 2015-12-31 00:08:31
tags: [centos, nginx, mysql, php]
---

#### 1. 关闭selinux

`vi /etc/selinux/config`

```
#SELINUX=enforcing #注释掉
#SELINUXTYPE=targeted #注释掉
SELINUX=disabled #增加这行
```
 
 然后 `reboot`  #重启系统
 	
#### 2. 系统约定

- 软件源代码包存放位置：/opt
- 源码包编译安装位置：/usr/local/软件名字

#### 3. 下载软件工具包

- 先进入opt目录，`cd /opt`
- 下载nginx1.4.4  `wget http://nginx.org/download/nginx-1.4.4.tar.gz`
- 下载pcre8.35 `wget http://exim.mirror.fr/pcre/pcre-8.35.tar.gz`
- 下载mysql5.5.46 `wget http://mirrors.sohu.com/mysql/MySQL-5.5/mysql-5.5.46.tar.gz`
- 下载PHP5.3.28 `wget http://mirrors.sohu.com/php/php-5.3.28.tar.gz`
- 下载cmake（MySQL编译工具）`wget http://www.cmake.org/files/v2.8/cmake-2.8.8.tar.gz`
- 下载libmcrypt（PHPlibmcrypt模块）`wget http://nchc.dl.sourceforge.net/project/mcrypt/Libmcrypt/2.5.8/libmcrypt-2.5.8.tar.gz`

#### 4. 安装常用编译工具及库文件
- yum install make apr* autoconf automake bzip2 bzip2-devel curl curl-devel gcc gcc-c++ gcc-g77 e2fsprogs e2fsprogs-devel zlib* zlib-devel openssl openssl-devel pcre-devel gd gd-devel keyutils patch perl compat* mpfr cpp glibc libgomp libstdc++-devel ppl cloog-ppl keyutils-libs-devel libcom_err-devel libsepol-devel libselinux-devel krb5-devel zlib-devel libXpm* libvpx libjpeg libpng zlib libXpm libXpm-devel t1lib libt1-devel freetype freetype-devel libpng* libpng10 libpng10-devel libpng-devel php-common php-gd ncurses* ncurses-devel libtool* libtool-libs libxml2-devel patch glibc glibc-devel glib2 glib2-devel krb5 krb5-devel libevent libevent-devel libidn libidn-devel nss_ldap openldap openldap-clients openldap-devel openldap-servers openssl openssl-devel pspell-devel net-snmp* net-snmp-devel -y
- mysql 需要安装的工具
- yum -y install ntp vim-enhanced gcc gcc-c++ flex bison autoconf automake bzip2-devel ncurses-devel zlib-devel libjpeg-devel libpng-devel libtiff-devel freetype-devel libXpm-devel gettext-devel pam-devel libtool libtool-ltdl openssl openssl-devel fontconfig-devel libxml2-devel curl-devel libicu libicu-devel libmcrypt libmcrypt-devel libmhash libmhash-devel

#### 5. 安装cmake及MySQL
- 安装cmake

	```
	cd /opt && tar zxvf cmake-2.8.8.tar.gz
	cd cmake-2.8.8
	./configure && make && make install
	```
		
- 创建所需目录

	```
	mkdir -pv /usr/local/mysql/data
	```

- 创建mysql用户和mysql组

	```
	groupadd mysql	
	useradd -g mysql -s /usr/sbin/nologin mysql
	```

- 解压源码包
	
	```
	cd /opt
	tar zxvf mysql-5.5.46.tar.gz
	cd mysql-5.5.46
	```
		
- cmake编译

	```
	cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/usr/local/mysql/data -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_unicode_ci -DWITH_READLINE=1 -DWITH_SSL=system -DWITH_EMBEDDED_SERVER=1 -DENABLED_LOCAL_INFILE=1 -DDEFAULT_COLLATION=utf8_general_ci -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_DEBUG=0
	```
		
- 安装

	```
	make && make install
	```
	
- 复制配置文件
	 
	```
	cp support-files/my-medium.cnf /etc/my.cnf
	```
	
- 设置权限

	```
	chmod +x /usr/local/mysql
	chown -R mysql：mysql /usr/local/mysql
	chown -R mysql:mysql /usr/local/mysql/data
	```
		
- 配置开机自动启动

	```
	cp support-files/mysql.server /etc/init.d/mysqld
	chmod +x /etc/init.d/mysqld
	chkconfig –add mysqld
	chkconfig mysqld on
	```
		
- 修改配置文件 `vim /etc/my.cnf`

	在[mysqld]中添加
		
	```
	datadir = /usr/local/mysql/data
	log-error = /usr/local/mysql/data/error.log
	pid-file = /usr/local/mysql/data/mysql.pid
	user = mysql
	tmpdir = /tmp
	```
 
- 初始化数据库 
	
	```
	/usr/local/mysql/scripts/mysql_install_db –user=mysql –basedir=/usr/local/mysql –datadir=/usr/local/mysql/data
	```

- 手动启动MySQL 
	
	```
	service mysqld start
	```
	
- 编辑/etc/profile 
	
	```
	vi /etc/profile
	```
	
	
	把mysql服务加入系统环境变量：在最后添加下面这一行
	
	```
	export PATH=$PATH:/usr/local/mysql/bin
	```
	
- 使配置立即生效 
	
	```
	source /etc/profile
	```
	
- 测试MySQL是否启动，查看是否有mysql进程与端口
		
	```
	ps -ef | grep mysql
	netstat -tnlp | grep 3306
	```
		
- 测试mysql,mysqladmin,mysqldump命令是否能正常使用, 读取MySQL的版本信息

	```
	mysqladmin version
	```
- 设置Mysql密码 
	
	```
	mysql_secure_installation
	```

======到此MySQL编译安装完成=====
		
#### 6. 安装pcre及Nginx
- 安装pcre
	
	```
	cd /opt
	tar zxvf pcre-8.35.tar.gz
	cd pcre-8.35
	```
	
- 创建安装目录 `mkdir /usr/local/pcre`
- 配置 `./configure --prefix=/usr/local/pcre`
- 安装pcre `make && make install`
- 安装nginx ,注意:--with-pcre=/opt/pcre-8.35指向的是源码包解压的路径，而不是安装的路径，否则会报错

	```
	cd /opt
	groupadd www #添加www组
	useradd -g www www -s /bin/false #创建nginx运行账户www并加入到www组，不允许www用户直接登录系统
	tar zxvf nginx-1.4.4.tar.gz
	cd nginx-1.4.4
	./configure --prefix=/usr/local/nginx --without-http_memcached_module --user=www --group=www --with-http_stub_status_module --with-openssl=/usr/ --with-pcre=/opt/pcre-8.35
	make && make install
	```
	
- 启动nginx `/usr/local/nginx/sbin/nginx`
- 设置nginx开启启动， 编辑启动文件 `vi /etc/rc.d/init.d/nginx`
	
	添加下面内容
		
	```
	#!/bin/bash

	# nginx Startup script for the Nginx HTTP Server
	# it is v.0.0.2 version.
	# chkconfig: - 85 15
	# description: Nginx is a high-performance web and proxy server.
	# It has a lot of features, but it's not for everyone.
	# processname: nginx
	# pidfile: /var/run/nginx.pid
	# config: /usr/local/nginx/conf/nginx.conf
	nginxd=/usr/local/nginx/sbin/nginx
	nginx_config=/usr/local/nginx/conf/nginx.conf
	nginx_pid=/usr/local/nginx/logs/nginx.pid
	RETVAL=0
	prog="nginx"
	# Source function library.
	. /etc/rc.d/init.d/functions
	# Source networking configuration.
	. /etc/sysconfig/network
	# Check that networking is up.
	[ ${NETWORKING} = "no" ] && exit 0
	[ -x $nginxd ] || exit 0
	# Start nginx daemons functions.
	start() {
	if [ -e $nginx_pid ];then
	echo "nginx already running...."
	exit 1
	fi
	echo -n $"Starting $prog: "
	daemon $nginxd -c ${nginx_config}
	RETVAL=$?
	echo
	[ $RETVAL = 0 ] && touch /var/lock/subsys/nginx
	return $RETVAL
	}
	# Stop nginx daemons functions.
	stop() {
	echo -n $"Stopping $prog: "
	killproc $nginxd
	RETVAL=$?
	echo
	[ $RETVAL = 0 ] && rm -f /var/lock/subsys/nginx /usr/local/nginx/logs/nginx.pid
	}
	reload() {
	echo -n $"Reloading $prog: "
	#kill -HUP `cat ${nginx_pid}`
	killproc $nginxd -HUP
	RETVAL=$?
	echo
	}
	# See how we were called.
	case "$1" in
	start)
	start
	;;
	stop)
	stop
	;;
	reload)
	reload
	;;
	restart)
	stop
	start
	;;
	status)
	status $prog
	RETVAL=$?
	;;
	*)
	echo $"Usage: $prog {start|stop|restart|reload|status|help}"
	exit 1
	esac
	exit $RETVAL
	```

- 赋予文件执行权限 `chmod 775 /etc/rc.d/init.d/nginx`
- 设置开机启动 `chkconfig nginx on`
- 重启nginx `/etc/rc.d/init.d/nginx restart`

=====至此Nginx安装完毕=====

#### 7. 安装libmcrypt、jpeg、png、freetype及PHP
- 安装libmcrypt
		
	```
	cd /opt
	tar zxvf libmcrypt-2.5.8.tar.gz
	cd libmcrypt-2.5.8
	./configure && make && make install
	```
		
- 安装 jpeg6

	```
	mkdir /usr/local/jpeg6
	mkdir /usr/local/jpeg6/bin
	mkdir /usr/local/jpeg6/lib
	mkdir /usr/local/jpeg6/include
	mkdir /usr/local/jpeg6/man
	mkdir /usr/local/jpeg6/man/man1
	cd /opt
	```

- 下载jpegsrc.v6b.tar.gz
		
	```
	wget http://pkgs.fedoraproject.org/repo/pkgs/libjpeg/jpegsrc.v6b.tar.gz/dbd5f3b47ed13132f04c685d608a7547/jpegsrc.v6b.tar.gz
	tar zxvf jpegsrc.v6b.tar.gz
	cd jpeg-6b
	./configure --prefix=/usr/local/jpeg6/ --enable-shared --enable-static
	make && make install
	```
		
- 如果上面执行报错 
	> *make: ./libtool: Command not found* 
	
	解决方法：
	
	- 下载libtool
		
		```
		cd /opt
		wget http://ftp.gnu.org/gnu/libtool/libtool-2.2.6a.tar.gz
		tar zxvf libtool-2.2.6a.tar.gz
		cd libtool-2.2.6
		./configure && make && make install
		```

	- 更新jpeg-6b目录下的2个文件

		```
		cd /opt/jpeg-6b
		cp /usr/share/libtool/config/config.sub .
		cp /usr/share/libtool/config/config.guess .
		make clean
		./configure --prefix=/usr/local/jpeg6/ --enable-shared --enable-static
		make && make install
		```

- 安装png
	
	```
	cd /opt
	wget http://downloads.sourceforge.net/libpng/libpng-1.6.12.tar.gz
	tar zxvf libpng-1.6.12.tar.gz
	cd libpng-1.6.12
	./configure --prefix=/usr/local/png --enable-shared
	make && make install
	```
		
- 安装freetype

	```		
	cd /opt
	wget http://download.savannah.gnu.org/releases/freetype/freetype-2.5.3.tar.gz
	tar zxvf freetype-2.5.3.tar.gz
	cd freetype-2.5.3
	./configure --prefix=/usr/local/freetype --enable-shared
	make && make install
	```
		
- 安装PHP

	```		
	cd /opt
	tar zxvf php-5.3.28.tar.gz
	cd php-5.3.28
	mkdir -p /usr/local/php5
	./configure --prefix=/usr/local/php5 --with-config-file-path=/usr/local/php5/etc --with-mysql=/usr/local/mysql --with-mysqli=/usr/local/mysql/bin/mysql_config --with-pdo-mysql=/usr/local/mysql --with-mysql-sock=/tmp/mysql.sock --with-gd --with-iconv --with-zlib --enable-xml --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --enable-mbregex --enable-fpm --enable-mbstring --enable-ftp --enable-gd-native-ttf --with-openssl --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --without-pear --with-gettext --enable-session --with-mcrypt --with-curl --with-jpeg-dir=/usr/local/jpeg6/ --with-png-dir=/usr/local/png --with-freetype-dir=/usr/local/freetype
	make && make install
	```
	
- 配置php
		
	```
	cp php.ini-production /usr/local/php5/etc/php.ini #复制php配置文件到安装目录
	rm -rf /etc/php.ini #删除系统自带配置文件
	ln -s /usr/local/php5/etc/php.ini /etc/php.ini #添加软链接
	cp /usr/local/php5/etc/php-fpm.conf.default /usr/local/php5/etc/php-fpm.conf #拷贝模板文件为php-fpm配置文件
	vi /usr/local/php5/etc/php-fpm.conf #编辑
	user = www #设置php-fpm运行账号为www
	group = www #设置php-fpm运行组为www
	pid = run/php-fpm.pid #取消前面的分号
	```

- 设置 php-fpm开机启动
	
	```
	cp /opt/php-5.3.28/sapi/fpm/init.d.php-fpm /etc/rc.d/init.d/php-fpm #拷贝php-fpm到启动目录
	chmod +x /etc/rc.d/init.d/php-fpm #添加执行权限
	chkconfig php-fpm on #设置开机启动
	```
		
- 编辑php.ini 配置文件 `vi /usr/local/php5/etc/php.ini`

	```
	disable_functions = passthru,exec,system,chroot,scandir,chgrp,chown,shell_exec,proc_open,proc_get_status,ini_alter,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server,escapeshellcmd,dll,popen,disk_free_space,checkdnsrr,checkdnsrr,getservbyname,getservbyport,disk_total_space,posix_ctermid,posix_get_last_error,posix_getcwd, posix_getegid,posix_geteuid,posix_getgid, posix_getgrgid,posix_getgrnam,posix_getgroups,posix_getlogin,posix_getpgid,posix_getpgrp,posix_getpid, posix_getppid,posix_getpwnam,posix_getpwuid, posix_getrlimit, posix_getsid,posix_getuid,posix_isatty, posix_kill,posix_mkfifo,posix_setegid,posix_seteuid,posix_setgid, posix_setpgid,posix_setsid,posix_setuid,posix_strerror,posix_times,posix_ttyname,posix_uname
	```
		
- 列出PHP可以禁用的函数，如果某些程序需要用到这个函数，可以删除，取消禁用。

	```
	找到：;date.timezone =
	修改为：date.timezone = PRC #设置时区
	找到：expose_php = On
	修改为：expose_php = OFF #禁止显示php版本的信息
	找到：short_open_tag = Off
	修改为：short_open_tag = ON #支持php短标签
	```
		
=====至此php5安装完毕=====


#### 8. 配置nginx支持php

- 编辑配置文件,需做如下修改 `vi /usr/local/nginx/conf/nginx.conf`
 
 	```
	user www www; #首行user去掉注释,修改Nginx运行组为www www；
	```
	提示：必须与/usr/local/php5/etc/php-fpm.conf中的user,group配置相同，否则php运行出错。
	
- 添加nginx站点目录
 
	```
	cd /usr/local/nginx/	#进入nginx配置目录
	mkdir sites-available  #添加可用站点目录
	mkdir sites-enabled    #添加启用站点目录
	cd sites-available
	```

- 添加默认站点文件 `vi default`

	```
	server {
   	listen       80;
   	server_name  localhost;

   	#charset koi8-r;

		root  html;
		index index.php index.html;

   	location / {
   		try_files $uri $uri/ =404;
		}

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        # }

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        location ~ /\.ht {
            deny  all;
        }

        #error_log /var/log/nginx/default_error.log;
        #access_log /var/log/nginx/defalut_access.log;

    }
	```

- 建立软链接文件 `ln -s /usr/local/nginx/sites-availabel/defalut /usr/local/nginx/sites-enabled/default`
- 重启nginx `/etc/init.d/nginx restart` 或 `service nginx restart`
- 其他
	
	```
	cd /usr/local/nginx/html/ #进入nginx默认网站根目录
	rm -rf /usr/local/nginx/html/* #删除默认测试页
	vi index.php #编辑
	<?php
	phpinfo();
	?>
	```
- 设置目录所有者与权限
 
 	```
	chown www.www /usr/local/nginx/html/ -R #设置目录所有者
	chmod 700 /usr/local/nginx/html/ -R #设置目录权限
	```
		 
- **重启系统** `shutdown -r now`  必须操作！
		
=====至此Ningx与PHP安装配置完毕=====
