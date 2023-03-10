# 用 WebP 加速你的网页加载

> 原文：<https://dev.to/anwar_nairi/speed-up-your-web-page-loading-with-webp-24bk>

## 图像被广泛使用

图像在我们的带宽中占据越来越多的空间。

[W3Techs 统计数据](https://w3techs.com/technologies/overview/image_format/all)显示，90%的网站使用图片。

对于[news.google.com](https://news.google.com)来说，图片占了主页上加载的资产的 44 %，占用了 30%的加载时间(来源:我用 DevTools 手动分析了页面)。

## Webp

Google 提出了 WebP 图片格式，它提供了更小的图像尺寸。

根据 Google 对[不同无损压缩算法与 webp](https://developers.google.com/speed/webp/docs/webp_lossless_alpha_study) 进行的案例研究，与传统 png 压缩库相比，后者节省了高达 42%的字节。

我还对 JPEG 图像(1920x1080)进行了测试，结果如下:

[![WebP versus JPEG image format](img/4435674727825c9d2a96388aca292b0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ETq9xSqE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y8o30d207vajqmou81gm.png)

正如你所看到的，JPEG 图片的重量为 530KB，而 WebP 图片的重量为 195KB，轻了 36%，这与谷歌针对 PNG 的声明相差不远。

*[原始图像](https://www.pexels.com/photo/adventure-alpine-altitude-austria-355241/)*

## 覆盖范围

WebP 在所有最流行的浏览器上都得到了正确的支持(目前覆盖率为 75%)。

| 浏览器 | 支持 | 最低版本 | 发布日期 |
| --- | --- | --- | --- |
| 微软公司出品的 web 浏览器 | ✘ |  |  |
| 边缘 | ✔ | Eighteen | 2018 年 11 月 13 日 |
| 铬 | ✔ | Seventy-one | 2018 年 12 月 5 日 |
| 火狐浏览器 | ✔ | Sixty-five | 2019 年 1 月 29 日 |
| 歌剧 | ✔ | fifty-six | 2018 年 9 月 25 日 |
| 旅行队 | ✘ |  |  |

*资料来源:[https://caniuse . com/# feat = web](https://caniuse.com/#feat=webp)*

## 在生产中使用 WebP

下面是一个使用 [<图片>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture) HTML 标签
优化网页的例子

```
<!-- before -->
<img src="/img/mountain.jpg" alt="Moutains view" />

<!-- after -->
<picture>
  <source type="image/webp" srcset="/img/mountain.webp" />
  <img src="/img/mountain.jpg" alt="Mountains view" />
</picture> 
```

Enter fullscreen mode Exit fullscreen mode

如果用户代理不支持 WebP，浏览器将退回到`<img />`。图片是一个 HTML 标签，覆盖了 87%的(感谢 [Sylvain](https://dev.to/martyonthefly) 改进这一部分)，所以如果浏览器不支持`<picture>`标签，它仍然会退回到`<img />`标签(感谢 [Fotis](https://dev.to/isfotis) 提供的信息)。).

标签的功能更加强大，例如，它可以让你根据当前浏览器视窗的宽度来选择图像的大小。在 [MDN 页面](https://developer.mozilla.org/fr/docs/Web/HTML/Element/picture)了解更多信息。

## 将图片转换成 WebP 格式的工具

网上有很多服务可以将图片转换成 WebP。我个人用 [Gulp.js](https://gulpjs.com/) 自动完成这项任务。

```
const gulp = require("gulp");
const webp = require("gulp-webp");

gulp.task("webp", function() {
  return gulp.src("src/img/**/*.{jpg,jpeg,png,svg}")
    .pipe(webp())
    .pipe(gulp.dest("dist/img"));
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 更进一步

让我向您推荐 Boris 的这篇令人难以置信的完整的图像优化文章。

[![borisschapira](img/e1b1eee54f7af4b344f39be9ccdcbbcc.png)](/borisschapira) [## 优化图像以减轻页面重量:文件格式、工具和 RWD

### 鲍里斯·夏皮拉 11 月 8 日 1710 分钟阅读

#performance #images #webperf #webdev](/borisschapira/optimize-images-to-reduce-page-weight-file-formats-tools-and-rwd-8g2)

## 结束语

建立或维护一个网站也伴随着对我们用户的责任:我们也应该在一个轻型数据计划中考虑用户。

我认为 web 开发在优化方面提供了如此多的可能性。这是通过减少用户带宽使用，同时减少服务器 i/o 时间来提高参与度的绝佳机会。

这就是为什么我认为在不损害用户体验的情况下削减这些宝贵的字节时，WebP 可能是一个很好的盟友。

快乐的优化！

封面照片由来自[像素](https://pixabay.com/en/speed-car-vehicle-drive-automobile-1249610/)的 [Pixabay](https://www.pexels.com/@pixabay) 拍摄