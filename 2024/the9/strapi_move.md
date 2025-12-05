这部分为迁出机上的操作
# mysqldump
```
mysqldump -h aitu-db01.cnewi6uyauwp.us-east-1.rds.amazonaws.com -u aitu -pAi_tu_3421511 the9cms > the9cms.sql
```
# 打包拷贝工程目录
```
cd the9cms
rm -rf node_modules # 删除lib,方便copy
zip -r the9cms.zip the9cms
```


---------- 以下部分需要在新机上操作 ----------
# 安装node
```
# installs nvm (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
# download and install Node.js (you may need to restart the terminal)
nvm install 20
node -v # v20.15.1
npm install -g yarn
```

# 安装依赖
```
cd th9cms
yarn install
```

# 配置环境变量
更新.env文件， 主要修改mysql(8)连接的配置。

# 导入数据库
```
# CREATE DATABASE the9cms CHARACTER SET utf8 COLLATE utf8_general_ci;
> mysql -u username -p the9cms < the9cms.sql
```

# 验证
```
yarn develop
# 或者 yarn start
```

# nginx 配置
```
server {
    listen 80;
    server_name the9admin.bongos.ai;
    return 301 https://$host$request_uri;
}
server {
    listen 443 ssl;
    server_name the9admin.bongos.ai; # 应该是the9.com吧

    location / {
        proxy_pass http://localhost:1337;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

        proxy_buffering off;
        chunked_transfer_encoding on;
    }

    ssl_certificate xxx.cer;
    ssl_certificate_key xxx.key;
}
```

# systemd
```
[Unit]
Description=Strapi
After=network.target

[Service]
Type=simple
User=ubuntu
ExecStart=/home/ubuntu/.nvm/versions/node/v20.15.1/bin/yarn start
WorkingDirectory=/home/ubuntu/projects/the9cms
Restart=always
Environment=NODE_ENV=production
Environment=PATH=/home/ubuntu/.nvm/versions/node/v20.15.1/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

[Install]
WantedBy=multi-user.target
```

# Log
```
cd the9cms
mkdir logs
yarn add winston
cd config
vim logger.js
```
```
const { transports, createLogger, format } = require('winston');

module.exports = {
  transports: [
    new transports.File({
        filename: process.env.LOG_FILE_PATH || 'logs/strapi.log', // 默认值为 logs/strapi.log
    })
  ],
};
```
.env里增加
LOG_FILE_PATH=logs/strapi.log