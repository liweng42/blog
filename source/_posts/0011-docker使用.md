---
title: 0011-docker使用
date: 2020-11-18 16:44:42
tags:
---

docker 基本使用，编排php环境
https://zhuanlan.zhihu.com/p/265954653

https://juejin.cn/post/6844903892187906056

https://www.cnblogs.com/gerenboke/p/13637998.html

https://geixue.com/blogs/channels/docker/build-nginx-php-mysql-docker-boskik

docker pull nginx
docker run --name tmp-nginx -d nginx
docker cp tmp-nginx:/etc/nginx /Users/jerry/workspace/docker/nginx
docker rm -f tmp-nginx

docker run --name run-nginx -d -p 80:80 -v /Users/jerry/workspace/docker/www:/usr/share/nginx/html:ro nginx

docker rm -f run-nginx


docker build -t my-php-fpm:2021.2 .

docker run --name tmp-my-php-fpm -d my-php-fpm:2021.2
docker cp tmp-my-php-fpm:/usr/local/etc /Users/jerry/workspace/docker/php7.2
docker rm -f tmp-my-php-fpm


vi /Users/jerry/workspace/docker/nginx/conf.d/default.conf

 location ~ \.php$ {
        fastcgi_pass   php-fpm-container:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /var/www/html$fastcgi_script_name;
        fastcgi_param  SCRIPT_NAME      $fastcgi_script_name;
        include        fastcgi_params;
    }


cp /Users/jerry/workspace/docker/php7.2/etc/php/php.ini-development /Users/jerry/workspace/docker/php7.2/etc/php/php.ini

vi /Users/jerry/workspace/docker/php7.2/etc/php/conf.d/docker-php-ext-xdebug.ini

xdebug.remote_enable = On
xdebug.remote_handler = dbgp
xdebug.remote_host = host.docker.internal 
xdebug.remote_port = 9001
xdebug.remote_log = /var/log/php/xdebug.log
xdebug.idekey = PHPSTOR


docker run --name run-my-php-fpm \
-v /Users/jerry/workspace/docker/www:/var/www/html \
-v /Users/jerry/workspace/work-shh/code/api:/var/www/shh-api.test \
-v /Users/jerry/workspace/docker/php7.2/etc:/usr/local/etc \
-v /Users/jerry/workspace/docker/php7.2/log:/var/log/php \
-d my-php-fpm:2021.2


docker run --name run-nginx \
-p 80:80 \
-p 443:443 \
--link run-my-php-fpm:php-fpm-container \
-v /Users/jerry/workspace/docker/www:/var/www/html \
-v /Users/jerry/workspace/docker/nginx:/etc/nginx \
-v /Users/jerry/workspace/docker/ssl:/etc/nginx/ssl \
-v /Users/jerry/workspace/docker/nginx/log:/var/log/nginx \
-v /Users/jerry/workspace/work-shh/code/api:/var/www/shh-api.test \
-v /Users/jerry/workspace/labs/mytest.local:/var/www/mytest.local \
-d nginx


