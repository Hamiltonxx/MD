# Mesh
In PyMesh, a Mesh consists of 3 parts: geometry, connectivity and attributes.
Geometry consists of vertices, faces and generalized voxels.  
Connectivity contains adjacency information, including vertex-vertex,vertex-face,vertex-voxel,face-face,face-voxel and voxel-voxel adjacencies.
Attributes are arbitrary value field assigned to a mesh.

# 8 Libraries for Mesh, Point Cloud and Data Visualization
Who said you need C++ knowledge to create fast, responsive point cloud, mesh or data visualizations?
Open3D, Trimesh, Vedo(V3do), Pyrender, PlotOptiX, Polyscope, PyVista and Simple-3dviz.  

可视化(visualization),动画(animation),体素化(voxelization),特征提取(feature extraction),距离计算(distance calculation),表面生成(surface generation),网格划分(meshing)

# Visualization
很长一段时间，人们用Python的Matplotlib来可视化3D内容。它的缺点是没有GPU硬件加速，意味着当你输入一定量的3D点和面之后，程序变的很卡直至不响应。这导致许多人转向PCL(Point Cloud Library)来作为一站式mesh和point cloud解决方案。PCL现在仍是最优秀的3D分析库之一，但它并不完美。  
## Open3D
Open3D之于3D视觉犹如OpenCV之于2D视觉。
```
pip install open3d
```