# 地址
https://cmsnew.the9.com/admin

# 登录
邀请注册后即可登录。 如果没有邀请邮件，请联系Patrick或Michael。  
用户分Editor和Admin两种角色。Editor没有publish权限。  

# 编辑新闻
点击左侧导航里的Content Manager, 再点击News, 出现新闻列表。  
点击右上角的Create new entry按钮即可进行编辑。  
![](https://en.the9.com/images/docs/editnews.png)

# 编辑器指引
content部分是一个Markdown富文本编辑器。可以直接用markdown语法编辑，如果不熟悉markdown语法，可以用它的功能控件。  
以第53篇《The9 Establishes Joint Venture to Operate New MIR Game Joint Venture Partner Committed Annual Revenue of RMB600 Million, Annual Profit of RMB200 Million and Annual Increase of 30%》为例,常用的几个控件有:  
h1, h2 一级标题二级标题，如最后一段的 \# Investor Relations Contact  
B/Bold 加粗, 如开头的 \*\*Shanghai China, August 22, 2024\*\*  
NumberList 参看第四段  
Quote 参看第四段  
Image \!\[\]\(\)  
段落与换行 输入空行或者在文末加两个空格

# 上传图片视频
点击 Media Library -> Add new assets 即可上传文章或封面所需的图片。  
点击图文按钮，选择刚刚上传的图片； 或者拷贝图片的url地址到文章内。  
如果没有上传封面，系统会从预置库里随机分配一张。

# 其他字段说明
title、introduction和category必须填。 英文新闻的category是ennews,中文新闻的category是cnnews。   
cover字段是显示在官网上的封面图片，不填会用默认图片。  
schedule_at 字段用于定时发布。如果新闻还需要审核，则不填。  
填好后可以点击Preview mode按钮进行预览。  
如果效果没问题，则点击右上角的Save按钮。 此时新闻就处于Draft状态了。  
如果想要其他展示效果，可以联系开发人员。  
![](https://en.the9.com/images/docs/preview.png)

# 审核
编辑人员点击reviewmail, 填上Article Id(刚刚编辑完成保存成draft状态的新闻ID)，点击send Email按钮，就能发送申请审核的邮件。  
默认审核人(收件人)是George, 邮件内容为新闻的预览地址，https://en.the9.com/draft/article_id。  
预览页面的底部有个"审核通过并发布按钮"，审核人员点击了就会发布这篇新闻。  
![](https://en.the9.com/images/docs/publish2.png)

# 测试环境
为了测试新功能，且不影响生产(正式)环境的数据。我们搭建了测试服务器。  
测试官网地址:  https://the9.bongos.ai  
测试cms地址:  https://the9admin.bongos.ai/admin  
同样，新闻预览地址也改成了 the9.bongos.ai。  
这个站点的新闻可以随意上线下线增加删除。  
所有以上操作都不变，只是数据库和生产(正式)环境不一定一致。