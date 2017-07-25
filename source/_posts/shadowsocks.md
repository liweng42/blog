1、服务器安装好Shadowsocks，配置好
2、MACBOOK 安装ShadowsocksX 客户端，链接上服务器
3、浏览器chrome与firefox配置好翻墙代理，测试OK
4、MACBOOK 安装privoxy 客户端，配置好listen 地址为 0.0.0.0:8118，forward-socks5 / 127.0.0.1:1080
5、MACBOOK 启动privoxy，本地命令行 curl 测试IP地址为翻墙出去的服务器IP，OK
6、vagrant 当中命令行配置http代理，http_proxy
export http_proxy=http://127.0.0.1:8118 && export https_proxy=http://127.0.0.1:8118

export http_proxy=http://192.168.10.1:8118 && export https_proxy=http://192.168.10.1:8118

export http_proxy=http://192.168.1.144:8118 && export https_proxy=http://192.168.1.144:8118

sudo /Applications/Privoxy/startPrivoxy.sh


1、服务器安装Shadowsocks，
apt-get update
apt-get install python-pip
pip install shadowsocks
vi /etc/shadowsocks.json
{
    "server":"139.162.114.5",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"iloveyou",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}

ssserver -c /etc/shadowsocks.json start

vi /etc/rc.local
在exit 0 加入一行：/usr/bin/python /usr/bin/ssserver -c /etc/shadowsocks.json  -start


unset http_proxy
unset https_proxy