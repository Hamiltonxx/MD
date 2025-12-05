# 实践过程
## 拿到权限
```python
import base64
from email import header 
import requests 

appkey = "hpN2yoU7UgYJswBb9t2VbclYmk0Cyhdg"
appsecret = "g2obPcpswcRkpAIdHJy1oHTpWpboaliY"

tmpstr = appkey + ":" + appsecret
auth = base64.b64encode(tmpstr.encode()).decode()

headers = {"Authorization":f"Basic {auth}"}
resp = requests.post("https://api.bimface.com/oauth2/token",headers=headers)
print(resp.json())
token = resp.json()['data']['token']

headers = {"Authorization":f"Bearer {token}"}
```
## 上传文件
```python
fileurl = "https://pro-developer.oss-cn-shanghai.aliyuncs.com/models/心肌病房/阜外医院_王字楼_电气2.rvt"
data = {"name":"阜外医院_王字楼_电气2.rvt","url":fileurl}
resp = requests.put('https://file.bimface.com/upload',data=data,headers=headers)
print(resp.json())
```
## 获取fileId
```python
fileId = 10000752757532
fileId = 10000752817726
fileId = 10000752822328  # 医院
fileId = 10000752846032    #长阳创谷建筑结构.rvt
fileId = 10000752908529    #心肌病房土建.rvt
fileId = 10000752902962    #阜外医院_王字楼_暖通.rvt
fileId = 10000752911417    #阜外医院_王字楼_电气.rvt
fileId = 10000752965196    #阜外医院_王字楼_电气2.rvt
fileId = 10000752913004    #阜外医院_王字楼_给排水.rvt
fileId = 10000752918435    #阜外医院_王字楼_给排水2.rvt
```
## 查看文件信息
```python
resp = requests.get(f'https://file.bimface.com/files/{fileId}',headers=headers)
print(resp.json())
```
## 转换文件
```python
data = {"source":{"compressed":False,"fileId":fileId},"config":{"texture":True}}
resp = requests.put('https://api.bimface.com/translate',json=data,headers=headers)
print(resp.json())
```
## 查询转换结果
```python
resp = requests.get(f'https://api.bimface.com/translate?fileId={fileId}',headers=headers)
print(resp.json())
```
## 创建离线包
```python
resp = requests.put(f'https://api.bimface.com/files/{fileId}/offlineDatabag',headers=headers)
print(resp.json())
```
## 查询离线包
```python
resp = requests.get(f'https://api.bimface.com/files/{fileId}/offlineDatabag',headers=headers)
print(resp.json())
```
## 获取离线包下载地址
```python
resp = requests.get(f'https://api.bimface.com/data/databag/downloadUrl?fileId={fileId}',headers=headers)
print(resp.json())
```
## 前端显示离线包

## 集成模型
```python
data = {"sources":[{"fileId":10000752908529},{"fileId":10000752902962},{"fileId":10000752965196},{"fileId":10000752918435}]}
resp = requests.put('https://api.bimface.com/integrate',json=data,headers=headers)
print(resp.json())
```
## 获取集成id
```python
integrateId = 2425181203315776
integrateId = 2425211730122400
integrateId = 2425618916698016
```
## 查询集成
```python
resp = requests.get(f'https://api.bimface.com/integrate?integrateId={integrateId}',headers=headers)
print(resp.json())
```
## 获取viewToken
```python
resp = requests.get(f'https://api.bimface.com/view/token?integrateId={integrateId}', headers=headers)
print(resp.json())
```
## 前端显示


# WEB接口
## 获取viewToken
展示模型前先调用此接口获取viewtoken,如果是单文件模型，参数为fileId,如果是集成模型，参数为integrateId
```shell
curl -X POST -d '{"fileId":"10000752822328"}' https://developer.yijianar.com/api/bimface_viewtoken
```