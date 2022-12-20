# 在噩梦中看到构建工具？我是。

> 原文：<https://dev.to/virtuallycreative/seeing-build-tools-in-your-nightmares-i-was-ddo>

*现在，这是一个关于*
*我的建设如何被颠覆*
*的故事，我想花一分钟*
*就坐在那里*
*我会告诉你我是如何在吃梨的时候拯救我的网站建设的。*

*在项目的 node_modules init 和 raised 中*
*在 VS 代码上我花了大部分时间*
*放松马鑫*
*并且只是在校外写了一些 Gulpjs*
*当几个不怀好意的管道*
*开始在我的构建工具罩中制造麻烦的时候*
*我得到了一点*

最近，我有机会重构一些旧的遗留网站。我们有许多这样的模板，为了简单起见，我们为许多应用程序使用了一个标准化的 Gulp 构建模板。我觉得是时候重新审视这个构建模板了，因为我在 2017 年制作了它，尽管它经常被使用，但我一直没有时间更新它。

在做一些研究时，我偶然发现了 [Brunch.io](https://brunch.io) ，一旦我开始了解更多，我就被吸引住了。作为一个使用过很多可用的构建工具(Gulp、Grunt、NPM、package、Webpack 等)的人..)我惊讶于设置、配置和简单构建是如此简单。

最初 350 多行的`gulpfile.js` build glory 在一个`brunch-config.js`文件中被浓缩为仅仅 39 行！

说真的。我没开玩笑！我们来分析一下。

## 把文件一饮而尽

这是我在微型网站项目中使用的“海量”gulp.js 构建文件。

```
/**
 * Created by Alexander Romano on 02/05/2017.
 * Front-End DevOps - Automate using GulpJS
 *
 * A little dependent heavy, but not without purpose...
 */

// Assigning modules to local variables
var gulp = require('gulp');
var less = require('gulp-less');
var syncDocs = require('browser-sync').create();
var browserSync = require('browser-sync').create();
var header = require('gulp-header');
var cleanCSS = require('gulp-clean-css');
var rename = require("gulp-rename");
var pkg = require('./package.json');
var merge = require('merge-stream');
var connect = require('gulp-connect');
var useref = require('gulp-useref');
var uglify = require('gulp-uglify');
var gulpIf = require('gulp-if');
var cssnano = require('gulp-cssnano');
var runSequence = require('run-sequence');
var imagemin = require('gulp-imagemin');
var cache = require('gulp-cache');
var del = require('del');
var sourcemaps = require('gulp-sourcemaps');

// Sets Up Rollbar Error Reporting
// Get a free account at https://rollbar.com/signup/
var rollbar = require('gulp-rollbar');
// Make sure to get your Token and Version from Rollbar account first
// Paste in the values for the specific project
var rollbarToken = "###";
var rollbarVersion = "###";
// Set Production URL for Rollbar
var rollbarURL = "https://projectname.netlify.com";

// Set banner content
var banner = ['/*!\n',
    ' * Virtually(Creative) - &amp;amp;lt;%= pkg.title %&amp;amp;gt; v&amp;amp;lt;%= pkg.version %&amp;amp;gt; (&amp;amp;lt;%= pkg.homepage %&amp;amp;gt;)\n',
    ' * Copyright ' + (new Date()).getFullYear(), ' &amp;amp;lt;%= pkg.author %&amp;amp;gt;\n',
    ' * Licensed under &amp;amp;lt;%= pkg.license.type %&amp;amp;gt; (&amp;amp;lt;%= pkg.license.url %&amp;amp;gt;)\n',
    ' */\n',
    ''
].join('');

/**
 * BROWSER SYNC
 */
// Starts BrowserSync on the Compiled Dev Folder (APP)
gulp.task('browserSync:dev', function () {
    browserSync.init({
        server: {
            baseDir: 'app'
        },
    });
});

// Starts BrowserSync on the Compiled Dev Folder (DEMO)
gulp.task('browserSync:demo', function () {
    browserSync.init({
        server: {
            baseDir: 'demo'
        },
    });
});

/**
 * USEREF
 */
// Starts the DEV concat of all JS/CSS wrapped in Build Comments
// Pushes errors to Rollbar service to monitor during development
// Dumps the results in DEMO folder
gulp.task('useref:dev', function () {
    return gulp.src('app/*.html')
        .pipe(useref())
        .pipe(sourcemaps.init())
        .pipe(sourcemaps.identityMap())
        .pipe(gulpIf('app/js/*.js', uglify()))
        .pipe(rollbar({
            accessToken: rollbarToken,
            version: rollbarVersion,
            sourceMappingURLPrefix: 'http://localhost:3000'
        }))
        .pipe(gulpIf('app/css/*.css', cssnano()))
        .pipe(gulp.dest('demo/'));
});

// Starts the PROD concat of all JS/CSS wrapped in Build Comments
// Pushes errors to Rollbar service to monitor during production
// Dumps the results in the DIST folder
gulp.task('useref:prod', function () {
    return gulp.src('app/*.html')
        .pipe(useref())
        .pipe(sourcemaps.init())
        .pipe(sourcemaps.identityMap())
        .pipe(gulpIf('app/js/*.js', uglify()))
        .pipe(rollbar({
            accessToken: rollbarToken,
            version: rollbarVersion,
            sourceMappingURLPrefix: rollbarURL
        }))
        .pipe(gulpIf('app/css/*.css', cssnano()))
        .pipe(gulp.dest('dist/'));
});

/**
 * IMAGE OPTIMIZATION &amp;amp;amp; CACHING
 */
// Finds and optimizes all images and caches results
// Only need to redo cache if new images are added after build
gulp.task('images:dev', function () {
    return gulp.src('app/img/**/*.+(png|jpg|jpeg|gif|svg)')
        // Caching images that ran through imagemin
        .pipe(cache(imagemin({
            interlaced: true
        })))
        .pipe(gulp.dest('demo/img'));
});

// Finds and optimizes all images and caches results
// Only need to redo cache if new images are added after build
gulp.task('images:prod', function () {
    return gulp.src('app/img/**/*.+(png|jpg|jpeg|gif|svg)')
        // Caching images that ran through imagemin
        .pipe(cache(imagemin({
            interlaced: true
        })))
        .pipe(gulp.dest('dist/img'));
});

/**
 * ASSET COPIES
 * FONTS &amp;amp;amp; VIDEOS
 */

// Copies font's to DEMO folder
gulp.task('fonts:dev', function () {
    return gulp.src('app/fonts/**/*')
        .pipe(gulp.dest('demo/fonts'));
});

// Copies fonts to dist folder
gulp.task('fonts:prod', function () {
    return gulp.src('app/fonts/**/*')
        .pipe(gulp.dest('dist/fonts'));
});

// Copies vids (if any) to demo folder
gulp.task('vids:dev', function () {
    return gulp.src('app/vids/**/*')
        .pipe(gulp.dest('demo/vids'));
});

// Copies vids (if any) to dist folder
gulp.task('vids:prod', function () {
    return gulp.src('app/vids/**/*')
        .pipe(gulp.dest('dist/vids'));
});

/**
 * DEPENDANTS (NPM / BOWER)
 */

/**
 * CLEAN (Delete)
 */
// Cleans DIST folder
gulp.task('clean:prod', function () {
    return del.sync('dist');
});

// Cleans DEMO folder
gulp.task('clean:demo', function () {
    return del.sync('demo');
});

/**
 * MAIN BUILD TASKS
 */

// Main Dev task
// Runs Browser Sync with Watcher
gulp.task('dev', ['browserSync:dev'], function () {
    // Reloads the browser whenever HTML or JS files change
    gulp.watch('app/*.html', browserSync.reload);
    gulp.watch('app/js/**/*.js', browserSync.reload);
    gulp.watch('app/css/**/*.css', browserSync.reload);
});

// Main DEMO task
// Runs Browser Sync with Watcher
gulp.task('demo', ['browserSync:demo'], function () {
    // Reloads the browser whenever HTML or JS files change
    gulp.watch('demo/*.html', browserSync.reload);
    gulp.watch('demo/js/**/*.js', browserSync.reload);
    gulp.watch('demo/css/**/*.css', browserSync.reload);
});

// Main Dev Build task
// Builds Demo Folder by running all :dev tasks
gulp.task('demo:build', function (callback) {
    runSequence('clean:demo',
        ['useref:dev', 'fonts:dev', 'vids:dev', 'default'], 'images:dev', 'demo',
        callback
    );
});

// Main PROD Build task
// Builds Dist folder by running all :prod tasks
gulp.task('prod:build', function (callback) {
    runSequence('clean:prod',
        ['useref:prod', 'fonts:prod', 'vids:prod', 'default'], 'images:prod',
        callback
    );
});

// Default PROD Build task
// Builds Dist folder by running all :prod tasks
// typically invoked during an automated deployment
// Default task
gulp.task('default', ['prod:build']);
// Default "build" task
gulp.task('build', function (callback) {
    runSequence('prod:build', callback);
});

// Less task to compile the less files and add the banner
gulp.task('less', function () {
    return gulp.src('app/less/*.less')
        .pipe(less())
        .pipe(header(banner, {
            pkg: pkg
        }))
        .pipe(gulp.dest('css'))
        .pipe(browserSync.reload({
            stream: true
        }));
});

// Minify CSS
gulp.task('minify-css', function () {
    var vCBP = gulp.src('app/css/*.css')
        .pipe(cleanCSS({
            compatibility: 'ie8'
        }))
        .pipe(rename({
            suffix: '.min'
        }))
        .pipe(gulp.dest('css'))
        .pipe(browserSync.reload({
            stream: true
        }));

    var vCVS = gulp.src('vendor/*/css/*.css')
        .pipe(cleanCSS({
            compatibility: 'ie8'
        }))
        .pipe(rename({
            suffix: '.min'
        }))
        .pipe(gulp.dest('css'))
        .pipe(browserSync.reload({
            stream: true
        }));

    return merge(vCBP, vCVS);
});

// Minify JS
gulp.task('minify-js', function () {
    var vCBPjs = gulp.src('app/js/*.js')
        .pipe(uglify())
        .pipe(header(banner, {
            pkg: pkg
        }))
        .pipe(rename({
            suffix: '.min'
        }))
        .pipe(gulp.dest('js'))
        .pipe(browserSync.reload({
            stream: true
        }));

    var vCVendorjs = gulp.src('vendor/*/scripts/*.js')
        .pipe(uglify())
        .pipe(header(banner, {
            pkg: pkg
        }))
        .pipe(rename({
            suffix: '.min'
        }))
        .pipe(gulp.dest('js'))
        .pipe(browserSync.reload({
            stream: true
        }));

    return merge(vCBPjs, vCVendorjs);
});

// Copy Bootstrap core files from node_modules to vendor directory
gulp.task('bootstrap', function () {
    return gulp.src(['node_modules/bootstrap/dist/**/*', '!**/npm.js', '!**/bootstrap-theme.*', '!**/*.map'])
        .pipe(gulp.dest('vendor/bootstrap'));
});

// Copy jQuery core files from node_modules to vendor directory
gulp.task('jquery', function () {
    return gulp.src(['node_modules/jquery/dist/jquery.js', 'node_modules/jquery/dist/jquery.min.js'])
        .pipe(gulp.dest('vendor/jquery'));
});

// Copy Magnific Popup core files from node_modules to vendor directory
gulp.task('magnific-popup', function () {
    return gulp.src(['node_modules/magnific-popup/dist/*'])
        .pipe(gulp.dest('vendor/magnific-popup'));
});

// Copy ScrollReveal JS core JavaScript files from node_modules
gulp.task('scrollreveal', function () {
    return gulp.src(['node_modules/scrollreveal/dist/*.js'])
        .pipe(gulp.dest('vendor/scrollreveal'));
});

// Copy Font Awesome core files from node_modules to vendor directory
gulp.task('fontawesome', function () {
    return gulp.src([
            'node_modules/font-awesome/**',
            '!node_modules/font-awesome/**/*.map',
            '!node_modules/font-awesome/.npmignore',
            '!node_modules/font-awesome/*.txt',
            '!node_modules/font-awesome/*.md',
            '!node_modules/font-awesome/*.json'
        ])
        .pipe(gulp.dest('vendor/font-awesome'));
});

// Copy all dependencies from node_modules
gulp.task('copy', ['bootstrap', 'jquery', 'fontawesome', 'magnific-popup', 'scrollreveal']);

// Create browserSync task for Docs
gulp.task('syncDocs', function () {
    browserSync.init({
        server: {
            baseDir: 'docs/'
        }
    });
});

// Watch Task that watches for HTML/JS changes in Docs folder and reloads BrowserSync
gulp.task('docs', ['syncDocs'], function () {
    // Reloads the browser whenever HTML or JS files change
    gulp.watch('*.html', syncDocs.reload);
    gulp.watch('assets/*.js', syncDocs.reload);
    gulp.watch('assets/*.css', syncDocs.reload);
}); 
```

Wowza，356 行(包括评论)只是为了建立一个单页微型网站。
这是怎么回事？查看每个部分中的评论，以获得一个想法。

## brunch . io 区别

相比较而言，早午餐比较简单。Brunch 编译、连接和(可选地)缩小您的脚本和样式。它还可以将 JavaScript 文件打包到 AMD 或 CommonJS 模块中。Brunch 自动以正确的顺序将插件应用到正确的文件上——因此，有了正确的插件，一个`.coffee`文件将被转换成一个`.js`文件，然后被缩小，不需要显式的设置。要了解更多信息，请查看 brunch.io 文档。

早午餐有几个有助于保持简单的惯例，但你不必全部遵守。

首先，Brunch 要求您指定一个名为`assets`的文件夹，它被直接复制到您的输出文件夹中，没有任何修改。

其次，大多数 Brunch 项目有两个独立的 JavaScript 文件- `app.js`，其中包含您的代码，和`vendor.js`，用于所有外部库，包括 bower 包。这允许您将文件打包到模块中，而不影响外部库。

### 利用早午餐

早午餐命令有两个主要命令- `brunch build --production`和`brunch start`。

Build 运行早午餐编译过程并立即退出，相反，start 编译所有内容，然后等待对任何文件的更改，然后立即编译并更新文件。

与 Grunt 或 Gulp with plugins 不同，Brunch 会缓存您的文件，因此在初始编译之后，start 命令会非常快。

默认情况下，缩小是禁用的。Brunch 有一个`--production`标志，它可以缩小输出文件，删除源地图并禁用自动重载插件。

好的，在 Gulp.js 文件中有很多我们需要替换的东西。

为了做到这些，这里是我们的`brunch-config.js`看起来的样子，

```
module.exports = {
  files: {
    javascripts: {
      joinTo: {
        'vendor.js': [
          'vendor/jquery/1.11.3/jquery.min.js', // include specific file
          'vendor/jquery/mobile/touch/1.4.5/jquery.mobile.touch.min.js', // include specific file
          'vendor/bootstrap/3.3.5/bootstrap.min.js', // include specific file
          'vendor/global/js/custom.js', // include specific file
        ],
        'app.js': /^app/ // all code from 'app/'
      },
    },
    stylesheets: {
      joinTo: {
        'vendor.css': /^vendor/, //all code from 'vendor/'
        'app.css': /^app/ // all code from 'app/'
      }
    }
  },
  plugins: {
    cssnano: {
      autoprefixer: {
        add: true
      },
    }
  },
  swPrecache: {
    swFileName: 'sw.js',
    'autorequire': false,
    options: {
      staticFileGlobs: ['public/**/*.*'],
      stripPrefix: 'public/',
    }
  }
} 
```

但是等等，图像缩小在哪里，那个 swPrecache 是什么？项目中已经安装了 Image min-bruch 包，图像缩小已经完成。

需要把你的应用变成一个进步的网络应用？添加 swprecache-brunch 来自动生成您的`service-worker.js`和离线缓存资产。

### 使用节点模块

从早午餐 0.2.3 开始，默认启用 NPM 集成，因此没有额外的设置！只需像平常一样，在你的应用程序中需要你的前端包，Brunch 就会解决剩下的问题！通常，有一个打包早午餐，可以满足您的需求，自动配置开箱即用的项目。

## Package.json

为了突出早午餐的易用性，这里有一个`package.json`依赖部分来展示我的意思。一旦你安装了一个`-brunch`包，通常你需要做的就是让它工作！

```
 "devDependencies":  {  "auto-reload-brunch":  "^2",  "babel-brunch":  "~6.0",  "babel-preset-latest":  "^6",  "brunch":  "^2.10.17",  "cssnano-brunch":  "^1.1.8",  "imagemin-brunch":  "^1.1.0",  "sw-precache-brunch":  "^2.0.1",  "uglify-js-brunch":  "^2"  },  "dependencies":  {}  } 
```

#### 在一饮而尽的辩解中...

为 Gulp 辩护，上面的 gulp.js 文件相当粗糙，有许多重复的代码段&冗长的注释可以很容易地被删除并重构为更简洁的函数，然后在需要时调用。

我明白了。

但是，部分原因是，我仍然只需要 40 行或更少的代码就可以达到我想要的结果，而不是必须进行大规模的重构工作。如果这不是我自己的项目，而是我采用的另一个开发人员的项目，并且看到了那个巨大的 Gulp 文件，并被告知要“做得更好”，那该怎么办？

我确信`brunch-config.js`中的这 40 行比我考虑如何重构上面的代码花费的时间还要少。

尽管如此，正如我们前面提到的，通过对项目的设置做一些基本的假设，Brunch 极大地简化了设置过程。不幸的是，如果你的项目非常独特，那么尝试定制早午餐来配合你已有的工作可能会非常令人头疼。

反过来说，Gulp 在这里真的大放异彩。Gulp 的配置文件肯定比 Brunch 的要求更高，但它提供了几乎无限的灵活性。您可以通过 Gulp API 和 CLI 文档中提供的许多选项来控制任务运行器的各个方面。当然，那些 2800+的插件也没什么坏处...

* * *

你们是如何处理一些看起来更简单的构建的？使用 Webpack 或其他什么工具可以用更少的代码来完成吗？让我知道！很想知道你们是如何建造管道的。

如果任何人有问题，请随时联系我们！

特色图片来源:Oomph Inc - 2017