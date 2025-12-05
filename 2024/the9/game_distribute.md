# QuickSDK接入

## 开发者游戏管理后台
http://quickgame.open.quicksdk.com/  
测试账号：quickgame_dev  
测试密码：abc123456  

个人理解: 这部分是商务对接后，quickgame会给我们开通一个后台，供运营人员查看游戏数据。

## 游戏盒子APP
手机端：http://quickgame.demo.quicksdk.com/gamesite/downloadApp  扫码下载
PC端：http://quickgame.demo.quicksdk.com/gamesite/index  下拉右侧下载

个人理解: 这部分是所有对接完成后，由quickgame生成的下载链接。

## QuickSDK接口文档
http://14.103.113.255:8082/docs/index/aid/999  
主要接口有: 用户注册、用户登录、获取用户订单、手机号码登录、用户实名认证、获取用户平台币余额等。  

薛老师提供的在线接口文档，对接需要准备openId和productCode, 在"开发者游戏管理后台"查看。  
此外需要开发人员生成sign, sign验证通过才能对接。

## 接口url
14.103.113.255
## demo地址
14.103.113.255/test/h5
## H5 SDK
http://14.103.113.255/static/lib/libSDK.js  

这部分是大师提供的文档《国内SDK H5对接文档.docx》。 只需要productCode就能调用(quickgame封装了libSDK)。 他们还在开发中。

## 登录
```
H5SDK.login(function(loginNotify){
  console.log(loginNotify);
})
```
初始化成功后，调用此接口显示一个用户登录页面。  
目前调不通。 
据文档说明好像我们不用做登录页面了。
