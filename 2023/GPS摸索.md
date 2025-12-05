# 什么是GPS坐标
GPS坐标 = 给定位置的经度(longitude)和纬度(latitude). 简言之，纬度坐标是相对于赤道的一个点，而经度坐标是相对于英国格林威治皇家天文台子午线的一个点。
## WGS-84坐标系
地心坐标系、GPS原始坐标系。在中国，任何一个地图产品都不允许使用此坐标系，为了保密。
## GCJ-02坐标系
国测局坐标系、火星坐标系。国测局02年发布的坐标系，对经纬度数据作加密，加入了随机偏差。  
国内互联网地图至少使用GCJ-02进行首次加密，不允许直接使用WGS-84坐标，同时也不可转换为WGS-84坐标。  
高德、腾讯、Google中国地图都使用它。国内使用最广的坐标系。
## GCJ-02进一步偏移算法
百度坐标系、搜狗坐标系、图吧坐标系。
## 我们用哪个
WGS-84 -> GCJ02坐标系； WGS-84 -> 百度坐标系；算法都可以在网上找到。 国内通常用GCJ-02坐标系。 也是笔者最常使用的(高德和腾讯地图)。下面提供一个WGS-84 -> GCJ-02的python实现
```python
import math 

pi = 3.1415926535897932384626
a = 6378245.0
ee = 0.00669342162296594323

def wgs_gcj(lon, lat):
    dLat = transform_lat(lon - 105.0, lat - 35.0)
    dLon = transform_lon(lon - 105.0, lat - 35.0)
    radLat = lat / 180.0 * pi
    magic = math.sin(radLat)
    magic = 1 - ee * magic * magic
    sqrtMagic = math.sqrt(magic)
    dLat = (dLat * 180.0) / ((a * (1 - ee)) / (magic * sqrtMagic) * pi)
    dLon = (dLon * 180.0) / (a / sqrtMagic * math.cos(radLat) * pi)
    mgLat = lat + dLat
    mgLon = lon + dLon
    return str(mgLon)+","+str(mgLat)

def transform_lat(x, y):
    ret = -100.0 + 2.0 * x + 3.0 * y + 0.2 * y * y + 0.1 * x * y + 0.2 * math.sqrt(abs(x))
    ret += (20.0 * math.sin(6.0 * x * pi) + 20.0 * math.sin(2.0 * x * pi)) * 2.0 / 3.0
    ret += (20.0 * math.sin(y * pi) + 40.0 * math.sin(y / 3.0 * pi)) * 2.0 / 3.0
    ret += (160.0 * math.sin(y / 12.0 * pi) + 320 * math.sin(y * pi / 30.0)) * 2.0 / 3.0
    return ret

def transform_lon(x, y):
    ret = 300.0 + x + 2.0 * y + 0.1 * x * x + 0.1 * x * y + 0.1 * math.sqrt(abs(x))
    ret += (20.0 * math.sin(6.0 * x * pi) + 20.0 * math.sin(2.0 * x * pi)) * 2.0 / 3.0
    ret += (20.0 * math.sin(x * pi) + 40.0 * math.sin(x / 3.0 * pi)) * 2.0 / 3.0
    ret += (150.0 * math.sin(x / 12.0 * pi) + 300.0 * math.sin(x / 30.0 * pi)) * 2.0 / 3.0
    return ret
```

# 如何查看自己的GPS坐标
## 自带的apple maps
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1659492713685_IMG_FFF43148C31B-1.jpeg" width="400" height="800">

点击右上角的箭头，再点我的位置，就能看到。这种做法需要科学上网  

![mac maps](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1659492862491_macmaps.png)  
可惜手机上不能显示，而用mac里的maps from iphone却能显示。同样mac也要科学上网。  
显然这种做法在公开场合不被允许。
## iphone指南针app
可惜笔者的iphone 12指南针就真的只有“指南”作用了。
## google maps
安装google maps app, 在地图上的点(可以是自己位置的点)长按形成pin,上拉信息就能看到。同样也需要科学上网。

<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1659493742044_IMG_59463A7327A1-1.jpeg" width="400" height="800">

## 误差
据笔者体验，经纬度数据如果小数点后三位是一样的，则基本可以认为是同一地方，否则，就会有偏差。  
但上面两张apple map和google map的经纬度差的也太大了？  
通过调用前面WGS-84 -> GCJ-02 算法，发现，原来google map的经纬度刚好是apple map的做了转换后的结果。 由此可以推断，apple map用的是WGS 84坐标系，而google map(中国)用的是GCJ 02坐标系。

# RTK
GPS，当然精度越高越好。RTK(Real Time Kinematics)能将GPS模块的精度调到几毫米，这就是为什么我们必须把它放在板子上的原因。市面上通常能买到ZED-F9P，它是用于高精度GNSS和GPS定位解决方案的顶级模块，里面的RTK能达到10mm的三维精度。要做到这个程度，也会有一些强制要求。它需要晴朗的天空和来自RTCM源的数据校正。  
## 通信端口
ZED-F9P有5个通信端口可以同时使用。你可以在I2C读NMEA(National Marine Electronics Association)数据时在UART上发送配置指令。

# 问题随想
Q: 国内以高德为首的地图服务商，以美团为首的本地生活应用和以滴滴为首的打车软件，是如何合法拿到GPS数据的？  
A: 笔者没有在上面三家大厂待过，初步推断为通过手机GPS接收器拿到WGS-84坐标，通过简单算法换算成GCJ-02坐标。  
Q: 在没有网络的情况下，如何拿到GPS坐标？  
A:   
Q: RTK精度是厘米级的，那手机(iPhone) GPS精度/误差是多少？  
A: 我们目前无法用确切的数据或术语来说明iPhone上GPS的准确性。但有一条是确定的：你使用带GPS的app越多，GPS就越准确。通常一些LBS app会经常获取你的位置信息，在不断校正的过程中，使定位变准确。  
Q: 能不能把RTK模块做成手机(iphone)或平板(ipad)的外设？  
A:  

# 基于RTCMv3的网络RTK
高精度定位是测绘和自动驾驶两大领域的必研技术。通常，设备非常昂贵导致普及困难。基于RTCM v3的网络RTK，却能以较低的价格实现高精度定位。
## 概念梳理
### GNSS
Global Navigation Satellite System,全球导航系统。一般指美国的GPS,俄罗斯的GLONASS,欧盟的Galileo和中国的Beidou. 中国有不少GNSS基站。
### NMEA
National Marine Electronics Association,(美国)国家海洋电子协会。最初的标准格式。
### RINEX
Receiver Independent Exchange Format,接收器独立交换格式。是卫星导航系统原始数据的交换格式。GPS,GLONASS,Galileo和Beidou都是基于这个格式。
### RTCM-sc104
Radio Technical Commission for Maritime - Special Committee 104,(美国)海上无线电技术委员会第104特别技术委员会。
### RTCM v3
RTCM-sc104根据RINEX开发的通信协议，也称RTCM ver3或RINEX ver3. 以下RTCM v3简称RTCM

## 工作原理
RTCM协议的数据格式，简单说就是定义了卫星导航数据的头文件、信息长度、编解码以及各个GNSS数据长度、位置等。只要遵循这个协议，就可以接收GPS,GLONASS,Galileo和Beidou的信息。  
之前的RTK,要通过在某个地方设定一个基站base,移动设备上装rover。base是固定的，接收卫星的载波相位A,rover在移动过程中接受卫星的载波相位B和base的载波相位A。A和B做差分，就可以得到载波相位的误差C。rover通过误差C对自身的B不停进行修正，就可以得到1~2cm的精确度。  
基于网络的RTK,则是base把自身的数据链，修正信息发送给rover,rover就可以通过计算并消除误差，实现廉价GPS利用RTK将精度提升到1~2cm.  
## ublox
GPS设备的价格能低到什么程度？以ublox公司的GNSS芯片为例，ublox官网没有挂出价格，但通过用ublox芯片做产品的Sparkfun，可以大概看出ublox的芯片价格是$220. 对于手机来说，有点小贵，对于要高精度定位的测绘和自动驾驶来说呢？
[SparkFun GPS-RTK2](https://www.sparkfun.com/products/15136)
最后一个问题，我们开车通常打开手机导航，是否意味着手机已经实现了高精度定位？如果精度没有厘米级，那么有分米级/车道级的精度吗？
## 实践


# RTK的种类和精度
