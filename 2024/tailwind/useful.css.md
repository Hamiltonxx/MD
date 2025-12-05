# 占位图
https://picsum.photos/width/height
https://picsum.photos/squareborder
https://picsum.photos/id/237/width/height
https://picsum.photos/seed/8642/width/height
https://picsum.photos/seed/8642/width/height?grayscale
https://picsum.photos/seed/8642/width/height?blur
https://picsum.photos/200/300.webp

# Google Font Mirror
https://fonts.googleapis.com/css?family=Roboto
https://google-fonts.mirrors.sjtug.sjtu.edu.cn/css?family=Roboto

# NPM Mirror
```
cd helloflex
cp src/notification.html src/rating.html
touch src/rating.css

npx --registry http://mirrors.cloud.tencent.com/npm/ tailwindcss -i src/rating.css -o src/output.css
npx --registry https://registry.npmmirror.com create-next-app@latest .
```

# HTTP Proxy
```shell
export HTTP_PROXY=127.0.0.1:58591
export HTTP_PROXY=socks5://127.0.0.1:1080
export HTTPS_PROXY=socks5://127.0.0.1:1080
# npm config get proxy
# npm config delete proxy
```
