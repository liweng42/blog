---
title: Ubuntu 16.04 安装 python3 flask nginx gunicorn supervisor
---

### 更新系统
``` bash
sudo apt-get update
```

### 安装 python3
``` bash
sudo apt-get install python3-pip python3-dev nginx
```

### 安装 virtualenv
``` bash
sudo pip3 install virtualenv
```

### 查看系统中已经安装过的python版本
``` bash
ll /usr/bin/py*
```
下面截图是我的系统中安装的python版本，可以看到已经有python2.7与python3.5了
![](http://image.liweng42.com/uploads/2017/07/e0098591344c8cd3.png)

### 安装配置 Flask
* 假设你的 Flask 目录名为：myproject，进入 myproject 目录，并创建一个名为 "python3.5" 的虚拟环境，事实上这个虚拟环境的名称叫什么都可以，具体virtualenv怎么使用参考 [virtualenv介绍](http://pythonguidecn.readthedocs.io/zh/latest/dev/virtualenvs.html)

``` bash
cd ~/myproject
virtualenv -p /usr/bin/python3.5 python3.5
```

* 激活这个虚拟环境，查看版本，为3.5+
``` bash
source python3.5/bin/activate
python --version
```

* 安装 flask 项目的依赖
``` bash
pip install -r requirements.txt
```

* 这个项目依赖文件 requirements.txt 可以这样产生
``` bash
pip freeze > requirements.txt
```
* 也可以这样产生，参考 [pipreqs](https://github.com/bndr/pipreqs)
``` bash
pipreqs ~/myproject
```

* 查看防火墙，并增加相应端口进去，8080 与 8001 后面不用可以关闭
``` bash
sudo ufw status
sudo ufw allow 80
sudo ufw allow 8080
sudo ufw allow 8001
```

* 针对 Flask 项目部署时一定要设置 Debug = False
``` bash
Debug = False
```

* 以下的配置为我自己项目的配置使用，请根据自己项目的实际情况来操作
``` bash
python manage.py runserver -h 0.0.0.0 -p 8080
```

* 直接在浏览器输入 ip:8080，即可看到页面
``` bash
ctrl + c 停止服务
```
* Flask 配置完毕

### 安装 gunicorn
* 安装 gunicorn， 此时仍然在python3.5 的虚拟env下面
``` bash
pip install gunicorn
pip install gevent
```

* 配置 gunicorn， 把下面的配置存储为一个文件 gunicorn_deploy_config.py
``` bash
# gunicron 部署配置

# 绑定的ip及端口号
bind = '127.0.0.1:8001'
# 进程数
"""
# (2 Workers * CPU Cores) + 1
# ---------------------------
# For 1 core  -> (2*1)+1 = 3
# For 2 cores -> (2*2)+1 = 5
# For 4 cores -> (2*4)+1 = 9
"""
workers = 2
# 监听队列
backlog = 2048
# 使用gevent模式，还可以使用sync 模式，默认的是sync模式
worker_class = "gevent"
debug = True
# 你项目的根目录, app 启动的文件所在目录
chdir = '/var/www/myproject'
# 进程名称
proc_name = 'gunicorn.pid'
```

* 启动 gunicorn，假设你的 app.run() 所在的文件为：wsgi_gunicorn.py，则
``` bash
/var/www/myproject/python3.5/bin/gunicorn -k gevent -c /var/www/myproject/gunicorn_deploy_config.py wsgi_gunicorn:app
```

* 直接在浏览器输入 ip:8001 即可看到页面，表明gunicorn安装成功

### 安装配置 nginx
* 安装 nginx
``` bash
sudo apt install nginx
```

* 创建站点配置文件
``` bash
cd /etc/nginx/sites-available/
vi myproject
```

* 把下面的 nginx 配置copy进去
``` bash
server {
        listen 80;
        root /var/www/myproject;

        server_name www.myproject.com;

        location /static {
                alias /var/www/myproject/app/static;
                expires max;
        }

        location / {
            proxy_pass http://127.0.0.1:8001;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

}
```

* 创建站点文件软连接并reload
``` bash
cd ../sites-enabled/
ln -s /etc/nginx/sites-available/myproject
service nginx reload
```

* 访问 www.myproject.com 查看网站

### 安装配置 supervisor
* 安装 supervisor
``` bash
sudo apt-get install supervisor
```

* 创建 myproject 项目配置文件
``` bash
cd /etc/supervisor/conf.d
vi myproject.conf
```

* 将supervisor项目文件配置copy进去
``` bash
[program:myproject-gunicorn]
command = /var/www/myproject/python3.5/bin/gunicorn -k gevent -c /var/www/myproject/gunicorn_deploy_config.py wsgi_gunicorn:app
directory = /var/www/myproject/
user = root
```

* 重启supervisor，查看运行状态
``` bash
sudo service supervisor restart 
supervisorctl status
```

### 更新 Flask 项目代码后，需要重启supervisor
* 先结束进程
``` bash
kill $(ps aux|grep 'gunicorn'|awk '{print $2}')
service supervisor restart
```
* supervisor 检查某个进程状况并杀掉，该命令可能会用到
``` bash
supervisorctl status myproject-gunicorn | sed "s/.*[pid ]\([0-9]\+\)\,.*/\1/" | xargs kill -HUP
```

完！