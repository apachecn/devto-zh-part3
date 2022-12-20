# 让你的建设更快与吞咽 4

> 原文：<https://dev.to/khalyomede/make-your-builds-quicker-with-gulp-4-kge>

大口 4 版出来了！这种迁移非常简单，只涉及最少的破坏性更改，同时带来了一个非常有用的特性:能够并行化任务。

## 从 3 升级到 4

你可以跟随一个最受欢迎的[媒体帖子](https://codeburst.io/switching-to-gulp-4-0-271ae63530c0)来成功地完成转变。

萨姆还在他的食谱上写下了如何让转变变得轻而易举。

## 文章不再可用

## 用大口压缩图像 3

在 Gulp 4 之前，这是你可能已经做的压缩图像。

```
const gulp = require("gulp");
const imagemin = require("gulp-imagemin");
const webp = require("gulp-webp");

gulp.task("pictures", function() {
  return gulp.src("src/img/**/*.{jpg,jpeg,png,svg}")
    .pipe(imagemin())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("webp", function() {
  return gulp.src("src/img/**/*.{jpg,jpeg,png}")
    .pipe(webp())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("img", ["pictures", "webp"]); 
```

这意味着

> 把我的 jpeg，png，svg 文件一个一个压缩，等都压缩好了再转换成 webp。

这很好，但是主要的警告是你的`picture`任务必须一个接一个地处理你所有的图像。

如果我们考虑一下，我们可以按照文件类型来划分我们的过程:`png`、`jpeg`、`svg`。这是可能的，因为 [gulp-imagemin](https://www.npmjs.com/package/gulp-imagemin) 使用不同的库来压缩图像( [SVGO](https://www.npmjs.com/package/svgo) 、 [PNGQuant](https://www.npmjs.com/package/pngquant) 、 [JPEGTran](https://www.npmjs.com/package/jpegtran) )。

## 用大口压缩图像 4

首先让我们保持同样的算法，并使用新的 [`gulp.series()`](https://gulpjs.com/docs/en/api/series) 方法。

```
const gulp = require("gulp");
const imagemin = require("gulp-imagemin");
const webp = require("gulp-webp");

gulp.task("picture", function() {
  return gulp.src("src/img/**/*.{png,jpg,jpeg,svg}")
    .pipe(imagemin())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("webp", function() {
  return gulp.src("src/img/**/*.{png,jpg,jpeg}")
    .pipe(webp())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("img", gulp.series("picture", "webp")); 
```

[![Doing the same, but with modern methods](img/a8b4290314be091881c585fd6408c6c7.png)](https://i.giphy.com/media/C6JQPEUsZUyVq/giphy.gif)

如果您在控制台上运行`gulp img`,您将得到相同的输出。现在我们使用最新的 Gulp 4 特性，非常直接的迁移！

让我们把任务分开。

```
const gulp = require("gulp");
const imagemin = require("gulp-imagemin");
const webp = require("gulp-webp");

gulp.task("png", function() {
  return gulp.src("src/img/**/*.png")
    .pipe(imagemin())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("jpg", function() {
  return gulp.src("src/img/**/*.{jpg,jpeg}")
    .pipe(imagemin())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("svg", function() {
  return gulp.src("src/img/**/*.svg")
    .pipe(imagemin())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("webp", function() {
  return gulp.src("src/img/**/*.{png,jpg,jpeg}")
    .pipe(webp())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("img", gulp.series("png", "jpg", "svg", "webp")); 
```

同样，什么都没有改变，只是让接下来的事情变得更容易。

现在有趣的部分是:让我们并行运行前三个任务。

```
const gulp = require("gulp");
const imagemin = require("gulp-imagemin");
const webp = require("gulp-webp");

gulp.task("png", function() {
  return gulp.src("src/img/**/*.png")
    .pipe(imagemin())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("jpg", function() {
  return gulp.src("src/img/**/*.{jpg,jpeg}")
    .pipe(imagemin())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("svg", function() {
  return gulp.src("src/img/**/*.svg")
    .pipe(imagemin())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("webp", function() {
  return gulp.src("src/img/**/*.{png,jpg,jpeg}")
    .pipe(webp())
    .pipe(gulp.dest("dist/img"));
});

gulp.task("img", gulp.series(gulp.parallel("png", "jpg", "svg"), "webp")); 
```

这意味着:

> 以你想要的任何顺序压缩所有的 png、jpg 和 svg 文件，但是在开始将它们转换成 webp 之前要等待。

使用新的 [`gulp.parallel()`](https://gulpjs.com/docs/en/api/parallel) ，执行并行化任务简直易如反掌！

## 更进一步

现在这样好多了，但是还有一件小事让我很烦。如果你注意到了，这是我们图片中使用的斑点:

```
".{png,jpg,jpeg,svg}" 
```

但是 webp blob 缺少 svg:

```
".{png,jpg,jpeg}" 
```

我这样做是有意的，因为我不想将我的 SVG 文件转换成 WebP:它们在保持高质量的同时，对响应性的响应是完美的，所以我不想失去这个特性。

这也意味着我们的那个`webp`任务不必在 svg 完成之前等待被压缩。所以我们可以添加另一个优化层，如下所示。

```
gulp.task('picture', gulp.parallel('png', 'jpg'));

gulp.task('img', gulp.parallel(gulp.series('picture', 'webp'), 'svg'); 
```

这意味着:

> 在用 webp 压缩 jpg 和 png 文件的同时压缩 SVG 文件等待 jpg 和 png 文件转换成 webp。

## 结论

我喜欢 Gulp 出色的用户体验。构建复杂的捆绑逻辑是如此简洁明了。

如果你想了解更多关于 gulp 及其所有特性的信息，包括在更新文件时观察文件变化和执行任务等等，请查看文档。

Gulp 还支持现代的 Javascript 符号，所以你可能想要编写你喜欢的任务，比如:

```
const { src, dest, series, parallel } = require("gulp");
const imagemin = require("gulp-imagemin");
const webp = require("gulp-webp");

const png = () => src("src/img/**/*.png")
  .pipe(imagemin())
  .pipe(dest("dist/img"));

const jpg = () => src("src/img/**/*.{jpg,jpeg}")
  .pipe(imagemin())
  .pipe(dest("dist/img"));

const webp = () => src("src/img/**/*.{png,jpg,jpeg}")
  .pipe(webp())
  .pipe(dest("dist/img"));

const img = series(parallel(png, jpg), webp);

module.exports = { img }; 
```

我希望你和我一样对这些新功能感到惊讶！捆绑愉快！