  今天要讲的主题是Revit Online,或者叫BIM Online,或者Modeling Online. 这里会横跨建筑和计算机两个领域。

# 背景
## 国外背景
1. 元宇宙和数字孪生概念盛行  
  元宇宙讲的是虚拟世界和现实世界的一种融合，让你在虚拟世界里玩游戏、做交易甚至旅游。而数字孪生也是同样，在计算机里再造一个现实世界。而建筑是他们最重要的应用领域之一。  
2. VDC越来越流行  
  Visual Design and Construction.虚拟设计施工。可以简单理解为面向施工阶段BIM的具象化。强调利用BIM来协调Product(交付物),Organization(组织结构),Process(工作流程)。用Metrics(度量数据)来提升项目管理能力。
## 国内背景
1. BIM标准与国际接轨  
  虽然与欧美国家的企业主导、自下而上不同，我们通常是政府制定标准国企推广实施。但和国外(尤其美国)的BIM标准框架体系基本一致。
2. 软件层面受限严重  
  目前国内很多施工企业慢慢从反对BIM，认为不如经验慢慢开始主动认识学习BIM. 只是受到软件、模型深度、可操作性、可视化等多方面因素制约。

# 意义
  从便利性和可视化入手，让大家随时随地能掏出模型。此外，让非建筑相关专业，没有安装revit软件的人也能看到你的模型。
## BIM Levels
Level 0: 基于CAD手绘/打印纸，0协作  
Level 1: 2D施工绘图+3D建模  
Level 2: 团队在各自的3D模型上工作  
Level 3: 团队在共享的3D模型上工作  
Level 4,5,6: 增加时间、预算、消耗的精准计算  
我们的目标是在Level 3上面做到更好。

# Revit文件
.rvt扩展名文件是Autodesk专有的文件格式，是Autodesk用于为平面图、立面图和剖面图创建3D建筑设计的BIM程序。  
Question: 有没有方法在不安装Revit软件的前提下，读写rvt文件？  
通常来讲，在Revit体系之外，访问revit数据是不可能实现的。最接近于实现的做法是提取它的database作为OLE Compound File.使之可以用7zip打开。但这种做法也有问题，可能会漏掉很多数据。

# 闭源与开源
Revit,Catia vs BimX,BlenderBim  
闭源的优势：服务、文档、效果； 劣势：收费、定制修改困难、数据与版权顾虑  
开源的优势：免费、定制修改灵活、数据与版权； 开源的劣势：用的人少，开源社区力量有限  
如何取舍取决于我们的目的。 对于有志于创建BIM标准(OSBim),服务专业领域应用，走出差异化道路的我们来说，开源这条路径就值得尝试。原因有二： 1. 闭源的数据格式、标准、版权等问题会极大地阻碍我们的发展； 2. 正因开源社区做的不够好，我们才会有机会。

# 原理
为了在web端显示3D模型，必然会用到WebGL.但WebGL使用复杂，所以市面上有不少对WebGL做了封装的Javascript库，比如threejs和babylonjs.那如何能让web显示revit模型呢？以闭源的forge viewer和开源的va3c viewer为例，forge viewer是Autodesk基于WebGL开发的针对SVF/SVF2格式文件的查看器。也就是revit文件需要被翻译成SVF/SVF2才能被查看。不幸的是SVF/SVF2格式标准并不对外开放，甚至Autodesk还下架了rvt -> svf的转换器。va3c的标准是开放的，va3c viewer的背后是threejs引擎。  
广联达BimFace是国内做revit模型处理和查看器做的最好的厂商之一。BimFace和Forge一样，也需要我们上传模型。translate成web能处理的格式。只是BimFace没说变成什么中间格式，更没有格式标准。但它的好处是，能下载离线数据包，且数据在国内，有政府背书。

# 实践
## va3c
项目地址 [https://github.com/va3c/RvtVa3c](https://github.com/va3c/RvtVa3c)
作者介绍 [https://thebuildingcoder.typepad.com/blog/2014/05/rvtva3c-revit-va3c-generic-aec-viewer-json-export.html](https://thebuildingcoder.typepad.com/blog/2014/05/rvtva3c-revit-va3c-generic-aec-viewer-json-export.html)
这是我们的第一个尝试。revit软件安装va3c插件，在revit导出时就导出成va3c json格式文件。最后javascript load va3c文件就能在浏览器里查看。这种方式的弊端是需要模型提供者在Revit软件里安装va3c插件，或者我们在服务器端增加va3c处理管道。此外，因为没有加载和渲染的优化，大模型会需要加载很长的时间甚至加载失败。曾经测试500M以上文件就会失败。
## Autodesk Forge
官方文档 [https://forge.autodesk.com/developer/documentation](https://forge.autodesk.com/developer/documentation)
### online流程
1. 模型上传。 把revit文件上传至Autodesk OSS.  
2. 模型转换。 把rvt格式转换成SVF/SVF2格式。  
3. 模型查看。  

![长阳办公室](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1658280716214_forge.png)
### offline流程
由于Autodesk不开放SVF标准，且已经把rvt->svf转换器下架，我们只能根据它的遗留程序代码，推导出转换过程。另外，本demo在实际vue前端开发中也遇到问题。
![剃须刀](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1658280680856_shaver.png)
## BimFace
### online
1. 模型上传。 把revit文件上传至BimFace,得到fileId  
2. 模型转换。 根据fileId作translate  
   (模型集成) 多专业的模型文件可以集成。得到integrateId  
3. 模型查看。 根据fileId或integrateId查看  

![集成模型](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1658280894837_integrate.png)
### offline
1. 创建离线包。 根据fileId创建offlineDatabag.  
2. 下载离线包。 根据fileId拿到download url,并下载  
3. 服务器离线部署。  
4. 引入JSSDK，并调用离线包。  

# 挑战
## OSBim
OSBim格式为以见科技(上海)有限公司自研的3D模型转换与查看的数据中间格式。既有的产品线已经实现了从rvt tranlsate成OSBim(json)格式，再通过unity渲染查看。只是unity的使用范围还不够广，市面上又对web查看有强烈的需求。所以我们已经开始探讨OSBim的web查看的可行性。  
初步探讨结论是：  
1. 通过threejs的THREE.ObjectLoader(),加载OSBim json或mesh,也就是自己overwrite load函数；  
2. 通过仔细比对osbim和va3c两个json格式，找到一种数据映射。  
这两种方式都要求开发人员有一定的三维模型处理经验。第一种方式，还要求开发人员具备扎实的javascript功底；第二种方式则需要大量调试。  