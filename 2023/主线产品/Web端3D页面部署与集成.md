# Web端3D页面部署与集成文档
## 将3D页面部署为单独网页(基于阿里云的部署方案)
### 一、准备工作

#### 1、服务器（ECS或轻量应用皆可）
系统：Windows Server或Linux </br>
CPU：2核4G</br>
带宽：6M

#### 2、已备案域名
需准备一个可用的域名用以配置CDN加速

#### 3、CDN服务
开通并购买全站加速下行流量包<br><br>

### 二、部署流程-Windows Server篇
#### 1、远程登录服务器并打开服务器管理器，选择添加角色和功能，依次勾选“IIS”和“应用程序开发”；

![](https://pro-developer.oss-cn-shanghai.aliyuncs.com/83/img/1664262202930_图片6.png)</br>
![](https://pro-developer.oss-cn-shanghai.aliyuncs.com/83/img/1664262233299_图片7.png)

#### 2、在IIS中，添加一个新的网站，指定物理路径；

![](https://pro-developer.oss-cn-shanghai.aliyuncs.com/83/img/1664262403891_图片8.png)

#### 3、将程序文件夹拷贝至服务器映射的物理路径下（文件夹名可自定义），将配置文件web.config拷贝（或者直接新建一个）至程序文件夹同级目录；

![](https://pro-developer.oss-cn-shanghai.aliyuncs.com/83/img/1664262437890_图片9.png)<br>

配置文件内容为：</br>
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <staticContent>
            <mimeMap fileExtension="." mimeType="application/octet-stream" />
            <mimeMap fileExtension=".bytes" mimeType="application/octet-stream" />
            <mimeMap fileExtension=".unityweb" mimeType="application/octet-stream" />
	        <mimeMap fileExtension=".json" mimeType="application/octet-stream" />
        </staticContent>
    </system.webServer>
</configuration>
```
其中.json配置是针对Windows Server2008独立添加，其他版本系统可据情况而定。<br><br>


### 三、部署流程-Linux篇
#### 1、将程序文件拷贝到服务器某路径下；
#### 2、使用SSH远程登录服务器，安装nginx，命令如下：
```
sudo su root
sudo apt update
apt-get install nginx
```
前往/etc/nginx路径打开nginx.conf文件进行编辑，要点如图：</br>

![](https://pro-developer.oss-cn-shanghai.aliyuncs.com/83/img/1664262537270_图片10.png)

#### 3、启动nginx服务，命令如下：
```
service nginx start
```
<br>

### 四、域名及CND配置
#### 1、前往阿里云控制台，在CDN下添加针对该服务器的域名加速，审核通过后获得CNAME；

![](https://pro-developer.oss-cn-shanghai.aliyuncs.com/83/img/1664262622762_图片11.png)

#### 2、前往阿里云控制台，在域名解析下将上一步的CNAME解析到对应的域名，此时网站已经可以使用域名访问。
<br>
以上，网站已全部部署完成并实现高速访问。
<br><br><br>


## 将3D页面与其他网页集成
我们使用webassembly技术，将unity发布的web3D内容与其他JS原生网页进行集成与内嵌：<br>

![](https://pro-developer.oss-cn-shanghai.aliyuncs.com/83/img/1664263232870_图片12.png)<br>

如果需要将web3d呈现到已有的网站页面上，可使用iframe将页面进行内嵌。<br>

![](https://pro-developer.oss-cn-shanghai.aliyuncs.com/83/img/1664263318000_图片13.png)<br>

将we3d项目资源作为一个文件夹放入原网站的部署空间中，假设需要嵌入的网页是index.html，我们打开index.html在合适的位置插入web3d页面：<br>
```html
<div >
<a>以下是内嵌的web3d页面:</a></br></br>
<iframe src="./3d/index.html"  frameborder="0" scrolling="auto"  height="800px" width="1200px" ></iframe>
</div>
```
<br><br>

## 将unity webassembly与其他前端框架整合
如Vue/React/Angular框架与无框架的整合<br>
参考：https://blog.csdn.net/weixin_33858249/article/details/88678541