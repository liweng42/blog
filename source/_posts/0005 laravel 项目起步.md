---
title: laravel 项目起步
date: 2016-07-27
tags: laravel
---

* 建表
``` bash
php artisan make:migration create_users_table --create=users #创建users表
```
* 建立表的字段
``` bash
php artisan make:migration add_votes_to_users_table --table=users #给users表增加votes字段
```
* 建立模型
``` bash
php artisan make:model name
```
* 模型与user的互相绑定
``` bash
写方法  hasmany  belengsto......
```
* 建立代理类
``` bash
make:repository
make:bindings
```
* 在controler里面设置代理类
``` php
    public function __construct(CourselistRepository $repository)
    {
        $this->repository = $repository;

        $this->middleware('auth',['except'=>['index','show']]);
        # code...
    }
```
* index  显示  然后跳转到 create   ceate里面会传递一些需要的东西
* creat 会跳转到 store
* 设置 request 设置验证用户的输入条件
``` bash
php artisan make:request
```