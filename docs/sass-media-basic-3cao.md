# Sass 媒体基础

> 原文：<https://dev.to/alicannklc/sass-media-basic-3cao>

[![](img/20779078298eb41f8af3f8f13ac1cf3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LLjaIN37--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/Alicannklc/sass-media-basic/raw/master/ezgif-2-df3a00336c.gif)

| 钥匙 | 价值 |
| --- | --- |
| xphone | 320 像素 |
| 电话 | 480 像素 |
| 药片 | 768 像素 |
| 平板电脑-md | 1024px |
| 宽银幕 | 1200 像素 |
| widescreen-x | 1440 像素 |
| widescreen-xl | 1920 像素 |

最小宽度

```
 // min-width >=
  .row {
  width: 100%;
  height: 60px;
  position: relative;
  transition: all .2s ease-out;
  z-index: 8;
  @include media('>=Key') {
    margin: 0px;
    color: wheat;
  }
} 
```

输出 Css

```
.row {
  width: 100%;
  height: 60px;
  position: relative;
  transition: all .2s ease-out;
  z-index: 8; }
  @media (min-width: key) {
    .row {
      margin: 0px;
      color: wheat; }
       } 
```

最大宽度

```
// max-width <=
  .row {
  width: 100%;
  height: 60px;
  position: relative;
  transition: all .2s ease-out;
  z-index: 8;
  @include media('<=Key') {
    margin: 0px;
    color: wheat;
  }
} 
```

输出 Css

```
.row {
  width: 100%;
  height: 60px;
  position: relative;
  transition: all .2s ease-out;
  z-index: 8; }
  @media (max-width: key) {
    .row {
      margin: 0px;
      color: wheat; }
       } 
```

使用

```
// Min >=
// Max <=
  @include media('>=Key or px') 
```

[Github](https://github.com/Alicannklc/sass-media-basic)

[包含-媒体](https://include-media.com/)