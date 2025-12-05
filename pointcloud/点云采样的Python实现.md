# 为什么需要采样
![https://pro-developer.oss-cn-shanghai.aliyuncs.com/img/factory.gif](https://pro-developer.oss-cn-shanghai.aliyuncs.com/img/factory.gif)  
点云数据实在太吸引人了。我们可以通过一堆离散化的点来获得世界实体的几何描述。但3D点云的一个主要问题是，如果数据密度太大，超出了实际应用所需，会导致在后续的数据处理或可视化中需要更多计算消耗。现在我们用python脚本通过采样的方法来降低密度。

# 光学原理
通常，采样方法有三种：随机方法、最小距离方法和网格方法。  
随机方法最简单，随机挑选一定数量的点即可。最小距离方法，子集中点的距离都应超过最小距离。网格方法，创建(体素)网格结构，选择代表数据点。 后两种方法可以在缩减后的点云里做到更均匀的空间分布。

# 实验步骤
## 准备点云数据
点云数据文件通常可以在下列这些地方下载  
[以见Open Data](https://dev.yijianar.com/dataset)
[OpenTopography](https://www.opentopography.org/)
[云平台](https://drive.google.com/drive/folders/1Ih_Zz9a6UcbUlaA-puEB_is7DYvXrb4w)  
这里我们使用的点云是 [https://drive.google.com/file/d/12Iy4fkJ1i1Xh-dzGvsf_M66e8eVa1vyx/view?usp=sharing](https://drive.google.com/file/d/12Iy4fkJ1i1Xh-dzGvsf_M66e8eVa1vyx/view?usp=sharing)
## 准备Python环境
```shell
pip install laspy
```
```python
import numpy as np 
import laspy as lp 

point_cloud = lp.file.File("./resource/NZ19_Wellington.las")
# Separate coordinates X,Y,Z from colors Red,Green,Blue fields.
points = np.vstack((point_cloud.x, point_cloud.y, point_cloud.z)).transpose()
colors = np.vstack((point_cloud.red, point_cloud.green, point_cloud.blue)).transpose()
# 以上两个方法把(nx3)矩阵转置成(3xn)矩阵
```
## 随机采样
```python
factor=160
decimated_points_random = points[::factor]
```
每160行数据抽取一行数据。即抽取到第0、160、320...行(x,y,z)数据。
## 网格采样
```python
import enum
import numpy as np 
import laspy as lp 

point_cloud = lp.file.File("./resource/NZ19_Wellington.las", mode="r")
# Separate coordinates X,Y,Z from colors Red,Green,Blue fields.
points = np.vstack((point_cloud.x, point_cloud.y, point_cloud.z)).transpose()
colors = np.vstack((point_cloud.red, point_cloud.green, point_cloud.blue)).transpose()
# 以上两个方法把(nx3)矩阵转置成(3xn)矩阵

#The decimation strategy, by setting a decimation factor
factor=160
decimated_points = points[::factor]
decimated_colors = colors[::factor]
len(decimated_points)

# Initialize the number of voxels to create to fill the space including every point
voxel_size=6
nb_vox=np.ceil((np.max(points, axis=0) - np.min(points, axis=0))/voxel_size)
#nb_vox.astype(int) #this gives you the number of voxels per axis

# Compute the non empty voxels and keep a trace of indexes that we can relate to points in order to store points later on.
# Also Sum and count the points in each voxel.
non_empty_voxel_keys, inverse, nb_pts_per_voxel= np.unique(((points - np.min(points, axis=0)) // voxel_size).astype(int), axis=0, return_inverse=True, return_counts=True)
idx_pts_vox_sorted=np.argsort(inverse)
#len(non_empty_voxel_keys) # if you need to display how many no-empty voxels you have

voxel_grid={}
grid_barycenter,grid_candidate_center=[],[]
last_seen=0

for idx,vox in enumerate(non_empty_voxel_keys):
    voxel_grid[tuple(vox)]=points[idx_pts_vox_sorted[last_seen:last_seen+nb_pts_per_voxel[idx]]]
    grid_barycenter.append(np.mean(voxel_grid[tuple(vox)],axis=0))
    grid_candidate_center.append(voxel_grid[tuple(vox)][np.linalg.norm(voxel_grid[tuple(vox)]-np.mean(voxel_grid[tuple(vox)],axis=0),axis=1).argmin()])
    last_seen += nb_pts_per_voxel[idx]

import matplotlib.pyplot as plt
from mpl_toolkits import mplot3d

ax = plt.axes(projection='3d')
ax.scatter(decimated_points[:,0], decimated_points[:,1], decimated_points[:,2], c = decimated_colors/65535, s=0.01)
plt.show()
```

# 总结方法
```python
#Define a function that takes as input an array of points, and a voxel size expressed in meters. It returns the sampled point cloud
def grid_subsampling(points, voxel_size):

  nb_vox=np.ceil((np.max(points, axis=0) - np.min(points, axis=0))/voxel_size)
  non_empty_voxel_keys, inverse, nb_pts_per_voxel= np.unique(((points - np.min(points, axis=0)) // voxel_size).astype(int), axis=0, return_inverse=True, return_counts=True)
  idx_pts_vox_sorted=np.argsort(inverse)
  voxel_grid={}
  grid_barycenter,grid_candidate_center=[],[]
  last_seen=0

  for idx,vox in enumerate(non_empty_voxel_keys):
    voxel_grid[tuple(vox)]=points[idx_pts_vox_sorted[last_seen:last_seen+nb_pts_per_voxel[idx]]]
    grid_barycenter.append(np.mean(voxel_grid[tuple(vox)],axis=0))
    grid_candidate_center.append(voxel_grid[tuple(vox)][np.linalg.norm(voxel_grid[tuple(vox)]-np.mean(voxel_grid[tuple(vox)],axis=0),axis=1).argmin()])
    last_seen+=nb_pts_per_voxel[idx]

  return grid_candidate_center
```
```python
#Execute the function, and store the results in the grid_sampled_point_cloud variable
grid_sampled_point_cloud variable = grid_subsampling(point_cloud, 6)

#Save the variable to an ASCII file to open in a 3D Software
%timeit np.savetxt(output_path+dataname+"_sampled.xyz", grid_sampled_point_cloud variable, delimiter=";", fmt="%s")
```

# 后续
除了上面三种采样方法之外，还有一些更高级的采样方法。