# 背景
经过多年努力，我们以见科技已经在三维模型的转换、查看、存储、网络传输等技术领域取得了不少备受鼓舞的突破。转换方面，我们定义了Osbim模型转换数据中间格式，并为此开发了模型服(Server)；查看方面，我们一方面迎合Autodesk Forge、BimFace等大厂的接口做了WEB查看器，另一方面用Threejs/WebGL等相对底层的技术开发自己的查看器甚至主流软件(如Revit)的插件；存储方面，我们打造了构件数据库和自己的模型对象存储(OSS)解决方案，为BIM和AR打下基础；网络传输方面，我们做了(阿里/Azure)云服务和局域网高性能GPU服务的一体化，做到了兼顾平衡处理性能、实时传输和可用性。  
虽然我们主要服务建筑行业，并为此做了很多定制化的功能开发。但这些基础技术应用非常广泛，很多热门行业都或多或少会用到，所以在很长一段时间内，它们都会是前沿技术的存在，吸引着国内外大批研究开发人员。我们的开发者平台，以融合前沿技术的发展、解决生产研发中的问题为导向，一方面积极贡献我们的图形学技术进展，另一方面探索更优秀的建筑领域BIM+AR解决方案。

# USD
对于不熟悉USD的人来说，可以把它理解成一种文件格式(不是美元货币),它是Universal Scene Description的缩写，是皮克斯(公司)3D图形管道的核心部分，在几年前开源，现已被诸多大型三维模型软件采用，极有可能成为模型数据格式互通的标准。  
USD最大的特点是它的高度可组合性，让你可以从更基础的图元开始构建任意级别的场景或组件。该实操起源于电影制作，但对于需要高保真3D表达的行业来说，这种格式具有巨大的潜力。目前，3ds Max, Maya, Revit, Fusion 360, Blender等软件均已支持导出成USD。

# 实现方式
根据我们现有的在Web查看器开发上的实践和USD方向上的摸索。我们认为可能有五种路径可以实现:  
1. 把USD翻译成glTF. glTF在web领域已被广泛支持。不幸的是，某些有价值的特性，如协作相关的特性，将被丢失。  
2. 用Javascript实现一个Loader. 在静态场景绝对可行，但随着时间的推移，维护会越来越困难，而且在处理物理(属性)方面非常棘手。  
3. 将USD toolkit编译为WebAssembly. 只是USD需要64位内存寻址，而浏览器目前只支持WASM的32位寻址。此外还要关注编译的二进制文件大小。  
4. 参考Pixar的Hydra渲染引擎。它已被应用于usdview工具，只是需要开发者有强大的阅读源码的能力。  
5. 套用Jupyter的外壳。Nvidia有一个官方demo在Jupyter上作模型展示，只是它的display_usd方法并没有给出源代码。很可能我们得自己琢磨open3d的jupyter展示方法。  
<video src="https://developer.download.nvidia.com/Omniverse/video/Usd-Interactive-Content-1.m4v" width="50%" preload="auto" controls autoplay muted playsinline loop> </video>  

这里，每一种方式都有它的障碍。我们需要根据自己有绝对自信的技术领域来实现。目前兵分两路，选项2和选项5分别对于我们的前端和后端开发来说，更有吸引力。

# 预期
Nvidia CEO黄仁勋在一次采访中表达了对USD的预期  
> I’m hoping that everybody will go to USD and then it will become essentially the HTML of the metaverse

虽然我们的很多努力还在实验阶段，对于我们自己标准产品的影响，甚至对行业的影响，还不甚清晰。但我们会及时发布demo，在反馈中不断改进和前行。如果有朝一日，USD在三维模型领域大一统，我们的Web查看器就能积累足够的先行优势。对于公司来说就是技术壁垒。而在此之前，我们会用Nvidia Omniverse来配合解决实际生产问题。

# 实现思路(草稿)
## 简易版
1. 如果用户提供usdz模型文件，则直接上传；如果用户提供usd文件，则服务器端通过usdzip打包成usdz再上传；如果用户提供非usd/usdz模型文件，则服务器端通过omniverse重新导出成usd文件，再usdzip，再上传  
2. 网页端集成 [three-usdz-loader](https://www.npmjs.com/package/three-usdz-loader)  
3. 网页端加载 usdz文件路径，显示  

举例: 用 [示例文件](https://docs.fileformat.com/3d/sample.usdz) 上传到 [usdz-viewer](https://www.usdz-viewer.net/)  
这种方式目前遇到的问题是，usd打包成usdz后，web加载会缺少光照。
## 完善版
1. usd文件不需要打包成usdz,而是在服务器端解析或转换文件格式  
2. 网页端通过threejs渲染各个Mesh  
3. 网页端可以适当改进各个Mesh的显示效果  
4. 文件过大时，网页端得考虑LOD
## 后续
当查看器初步完成后，再考虑增加深度学习算法，以及BIM的管理。
