# 参数
参数 | 含义 | 示例 
 --- | --- | ---
target | 检测目标 | edge,crack,helmet,rebar,mask
filepath | 直传完成后返回的路径 | 74/hello.jpg

# 接口
## 图片检测
```shell
curl -X POST -d '{"target":"edge","filepath":"74/hello.jpg","token":""}' https://developer.yijianar.com:8899/detection/image
```
## 视频检测
```shell
curl -X POST -d '{"target":"edge","filepath":"74/hello.mp4","token":""}' https://developer.yijianar.com:8899/detection/video
```
## 异步查询进度
```shell
curl -X POST -d '{"filepath":"74/hello.mp4","token":""}' https://developer.yijianar.com:8899/detection/querying
```

