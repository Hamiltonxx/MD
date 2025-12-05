# 域名

aigc.cirray.cn

## 证书

ACME 最著名的实现是由 Let's Encrypt 提供的。  
Let's Encrypt 颁发的证书有效期为 90 天。需要自动续期。  
使用 ACME 协议获取 SSL/TLS 证书，通常用: 1. Certbot; 2. acme.sh; 3. Caddy  
acme.sh 是一个简单且强大的 shell 脚本，用于从 CA 处获取、续订和管理 SSL/TLS 证书。bb

```shell
curl https://get.acme.sh | sh

crontab -l
5 7 * * * "/home/ubuntu/.acme.sh"/acme.sh --cron --home "/home/ubuntu/.acme.sh" > /dev/null

source ~/.bashrc
```

```shell
acme.sh --register-account -m hamiltonhgz@gmail.com
```

## 生成证书

# 使用 DNS API 模式申请证书

```shell
export Ali_Key=""
export Ali_Secret=""
# 下次就不用再执行这个命令了
acme.sh --issue --dns dns_ali -d *.cirray.cn --force

acme.sh --issue --dns dns_ali -d aigc.cirray.cn
```

Order status is processing, lets sleep and retry. 等几次 retry 就好。

## 安装证书到 Nginx

```shell
sudo mkdir /etc/nginx/certs

acme.sh --install-cert -d aigc.cirray.cn \
    --cert-file ~/temp/aigc.cert.pem \
    --key-file ~/temp/aigc.key.pem \
    --fullchain-file ~/temp/aigc.fullchain.pem \
    --reloadcmd "sudo systemctl reload nginx"
# sudo acme.sh 会报错
```

可能需要

```shell
acme.sh --register-account -m hamiltonhgz@gmail.com
# Registered
# ACCOUNT_THUMBPRINT='3Y9kj29OiHL4BOO1kTCn2Qs6KiFao_pj3Ug2qMu2drM'
# 或
acme.sh --set-default-ca --server letsencrypt
```

# 403 Forbidden

```shell
sudo tail /var/log/nginx/error.log

# 最后
sudo vim nginx.conf
# user ubuntu;
```
