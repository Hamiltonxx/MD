# LAS文件
LAS是一个通用的3D点数据存储文件格式。LAZ是它的无损压缩。  
最新的LAS规范是LAS 1.4, pylas支持1.2～1.4.  
LAS文件包含三部分: Header, VLRs, Point Records. header包含版本、点格式等信息；VLRs(Variable Length Record)存储一些附加信息如SRS(Spatial Reference System)；Point Records是占比最大的一部分。
## Point Format
| Point Format | Dimensions |
| --- | --- |
| 0 | X,Y,Z,intensity,return_number,number_of_returns,scan_direction_flag,edge_of_flight_line,classification,synthetic,key_point,withheld,scan_angle_rank,user_data,point_source_id |
| 1 | (Point Format 0), gps_time |
| 2 | (Point Format 0), red, green, blue |
| 3 | (Point Format 0), gps_time, red, green, blue |
| 4 | (Point Format 0), gps_time, wavepacket_index, wavepacket_offset, wavepacket_size, return_point_wave_location, x_t, y_t, z_t |
| 5 | (Point Format 4), red, green, blue |
| 6 | X,Y,Z,intensity,return_number,number_of_returns,synthetic,key_point,withheld,overlap,scanner_channel,scan_direction_flag,edge_of_flight_line,classification,user_data,scan_angle_rank,point_source_id,gps_time |
| 7 | (Point Format 6), red, green, blue |
| 8 | (Point Format 7), nir |
| 9 | (Point Format 6), wavepacket_index, wavepacket_offset, wavepacket_size, return_point_wave_location, x_t, y_t, z_t |
| 10 | (Point Format 9), red, green, blue, nir |

# 基本操作
## reading
```python
import pylas
las = pylas.read('pointcloudfile.las')
print(np.unique(las.classification))
```
## opening
如果只想读header和vlrs,而不想把points读进来，可以用pylas.open()
## chunked reading
有时文件太大超出了RAM的能力，pylas.open()返回的LasReader也可以用来读points chunk by chunk, LasReader.chunk_iterator(). 可以让你对大文件做些如分割、过滤之类的处理。
```python
import pylas
with pylas.open("big_file.laz") as f:
  for points in f.chunk_iterator(1_000_000):
    do_something_with(points)
```
## writing
可以通过LasData.write()来写文件。同样，写文件也有chunk by chunk的方法。
```python
import pylas
with pylas.open("big_file.laz") as f:
  with pylas.open("grounds.laz", mode="w", header=f.header) as writer:
    for points in f.chunk_iterator(1_234_567):
      writer.write_points(points[points.classification==2])
```
## creating
pylas.create()
## converting
pylas.convert()
## 访问点数据
点的数据维度通过point format id查看。不同id代表不同维度/属性数据。  
也可以通过 list(las.point_format.dimension_names) 来查看

# 调用示例
## 通过classification筛选点
```python
import laspy 
import numpy as np 
las = laspy.read("resource/NEONDSSampleLiDARPointCloud.las")

print(las.point_format.id)
print(list(las.point_format.dimension_names))

print(np.unique(las.classification))

new_file = laspy.create(point_format=las.header.point_format,file_version=las.header.version)
new_file.points = las.points[las.classification == 1]
new_file.write('extracted_points.las')
```
## 从0创建las文件
```python
import laspy 
import numpy as np 

# 0. Creating some dummy data
my_data_xx, my_data_yy = np.meshgrid(np.linspace(-20,20,15), np.linspace(-20,20,15))
my_data_zz = my_data_xx ** 2 + 0.25 * my_data_yy ** 2
my_data = np.hstack((my_data_xx.reshape((-1,1)),my_data_yy.reshape((-1,1)),my_data_zz.reshape((-1,1))))

# 1. Create a new header
header = laspy.LasHeader(point_format=3, version="1.2")
header.add_extra_dim(laspy.ExtraBytesParams(name="random",type=np.int32))
header.offsets = np.min(my_data,axis=0)
header.scales = np.array([0.1,0.1,0.1])

# 2. Create a Las
las = laspy.LasData(header)
las.x = my_data[:, 0]
las.y = my_data[:, 1]
las.z = my_data[:, 2]
las.random = np.random.randint(-1503, 6546, len(las.points), np.int32)

las.write("new_file.las")

# 3. Create a LasWriter and a point record, then write it
with laspy.open("somepath.las", mode="w", header=header) as writer:
    point_record = laspy.ScaleAwarePointRecord.zeros(my_data.shape[0], header=header)
    point_record.x = my_data[:, 0]
    point_record.y = my_data[:, 1]
    point_record.y = my_data[:, 2]
    writer.write_points(point_record)
```

# 完整示例
首先laspy.read()会把全部数据读进内存，我们可以用它返回的LasData对象来访问数据。
```python
import laspy
las = laspy.read("point_cloud.las")
```
