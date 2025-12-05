---
mysql数据库备份
---
# 简单直观备份
Server A 
```shell
mysqldump -u aigc -p aigcdb > aigcdb0229.sql
```
Server B
```shell
scp hamilton@sh2.cirray.cn:~/temp/aigcdb0229.sql .
> CREATE DATABASE aigcdb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
mysql -u aigc -p aigcdb < aigcdb0229.sql
```

# 增量备份
