在图像处理和点云处理中，数值计算是我们必须要熟练掌握的基本功。复杂的图像算法和3D点云算法都是以此为基础。  
NumPy是"Numerical Python"的简写，是专门处理(多维)数组/矩阵的Python库。

# 为什么用NumPy
Python已经有list来表达和处理数组，但它比较慢，NumPy提供的数组对象比python list快50倍以上。(多维)数组在数据科学里很常见，速度和资源对它来说很重要。  
为什么NumPy会比list快？因为NumPy存储数据在连续的内存块里，所以访问和操作就非常高效。

# Basic
## Create Array
NumPy里的array对象叫ndarray.
```python
import numpy as np 
arr = np.array([1,2,3,4,5])
arr2 = np.array((1,2,3,4,5))
print(arr)
print(arr2)
print(type(arr))


# 0，1，2，3 -D Array
a = np.array(42)
b = np.array([1,2,3,4,5])
c = np.array([[1,2,3],[4,5,6]])
d = np.array([[[1,2,3],[4,5,6]],[[1,2,3],[4,5,6]]])
print(a.ndim, b.ndim, c.ndim, d.ndim)
```
### 创建方法列表
FUNCTION | DESCRIPTION 
-- | --
array() | Create an array
asarray() | Convert the input to an array
asanyarray() | Convert the input to an ndarray, but pass ndarray subclasses through
ascontiguousarray() | Return a contiguous array in memory (C order)
asmatrix() | Interpret the input as a matrix
empty() | Return a new array of given shape and type, without initializing entries
empty_like() | Return a new array with the same shape and type as a given array
eye() | Return a 2-D array with ones on the diagonal and zeros elsewhere.
identity() | Return the identity array
ones() | 	Return a new array of given shape and type, filled with ones
ones_like() | Return an array of ones with the same shape and type as a given array
zeros() | Return a new array of given shape and type, filled with zeros
zeros_like() | Return an array of zeros with the same shape and type as a given array
full_like() | Return a full array with the same shape and type as a given array
copy() | Return an array copy of the given object
frombuffer() | Interpret a buffer as a 1-dimensional array
fromfile() | Construct an array from data in a text or binary file
fromfunction() | Construct an array by executing a function over each coordinate
fromiter() | Create a new 1-dimensional array from an iterable object
fromstring() | A new 1-D array initialized from text data in a string
loadtxt() | Load data from a text file
arange() | Return evenly spaced values within a given interval
linspace() | Return evenly spaced numbers over a specified interval
logspace() | Return numbers spaced evenly on a log scale
geomspace() | Return numbers spaced evenly on a log scale (a geometric progression)
meshgrid() | Return coordinate matrices from coordinate vectors
mgrid() | nd_grid instance which returns a dense multi-dimensional “meshgrid
ogrid() | nd_grid instance which returns an open multi-dimensional “meshgrid
diag() | Extract a diagonal or construct a diagonal array
diagflat() | Create a two-dimensional array with the flattened input as a diagonal
tri() | An array with ones at and below the given diagonal and zeros elsewhere
tril() | Lower triangle of an array
triu() | Upper triangle of an array
vander() | Generate a Vandermonde matrix
mat() | Interpret the input as a matrix
bmat() | Build a matrix object from a string, nested sequence, or array

### 常用创建示例
```python
print(np.arange(4).reshape(2,2))
print(np.arange(4,10))
print(np.arange(4,19,3))
print(np.arange(1,2,0.1))

print(np.zeros(3, dtype=int))
print(np.zeros([3,3], dtype=int))
print(np.zeros([3,3]))
print(np.ones([3,3], dtype=int))
print(np.ones([3,3]))

print(np.linspace(2,3,5))
print(np.linspace(0,2,11))

x = np.linspace(-4,4,9)
y = np.linspace(-5,5,11)
x_1, y_1 = np.meshgrid(x,y)
print(x_1)
print(y_1)
print(x_1.shape)
print(y_1.shape)

print(np.eye(2, dtype=float))
print(np.eye(4,5,k=-1))
```

## Accress Elements
```python
arr = np.array([[1,2,3,4,5],[6,7,8,9,10]])
print('2nd element on 1st row: ', arr[0,1])
```
## Slicing
和list一样，如arr[1:3]
## Data Type
i - integer, b - boolean, u - unsigned integer, f - float, c - complex float,  
m - timedelta, M - datetime, O - object, S - string, U - unicode string  
其中 i, u, f, S, U 可以跟个长度
```python
arr = np.array([1,2,3,4,5])
print(arr)
print(type(arr))
print(arr.dtype)
arr2 = np.array(["abc","goodnight","world"])
print(arr2.dtype)
arr3 = np.array([1,2,3,4], dtype='S')
print(arr3)
print(arr3.dtype)
arr4 = np.array([1,2,3,4], dtype='i4')
print(arr4)
print(arr4.dtype)

arr5 = np.array([1.1, 2.1, 3.1])
newarr = arr5.astype('i')
newarr = arr5.astype(int)
print(newarr)
print(newarr.dtype)
```
## Copy vs View
copy和view的区别是copy是一个新数组，而view是原数组的一部分。copy上的改动不会影响原数组，而view上的改动则会改变原数组。  
```python
arr = np.array([1,2,3,4,5])
x = arr.copy()
y = arr.view()
arr[0] = 42
print(arr)
print(x)
print(y)
```
## Shape and Reshape
ndarray的shape属性是一个tuple,表明每个维度的元素个数
```python
d = np.array([[[1,2,3],[4,5,6]],[[1,2,3],[4,5,6]]])
print(d.shape)  # (2,2,3)
```
```python
arr = np.array([1,2,3,4,5,6,7,8,9,10,11,12])
# 1D -> 2D
newarr = arr.reshape(4,3)
print(newarr)   # [[1 2 3] [4 5 6] [7 8 9] [10 11 12]]
# 1D -> 3D
newarr = arr.reshape(2,3,2) 
print(newarr)   # [[[1 2] [3 4] [5 6]] [[7 8] [9 10] [11 12]]]
# Flatting
newarr = arr2.reshape(-1)
print(newarr)
```
## Iterating
```python
arr = np.array([[[1, 2, 3], [4, 5, 6]], [[7, 8, 9], [10, 11, 12]]])
for x in arr:
  print(x)
for x in arr:
  for y in x:
    for z in y:
      print(z)
```
## Join Arrays
### concatenate函数
```python
arr1 = np.array([1,2,3])
arr2 = np.array([4,5,6])
arr = np.concatenate((arr1,arr2))
print(arr)
arr1 = np.array([[1,2],[3,4]])
arr2 = np.array([[5,6],[7,8]])
arr = np.concatenate((arr1,arr2),axis=0) 
arr = np.concatenate((arr1,arr2),axis=1) 
print(arr)
```
### stack函数
stack和concatenate很像，唯一区别是stack可以along new axis
```python
arr1 = np.array([1,2,3])
arr2 = np.array([4,5,6])
arr = np.concatenate((arr1,arr2))
print('concatenate')
print(arr)
newarr = np.stack((arr1,arr2))
newarr0 = np.stack((arr1,arr2),axis=0)
newarr1 = np.stack((arr1,arr2),axis=1)
newarrh = np.hstack((arr1,arr2))
newarrv = np.vstack((arr1,arr2))
newarrd = np.dstack((arr1,arr2))
print('stack')
print(newarr)
print(newarr0)
print(newarr1)
print(newarrh)
print(newarrv)
print(newarrd)
```
## Split
Split是Join的逆过程。
```python
arr = np.array([1,2,3,4,5,6])
newarr = np.array_split(arr, 3)
print(newarr)
arr = np.array([[1,2],[3,4],[5,6],[7,8],[9,10],[11,12]])
newarr = np.array_split(arr, 3)
print(newarr)
```
## Search & Sort
略
## Filter
```python
arr = np.array([41,42,43,44])
x = [True,False,True,False]
newarr = arr[x]
print(newarr)
filter_arr = arr > 42
filter_arr2 = arr % 2 == 0
newarr = arr[filter_arr]
newarr2 = arr[filter_arr2]
print(filter_arr)
print(filter_arr2)
print(newarr)
print(newarr2)
```
## 数学操作
```python
arr1 = np.array([[4,7],[2,6]], dtype=np.float64)
arr2 = np.array([[3,6],[2,8]], dtype=np.float64)
print(np.add(arr1,arr2))  # [[7 13] [4,14]]
print(np.sum(arr1))  # 19
print(np.sqrt(arr1)) # [[2 2.x] [1.414 2.x]]
trans_arr = arr1.T 
print(trans_arr)  # [[4 2] [7 6]]
```