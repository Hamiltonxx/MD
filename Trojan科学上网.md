# 前言
GFW不息，奋斗不止。从最初的Shadowsocks,到V2Ray,再到Trojan，演绎了一批追求自由曙光的人与GFW的斗智斗勇的历程。一提科学上网，大多数人首先会想到VPN，其实这两者不是一回事。简单来说，一个说的是协议，一个说的是软件。 本文将指导服务器的安装搭建和不同平台客户端的安装与连接。如果你不想知道原理，不想安装服务器，可以直接跳到客户端部分。

# Trojan简介
Trojan官网文档给了一个很简短的定义,参照[https://trojan-gfw.github.io/trojan](https://trojan-gfw.github.io/trojan/) Trojan是一个能绕过GFW而不被识别的机制。在穿透GFW时，人们认为强加密和随机混淆可能会欺骗GFW的过滤机制。然而，Trojan实现了这个思路的反面：它模仿了互联网上最常见的HTTPS协议，以诱骗GFW认为它就是HTTPS，从而不被识别。网络上通常把它理解为v2ray+ws+TLS的lite版，但安全和性能方面一般比v2ray+ws+TLS更好。

![http://blog.cirray.cn/images/Trojan%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.svg](http://blog.cirray.cn/images/Trojan%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.svg)
## Trojan-Go 访问原理
当一个客户端试图连接Trojan-Go的监听端口时,会发生下面的事情:  
1. 如果TLS握手成功,检测到TLS的内容非Trojan协议(有可能是HTTP请求,或者来自GFW的主动探测). Trojan-Go将TLS连接代理到本地127.0.0.1:80上的HTTP服务。这时在远端看来, Trojan-Go服务就是一个HTTPS网站。  
2. 如果TLS握手成功,并且确认是Trojan协议头部,并且其中的密码正确,那么服务器将解析来自客户端的请求并进行代理,否则和上一步的处理方法相同。  
3. 如果TLS握手失败, 说明对方使用的不是TLS协议进行连接。此时Trojan-Go将这个TCP连接代理到本地127.0.0.1:1234上运行的HTTPS服务(或者HTTP服务),返回一个展示400 Bad Request的HTTP页面。  

# Trojan服务器搭建
## 第一步 购买VPS
推荐上搬瓦工购买。自己使用的话买个50刀一年的足够了。可以选美国加州、洛杉矶等美国西海岸城市的服务器。
## 第二步 购买域名
可以在阿里云上随便买个便宜的域名，然后把域名解析到上面购买的服务器的ip地址。
## 第三步 装系统
一般在购买时就会让你选择一个os版本进行安装。推荐使用最新Ubuntu Server。
## 第四步 下载运行一键脚本
```bash
curl -O https://raw.githubusercontent.com/Miuzarte/hijk.sh/main/Original/trojan-go.sh && chmod +x trojan-go.sh
sudo ./trojan-go.sh
```
按照提示一步一步往下执行即可。最近脚本里有点bug，CA服务器如果无法访问可以仍旧改回letsencrypt
```bash
/root/.acme.sh/acme.sh --set-default-ca --server letsencrypt
```
## 最后一步 拿到凭证
一键脚本结束后，可以拿到服务器地址(即购买的域名或者IP)，端口(默认为443)，密钥(上一步设置的)。  
这里提供一个凭证
```bash
Server Address: zzn.cirray.cn
Port: 443
Password: 123456
```

# Trojan客户端
Trojan-Qt5 目前最新版本为1.4.0，由于作者已经停止更新，因此1.4.0也算是最后一个版本。  

操作系统 | 下载地址
--- | ---
Windows | https://ssrvps.org/wp-content/uploads/2020/08/22/Trojan-Qt5-Windows.7z
MacOS | https://ssrvps.org/wp-content/uploads/2020/08/22/Trojan-Qt5-MacOS.dmg
Linux | https://github.com/charlieethan/Trojan-Qt5/releases/download/v1.4.0/Trojan-Qt5-Linux.AppImage
iOS(iphone) | OneClick等APP
Android | Igniter等APP

![](https://ssrvps.org/wp-content/uploads/2020/08/20200822_5f40a6a15ec54.jpg)

# Tips
一般我们会用到两种模式，PAC模式和Global(全局)模式。  
Global模式会使所有流量都走服务器。但通常我们只需要国外流量走服务器即可，所以我们一般用PAC模式。 当有的网页打不开时可以换成Global模式。  
对于Mac和Linux用户，通常我们需要在Terminal上FQ. 因为我们通常通过命令行来安装软件和依赖库。
## MacOS
```shell
export https_proxy=http://127.0.0.1:58591 http_proxy=http://127.0.0.1:58591 all_proxy=socks5://127.0.0.1:51837
```
## Ubuntu
config
```json
{
    "run_type": "client",
    "local_addr": "127.0.0.1",
    "local_port": 1080,
    "remote_addr": "trojan.cirray.cn",
    "remote_port": 443,
    "password": [
        "zaq1xsw2"
    ],
    "log_level": 1,
    "ssl": {
        "verify": false,
        "verify_hostname": true,
        "cert": "",
        "cipher": "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES128-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA:AES128-SHA:AES256-SHA:DES-CBC3-SHA",
        "cipher_tls13": "TLS_AES_128_GCM_SHA256:TLS_CHACHA20_POLY1305_SHA256:TLS_AES_256_GCM_SHA384",
        "sni": "",
        "alpn": [
            "h2",
            "http/1.1"
        ],
        "reuse_session": true,
        "session_ticket": false,
        "curves": ""
    },
    "tcp": {
        "no_delay": true,
        "keep_alive": true,
        "reuse_port": false,
        "fast_open": false,
        "fast_open_qlen": 20
    }
}
```
```shell
wget https://github.com/trojan-gfw/trojan/releases/download/v1.16.0/trojan-1.16.0-linux-amd64.tar.xz
tar xvf && cd
vim config.json
./trojan
```
```shell
sudo apt install proxychains-ng, curl
vim /etc/proxychains4.conf
    socks5 127.0.0.1 1080
proxychains4 wget www.google.com
```
检验自己的ip
```shell
curl -4 ip.sb
proxychains4 curl -4 ip.sb
```

# 附 设置SOCKS5代理
```bash
ssh zzn.cirray.cn -l root -p 26388 -D 12345
# 输入密码
```
此时建立了本地和服务器之间的tunnel。
在浏览器的 设置 -> 网络设置 -> 手动配置Proxy代理  
SOCKS Host: 127.0.0.1 Port: 12345  SOCKS v5  
在Proxy DNS when using SOCKS v5处打勾  
结束。此时浏览器就可以访问外网。

# SSH端口转发
SSH Port Forwarding,或叫SSH Tunneling,是另一种穿越防火墙的技术。  
Question 1: A能ping通B,B能ping通C,A ping不通C,A要想ping通C要怎么办？  
Question 2: 假设我们的局域网网段是192.168.50.1,里面有一台服务器S:192.168.50.231,我们的用户机子C在子网下，子网网段是192.168.5.1,C的IP是192.168.5.164. 显然C能ping通S，但S ping不通C。想让S ping通C该怎么办？  
共有三种SSH port forwarding:  
1. Local Port Forwarding. Forwards a connection from the client host to the SSH server host and then to the destination host port.
2. Remote Port Forwarding. Forwards a port from the server host to the client host and then to the destination host port.
3. Dynamic Port Forwarding. creates a SOCKS proxy server that allows communication across a range of ports.
## Local Port Forwarding
Local port forwarding allows you to forward a port on the local machine to a port on the remote machine, which is then forwarded to a port on the destination machine. destination machine 可以= remote machine.
假设你有一个mysql server运行在db001.host(private network),端口3306, 机子pub001.host可以访问到它。现在我们想让自己的mysql client通过pub001来访问mysql server.
```shell
ssh -L 3336:db001.host:3306 user@pub001.host
```
这时我们把mysql client连接数据库地址指向127.0.0.1:3336, 就会被转发到db001.host:3306
```shell
ssh -L [LOCAL_IP:]LOCAL_PORT:DESTINATION:DESTINATION_PORT [USER@]SSH_SERVER
```
## Remote Port Forwarding
假设你在自己的机子上开发了一个web应用，你想展示给远方朋友看。你没有public IP,朋友没法访问。这时你需要一台SSH server, 设置remote port forwarding.
```shell
ssh -R 8080:127.0.0.1:4000 -N -f dev@beta-dev.yijianar.com
```
-f表示background执行；-N表示not to execute a remote command. 输完beta-dev的登录密码后，在本地
```shell
python3 -m http.server 4000
```
远方朋友就能通过浏览器访问 http://beta-dev.yijianar.com:8080 看到你的本地web应用啦。
注意，先确保SSH server里8080端口没被占用。 可以用
```shell
sudo netstat -tupln | grep 8443
sudo lsof -i -P -n | grep LISTEN
```
查看端口占用情况
## Dynamic Port Forwarding
动态端口转发可以让你在local机子上创建一个socket来作为SOCKS proxy server.当client连接到这个端口时，connection会转发到remote机子，再转发给destination机子的dynamic port. 
```shell
ssh -D [LOCAL_IP:]LOCAL_PORT [USER@]SSH_SERVER
ssh -D 9090 -N -f root@trojan.cirray.cn
```