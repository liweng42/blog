---
title: centos 6.5下 vsftp 虚拟用户配置
date: 2017-10-17
tags: vsftp
---
centos 6.5下 vsftp 虚拟用户配置

### 基本知识
* 虚拟用户：与系统无关联，不能登入系统，只能访问FTP服务器
* vsftp的服务进程是vsftpd
* vsftpd的配置文件是/etc/vsftpd/vsftpd.conf .
* vsftpd的用户文件是/etc/vsftpd/ftpusers
* vsftpd的用户文件是/etc/vsftpd/user_list
* 推荐使用虚拟用户登入vs-FTP服务器

### 安装vsftp
* 查看是否安装vsftpd服务，并安装
```
chkconfig --list
yum -y install vsftpd
```

### 配置虚拟用户
* 检查服务器selinux是否开启，如果开启，关闭selinux
    ```
    /usr/sbin/sestatus -v
    ```
* 关闭selinux方法
    修改/etc/selinux/config 文件，  将SELINUX=enforcing改为SELINUX=disabled，  重启机器即可

* 创建虚拟用户文本文件,添加虚拟用户和密码
    ```
    cd /etc/vsftpd/
    touch  vuser.txt
    ```
    奇数行是用户名，偶数是密码，例如：
    ```
    test
    123456
    ```
* 安装 db_load
    ```
    yum install db4-utils db4-devel db4-4.3
    ```
* 生成虚拟数据库文件
    ```
    db_load –t hash -T –f /etc/vsftpd/vuser.txt  /etc/vsftpd/vuser.db  #这个可能不管用
    db_load -T -t hash -f vusers.txt vuser.db
    ```
* 配置PAM文件，目的是对客户端进行验证，编辑/etc/pam.d/vsftpd，注释掉所有内容后添加
    ```
    auth                 required     pam_userdb.so   db=/etc/vsftpd/vuser  
    account              required     pam_userdb.so   db=/etc/vsftpd/vuser
    ```
* 修改虚拟数据库文件vuser.db的权限为 700
    ```
    chmod   700  vuser.db
    ```
* 修改vsftpd.conf配置文件，使虚拟用户可以访问vsftpd服务器，增加以下参数，注意：“=”两边不能有空格
    ```
    guest_enable=YES             ####激活虚拟账户  
    guest_username=ftp           ####把虚拟账户绑定为系统账户ftp
    pam_service_name=vsftpd      ####使用PAM验证  
    ```
* 设置虚拟用户的主配置文件，编辑vsftpd.conf文件，添加
    ```
    user_config_dir=/etc/vsftpd/vsftpd_user_conf 
    ```
* 设置虚拟用户配置文件,与虚拟账户同名
    ```
    touch  /etc/vsftpd/vsftpd_user_conf/test
    ```
* 编辑刚才创建的test文件
    ```
    local_root=/ftpdir/               #### 指定虚拟用户在系统用户下面的路径
    anon_world_readable_only=NO       ###浏览FTP目录和下载  
    anon_upload_enable=YES            ###允许上传  
    anon_mkdir_write_enable=YES       ###建立和删除目录  
    anon_other_write_enable=YES       ####改名和删除文件  
    ```
* 注意要修改好 local_root 对应的目录权限，一般设置为ftp用户与用户组即可读写

* vsftpd.conf部分解释：
    ```
    #登录和访问控制选项优化：
        listen=YES/NO                          #设置独立进程控制vsftpd
        anonymous_enable=YES/NO                #允许/禁止匿名用户登陆
        banner_file=/etc/vsftp/banner_file     #在文件banner_file添加欢迎词即可
        cmds_allowed=HELP,DIR,QUIT,!           #列出被允许使用的FTP命令
        ftpd_banner=welcome to ftp server      #和第三条一样是欢迎词屏幕，方法不一样
        local_enable=YES/NO                    #允许/禁止本地用户登陆
        pam_service_name=vsftpd                #使用pam模块进行ftp客户端的验证
        userlist_deny=YES/NO                   #禁止/允许文件列表user_list的用户访问ftp服务器
        userlist_file=/etc/vsftpd/user_list    #用户列表文件
        userlist_enable=YES/NO                 #激活/失效userlist_deny功能  
        tcp_wrappers=YES/NO                    #启用/不启用tcp_wrappers控制服务访问的功能          

    #匿名用户选项的优化：
        anon_mkdir_write_enable=YES/NO         #允许/禁止匿名用户创建目录、删除文件
        anon_root=/path/to/file                #设置匿名用户的根目录，默认是/var/ftp/
        anon_upload_enable=YES/NO              #允许/禁止匿名用户上传
        anon_world_readable_only=YES/NO        #禁止/允许匿名用户浏览目录和下载
        ftp_username=anonftpuser               #把匿名用户绑定到系统用户名
        no_anon_password=YES/NO                #不需要/需要匿名用户的登录密码

    #本地用户选项的优化：
        chmod_enable=YES/NO                    #允许/禁止本地用户修改文件权限
        chroot_list_enable=YES/NO              #启用/不启用禁锢本地用户在家目录
        chroot_list_file=/path/to/file         #建立禁锢用户列表文件，一行一个用户
        guest_enable=YES/NO                    #激活/不激活虚拟用户
        guest_username=系统实体用户              #虚拟用户绑定在某个实体用户上
        local_root=/path/to/file               #指定或修改本地用户的根目录
        local_umask=具体权位数字                 #设置本地用户新建文件的权限
        user_config_dir=/path/to/file          #激活虚拟用户的的主配置文件

    #目录选项的优化：
        text_userdb_names=YES/NO               #启用/禁用用户的名称取代用户的UID 

    #文件传输选项优化：
        chown_uploads=YES/NO                   #启用/禁用修改匿名用户上传文件的权限
        chown_username=账户                    #指定匿名用户上传文件的所有者
        write_enable=YES/NO                   #启用/禁止用户的写权限
        max_clients=数字                       #设置FTP服务器同一时刻最大的连接数
        max_per_ip=数字                        #设置每ip的最大连接数

    #网络选项的优化：
        anon_max_rate=数字                     #设置匿名用户最大的下载速率（单位字节）
        local_max_rate=数字                    #设置本地用户最大的下载速率
    
    #邮件相关：
        banned_email_file=/etc/vsftpd/vsftpd.banned_emails     #允许/禁止邮件的使用的存放路径和目录
        deny_email_enable=YES/NO                               #允许/禁止匿名用户使用邮件作为密码        
    ```            