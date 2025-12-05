# 开发环境搭建
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
```
pip install usd-core
```
官方的通过source build的方法，可能会遇到各种各样的问题。最大的问题是不支持3.10。好在最近两礼拜它更新了python support.以后就可以通过pip install来直接安装了。

# Hello World
```python
from pxr import Usd 
stage = Usd.Stage.CreateNew('HelloWorldRedux.usda')
xform = stage.DefinePrim('/hello','Xform')
sphere = stage.DefinePrim('/hello/world','Sphere')
stage.GetRootLayer().Save()
```
这里的root layer也可以通过usdview查看。usd把模型数据分解成不同层级的图元数据。
```shell
python helloworldredux.py
usdview HelloWorldRedux.usda
usdcat HelloWorldRedux.usda 
```

# 查看属性
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
这里返回一个2x3矩阵数据。可以作为场景描述里没有值时的回滚。
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

# 引用layer
```python
from pxr import Usd, UsdGeom 
stage = Usd.Stage.Open('HelloWorld.usda')
hello = stage.GetPrimAtPath('/hello')
stage.SetDefaultPrim(hello)
UsdGeom.XformCommonAPI(hello).SetTranslate((4,5,6))
print(stage.GetRootLayer().ExportToString())
stage.GetRootLayer().Save()

refStage = Usd.Stage.CreateNew('RefExample.usda')
refSphere = refStage.OverridePrim('/refSphere')
# print(refSphere.GetRootLayer().ExportToString())

refSphere.GetReferences().AddReference('./HelloWorld.usda')
print(refStage.GetRootLayer().ExportToString())
refStage.GetRootLayer().Save()

refXform = UsdGeom.Xformable(refSphere)
refXform.SetXformOpOrder([])
print(refStage.GetRootLayer().ExportToString())

refSphere2 = refStage.OverridePrim('/refSphere2')
refSphere2.GetReferences().AddReference('./HelloWorld.usda')
print(refStage.GetRootLayer().ExportToString())
refStage.GetRootLayer().Save()

overSphere = UsdGeom.Sphere.Get(refStage, '/refSphere2/world')
overSphere.GetDisplayColorAttr().Set([(1,0,0)])
print(refStage.GetRootLayer().ExportToString())
refStage.GetRootLayer().Save()

print(refStage.ExportToString())
```
这里相当于把别的usda文件当作layer来引用，这样就让代码变得模块化。
```
#usda 1.0
(
    doc = """Generated from Composed Stage of root layer /Users/hami/prj3d/myusd/tutorials/RefExample.usda
"""
)

def Xform "refSphere"
{
    custom double3 xformOp:translate = (4, 5, 6)
    uniform token[] xformOpOrder = []

    def Sphere "world"
    {
        float3[] extent = [(-4, -4, -4), (4, 4, 4)]
        color3f[] primvars:displayColor = [(0, 0, 1)]
        double radius = 2
    }
}

def Xform "refSphere2"
{
    custom double3 xformOp:translate = (4, 5, 6)
    uniform token[] xformOpOrder = ["xformOp:translate"]

    def Sphere "world"
    {
        float3[] extent = [(-4, -4, -4), (4, 4, 4)]
        color3f[] primvars:displayColor = [(1, 0, 0)]
        double radius = 2
    }
}
```

# Layer 格式
主要有三种格式: .usda, .usdc 和 .usd。  
```shell
# Sphere.usda can be simply renamed to Sphere.usd
# Sphere.usd can be renamed to Sphere.usda when it uses the text format
# Sphere.usd can be renamed to Sphere.usdc when it used the binary format
usdcat Sphere.usda
usdcat -o NewSphere.usdc Sphere.usda  # convert this file to binary
usddiff Sphere.usda NewSphere.usdc
usdcat -o NewSphere.usda NewSphere.usdc
usddiff NewSphere.usdc NewSphere.usda
usdcat -o NewSphere_binary.usd --usdFormat usdc Sphere.usd
usddiff Sphere.usd NewSphere_binary.usd
usdcat -o NewSphere_text.usd --usdFormat usda NewSphere_binary.usd
usddiff NewSphere_binary.usd NewSphere_text.usd
```
简单理解,layer数据可以用.usda,.usdc或.usd后缀保存。.usda是文本模式，.usdc是编译模式。  
.usd的话需要进一步确定它的内容是.usda还是.usdc。  
.usda和.usdc可以通过usdcat工具互换。

# 遍历Stage
```shell
# usdview RefExample.usda  # Window > Interpreter
# [x for x in usdviewApi.stage.Traverse()]
# [x for x in usdviewApi.stage.Traverse() if UsdGeom.Sphere(x)]

# primIter = iter(Usd.PrimRange.PreAndPostVisit(usdviewApi.stage.GetPseudoRoot()))
# for x in primIter: print(x, primIter.IsPostVisit())

# select deactivate
# [x for x in usdviewApi.stage.Traverse()]
# [Usd.Prim(</refSphere>), Usd.Prim(</refSphere/world>), Usd.Prim(</refSphere2>)]
```

# 创建变量
```python
from pxr import Usd, UsdGeom 
stage = Usd.Stage.Open('HelloWorld.usda')
colorAttr = UsdGeom.Gprim.Get(stage, '/hello/world').GetDisplayColorAttr()
colorAttr.Clear()
print(stage.GetRootLayer().ExportToString())

rootPrim = stage.GetPrimAtPath('/hello')
vset = rootPrim.GetVariantSets().AddVariantSet('shadingVariant')
print(stage.GetRootLayer().ExportToString())

vset.AddVariant('red')
vset.AddVariant('blue')
vset.AddVariant('green')
print(stage.GetRootLayer().ExportToString())

vset.SetVariantSelection('red')
with vset.GetVariantEditContext():
    colorAttr.Set([(1,0,0)])
# colorAttr.Set() without edit context will write a local opinion
vset.SetVariantSelection('blue')
with vset.GetVariantEditContext():
    colorAttr.Set([(0,0,1)])
vset.SetVariantSelection('green')
with vset.GetVariantEditContext():
    colorAttr.Set([(0,1,0)])
print(stage.GetRootLayer().ExportToString())

print(stage.ExportToString(addSourceFileComment=False))

stage.GetRootLayer().Export('HelloWorldWithVariants.usda')
# usdview HelloWorldWithVariants.usda
# print(usdviewApi.stage.GetSessionLayer().ExportToString())
```
这里的变量可以简单理解为模型变量，不是代码里的变量。  
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1675837574595_variant.png" width="50%">

# 动画
```python
# usdview top.geom.usd
from pxr import Usd, UsdGeom, Gf, Sdf 

def MakeInitialStage(path):
    stage = Usd.Stage.CreateNew(path)
    UsdGeom.SetStageUpAxis(stage, UsdGeom.Tokens.z)
    stage.SetStartTimeCode(1)
    stage.SetEndTimeCode(192)
    return stage 

def Step1():
    stage = MakeInitialStage("Step1.usda")
    stage.SetMetadata('comment','Step 1: Start and end time codes')
    stage.Save()
# Step1()

def AddReferenceToGeometry(stage, path):
    geom = UsdGeom.Xform.Define(stage, path)
    geom.GetPrim().GetReferences().AddReference('./top.geom.usd')
    return geom 

def Step2():
    stage = MakeInitialStage('Step2.usda')
    stage.SetMetadata('comment','Step 2: Geometry reference')
    top = AddReferenceToGeometry(stage, '/Top')
    stage.Save()
# Step2()

def AddSpin(top):
    spin = top.AddRotateZOp(opSuffix='spin')
    spin.Set(time=1, value=0)
    spin.Set(time=192, value=1440)

def Step3():
    stage = MakeInitialStage('Step3.usda')
    stage.SetMetadata('comment', 'Step 3: Adding spin animation')
    top = AddReferenceToGeometry(stage, '/Top')
    AddSpin(top)
    stage.Save()
# Step3()

def AddTilt(top):
    tilt = top.AddRotateXOp(opSuffix='tilt')
    tilt.Set(value=12)

def Step4():
    stage = MakeInitialStage('Step4.usda')
    stage.SetMetadata('comment', 'Step 4: Adding tilt')
    top = AddReferenceToGeometry(stage, '/Top')
    AddTilt(top)
    AddSpin(top)
    stage.Save()
# Step4()

def Step4A():
    stage = MakeInitialStage('Step4A.usda')
    stage.SetMetadata('comment', 'Step 4A: Adding spin, then tilt')
    top = AddReferenceToGeometry(stage, '/Top')
    AddSpin(top)
    AddTilt(top)
    stage.Save()
Step4A()
```
这里最主要的操作是在几何上增加引用。用分步骤的方式可以清晰看到几何体的代码创建。  
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1675837987605_animation.png" width="50%">

# Shading
```python
from pxr import Gf, Kind, Sdf, Usd, UsdGeom, UsdShade

stage = Usd.Stage.CreateNew("simpleShading.usda")
UsdGeom.SetStageUpAxis(stage, UsdGeom.Tokens.y)

# We put both geometry and materials under a common "model root prim",
# which makes it safe to reference the model into another scene.
modelRoot = UsdGeom.Xform.Define(stage, "/TexModel")
Usd.ModelAPI(modelRoot).SetKind(Kind.Tokens.component)

# A simple card with same proportions as the texture we will map
billboard = UsdGeom.Mesh.Define(stage, "/TexModel/card")
billboard.CreatePointsAttr([(-430, -145, 0), (430, -145, 0), (430, 145, 0), (-430, 145, 0)])
billboard.CreateFaceVertexCountsAttr([4])
billboard.CreateFaceVertexIndicesAttr([0,1,2,3])
billboard.CreateExtentAttr([(-430, -145, 0), (430, 145, 0)])
texCoords = UsdGeom.PrimvarsAPI(billboard).CreatePrimvar("st", 
                                    Sdf.ValueTypeNames.TexCoord2fArray, 
                                    UsdGeom.Tokens.varying)
texCoords.Set([(0, 0), (1, 0), (1,1), (0, 1)])

# Now make a Material that contains a PBR preview surface, a texture reader,
# and a primvar reader to fetch the texture coordinate from the geometry
material = UsdShade.Material.Define(stage, '/TexModel/boardMat')
stInput = material.CreateInput('frame:stPrimvarName', Sdf.ValueTypeNames.Token)
stInput.Set('st')

# Create surface, and connect the Material's surface output to the surface 
# shader.  Make the surface non-metallic, and somewhat rough, so it doesn't
# glare in usdview's simple camera light setup.
pbrShader = UsdShade.Shader.Define(stage, '/TexModel/boardMat/PBRShader')
pbrShader.CreateIdAttr("UsdPreviewSurface")
pbrShader.CreateInput("roughness", Sdf.ValueTypeNames.Float).Set(0.4)
pbrShader.CreateInput("metallic", Sdf.ValueTypeNames.Float).Set(0.0)

material.CreateSurfaceOutput().ConnectToSource(pbrShader.ConnectableAPI(), "surface")

# create texture coordinate reader 
stReader = UsdShade.Shader.Define(stage, '/TexModel/boardMat/stReader')
stReader.CreateIdAttr('UsdPrimvarReader_float2')
# Note here we are connecting the shader's input to the material's 
# "public interface" attribute. This allows users to change the primvar name
# on the material itself without drilling inside to examine shader nodes.
stReader.CreateInput('varname',Sdf.ValueTypeNames.Token).ConnectToSource(stInput)

# diffuse texture
diffuseTextureSampler = UsdShade.Shader.Define(stage,'/TexModel/boardMat/diffuseTexture')
diffuseTextureSampler.CreateIdAttr('UsdUVTexture')
diffuseTextureSampler.CreateInput('file', Sdf.ValueTypeNames.Asset).Set("USDLogoLrg.png")
diffuseTextureSampler.CreateInput("st", Sdf.ValueTypeNames.Float2).ConnectToSource(stReader.ConnectableAPI(), 'result')
diffuseTextureSampler.CreateOutput('rgb', Sdf.ValueTypeNames.Float3)
pbrShader.CreateInput("diffuseColor", Sdf.ValueTypeNames.Color3f).ConnectToSource(diffuseTextureSampler.ConnectableAPI(), 'rgb')

# Now bind the Material to the card
billboard.GetPrim().ApplyAPI(UsdShade.MaterialBindingAPI)
UsdShade.MaterialBindingAPI(billboard).Bind(material)

stage.Save()
```
从shading这里也可以看出，USD的工作很基础，不像threejs等"高级"框架，加个light，顶多再加点简单设置就可以显示明暗。USD没有这样的封装，就得自行花很多代码去写。
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1675837042021_shading.png" width="50%">

# USDZ
Apple在WWDC Conference 2018上宣布,它开发了一种新的文件格式，可以和Pixar公司的USD格式一起工作，叫USDZ。这个格式专为开发者创建AR的3D模型而出。
## 什么是USDZ
A file with .usdz is an uncompressed and unencrypetd ZIP archive for the USD (Universal Scene Description) file format that contains and proxies for files of other formats (such as textures, and animations) embedded within the archive and runs them directly with the USD run-time without any need of unpacking.  
简言之，就是USD的压缩。
## 为什么需要一个新的文件格式
USD - Universal Scene Description, 是Pixar开发的，通过把很多资源文件聚合在一起来创建3D场景。它在各方面都很出色，只是在传输或交付文件时，会带来麻烦，因为有太多的资源文件需要协同组织。于是Pixar和Apple合力开发了以.usdz为扩展的新文件格式。它由许多对象文件组成，但仅被打包成一个对象文件。
## usd/usda转usdz
```shell
usdzip -a input.usda output.usdz
```
## 示例文件下载
[sample.usdz](https://docs.fileformat.com/3d/sample.usdz)

# 说明
站在传统后端开发人员的角度  
1. 由于接触usd的时间还比较短，目前还没形成usd知识体系的“全景地图”。后面还需增加usd相关编程经验，才能把usd在各三维软件厂商引起共识的原因阐述的更深刻。  
2. 与前端的开发配合还需要协商。  
3. 与建模人员处拿到usd模型文件的流程还需要协商。  
4. 最后需要补强模型处理的经验。