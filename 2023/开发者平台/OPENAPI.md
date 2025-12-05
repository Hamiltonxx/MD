# DOC生成器
## 背景
1. PPT时代已过去，DOC时代重新到来。  
2. 开发人员须统一DOC样式。  
3. 我们需要快速编辑、便捷发布、深入交流的DOC工具。

## 语法
MarkDown标准语法请参考 [https://www.markdownguide.org/basic-syntax/#html](https://www.markdownguide.org/basic-syntax/#html)  
目前我们能支持翻译的MarkDown 语法有:

HTML | MarkDown | 备注
--- | --- | ---
三级标题h1,h2,h3 | # ## ### |
正文 | 正文 |
换行 | 空行 |
分割线hr |    |
代码块 |  | 如果不带语言tag,则为返回数据块。调用示例必须加上对应的语言，便于语法高亮
URL链接 |  | 遵循markdown语法 
图片及链接 |  | 遵循markdown语法
表格 |  | 注意空行和英文竖线,遗留问题，大表格和小表格用不同间距显示使之美观
无序列表 | | 正方形marker
Blockquote | | 左边框加粗表示引用或强调

## 数学语法
数学符号是我们工程师交流的语言。数学语言简洁优美，往往是一篇优秀技术文章的灵魂所在。非常推荐研发人员把计算机问题或工程问题总结成数学问题。  
我们沿用LaTex的数学符号和数学表达式。在WEB前端只需要加入KaTeX的css和js,就能使HTML Node展示TeX语法的数学表达式。
这里总结一下TeX语法。
### 数学符号
*希腊字母* $ \alpha\ \beta\ \gamma\ \rho\ \sigma\ \delta\ \epsilon $  
*二进制运算符* $ \times\ \otimes\ \oplus\ \cup\ \cap $  
*关系运算符*  $ <\ >\ \subset\ \supset\ \subseteq\ \supseteq\ $  
*其他* $ \int\ \oint\ \sum\ \prod $
### 上标^和下标_
*勾股定理* $ x^2 + y^2 = z^2\  z = \sqrt{x^2 + y^2}\  a_1^2 + a_2^2 = a_3^2 $  
*方程式* $ x^{2\alpha} - 1 = y_{ij} + y_{ji}\  (a^n)^{r+s} = a^{nr+ns} $  
*定积分* $ \int\limits_0^1 x^2 + y^2 \ dx\    \int_0^1 x^2 + y^2 \ dx $  
*和与积* $ \sum_{i=1}^{\infty} \frac{1}{n^s} = \prod_p \frac{1}{1-p^{-s}} $  
### 括号
*括号* $ (x+y) [x+y] \{ x+y \} $  
*绝对值* $ |x+y| $  
*双竖线* $ \|x+y\| $  
*括号自适应* $ F = G \left( \frac{m_1 m_2}{r^2} \right) $
### 矩阵
*普通* $ \begin{matrix} 1 & 2 & 3\\a & b & c \end{matrix} $  
*圆括号* $ \begin{pmatrix} 1 & 2 & 3\\a & b & c \end{pmatrix} $  
*方括号* $ \begin{bmatrix} 1 & 2 & 3\\a & b & c \end{bmatrix} $  
*行列式* $ \begin{vmatrix} 1 & 2 & 3\\a & b & c \end{vmatrix} $  
### 分数和二项式
*二项式系数* $ \binom{k}{n} = \frac{k!}{n!(n-k)!} $
### 方程式与对齐
```latex
\begin{equation} \label{eq1}
  \begin{split}
    A & = \frac{\pi r^2}{2} \\
      & = \frac{1}{2} \pi r^2
  \end{split}
\end{equation}

\begin{align*}
  2x - 5y &= 8 \\
  3x + 9y &= -12
\end{align*}
```

# 实现说明
该工程的核心文件为一个python文件，在后台把.md文件转成.html文件；一个css文件，为html文件的显示样式，在上述转换过程中就插入html文件；一个js文件，添加前端逻辑如根据滚动内容定位nav title等。其余为附加文件，如代码高亮的css等。

生成的.html文件都是内容部分。nav title是根据内容提取h1,h2而生成的。为了简便起见，目前nav title只支持h1,h2两层。
另外，一般图片是上传图床或OSS, 我们提供了一个图片上传接口，上传图片后把图片地址拷到相应的位置即可。

## md文件上传
目前md文档上传只限于管理员操作。后续试情况开放个人。  
### URL
[https://developer.yijianar.com/api/md_upload](https://developer.yijianar.com/api/md_upload)
### 参数说明
参数名 | 含义 | 示例
--- | --- | ---
markdown | 本地的markdown文件 | 开发者平台.md
author | 作者，上传人 | 黄国政
tags | 标签 | '["Python","aiohttp"]'
folder | 上传到目录 | 标准API/开发者平台
force | 强制覆盖 | true 非必需

### 调用示例
```shell
curl -F markdown=@开发者平台.md -F author=黄国政 -F tags='["web","python","markdown"]' -F folder=对外API/测试文件夹  https://developer.yijianar.com/api/md_upload
```
### 返回结果
```html
<meta charset="UTF-8"> <link rel="stylesheet" href="https://developer.yijianar.com/stinger/css/atom-one-light.min.css">  <link rel="stylesheet" href="https://developer.yijianar.com/stinger/css/markdown.css"> <nav></nav> <article> <h1 class="filename">服务器维护</h1>
......
<script src="https://developer.yijianar.com/stinger/js/highlight.min.js"></script><script src="https://developer.yijianar.com/stinger/js/apidoc.js"></script> 
```

## 预览
虽然我们在OPENAPI里提供了预览功能。但这个要求我们先上传。推荐我们本地安装MarkDown工具预览，尤其推荐通过visual studio code上安装Markdown插件来预览。

# 文档接口说明
每个文档有核心文档、对内文档、一般文档三种属性。  
用户角色分一般成员、内部成员、超级管理员三种。内部成员的定义是:账号已绑定钉钉。 一般成员只能访问一般文档； 内部成员能访问一般文档和对内文档； 超级管理员能访问全部三种文档。
## 创建文件夹
我们的root有四个：核心API, 内部API, 对外API, 默认公开
```shell
curl -X POST -d '{"root":"对外API","folder":"测试文件夹"}' https://developer.yijianar.com/api/mkdir
```
### 文件夹列表
在分组管理里，需要查看文件夹列表
```shell
curl https://developer.yijianar.com/api/lsdir
```
## 文件与文件夹
### 删除文件夹
```shell
curl -X POST -d '{"root":"对外API","folder":"测试dir2"}' https://developer.yijianar.com/api/rmdir
```
### 重命名文件夹
```shell
curl -X POST -d '{"root":"对外API","folder":"测试dir","newname":"测试测试dir"}' https://developer.yijianar.com/api/rndir
```
### 删除文件
```shell
curl -X POST -d '{"root":"对外API","folder":"测试dir2","filename":"delfile.html"}' https://developer.yijianar.com/api/rmfile
```
### 重命名文件
```shell
curl -X POST -d '{"root":"对外API","folder":"测试dir","filename":"rnfile.html","newname":"newfile.html"}' https://developer.yijianar.com/api/rnfile
```
### 移动文件夹
```shell
curl -X POST -d '{"root":"标准API","folder":"开发者平台","destroot":"内部API"}' https://developer.yijianar.com/api/mvdir
```
### 移动文件
```shell
curl -X POST -d '{"srcfile":"标准API/开发者平台/开发者平台.html","destfile":"标准API/公开/开发者平台.html"}' https://developer.yijianar.com/api/mvfile
```
### 发布文件
```shell
curl -X POST -d '{"previewfiles":["标准API/开发者平台/开发者平台.preview.html"]}' https://developer.yijianar.com/api/publish_files
```
## 文档检索
### 按标题检索
```shell
curl -X POST -d '{"title":"服务器"}' https://developer.yijianar.com/api/search_title_in_apifolder
```
### 按标签检索
```shell
curl -X POST -d '{"tag":"服务器"}' https://developer.yijianar.com/api/search_tag_in_apifolder
```
### 按内容检索
```shell
curl -X POST -d '{"content":"服务器"}' https://developer.yijianar.com/api/search_content_in_apifolder
```
### 综合检索
```shell
curl -X POST -d '{"all":"服务器"}' https://developer.yijianar.com/api/search_all_in_apifolder
```
