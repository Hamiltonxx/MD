# 系统
ubuntu server 22.04 raspi

# 连wifi
```shell
ls /sys/class/net
ls /etc/netplan/
sudo vim /etc/netplan/50-cloud-init.yaml
# 在紧跟 version: 2行之后
wifis:
    wlan0:
        dhcp4: true
        optional: true
        access-points:
          "Onesight_5G":
            password: 'yijiankeji'
sudo netplan apply
ping www.baidu.com
ifconfig
```
## mdns
ip地址获取用的是DHCP,为了让局域网内其他机子能通过hostname来访问，我们装一下mdns
```shell
sudo apt install avahi-daemon
```
这样其他PC机就可以ping ubunturbp.local 和 ssh ubuntu@ubunturbp.local 来访问了。不用担心重启后IP地址改变。

# SSH SOCKS proxy
SOCKS代理是第5层代理，它可以用来建立安全路由和隧道。你可以创建一个从本地主机到远程主机的SOCKS代理，然后配置应用程序使得网络流量走代理路由。
## 免密ssh
```shell
ssh-keygen -t rsa
ssh-copy-id -i ~/.ssh/id_rsa.pub root@trojan.cirray.cn
```
## 运行代理
```shell
ssh -D34567 -fCqN root@trojan.cirray.cn
```
-f表示background执行，-C表示压缩所有数据，-q表示安静模式，-N表示不执行远程命令
## 检查tunnel
```shell
ss -natp | grep 34567
```
## 通过SOCKS代理连接
```shell
sudo apt install proxychains-ng
sudo vim /etc/proxychains4.conf
  socks5 127.0.0.1 34567
proxychains4 sudo curl ifconfig.me 
```

# 安装ROS2
```shell
locale  # check for UTF-8
sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
locale  # verify settings
sudo apt update && sudo apt install curl gnupg lsb-release
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(source /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
sudo apt update
sudo apt upgrade

sudo apt install ros-humble-ros-base
sudo apt install ros-humble-example-interfaces
```