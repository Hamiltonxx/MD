# 代码规范
## 编码
文件使用UTF-8编码。
## 缩进
统一使用4个空格缩进。1 tab = 4 spaces。
## 行宽
每行代码尽量不超过80个字符,特殊情况下略微超过但最长不得超过120.
## 引号
自然语言使用双引号"",机器标识使用单引号'',文档字符串使用三引号""" """。
## 空行
模块级函数和类定义之间空两行；  
类成员函数之间空一行；  
函数中可以用空行分隔出逻辑相关代码。
## import
import语句应该使用absolute import；  
import语句应该放在文件头部(模块说明及docstring之后)；  
## 空格
二元运算符两边各空一格；  
函数的参数列表中，,之后要有空格；  
## 换行
第二行缩进到括号的起始处；  
第二行缩进4个空格，适用于起始括号就换行的情形；  
使用反斜杠\换行；  
禁止复合语句；
if/for/while必须换行；
## docstring
所有公共模块、函数、类、方法，都应写docstring。
## 注释
在代码的关键或复杂部分，能写注释尽量写注释；  
行注释使用两个空格和语句分开；  
对函数参数、返回值等的说明采用numpy标准；
## 命名规范
模块首字母小写，尽量不要用下划线；  
类名使用驼峰(CamelCase)命名风格；  
函数名一律小写，多个单词用下划线隔开；  
变量名尽量小写，多个单词用下划线隔开；  
常量全部大写，多个单词用下划线隔开；
## 其他
上面列出的仅仅是我们重点关注的地方。其他规范请参照PEP 8(Python Enhancement Proposals)。 建议经常用IDE的pep8插件来reorg代码，以此维护代码的可读性。

# 数据库(表)设计规范
## 所有表必须使用InnoDB存储引擎
支持事务，支持行级锁，更好的恢复性和高并发性能。
## 数据库和表字符集统一使用UTF8MB4
兼容性更好，也避免字符集转换产生乱码。
## 所有表和字段都需要添加注释
使用comment从句添加表和列的备注，从一开始就要关心维护。
## 控制单表大小，建议控制在500万以内
表过大会造成修改表结构、备份、恢复都会有很大问题。可以用历史数据归档、分库分表等手段来控制数据量大小。
## 谨慎使用分区表
建议采用物理分表的方式管理大数据
## 禁止在数据库中存储图片、文件等大的二进制数据
会造成短时间内数据量快速增长，增加IO负担。通常数据库只存储文件地址信息。
## 字段设计
优先选择符合存储需要的最小的数据类型；  
避免使用TEXT、BLOB数据类型；  
避免使用ENUM类型；  
使用TIMESTAMP(4字节)或DATETIME(8字节)存储时间；  
同财务相关的金额类数据必须使用decimal类型(精准浮点)；
## 限制每张表上的索引数量
建议单张表索引不超过5个。常见索引列建议:  
出现在SELECT、UPDATE、DELETE语句的WHERE从句中的列；  
包含在ORDER BY、GROUP BY、DISTINCT中的字段；  
多表JOIN的关联列。 
## 避免使用子查询
可以把子查询优化为JOIN操作。
## 减少同数据库的交互次数
数据库更适合处理批量操作 合并多个相同的操作到一起，可以提高处理效率。
## 禁止为程序使用的账号赋予super权限
对于程序连接数据库账号，遵循权限最小原则；  
当达到最大连接数限制时，super权限只能留给DBA处理问题的账号使用。
## 其他
上面列出的仅仅是我们重点关注的地方。其他规范请参考MySQL官方参考手册的最佳实践部分。

# 接口设计规范
## 面向使用者建模
仔细定义"资源"，资源的语义须面向使用者。避免琐碎的API。
## 一类资源的两个URL
每个资源都应该只有两个基础URL(Endpoint)，一个用于集合，一个用于集合中的某个特定元素。
## 使用一致的复数名词
避免混用复数和单数形式。
## 保持URL简单短小
可将复杂或可选参数移动到查询字符串。
## 使用HTTP Method表示动作
URL中不应该包含动词。
## HTTP Method
每个资源都会对应一组操作方法，常见操作方法如下:  

| 操作类型 | HTTP映射 | 举例 |
| --- | --- | --- |
| 获取资源集合 | GET | curl -X GET https://foo.bar.com/api/users |
| 获取单个资源 | GET | curl -X GET https://foo.bar.com/api/users/123 |
| 创建资源 | POST | curl -X POST -d '{"name":"xx","age":30}' https://foo.bar.com/api/users |
| 更新资源 | PUT | curl -X PUT -d '{"name":"xx","age":20}' https://foo.bar.com/api/users/123 |
| 局部更新资源 | PATCH | curl -X PATCH -d '{"name":"xx","age":20}' https://foo.bar.com/api/users/123 |
| 删除资源 | DELETE | curl -X DELETE https://foo.bar.com/api/users/123 |

其中，POST/PUT和PATCH的区别在于，全部更新还是局部更新。POST/PUT为该资源的所有字段均被更新或覆盖。
## Response Body结构
使用相同的HTTP响应结构，推荐使用下列结构:
```
{
  "code": 0,          # 错误码
  "msg": "success",   # 提示信息
  "data": {           # 数据内容
    "id": 123,
    "name": "xxx"
  }
}
```
## HTTP状态码
使用合适的HTTP Status Code, 来表达响应的语义  

| HTTP | 描述 |
| --- | --- |
| 200 | No error. |
| 400 | Invalid argument.(参数错误) |
| 401 | Request not authenticated due to missing, invalid, or expired token. (Token认证错误) |
| 403 | Client does not have sufficient permission.(无权限) |
| 404 | Resource not found.(资源不存在) |
| 405 | The HTTP method in the request is not allowed on the resource.(请求的方法不支持) |
| 409 | Concurrency conflict, such as read-modify-write conflict.(并发冲突、读写冲突) |
| 429 | Either out of resource quota or reaching rate limiting.(限流错误) |
| 500 | Internal server error.(内部异常，通常为代码BUG) |
| 503 | Service unavailable.(服务不可用，可能为宕机) |
| 504 | Request deadline exceeded.(调用超时) |  

## 错误码
在使用 HTTP Status Code 的基础上，还需要有业务错误码，通过code字段返回。错误码由各业务方自行约定，业务内部自行划分区段。
## 按需返回
只返回使用方依赖数据的最小集，确保返回的字段都是对功能有意义的。
## 长耗时请求异步化
如果某个API需要很长时间才能完成，可以通过:  
1. 在服务端异步启动任务，并返回GUID标识的任务  
2. 客户端通过定时轮询，获取任务进度或状态  
3. 当任务完成/失败时，客户端获取到任务结果/失败原因。
## 其他
上面列出的仅仅是我们重点关注的地方。其他规范请参考Google API Design Guide.