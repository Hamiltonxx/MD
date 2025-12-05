AIServer提供的功能与服务可以分为四大块:  
  1. 图像  
  2. 视频  
  3. 流  
  4. OCR  

# 图像
## 接口 
/detection/image  
## 输入
分两类:  
  1. 以http开头的网络图片地址  
  2. 用户本地上传oss后的路径，如 /74/sample.png  

## 检测方法  
1. edge(边缘检测),直接调用OpenCV的Canny方法  
2. crack(裂纹检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/crack.pth  
3. rebar(钢筋检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/rebar.pth  
4. mask(口罩检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/mask.pth
5. helmet(头盔检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/helmet.pth  
6. area(危险区域检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/dangerous_area.pth  
7. pedestrian(行人检测),预训练模型和危险区域用的同一个  
8. segmentation(语义分割),调用segmentation的seg_load和seg_infer,预训练模型在 /WEIGHTROOT/segmentation.pth
9. pano(全景)  

## 输出
可以访问的图片URL地址, 如: https://onesight-developer.oss-cn-shanghai.aliyuncs.com/74/helloworld.jpg  
前端可以直接显示在页面上。

# 视频
## 下载
用户前端直传oss后，Server从OSS下载视频。
## 处理
用OpenCV读取视频文件，获取帧率、尺寸，根据检测方法对图片做检测，合成视频。
## 上传
处理完的视频上传到OSS,生成前端可以访问的视频URL
## 删除
删除本地视频文件

# 流
## 流的查看
首先，拿到视频流的内网地址。然后，在Server上用FFmpeg读取，转化成.m3u8文件(动态视频文件)。通过穿透，让客户端的web播放器从外网访问到.m3u8文件。最后当播放器关闭或网页关闭时，杀掉FFmpeg进程。

## 流的检测
进程控制有缺陷，未完成

# OCR
前端用户直传图片到OSS,Server从OSS处拿到图片，调用extract_words方法。该方法是部分修改了PaddleOCR2Pytorch的ocr方法，而所使用的模型也是来自Paddle。最后生成一张修改后的图片，以及一个储存了提取出的文字的txt。

# 镜现
和调度服协定的接口规范参看 [外问业务服](https://dev.yijianar.com/open-doc?fileId=751590822)  
分成0图像、1视频、2OCR三种类别。目前只有OCR完成对接。  
OCR的识别和开发者平台类似，只是用的命令行调用模式。测试会麻烦一些。
  