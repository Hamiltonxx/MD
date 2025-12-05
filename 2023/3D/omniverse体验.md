# 准备
Omniverse本身是一系列软件的集合。它对硬件要求较高，以Omniverse Create为例,推荐的硬件要求是Intel I7 CPU, 32G RAM, 8G vRAM, 8 CPU Cores, 512G SSD, GeForce RTX 2080 Quadro RTX 5000. 操作系统最好是windows, 因为有些图形软件不支持Linux，所以也就没有linux下的连接器(Connector)。

# 安装
在官网下载Omniverse Launcher AppImage, 安装Omniverse Launcher，进入EXCHANGE. 这里有我们所需要的全部软件，但每个需要各自安装，点击INSTALL按钮即可。 分FEATURED,APP,CONNECTOR,CONTENT-PACK,EXTENSION这几个板块. 下面介绍几个常用软件和连接器。
![Omniverse Exchange](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1675748054767_exchange.png)

# OMNIVERSE CODE
Omniverse Code is a developer focused Omniverse App.  
它提供了一小部分文档，展示如何用python来控制视图或场景。此外还有一些精美的素材和示例供下载。
![Code](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1675748190246_code.png)

# OMNIVERSE CREATE
Omniverse Create is an application built on Omniverse Kit that accelerates advanced scene composition and allows users to interactively assemble,light,simulate and render scenes in Pixar USD in real-time. Create lets you easily navigate,modify and render Pixar USD content in a live, synchronous environment.
FEATURES:
1. Simplify Scene Composition  
2. Breathtaking Photorealism  
3. Make it Move  
4. Recreate Reality  
5. Share it Anywhere  

简言之，Omniverse Create是一个基于USD内容的可交互的集成、仿真和渲染平台。对于用过建模软件的，不管是Revit,Unity还是Blender,3ds Max,Unreal,都可以考虑把它当作平替。
![Create](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1675748221848_create.png)

# OMNIVERSE VIEW
Omniverse View is a simple,powerful reference application for interactive viewing and annotating of 3D design projects in stunning,physically-accurate photorealism.
FEATURES:
1. Simple Intuitive Design  
2. Review with Ease  
3. Make it Real
4. Real-time Seamless  

简言之，Omniverse View是一个强大的查看器，具有物理精确和逼真效果。
![View](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1675748258398_view.png)

# AUTODESK REVIT OMNIVERSE CONNECTOR
NVIDIA Omniverse Revit Connector is a plug-in that offers a robust toolkit for Autodesk Revit users to live-sync and send model data to an NVIDIA Omniverse Nucleus server.  
也就是Revit软件可以实时发送模型数据给该Omniverse进行渲染。

# NECLEUS
Omniverse Nucleus is the database and collaboration engine of Omniverse. With Omniverse Nucleus, teams can have multiple users connected together live with multiple applications all at once. This allows people to use the application they are most comfortable and quickest with and really opens a lot of doors for rapid iteration.  
Nucleus是一个数据库和协同引擎。是协同工作者存放模型的地方。
![Necleus](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1675748291117_necleus.png)

# 下一步
为了更好的发挥出omniverse的优势。首先，你得把自己定位成模型设计人员，然后才是程序员。有一款自己相对熟悉的建模软件，可以通过Connector在Omniverse上工作，如果没有，则直接从Omniverse Create入手(学习)。然后这是一款协同软件，一开始可以是使用相同软件(如Revit)的两个人协同工作，共享结果。然后是不同软件的两个人协同。最后是多人多软件协同。在这期间，Omniverse提供了很多精美的示例(不一定是建筑)，我们可以使用它，模仿它。最后，我们用通用的数据格式(usd),用适合自己适合项目的方式编码创建和解析模型，形成最终的服务与方案。

