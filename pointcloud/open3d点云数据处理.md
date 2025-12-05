```shell
pip install open3d
```
# 简介
Open3D是支持快速开发处理3D数据的开源库。提供了三种数据格式: Point Cloud, Mesh, RGBD image. 它有一整套技术处理算法，如IO、采样、可视化和数据转换。此外还有一些常用算法，如法线估计、ICP配准等。  

# Point Cloud
格式 | 描述
xyz | 每行都有[x,y,z],x,y,z代表3D坐标
xyzn | 每行都有[x,y,z,nx,ny,nz],nx,ny,nz是法线(normals)
xyzrgb | 每行都有[x,y,z,r,g,b],r,g,b是[0,1]之间的浮点数
pts | 第一行是点数。下面每行是[x,y,z,i,r,g,b]
ply | ply同时包含点云和mesh数据
pcd | 见pcd
## ply
Polygon File Format(多边形文件格式)，也叫Stanford Triangle Format(斯坦福三角格式)  
图像对象被描述成一系列的多边形。  
这种文件格式有两种子格式。ASCII版本和二进制版本。  
希望促进在程序之间和人们之间进行图像对象交换。  
图形对象存储格式五花八门。我们需要一种简单灵活的单一文件格式来摆脱格式困境。  
PLY格式描述一个对象为一系列的点、面和其它元素以及属性。  
A PLY file contains the description of exactly one object. Sources of such objects include: hand-digitized objects, polygon objects from modeling programs, range data, triangles from marching cubes (isosurfaces from volume data), terrain data, radiosity models. Properties that might be stored with the object include: color, surface normals, texture coordinates, transparency, range data confidence, and different properties for the front and back of a polygon.
PLY格式不包含转换矩阵、对象实例化、模型层次。  
典型的PLY文件
```
Header
Vertex List
Face List
(lists of other elements)
```
## PCD
```
VERSION
FIELDS
SIZE
TYPE
COUNT
WIDTH
HEIGHT
VIEWPOINT
POINTS
DATA
```  

# 点云读写与可视化
```python
import open3d as o3d 

print("加载点云")
pcd = o3d.io.read_point_cloud("5floor_full_fast2.pcd") # 可以指定文件类型
print(pcd)

o3d.visualization.draw_geometries([pcd])    # 可视化单个点云,可视化多个点云用[pcd1,pcd2...]
# 可以通过+/-键来调整点的大小

print("保存点云")
o3d.io.write_point_cloud("write.pcd", pcd, True) # 默认False,保存为Binary; True 保存为ASCII
```

# k-d tree 与 Octree
```python
o3d.geometry.KDTreeFlann(pcd): 建立KDTree
search_knn_vector_3d(search_pt, k): K近邻搜索
search_radius_vector_3d(search_pt, radius): 半径R近邻搜索
search_hybrid_vector_3d(search_pt, radius, max_nn): 混合近邻搜索，半径radius内不超过max_nn个点
```
```python
import open3d as o3d 
import numpy as np 

print("加载点云")
pcd = o3d.io.read_point_cloud("5floor_full_fast2.pcd") # 可以指定文件类型
print(pcd)

pcd.paint_uniform_color([0.5,0.5,0.5])  # 将点云设置为灰色

pcd_tree = o3d.geometry.KDTreeFlann(pcd)    # 建立KDTree

pcd.colors[1500] = [0.5, 0, 0.5]    # 将第1500个点设置为紫色

print("使用K近邻,将第1500个点最近的5000个点设置为蓝色")
print(pcd.points[1500])
k = 500000
[num_k, idx_k, _] = pcd_tree.search_knn_vector_3d(pcd.points[1500], k) # 返回邻域点的个数和索引
np.asarray(pcd.colors)[idx_k[1:], :] = [0,0,1] # 跳过查询点进行赋色
print("k邻域内的点数为:", num_k)

o3d.visualization.draw_geometries([pcd])    # 可视化单个点云,可视化多个点云用[pcd1,pcd2...]
# 可以通过+/-键来调整点的大小
```
## 八叉树Octree
每个内部节点有8个子节点的树数据结构。通常用于三维点云的空间划分。

# 点云滤波
## 体素采样
使用规则体素栅格从输入点云创建均匀采样点云。分两步：1. 将点云进行体素划分  2. 对所有非空体素，取质心
```python
# 体素采样
voxel_size = 0.5
downpcd = pcd.voxel_down_sample(voxel_size)
print(downpcd)
```
## 统计滤波
statistical_outlier_removal 移除距离相邻点更远的点
## 半径滤波
radius_outlier_removal 移除球体中几乎没有邻居的点

# 点云数据何去何从
拿到点云数据后，如果直接在任何建模软件平台上应用都非常费时费力，此时有必要进行点云轻量化处理。同时点云联动建模软件实时精准定位创建模型。

# 如何评价点云模型质量
