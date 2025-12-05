# Creating a Strapi project
```
CREATE DATABASE cirraycms CHARACTER SET utf8 COLLATE utf8_general_ci; 
``` 
```shell
proxychains4 yarn create strapi-app cms
```
Custom > Skip > Typescript > mysql >  
    Database name: cirraycms  
    Host: sh.cirray.cn  
    Username: aigc  
    Password: aigc2023

# Admin
```
cd cms
yarn develop
```
http://cms.cirray.cn:1337/admin  
admin account: guozheng_huang@163.com  Poiu0987

# Content-Type Builder
title, content, cover, introduction, category, schedule_at

# Content Manager
Create an entry  
Settings > Roles

# Plugin
```
proxychains4 yarn strapi generate plugin
```
```
export default {
  'reviewmail': {
    enabled: true,
    resolve: './src/plugins/reviewmail'
  },
}
```
```
cd src/plugins/reviewmail
proxychains4 yarn
yarn build
```

# mail
```
    <script>
        function approveAndPublish() {
            const url = 'http://localhost:1337/api/news/{newsid}';
            fetch(url, {
                method: 'PUT',
                headers: { 'Content-Type': 'application/json'},
                body: JSON.stringify({data:{publishedAt:new Date()}})
            })
            .then(resp => resp.json())
            .then(data => alert('审核通过并发布成功!'))
            .catch(error => alert('审核失败: '+error.message))
        }
    </script>
```