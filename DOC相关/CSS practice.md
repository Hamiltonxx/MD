# Checkpoint
1. Content width with left and right space.  
2. Top and bottom space.
3. Typography: font family,size,weight,color
4. Buttons.

# Preparing codes and images
1. logo.png from logo.svg
2. hero.jpg

# Google Font
1. 下载 Noto_Sans_SC包  
2. 解压后，把文件夹拷到工程目录下  
3. css里引用
```css
@font-face {
  font-family: NotoSansSC;
  font-weight: 100;
  src: url('font/Noto_Sans_SC/NotoSansSC-Thin.otf') format('opentype');
}
@font-face {
  font-family: NotoSansSC;
  font-weight: 300;
  src: url('font/Noto_Sans_SC/NotoSansSC-Light.otf') format('opentype');
}
@font-face {
  font-family: NotoSansSC;
  font-weight: 400;
  src: url('font/Noto_Sans_SC/NotoSansSC-Regular.otf') format('opentype');
}
@font-face {
  font-family: NotoSansSC;
  font-weight: 500;
  src: url('font/Noto_Sans_SC/NotoSansSC-Medium.otf') format('opentype');
}
@font-face {
  font-family: NotoSansSC;
  font-weight: 700;
  src: url('font/Noto_Sans_SC/NotoSansSC-Bold.otf') format('opentype');
}
@font-face {
  font-family: NotoSansSC;
  font-weight: 900;
  src: url('font/Noto_Sans_SC/NotoSansSC-Black.otf') format('opentype');
}
```
4. 调用
```css
body {
  font-family: NotoSansSC, sans-serif;
}
h2 {
  font-weight: 500;
}
```