---
title: ubuntu16.04安装selenium与phantomjs
date: 2016-12-31 15:56:13
tags: python phantomjs 
---

#更新系统
sudo apt-get update

#安装中文语言包
apt-get install language-pack-zh-hans 

python -V
#安装 pip
sudo apt-get install python-pip -y
#安装 selenium
sudo pip install selenium
如果出现 locale.Error: unsupported locale setting 错误，则执行
export LC_ALL=C

#安装 python requests 模块
pip install requests

#安装phantomjs 1.9.8
sudo apt-get install build-essential chrpath libssl-dev libxft-dev -y

sudo apt-get install libfreetype6 libfreetype6-dev -y

sudo apt-get install libfontconfig1 libfontconfig1-dev -y

cd ~

export PHANTOM_JS="phantomjs-1.9.8-linux-x86_64"

wget https://bitbucket.org/ariya/phantomjs/downloads/$PHANTOM_JS.tar.bz2

sudo tar xvjf $PHANTOM_JS.tar.bz2

sudo mv $PHANTOM_JS /usr/local/share

sudo ln -sf /usr/local/share/$PHANTOM_JS/bin/phantomjs /usr/local/bin

phantomjs --version