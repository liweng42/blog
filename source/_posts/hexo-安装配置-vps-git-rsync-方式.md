---
title: hexo 安装配置 vps git rsync 方式
date: 2015-12-31 15:56:13
tags: hexo 
---

### 0. 整体思路
hexo MAC本机写博客后，使用 git 同步到 VPS 上的gitlab，在使用rsync同步到nginx服务器。

### 1. hexo 简介
hexo 是一套nodejs开发的开源博客程序，这个博客程序与wordpress最大的不同是可以本地运行然后将生成的静态文件上传到服务器，即本地写博客然后把生成的博客文章同步上传到服务器，服务器上就是一个纯静态的博客了。具体参考[hexo](https://hexo.io/zh-cn/)。

### 2. hexo 安装步骤
前提是安装nodejs，这个自行解决吧。:-)
	
    npm install hexo-cli -g
    hexo init blog
    cd blog
    npm install
    hexo server

然后访问 http://localhost:4000， 就可以看到博客了。

### 3. hexo 使用
进入blog 目录，使用 hexo 对应命令即可

```
cd ~/blog
hexo new "新博客"  #新建博客后，打开markdown编辑器写博客
hexo g            #hexo 产生静态文件
hexo s            #hexo 启动服务器
```

### 4. VPS 安装 rsync
- 4.1 先安装rsync

	```
	yum -y install rsync
	yum install xinetd
	chkconfig rsync on
	service xinetd start
	```

- 4.2 修改配置  `vim /etc/xinetd.d/rsync`
	
	```
	disable = no       //把disable = yes改成no   
	```


- 4.3 创建配置文件  `vim /etc/rsyncd.conf` 此文件默认不存在

	```
	[blog]
	# destination directory
	path = /var/www/www.yoursite.com/
	# Hosts you allow to copy (specify source Host)

	hosts allow = *
	hosts deny = *
	list = true
	uid = root
	gid = root
	read only = no
	```
	
### 5. mac 本机使用git提交到vps的gitlab库中
博客在MAC上写完后，需要把.md文件 git 到VPS上的gitlab
		
```
cd ~/blog/source/_posts
git add .
git commit -m '新博客注释'
git push origin master
```
	
### 6. hexo 生成静态文件后上传到nginx服务器
```
hexo g
rsync -az ~/blog/public/ root@ipaddress:/var/www/www.yoursite.com
```

### 7. hexo 配置
hexo 配置文件在 blog 目录下的 _config.yml 文件，一般需要修改 Site 配置节以及最后面的 theme 。
	
