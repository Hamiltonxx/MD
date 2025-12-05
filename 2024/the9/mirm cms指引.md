# 地址
https://cms.mirmcn.com/admin

# 登录
邀请注册后即可登录。 如果没有邀请邮件，请联系Patrick或Michael。
用户分Editor和Admin两种角色。Editor没有publish权限。

# 编辑新闻文章
点击左侧导航里的Content Manager, 再点击Article, 出现文章列表。  
点击右上角的Create new entry按钮即可进行编辑。  
![](https://en.the9.com/images/docs/editnews.png)

# 编辑器指引
content部分是一个Markdown富文本编辑器。可以直接用markdown语法编辑，如果不熟悉markdown语法，可以用它的功能控件。  
常用的几个控件有:  
h1, h2 一级标题二级标题，如最后一段的 \# Investor Relations Contact  
B/Bold 加粗, 如开头的 \*\*Shanghai China, August 22, 2024\*\*  
NumberList 参看第四段  
Quote 参看第四段  
Image \!\[\]\(\)  
段落与换行 输入空行或者在文末加两个空格

# 上传图片视频
## 上传文章配图
点击 Media Library -> Add new assets 上传即可。
## 上传游戏壁纸、视频、游戏截图、原画
这些文件建议传到各自folder里，比如壁纸传到wallpaper目录里。  
此外, caption字段必须填上，分别是wallpaper, video, screenshot, drawing。 
![](https://en.the9.com/images/docs/caption.png)
## 水印
有些图片需要水印，特别是壁纸，游戏截图等。把图片上传到watermarked目录下，就能自动在图片左下角加上水印。
## OSS
目前不考虑用户访问情况，以后可能会把图片视频传到oss，上传的Add new assets只需要填URL即可。  
目前上传文件大小限制100M。

# 其他字段说明
cover字段是显示在官网上的封面图片，不填会用默认图片。  
schedule_at 字段用于定时发布。如果新闻还需要审核，则不填。  
填好后可以点击Preview mode按钮进行预览。  
如果效果没问题，则点击右上角的Save按钮。 此时新闻就处于Draft状态了。  
如果想要其他展示效果，可以联系开发人员。  
![](https://en.the9.com/images/docs/preview.png)

# 审核
编辑人员点击reviewmail, 填上Article Id(刚刚编辑完成保存成draft状态的新闻ID)，点击send Email按钮，就能发送申请审核的邮件。  
默认审核人(收件人)是王其, 邮件里为文章的预览地址，https://mirmcn.com/draft/article_id。  
预览页面的底部有个"审核通过并发布按钮"，审核人员点击了就会发布这篇文章。  
![](https://en.the9.com/images/docs/publish2.png)
