# USD
USD = Universal Scene Description  
USD provides a rich toolset for reading,writing,editing and rapidly previewing 3D geometry,shading,lighting,physics and other graphics-related domains.

## USD can represent
USD organizes data into hierarchical namespaces of Prims

# Getting and Building the Code
The simplest way to build USD is to run the supplied build_usd.py script.
## MacOS
在venv下
```shell
pip install PySide6
pip install PyOpenGL
git clone https://github.com/PixarAnimationStudios/USD
python USD/build_scripts/build_usd.py /opt/local/USD
```
```shell
export PATH="$PATH:/opt/local/USD/bin"
export PYTHONPATH=$PYTHONPATH:/opt/local/USD/lib/python
```
```shell
usdview USD/extras/usd/tutorials/convertingLayerFormats/Sphere.usda
```
## Ubuntu

# Tutorials
## Hello World
```python
from pxr import Usd 
stage = Usd.Stage.CreateNew('HelloWorldRedux.usda')
xform = stage.DefinePrim('/hello','Xform')
sphere = stage.DefinePrim('/hello/world','Sphere')
stage.GetRootLayer().Save()
```
```shell
python helloworldredux.py
usdview HelloWorldRedux.usda
usdcat HelloWorldRedux.usda 
```
## Inspecting and Authoring Properties
```python
from pxr import Usd, Vt 
stage = Usd.Stage.Open('HelloWorldRedux.usda')
xform = stage.GetPrimAtPath('/hello')
sphere = stage.GetPrimAtPath('/hello/world')
print(xform.GetPropertyNames())
print(sphere.GetPropertyNames())
extentAttr = sphere.GetAttribute('extent')
print(extentAttr.Get())
# Vt.Vec3fArray(2, (Gf.Vec3f(-1.0, -1.0, -1.0), Gf.Vec3f(1.0, 1.0, 1.0)))
```
Return: a two-by-three array containing the endpoints of the sphere's axis-aligned, object-space extent, as expected for the fallback value. An attribute has a fallback value when no options about its value are authored in the scene description.
```python
radiusAttr = sphere.GetAttribute('radius')
radiusAttr.Set(2)
extentAttr.Set(extentAttr.Get() * 2)

sphereSchema = UsdGeom.Sphere(sphere)
color = sphereSchema.GetDisplayColorAttr()
color.Set([(0,0,1)])

print(stage.GetRootLayer().ExportToString())
stage.GetRootLayer().Save()
```
## Referencing Layers

## Traversing a Stage
In USD,a session layer holds transient opinions for the current working session that a user would not want to commit to the asset and thereby affect downstream departments.
## Authoring Variants
If we just invoked colorAttr.Set() without using the variant edit context, we would write a local opinion; specifically the same opinion we cleared at the beginning.  
It is import to first Clear() the local opinion for primvars:displayColor because local opinions are strongest.  
Strength order is a fundamental part of USD.  
Exporting a UsdStage writes the composed scene description with all the composition operators(like variants) fully evaluated. We call this flattening.
## Transformations,Time-sampled Animation,and Layer Offsets
We create the scene by starting with a USD file with static geometry,referencing it into another USD file,overlaying animation,and then using a third USD file to reference and re-time that animation.

# USDZ
