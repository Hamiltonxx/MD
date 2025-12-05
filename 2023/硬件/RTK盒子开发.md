# 硬件准备
| 硬件名 | 品牌 | 型号 | 数量 |
| --- | --- | --- | --- |
| raspberry pi | raspberry pi | 4B | 1 |
| Micro SD Card | SanDisk | A1 32G | 1 |
| GPS板卡 | 和芯星通 | UB482 | 1 |
| Sim卡 | 中国移动/电信 | 4G/5G | 1 |
| 4G模块 | Quectel | EC20 | 1 |
| 接线和供电板卡 | Trimble | BD970 | 1 |
| 天线 | 略 | 略 | 2 |
| 电源 | 略 | 略 | 1 |
| 墨水屏 | 略 | 略 | 1 |

# 资料准备
[raspberry pi os](https://downloads.raspberrypi.org/raspios_arm64/images/raspios_arm64-2022-04-07/2022-04-04-raspios-bullseye-arm64.img.xz)  
[UB482用户手册](https://www.unicorecomm.com/assets/upload/file/UB482_User_Manual_CH_R41.pdf)

# 硬件组装


# 烧录SD卡
用Raspberry Pi Imager烧录 Raspberry Pi OS with desktop(64 bit) 20220404版本。

# 开机与配置
## VNC Server
```shell
Menu > Preferences > Raspberry Pi Configuration > Interfaces. 
```
这里SSH已默认Enable. VNC, Serial Port, GPIO全都Enable. 
```shell
Options > Security > Encryption & Authication
```
Encryption: Prefer Off,  Authentication: VNC Password  
Users & Permissions: Standard User: password  
最后点 Apply 和 OK  
配置生效需要Reboot.
此时可以通过screen sharing等app，输入树莓派的局域网ip访问。 

# 软件安装
```shell
sudo apt update
sudo apt upgrade

sudo apt install python3-pil.imagetk
sudo apt install redis
```
## clone代码
```shell
git clone http://git.yijianar.com/guozheng/raspberry_rtk.git
cd raspberry_rtk
pip install -r requirements.txt
```
这样，我们就能通过sudo python main.py来启动程序了。

# 4G驱动
插入带有USB接口的EC20模块到树莓派.
```shell
lsusb
  Bus 001 Device 011: ID 2c7c:0125 Quectel Wireless Solutions Co., Ltd. EC25 LTE modem
lsmod
  usb_wwan               24576  1 option
  usbserial              45056  3 pl2303,usb_wwan,option
ls /dev | grep ttyUSB
  ttyUSB0
  ttyUSB1
  ttyUSB2
  ttyUSB3
  ttyUSB4
dmesg | grep ttyUSB
  [ 8368.784968] usb 1-1.3: GSM modem (1-port) converter now attached to ttyUSB1
  [ 8368.785568] usb 1-1.3: GSM modem (1-port) converter now attached to ttyUSB2
  [ 8368.786116] usb 1-1.3: GSM modem (1-port) converter now attached to ttyUSB3
  [ 8368.786647] usb 1-1.3: GSM modem (1-port) converter now attached to ttyUSB4
```
通过上面这些命令，可以看到树莓派已识别EC20模块。  
接下来安装wvdial依赖包，控制EC20模块博号上网。
```shell
sudo apt install wvdial
sudo nano /etc/wvdial.conf
  [Dialer Defaults]
  Init1 = ATZ
  Init2 = ATQ0 V1 E1 S0=0 &C1 &D2 +FCLASS=0
  Modem Type = Analog Modem
  Baud = 9600
  New PPPD = yes
  Modem = /dev/ttyUSB3
  ISDN = 0
  Phone = *99#
  Password = card
  Username = card
```
最后 sudo wvdial即可上网. 通过 ifconfig 也能看到ppp0的网络端口。说明已经能通过EC20访问移动网络了。  
但此时还不能ping通www.baidu.com 
```shell
ip route
sudo ip route del default via 192.168.x.x
sudo ip route del default via 10.64.64.64
sudo ip route add default via 10.64.64.64
``` 
这样就能ping通baidu.愉快上网了。  
故事还没结束，我们不能每次开机后都手动敲命令联网。
## 开机自动联网
```shell
sudo nano /etc/ppp/peers/wvdial
  defaultroute
  replacedefaultroute
```
设置wvdial的ppp0为默认路由，这样 route -n后就能看到ppp0排第一位了。  

# 网络穿透(删)
```shell
ssh-keygen -t rsa
ssh-copy-id -i ~/.ssh/id_rsa.pub hamilton@rtkbox.yijianar.com
ssh -R 8222:127.0.0.1:22 -N -f hamilton@rtkbox.yijianar.com
```
前两行把ssh密钥拷给服务器，使得能免密ssh服务器。  
第三行 -R 表示remote port forwarding, -N 表示not to execute a remote command, -f 表示background执行。
最后，用Remote Port Forwarding，别忘了服务器上改sshd_config. 实际ssh -R会在python里执行，因为要等待网络连接。
```shell
AllowTcpForwarding yes
GatewayPorts yes
```
这样我们就能通过
```shell
ssh yijian@rtkbox.yijianar.com -p 8222
```
来外网登录树莓派了.

# 开机自启
## /etc/rc.local
```shell
echo "sleep 15 to wait sim" >> /home/yijian/logs/system.log
sleep 10
echo "begin dial.." >> /home/yijian/logs/system.log
date +"%T"  >> /home/yijian/logs/system.log
sudo wvdial >> /home/yijian/logs/system.log &
date +"%T"  >> /home/yijian/logs/system.log

# echo "start sshr for remote login.." >> /home/yijian/logs/system.log
# sudo python3 /home/yijian/raspberry_rtk/sshr.py >> /home/yijian/logs/system.log &
# echo "I need x window to start ui. So start ui command is in systemd" >> /home/yijian/logs/system.log
```
注意，之前曾想在开机后自动运行SSH -R使盒子和rtkbox server建立TCP连接，使得开机后我们可以随时远程登录盒子。愿望是美好的，可是我们用的EC20 4G模块丢包比较严重，管道建立不稳，很快就会断开。
## systemd tkui.service
由于rc.local会先于图形界面运行。所以要运行tkinter UI，必须要在systemd设置
```shell
[Unit]
Description=Tkinter
After=syslog.target default.target multi-user.target

[Service]
User=yijian
ExecStart=/usr/bin/python3 /home/yijian/raspberry_rtk/main_ui.py
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```
```shell
sudo systemctl daemon-reload
sudo systemctl enable tkui
```
这样reboot，桌面启动完就会自动运行我们的main_ui.

# ui data separate
传感器产生的数据，我们不单想要让现场实施人员(盒子界面上)查看，还想让其他人通过开发者平台(伪实时)web查看。那我们就得把UI和数据做分离，且引入生产者(producer)/消费者(consumer)模式。显然通过GPS串口、USB、网络模块产生数据的一方为生产者，盒子UI和开发者平台为消费者。生产者生产的数据存入redis. 消费者通过微服务获取redis数据。 限于网络因素，开发者平台直连盒子会有困难。我们改用盒子每隔10秒向开发者平台数据库发送一次数据。这样web前端接口就可以通过调用开发者平台的接口而不是盒子的接口来获取空间数据。  
发送的数据格式是:
```shell
timestamp: 1662516218.394005
serial_number: 1000000059a5a9ff
phone_number: 18600001111
qianxun: cXhubm1zMDA1OmQxMWY1Mjk=\r\n\r\n
longitude: 121.52816420749998
latitude: 31.273895480833335
height: 7.3930
direction: 0

# 以下字段不显示在开发者平台界面上
direction_status: 3
satellite: 14
status: 5 (浮动解,可用)
```
如果点 recordtime 距离现在超过半分钟，可以认为设备离线。 前端可以用不同的颜色表示。
## cronjob
我们用crontab来做每隔10秒发送数据。由于crontab最小到分，没有秒的参数
```shell
* * * * * (cd /home/yijian/raspberry_rtk;python copy_data.py;sleep 10;python copy_data.py;sleep 10;python copy_data.py;sleep 10;python copy_data.py;sleep 10;python copy_data.py;sleep 10;python copy_data.py)
```
