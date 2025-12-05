LiDAR = Light Detection and Ranging. 是一门遥感技术。它通过计算一个脉冲(激光雷达发出的一束光)返回传感器所需的时间来测量距离。 LiDAR广泛应用于农业与森林管理、建筑、交通、城市规划、测绘、地质和采矿。  
处理LiDAR 3D点云数据充满挑战。一张激光雷达的图像的点的数量通常非常庞大，可能会达到数百万甚至上亿个点。本文我们使用Python的Laspy和open3D来快速处理和查看LiDAR数据。
# 数据结构
点云数据结构包含精确的三维(X,Y,Z)坐标以及强度、特征分类和GPS时间等属性。这些属性是必需的，不可少的补充信息。通常，有两种标准的LiDAR数据格式:.LAS和.LAZ。 las是存储3d点云数据的标准二进制文件格式，laz是las的压缩版本。  
其他文件格式还包括ASCII,XYZ,PLY及文本文件。.LAS文件比这些非二进制文件格式更高效，且能存储更多信息。 最新的LAS规范是 [LAS 1.4 规范](https://www.asprs.org/wp-content/uploads/2010/12/LAS_1_4_r13.pdf)  
它使用的点格式是Point Format 10, 而Point Format 10是在Point Format 6的基础上增加了RGB,Nir(near-infrared),波形数据.
![Point Format](https://miro.medium.com/max/1400/1*8LwuErpbpx-psgyyPnvMmQ.png)
# 点云文件获取
全世界可以免费获取点云文件的网站有
[Open Topography](https://www.opentopography.org/)  
[NEON](https://data.neonscience.org/data-products/explore)  
以见科技有限公司会把建筑类点云文件放在开发者平台的Open Data上
[Developer](https://dev.yijianar.com/dataset)
# 点云工具
有很多工具和软件可以用来处理点云数据:  
1. QGIS/GRASS(Desktop), 可以直接加载使用.las或者.laz  
2. Plas.io(Web), 网页工具，使用很方便  
3. Laspy(Python),用来处理LAS/LAZ的Python库  
4. Open3D(C++/Python),强大的开源3D数据处理库，有可视化功能。  

后面我们会大量用到3和4来做点云相关的编程工作。如果只是想快速查看或验证数据，可以使用1或2这种图形化工具。
# 读数据
```python
import laspy
las = laspy.read("resource/NZ19_Wellington.las")
las
# <LasData(1.2, point fmt: <PointFormat(2, 0 bytes of extra dims)>, 13993118 points, 0 vlrs)>
las.header.point_format
# <PointFormat(2, 0 bytes of extra dims)>
las.header.point_count
# 13993118
list(las.point_format.dimension_names)
# ['X', 'Y', 'Z', 'intensity', 'return_number', 'number_of_returns', 'scan_direction_flag', 'edge_of_flight_line', 'classification', 'synthetic', 'key_point', 'withheld', 'scan_angle_rank', 'user_data', 'point_source_id', 'red', 'green', 'blue']
las.X
# array([  4032000,  17767999,   4037000, ...,  40984000, -30647000, -5866000], dtype=int32)
```
# 写数据
```python
point_data = np.stack([las.X,las.Y,las.Z],axis=0).transpose((1,0))
point_data
#array([[ 68506260, 390272760,     31571],
      #  [ 68506057, 390272764,     31572],
      #  [ 68505659, 390272774,     31599],
      #  ...,
      #  [ 68747636, 389918893,     31996],
      #  [ 68747762, 389918889,     32296],
      #  [ 68747965, 389918898,     32223]])
```
```python
# filter classification
buildings = laspy.create(point_format=las.header.point_format, file_version=las.header.version)
buildings.points = las.points[las.classification==6]
buildings.write('buildings.las')
```
# 可视化
```python
geom = o3d.geometry.PointCloud()
geom.points = o3d.utility.Vector3dVector(point_data)
o3d.visualization.draw_geometries([geom])
```

