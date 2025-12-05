# 背景
为了更好地理解OsBim,有必要对mesh(及mesh生成器)和geometry等内容进行深入了解。  
OsBim的内容请参考文档 [《OSBIM》](https://dev.yijianar.com/open-doc?fileId=298846353)
## 为什么需要网格
网格生成 (Mesh Generation) 又称为网格剖分, 将一个有界的连续的几何空间划分成由有限个单纯形, 矩形, 多边形或多面体构成的离散的几何空间, 是有限元方法求解偏微分方程的第一步. 高质量的网格往往依赖于求解区域及方程特性, 同时一般能够带来更精确的有限元解。  
非工程师可能会困惑于有限元求解或有限元分析，可以参考知乎的这篇文章 [《有限元分析（FEA）是个什么东东》](https://zhuanlan.zhihu.com/p/56326567)

# Gmsh
Gmsh是一个开源的3D有限元网格(mesh)生成器,具有内置的CAD引擎和后处理器。其设计目标是提供一种快速、轻便且用户友好的网格剖分(meshing)工具，参数化输入，可视化灵活。Gmsh围绕四个模块(几何geometry、网格mesh、求解器solver和后处理post-processing)，可以通过图形界面、命令行、Gmsh脚本文件或C/C++、Python、Julia API来控制。
安装Gmsh和SDK  
Windows,Linux,MacOS对应的版本可以在 [官网下载](https://gmsh.info/)  
Meshing,是保证工业级精度的秘诀所在。通过meshing建模剖分，再导入商业软件进行仿真，这方面值得花一些时间投入。
## Geometry
Gmsh是用边界表达(Boundary Representation)法来定义模型的。体(volume)由面(surface)界定，面由线(curve)界定，线由两个端点(end point)界定。模型实体是拓扑实体，即它只处理模型中的邻接关系，用抽象拓扑类实现。内部的点、线、面可以嵌入体中，内部的点、线可以嵌入面中。  
模型实体的几何可以由不同的CAD引擎提供。Gmsh的内核是内置内核和OpenCASCADE内核。Gmsh不对两种内核的数据进行转换，只是对其查询，确保没有遗漏。它们分别对应geo前缀和occ前缀.

# 实操
```shell
git clone https://gitlab.onelab.info/gmsh/gmsh.git
```
找到tutorials/t1.geo, file->open之，出现一个方框。 在导航栏Mesh下级目录找到2D,点击后方框被切分成网格。这就是长方形贴片的meshing过程。  
打开t1.geo文件，看到它定义点线面的方式
```
定义参数 lc = 1e-2;
定义点 Point(1) = {0,0,0,lc};
定义线段 Line(1) = {1,2};
定义闭合线段 Line Loop(5) = {4,1,-2,3};
定义面Plane Surface(6) = {5}
```
可以看出，gmsh参数化建模的流程: 通过参数定义点的属性，连接相应的点构成线段，再连接一系列线段构成封闭的面。  
定义点需要四个变量，前三个为XYZ,第四个用来定义网格划分的疏密。
## 建一个最简单的房子
10个点、17条线、8个面构成一个最简单的房子
```
lc = 1e-1;
// 10 points
Point(1) = {-1,-1,0,lc};
Point(2) = {1,-1,0,lc};
Point(3) = {1,1,0,lc};
Point(4) = {-1,1,0,lc};
Point(5) = {-1,-1,1,lc};
Point(6) = {1,-1,1,lc};
Point(7) = {1,1,1,lc};
Point(8) = {-1,1,1,lc};
Point(9) = {-1,0,1.5,lc};
Point(10) = {1,0,1.5,lc};
// 17 lines
Line(1) = {1, 2};
Line(2) = {2, 3};
Line(3) = {3, 4};
Line(4) = {4, 1};
Line(5) = {5, 6};
Line(6) = {6, 7};
Line(7) = {7, 8};
Line(8) = {8, 5};
Line(9) = {10, 6};
Line(10) = {10, 7};
Line(11) = {8, 9};
Line(12) = {9, 5};
Line(13) = {10, 9};
Line(14) = {7, 3};
Line(15) = {8, 4};
Line(16) = {6, 2};
Line(17) = {5, 1};
// 8 faces without bottom
Line Loop(18) = {6,14,-2,-16};
Plane Surface(19) = {18};
Line Loop(20) = {7,15,-3,-14};
Plane Surface(21) = {20};
Line Loop(22) = {8,17,-4,-15};
Plane Surface(23) = {22};
Line Loop(24) = {5,16,-1,-17};
Plane Surface(25) = {24};
Line Loop(26) = {10,-6,-9};
Plane Surface(27) = {26};
Line Loop(28) = {10,7,11,-13};
Plane Surface(29) = {28};
Line Loop(30) = {13,12,5,-9};
Plane Surface(31) = {30};
Line Loop(32) = {8,-12,-11};
Plane Surface(33) = {32};
```
保存为house.geo
## gmsh软件可视化
打开下载的gmsh软件，File -> Open -> house.geo  
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1677038082415_geo.png" width="40%">  
点击Mesh -> 2D. 这样就能进行网格划分。  
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1677038572011_meshing.png" width="40%">  
如果我们想要整齐的正方形网格？  
在选项卡里，设置 Recombine all triangular meshes 和 Delaunay for quads算法  
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1677038713940_meshsquare.png" width="40%">  

## mesh数据保存
File -> Save mesh 就可以在相同文件夹下生成同名网格文件 house.msh. 我们需要的网格(points,elements)就存储在house.msh中，下一步就是提取这些数据并应用在有限元程序或软件中。  
house.msh的数据内容如下
```
$MeshFormat
4.1 0 8
$EndMeshFormat
$Entities
10 17 8 0
1 -1 -1 0 0
2 1 -1 0 0
......
33 -1 -1 1 -1 1 1.5 0 3 8 -12 -11 
$EndEntities
$Nodes
35 1425 1 1425
0 1 0 1
1
-1 -1 0
0 2 0 1
2
......
28
29
-0.9000000000002774 -1 0
-0.8000000000005548 -1 0
......
-1 0.08908524996783586 1.163786301240869
$EndNodes
$Elements
37 1670 1 1670
0 1 15 1
1 1 
0 2 15 1
2 2 
......
1670 144 145 187 186 
$EndElements
```
## CAD
Gmsh可以直接导入CAD文件并生成2D/3D网格，并且可以在UI上直接修改网格参数并指定网格大小。

# Python中使用Gmsh
## meshio
meshio可以读写很多格式的网格文件.  
> Abaqus, ANSYS msh, AVS-UCD, CGNS, DOLFIN XML, Exodus, FLAC3D, H5M, Kratos/MDPA, Medit, MED/Salome, Nastran (bulk data), Neuroglancer precomputed format, Gmsh (format versions 2.2, 4.0, and 4.1), OBJ, OFF, PERMAS, PLY, STL, Tecplot .dat, TetGen .node/.ele, SVG (2D only, output only), SU2, UGRID, VTK, VTU, WKT (TIN), XDMF.  

程序示例
```python
import meshio 
import numpy as np

mesh = meshio.read('./house.msh', \
    file_format='gmsh')

print(mesh.points)
print(mesh.cells_dict['triangle'])
# 保存数据或打印, 或者提供给其它使用网格的程序, 如有限元等等
# np.savetxt('points.txt', mesh.points)
# np.savetxt('cells.txt', mesh.cells['triangle'])
```
网格数据被提取并保存在文本文档里后，其它语言程序如C/C++,Matlab,R等均可以用这些网格数据。
## pygmsh
在Gmsh中，用户必须为创建的每个点、线、面、体提供唯一的ID。当创建了大量实体(ID)时，就会显得很混乱，因为不知道哪些ID在使用。  
用pygmsh则可以给几何实体变量命名，如点p1,p2,...,线l1,l2...,这样就可以避免编号的唯一性检查。  
程序示例
```python
import pygmsh
import numpy as np

with pygmsh.geo.Geometry() as geom:
  geom.add_rectangle(0.0,1.0,0.0,1.0,0.0,1.0)
  mesh = geom.generate_mesh()

print(mesh.points)
print(mesh.cells_dict['triangle'])
# 保存数据或打印, 或者提供给其它使用网格的程序, 如有限元等等
# np.savetxt('points.txt', mesh.points)
# np.savetxt('cells.txt', mesh.cells['triangle'])
```

至此，我们算基本迈入mesh的门槛了。但gmsh简约而不简单，阅读和理解它的示例，还需要我们花更多的时间。