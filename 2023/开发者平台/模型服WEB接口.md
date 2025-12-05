# task简单整理
Task | 参数 | 上传格式 
 --- | --- | ---
Revit转OsBim | rvtToOsBim | .rvt 
Revit转Assetbundle | rvtToAb | .rvt
Bentley转Assetbundle | bimToAb | .bim
Bentley转OsBim | bimToOsBim | .bim
Fbx转Assetbundle | fbxToAb | .fbx, .zip/.rar/.7z
Fbx压缩包转AssetBundle并附加文件 | fbxZipToAbAtFile | .zip, .7z
OsBim转OsLOD | osBimToLOD | .zip/.rar/.7z
OsBim转AssetBundle | osBimToAb | .zip/.rar/.7z
Multiple OsBim转Assetbundle | osBimGroupToAb | .zip/.rar/.7z
Osb转Assetbundle | osbToAb | .osb
PointCloud转Assetbundle | pcFilterToAb/pcLimitToAb | .ply/.pcd
PointCloud转LAS | pcToLas | .ply/.pcd
LAS转3MX | lasTo3MX | .las
PointCloud转3MX | pcTo3MX | .ply/.pcd
OSGB转3MX | osgbTo3MX | .zip/.rar/.7z
OSGB转B3DM | osgbToB3dm | .zip/.rar/.7z

# 任务接口
## 启动任务
```shell
curl -X POST -d '{"filename":"Cube0.rvt","size":100,"task":"rvtToOsBim","RvtVer":2020,"userid":74,"token":""}' https://developer.yijianar.com/api/start_task
```

## 重启任务
```shell
curl -X POST -d '{"taskid":"xxxx", "token":""}' https://developer.yijianar.com/api/restart_task 
```

## 取消任务
```shell
curl -X POST -d '{"taskid":"xxxx", "token":""}' https://developer.yijianar.com/api/cancel_task
```

## 查询任务进度
```shell
curl -X POST -d '{"taskids":["109161423352168449"],"token":""}' https://developer.yijianar.com/api/query_tasks
```
## 获取STS
```shell
curl -X POST -d '{"token":""}' https://developer.yijianar.com/api/oss_sts
```

# 调度服
## 注册
ServerId: 7151164286350070170
```
curl -X POST -H 'Content-Type: application/json' -d '{"PluginKey":"093e31dd941a3b118bb4bcfd23733a0f","ServerId":"7151164286350070170","TaskExecUrl":"https://dev.yijianar.com:8444/dispatch","SupportExecutors":[20],"ExecutorState":[true],"Versions":["default"]}' http://192.168.50.210:8080/v1/plugin/reg
```
## 状态更新
```
curl -X POST -H 'Content-Type: application/json' -d '{"PluginKey":"093e31dd941a3b118bb4bcfd23733a0f","ServerId":"7151164286350070170","ExecutorType":20,"Available":false}' http://192.168.50.210:8080/v1/plugin/state
```
## 任务更新
```
curl -X POST -H 'Content-Type: application/json' -d '{"PluginKey":"093e31dd941a3b118bb4bcfd23733a0f","TaskId":"abcdef123456","TaskProgress":1,"Message":"","ErrorCode":0}' http://192.168.50.210:8080/v1/plugin/taskState
```