# Node.js 任务执行人:他们适合你吗？

> 原文：<https://dev.to/bnevilleoneill/node-js-task-runners-are-they-right-for-you-1ml0>

[![](img/27f50d1872c85f5584d14e959824be19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mQJorY5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJw9V__6jYhm2amP74D_0lw.png)

这篇文章将涵盖以下主题:

*   Node.js 背后的历史和哲学
*   为什么为 Node.js 开发任务运行器
*   受欢迎的任务执行者采用的不同方法
*   bash 如何作为一种更简单的替代方法

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### node . js 简史

JavaScript 的工具生态系统令人难以置信。你很难找到任何其他语言有如此多的工具或如此多的不同用户参与这个工具。从最初在 [10 天](https://www.computer.org/csdl/mags/co/2012/02/mco2012020007.html)内设计的一门语言的卑微开始，到它的 [C10K](http://blog.caustik.com/2012/08/19/node-js-w1m-concurrent-connections/) 在服务器领域获得立足点，你不会找到另一门语言像这门语言一样具有可塑性。

流行的 JavaScript 服务器端实现 Node.js 于 2009 年首次推出。这个平台几乎在一夜之间，让前端开发人员迅速成为可行的后端开发人员，解除了前端团队的所有障碍。它的成功保证了一个可以轻松分发源代码的工具，在 2010 年，这个需求被 npm 满足了。

Node.js 被誉为快速、易用，也许是最吸引人的简单。它开始用电话联系其他平台的用户。PHP 就是这样一个平台——一种用来生成动态网站的语言。PHP 可能随时都有成千上万的全局函数可用，并且需要一堆配置文件。

Node.js 允许开发人员迁移到该平台，重新开始。由于太新，它还没有开发出其他语言的“包含电池”框架。Node.js 的指导原则之一是保持核心简单。你找不到用于连接 MySQL、生成 UUID 或计算 Levenshtein 距离的内置工具。

JavaScript 语言也在发生变化。由于用户域“多填充”，一些特性是向后兼容的。但是，为了让语言进步，它必须偶尔添加新的语法。开发人员渴望新的语法，但旧的浏览器是现实，这导致了 transpilers 的发展。

由于代码是用 web 的通用语言 JavaScript 编写的，Node.js 的简单性最终变得不那么重要了。Node.js 作为将前端资产从一种表示(如 ES7 或 SASS)转换为另一种表示(如 ES5 或 CSS)的工具，获得了越来越多的关注。不过，只有一个条件。JavaScript 工程师通常希望继续编写 JavaScript。这导致了任务运行器的开发:专门用于运行其他工具的 Node.js 工具。

### 任务跑者的崛起

构建一个网站基本上需要三种技术，每一种都由浏览器直接使用。第一个是 HTML，控制网页的结构。第二个是 CSS，控制网页的外观。最后，我们有 JavaScript，用于网站交互性编程。

对于简单的网站或小型团队来说，直接使用这些语言通常是一个不错的方法。然而，对于复杂的网站，或者由工程师团队构建的网站，每个人都有自己的专长，直接使用这些基本语言可能会有所欠缺。例如，考虑当公司网站的品牌改变时。几个不同样式文件中使用的十六进制颜色代码可能需要更改。对于原始的 CSS，这个操作需要跨几个团队的协调变化。使用 SASS，这样的更改可以在一行代码中完成。类似的概念也适用于 HTML，我们使用模板工具如 Mustache 或虚拟 DOM 如 React 生成标记。它们也适用于 JavaScript，工程师可以使用 async/await ES2017 语法编写代码，然后通过回调转换成复杂的 ES5 switch 语句。

此时，我们可能有一个网站需要将 SASS 编译成 CSS，ES2015 代码需要转换成 ES5，React/JSX 模板需要转换成原始 JavaScript。其他操作也是有益的，例如缩小编译的代码和将 PNG 图像压缩成它们的最小表示。在构建网站时，这些任务中的每一项都需要按照特定的顺序运行。根据特定网站构建的环境——例如它是为开发/调试目的或生产而构建的——一些任务必须被改变或完全跳过。这种复杂性激发了任务运行工具的产生。

两个受欢迎的 Node.js 任务运行者前来救援。第一个是 [Grunt](https://www.npmjs.com/package/grunt) ，第一次提交是在 2011 年 9 月。这个工具采用一种强制性的方法来配置不同的任务，构建深度嵌套的对象并调用一些方法。第二个是[大口](https://www.npmjs.com/package/gulp)，2013 年 7 月首次提交。这个工具采用了一种不同的方法，本质上更具功能性，将一个函数的输出通过管道传输到另一个函数的输入中，将结果流式传输。

让我们考虑一个简单的 web 应用程序，我们希望使用这些技术的子集来模拟它。这个应用程序依赖于多个 SASS 和 JS 文件。我们希望将 SASS 文件转换成 CSS，并连接结果。为了简洁起见，我们也将简单地将 JS 文件连接在一起，并假设[模块模式](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html)，而不是使用 CommonJS require 语句。让我们看看使用这些不同的任务运行器时这样的配置会是什么样子:

#### gruntfile.js

这种方法需要安装以下模块:`grunt`、`grunt-contrib-sass`、`grunt-contrib-concat`和`grunt-contrib-clean`。使用这种方法，我们可以运行`grunt style`、`grunt script`或`grunt build`来完成这两项工作。

```
const grunt = require('grunt');

grunt.initConfig({
  sass: {
    dist: {
      files: [{
          expand: true,
          cwd: './src/styles',
          src: ['*.scss'],
          dest: './temp',
          ext: '.css'
      }]
    }
  },
  concat: {
    styles: {
      src: ['./temp/*.css'],
      dest: 'public/dist.css',
    },
    scripts: {
      src: ['./src/scripts/*.js'],
      dest: 'public/dist.js',
    }
  },
  clean: {
    temp: ['./temp/*.css']
  }
});

grunt.loadNpmTasks('grunt-contrib-sass');
grunt.loadNpmTasks('grunt-contrib-concat');
grunt.loadNpmTasks('grunt-contrib-clean');

grunt.registerTask('style', ['sass', 'concat:styles', 'clean:temp']);
grunt.registerTask('script', ['concat:scripts']);

grunt.registerTask('build', ['style', 'script']); 
```

Enter fullscreen mode Exit fullscreen mode

#### gulpfile.js

前一个 Gulp 示例的等效 Gulp 版本如下。这要求我们安装了`gulp`、`gulp-sass`、`gulp-concat`和`node-sass`。使用这种方法，我们可以运行`gulp style`、`gulp script`或`gulp build`来完成这两项工作。

```
const gulp = require('gulp');
const sass = require('gulp-sass');
const concat = require('gulp-concat');
sass.compiler = require('node-sass');

gulp.task('style', function () {
  return gulp.src('./src/styles/*.scss')
    .pipe(sass().on('error', sass.logError))
    .pipe(concat('dist.css'))
    .pipe(gulp.dest('./public/'));
});

gulp.task('script', function () {
  return gulp.src('./src/scripts/*.js')
    .pipe(concat('dist.js'))
    .pipe(gulp.dest('./public/'));
});

gulp.task('build', gulp.series('style', 'script')); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，Gulp 的例子比 Grunt 的例子更简洁。

从哲学上讲，这两个工具采用不同的方法来实现可运行的任务，但最终它们允许您做类似的事情。再说一次，Grunt 是在 Gulp 之前引入的。他们在一生中都有着不相上下的受欢迎程度:

<figure>[![](img/c0d309e74d7cd548a7bc1310a8f72335.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZpFZEuId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AylqPvIGcj54VxMlmzcf2Uw.png) 

<figcaption>谷歌趋势:咕噜 vs 咕噜</figcaption>

</figure>

这两个项目都是高度模块化的，允许开发人员创建专门的插件。这些插件允许外部工具，如 eslint 或 *sass* 或 browserify，轻松集成到任务运行器中。我们在前面看到的代码中实际上有一个这样的例子:流行的 SASS 工具既有 grunt-contrib-sass 模块，也有 gulp-sass 模块。

这两个工具可能本质上已经“搞定”了。在撰写本文时，Grunts 的最后一次发布是在八个月前，Gulps 的最后一次发布是在一年前。“done”是什么意思，这个词在 JavaScript 社区中从字面上和比喻上都是四个字母的单词？那么，在这种情况下，这可能意味着核心任务运行模块做他们需要做的一切，任何额外的功能都可以通过插件添加。

Webpack 是一个类似于 Grunt 和 Gulp 的工具，它也可以用来获取源文件，以各种方式组合它们，并将它们输出到单个文件中。然而，它与 Grunt 和 Gulp 有很大的不同，因此将它与 Grunt 和 Gulp 进行比较是不公平的。它主要是一个基于需求和依赖关系层次的 JavaScript 转换工具。它绝对值得一提，因为它的受欢迎程度已经超过了 Grunt 和 Gulp。

第一次提交 Webpack 发生在 2012 年 3 月，在第一次提交 Grunt 和 Gulp 之间。在撰写本文时，它仍处于非常活跃的开发阶段，它的最后一篇文章是在几个小时前发表的。Grunt 和 Gulp 帮助执行许多类型的通用任务，而 Webpack 对构建前端资产更感兴趣。

Webpack 也可以使用名为 webpack.config.js 的文件以类似于 Grunt 和 Gulp 的方式进行配置。它也是高度模块化的，我们可以使用 sass-loader 等插件来实现类似的结果。它与前面提到的工具有自己的哲学差异。但是，在基于 Node.js 的流程最终转换资产并通过 JavaScript 文件进行配置的意义上，它仍然是相似的。

### 任务运行器替代品

对于最复杂的构建系统，使用 Node.js 任务运行器是完全有意义的。有一个临界点，构建过程会变得如此复杂，以至于用编写应用程序之外的语言来维护它是没有意义的。然而，对于许多项目来说，这些任务运行者最终是多余的。它们是我们需要添加到项目中并保持更新的*附加*工具。任务运行程序的复杂性很容易被忽略，因为它们很容易通过 npm install 获得。

在前面的例子中，我们看到我们需要 32MB 的模块来使用 Grunt，需要 40MB 的空间来使用 Gulp。这些简单的构建命令——连接两个 JavaScript 文件并编译/连接两个 SASS 文件——用 Grunt 花费了**250 毫秒**,用 Gulp 花费了**370 毫秒**。

Gulp 使用的方法是从一个操作中获取输出，然后通过管道将它们传递给另一个操作，这听起来应该很熟悉。同样的管道系统也可以通过命令行使用，我们可以通过使用 bash 脚本来实现自动化。macOS 和 Linux 电脑的用户已经可以使用这样的脚本特性了( [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10) 可以帮助 Windows)。

我们可以使用以下三个 bash 脚本来实现 Grunt 和 Gulp 示例所做的事情:

#### 外壳脚本

```
### style.sh #!/usr/bin/env bash cat ./src/styles/*.scss | sass > ./public/dist.css

### script.sh
#!/usr/bin/env bash
cat ./src/scripts/*.js > ./public/dist.js

### build.sh
#!/usr/bin/env bash
./style.sh
./script.sh 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用这种方法时，我们只需要一个 **2.5MB** sass 二进制文件(可执行)。执行整个构建操作的时间也减少了:在我的机器上，这个操作只需要**25 毫秒**。这意味着我们使用了大约 1/12 的磁盘空间，运行速度提高了 10 倍。对于更复杂的构建步骤，差异可能会更大。

#### package.json

它们甚至可以内嵌在 package.json 文件中。然后，可以通过 npm 运行风格、npm 运行脚本和 npm 运行构建来执行命令。

```
{
  "scripts": {
    "style": "cat ./src/styles/*.scss | sass > ./public/dist.css",
    "script": "cat ./src/scripts/*.js > ./public/dist.js",
    "build": "npm run style && npm run script"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，这是一种权衡。最大的区别是 bash 是一种 shell 脚本语言，语法完全不同于 JavaScript。对于一些从事 JavaScript 项目的工程师来说，编写构建复杂应用程序所需的适当脚本可能很困难。

另一个缺点是，bash 脚本要求我们想要合并的每个操作都有某种可执行文件。对我们来说幸运的是他们通常是。Browserify ，一个解析 CommonJS 需求并连接输出的工具，提供了一个可执行文件。[通天塔](https://github.com/babel/babel)，这个通行的 transpiler，也提供了一个可执行程序。萨斯、莱斯、Coffeescript、JSX:这些工具都有可执行文件。如果没有可用的，我们可以自己写，但是，一旦我们达到这一点，我们可能要考虑使用任务运行器。

### 结论

我们机器的命令行脚本功能非常强大。人们很容易忽略它们，尤其是当我们在 JavaScript 这样的高级语言上花了这么多时间的时候。正如我们今天所看到的，它们通常足够强大，可以完成我们的许多前端资产构建任务，并且通常可以更快地完成。当您开始下一个项目时，考虑使用这些工具，并且只有当您达到 bash 脚本的限制时，才切换到像 task runner 这样的更重的解决方案。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

post [Node.js 任务跑者:他们适合你吗？](https://blog.logrocket.com/node-js-task-runners-are-they-right-for-you-bb29ea30b7fa/)最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。