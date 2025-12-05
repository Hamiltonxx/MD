# 产品wiki解读
要使用Livox激光雷达产品，我们先得通读下它的产品wiki文档。这里我们只看Mid 40产品的介绍和使用。  
Mid系列是Livox研发的高性价比激光雷达，最远可探测至260米。(对比笔者的ydlidar的10米距离，20倍，当然价格也是它的数十倍)。
## FOV(Field of View)覆盖率
FOV覆盖率，主要讲述它的非重复扫描优于传统机械扫描激光雷达的地方。略过。
## 坐标系
![坐标系](https://livox-wiki-cn.readthedocs.io/zh_CN/latest/_images/Livox_Mid-40__coordinate_system.png)  
上图左表示，机子以摄像头所在点为原点O,正中光束方向为X轴，左右方向为Y轴，上下方向为Z轴。这里我们引入欧拉角的俯仰角(pitch),偏航角(yaw),旋转角(roll)的概念。上图中为俯视图，表示最大偏航角为38.4度。上图右为侧视图，表示最大俯仰角为38.4度。因为视角呈圆锥形，所以用不到旋转角。  
Mid 40没有内置IMU模块。  
## 数据格式
点云数据是激光探测仪探测到物体表面所有点云的总和。每个点云包含以下信息:  
1. 目标反射率。以0~255表示。其中0～150对应漫反射，151～255对应全反射。  
2. 坐标信息。Livox Mid 40我们只考虑直角坐标(x,y,z),不考虑球坐标(r,theta,fai)。  
3. Tag。标记信息由一个Byte组成,两个Bit为一组，共分4组，对应于保留位、回波序号、点强度属性、点空间位置属性。后两组用来表示噪点置信度。00表示噪点置信度低，即噪点的可能性低。  
## Mid-40的静态3D扫描案例
![Mid-40,GM6020 Motor,tripod](https://www1.djicdn.com/dps/759e5eff740c4b87fe295b4d3e6248bb.png)
在三脚架上装上Mid-40和一个电机，绕着Z轴旋转来扫描房间不同区域。然后整合3D数据生成整体3D图像。  
Mid-40的参数 [https://www.livoxtech.com/mid-40-and-mid-100/specs](https://www.livoxtech.com/mid-40-and-mid-100/specs)  
将Mid-40设为静态IP模式，并直接连接至Manifold(大疆主机盒子)的以太网接口，用于点云数据传输。Manifold的CAN接口用于控制电机和读取角度信息。CAN总线以1kHz的频率传输到Manifold, Mid-40也以1kHz的频率发送带有100个样本的点云数据包。 因此我们仅需将电机和数据包发送的角度数据关联在一起，就可以完成整体点云数据集成。  
![组装图](https://www1.djicdn.com/dps/f626ee6c6a2fc215e9f9a26c1db2799b.png)  
最后，案例效果图   
![案例](https://www1.djicdn.com/dps/5e9754a448f820c63466fd169e931caf.png)

# 实验一 一个视角的3D画面
我们做的第一个实验，是把Mid-40 Lidar启动，拿到第一个画面，读出点云数据，并验证产品wiki里的数据格式。  
## 启动Lidar
Mid 40通过转接器接网线和电源线。电源输入10~16V. 网线直接连到PC机。 此时PC机会显示PCI Ethernet Connected. 配置它的IP为192.168.1.222,子网掩码和网关分别为255.255.255.0和192.168.1.1, 因为Mid 40默认为1网段，所以我们的PC机也设成1网段。  
![Mid40装配](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1663581699642_mid40.jpg)  
上图从左至右分别为 Mid-40, 转接器和电源。 最后我们在Ubuntu的PC机中安装Livox Viewer,打开后点击play即可看到运行效果。  
![Livox Viewer](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1663580827326_livoxviewer.png)
这样Lidar就算正式启动了。 我们再看下保存下来的数据，验证下数据格式。 Livox Viewer可以保存las和csv两种格式。刚刚好，前者我们可以在Open3D里查看，后者我们直接用编辑器打开看数据格式。  
```csv
Version,Slot ID,LiDAR Index,Rsvd,Error Code,Timestamp Type,Data Type,Timestamp,X,Y,Z,Reflectivity,Tag,Ori_x,Ori_y,Ori_z,Ori_radius,Ori_theta,Ori_phi
5,1,1,0,0x00000000,0,0,2444124311890,4.971,-0.365,0.411,51,0,4971,-365,411,0,0,0
5,1,1,0,0x00000000,0,0,2444124311890,4.975,-0.371,0.42,52,0,4975,-371,420,0,0,0
5,1,1,0,0x00000000,0,0,2444124311890,4.985,-0.378,0.429,51,0,4985,-378,429,0,0,0
5,1,1,0,0x00000000,0,0,2444124311890,4.996,-0.385,0.439,53,0,4996,-385,439,0,0,0
5,1,1,0,0x00000000,0,0,2444124311890,4.994,-0.39,0.447,52,0,4994,-390,447,0,0,0
```
```python
import laspy 
import open3d as o3d
import numpy as np 

las = laspy.read("resource/hello.las")
print(las.header.point_count)

point_data = np.stack([las.X, las.Y, las.Z], axis=0).transpose((1, 0))

geom = o3d.geometry.PointCloud()
geom.points = o3d.utility.Vector3dVector(point_data)
o3d.visualization.draw_geometries([geom])
```
![Mid 40 one view](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1663637834780_mid40oneview.png)
很遗憾，我们还不能从这一个画面里看出什么。

