# 静态站点博客帖子生成器

> 原文：<https://dev.to/therealdanvega/static-site-blog-post-generator-3ma6>

如果你正在使用像[盖茨比](https://www.gatsbyjs.org/)或 [Gridsome](https://gridsome.org/) 这样的东西作为你的博客平台，没有用户界面来创建一篇新的博客文章。这个过程通常从创建一个目录或一系列目录开始，这取决于你的文章格式，然后创建一个新的 markdown 文件。

从这里开始，你必须添加一些描述你的文章的内容。这通常是针对你的博客需求的，但是你也可以有标题、段落、作者、日期等等。

这变得乏味，更糟糕的是一次又一次的复制/粘贴项目，这是我不喜欢做的。事实上，任何时候我发现自己复制/粘贴了一些东西，可能是时候找到解决这个问题的方法了。

在这篇文章中，我将带你浏览我写的博客文章生成器。这个脚本已经有过几次迭代，我肯定从其他做过类似事情的人那里学到了一些技巧。

## 创建&初始化脚本

你需要决定的第一件事是这个脚本要去哪里。答案没有对错，所以对我来说，我只是在根目录下创建了一个名为 scripts 的文件夹。我想这可能是我可能需要的随机脚本的地方，如果我以后找到一个更好的地方放它们，我可以重构。顺便说一句，这是我每次写代码时都会做的事情，找到一种快速的方法来完成它，让它工作，并在以后变得非常漂亮。

我要做的第一件事是在我的脚本文件夹中创建一个名为`newpost.js`的脚本。接下来，我们需要知道如何构建这个脚本。在某些情况下，我们可以把它从上到下写下来，但是在这个例子中，这是行不通的。

将代码包装在一个函数中并执行该函数是很常见的，我们有几种方法可以做到这一点。我们可以只写一个包含我们所有逻辑的普通函数，然后在脚本的末尾调用那个函数来开始。

```
function newPost() {
  console.log("create new post...");
}
newPost(); 
```

如果你要做的只是调用函数，有一个更好的方法。你可以写一个所谓的自执行函数，也称为立即调用函数表达式或 IIFE。为了实现这个目标

```
(function newPost() {
  console.log("create new post...");
})(); 
```

你也可以用箭头函数
来写

```
(() => {
  console.log("create new post...");
})(); 
```

就像任何普通函数一样，如果你要执行一个异步任务，你可以使用`async`关键字。在这种情况下，我们将引入一个库来帮助我们编写命令行应用程序，因此我们将从以下内容开始。

```
(async () => {
  console.log("create new post...");
})(); 
```

在测试之前，你需要添加一个新的脚本到你的`package.json`

```
"newpost": "node ./scripts/newpost.js" 
```

在这一点上，我会给脚本一个快速测试，只是为了确保一切都按预期工作。

```
npm run newpost 
```

## 接受用户输入

既然您已经准备好了脚本，是时候开始构建一些功能了。你需要做的第一件事是询问一些关于新职位的细节。这显然会因人而异，取决于你的需求，但这里是我想要的数据和我可以推断的数据。

*   标题
*   引用
*   标签

这些是我可以根据用户在上面输入的内容或脚本运行的时间来确定的项目列表。

*   鼻涕虫
*   日期
*   作者

这就是我开始工作所需要的一切。正如我之前所说的，这可能对你来说是不同的，但是你可以相应地调整。

### 询问者

为了帮助询问用户输入，我们将[安装包询问器](https://www.npmjs.com/package/inquirer)。Inquirer 是一组常见的交互式命令行用户界面。询问者应简化以下过程:

*   提供*错误反馈*
*   *提问*
*   *解析*输入
*   *验证*个答案
*   管理*分级提示*

要开始，您可以通过运行以下命令将它安装为一个开发依赖项:

```
npm install -D inquirer 
```

并在您的脚本中要求它

```
const inquirer = require("inquirer"); 
```

这个包能做的比我们用它做的要多得多，所以如果你有机会[看看文档](https://www.npmjs.com/package/inquirer)。你需要做的第一件事是询问过程参数。

```
const args = process.argv; 
```

> process.argv 属性返回一个数组，该数组包含启动 Node.js 进程时传递的命令行参数。第一个元素将是 process.execPath。如果需要访问 argv[0]的原始值，请参见 process.argv0。第二个元素是正在执行的 JavaScript 文件的路径。其余的元素将是任何附加的命令行参数。

如果你愿意，你可以检查用户提供的参数是否存在，并接受这些参数，但在这个例子中，我要说的是，只要没有自定义参数，我们就向用户询问一些数据。

```
if (args.length < 3) {
  const { title, excerpt, tags } = await inquirer.prompt([
    {
      type: "input",
      name: "title",
      message: "Post Title:"
    },
    {
      type: "input",
      name: "excerpt",
      message: "Post Excerpt:"
    },
    {
      type: "input",
      name: "tags",
      message: "Tags (comma separated):"
    }
  ]);
} else {
  log(error("Please don't provide any arguments to the new post generator"));
} 
```

我们稍后将讨论日志行，但现在让我们把重点放在 inquirer 上。当我们设置脚本时，我说过我们需要将自执行函数标记为 async，这就是原因。返回一个承诺，所以我们将在这里使用 await。

我们要求用户提供 3 种不同的数据

*   标题
*   引用
*   标签

我们可以只创建一个变量来保存响应，但是我们把响应分解成了 3 个变量。

```
const { title, excerpt, tags } = ... 
```

提供给 prompt 方法的数组参数中的每个对象都是一个问题。在我们的例子中，我们要求简单的输入，定义问题的名称和消息应该向用户显示什么。同样，这些可能会变得更加复杂，所以如果您有更具体的需求，请查看文档。

现在我们已经从用户那里得到了答案，我们可以用这些来构建我们的新帖子。

## 创建帖子目录

在我们开始创建任何文件夹或文件之前，你需要再做一些设置。

### 贴鼻涕虫

现在我有了文章的标题，我需要创建一个 slug。鼻涕虫是我的标题的 URL 友好版本，有助于搜索引擎优化。如果我的文章的标题是“我的第一篇文章”，那么 slug 应该是“我的第一篇文章”。

在这个简单的例子中，我们可能可以自己处理，但这会变得非常复杂。为此，我将安装一个名为 slugify 的[包，需要它，然后创建一个 slug。](https://www.npmjs.com/package/slugify) 

```
const slugify = require("slugify");

const slug = slugify(title); 
```

### 文件夹&网址格式

我的每篇博文都使用了以下格式

```
https://www.danvega.dev/{year}/{month}/{day}/{slug} 
```

到目前为止，我们有子弹，但现在我需要提取一些日期的部分。因为我们现在正在运行生成器，所以我假设我们今天要发布这个，并以此作为我们约会的基础。你可能会认为这更容易，但是处理日期在任何语言中都是不容易的事情之一。

```
const createdOn = new Date();
const year = createdOn.getFullYear();
const month = `${createdOn.getMonth() + 1 < 10 ? "0" : ""}${createdOn.getMonth() + 1}`;
const day = `${createdOn.getDate() < 10 ? "0" : ""}${createdOn.getDate()}`; 
```

现在我们已经有了日期部分，我们可以创建一个名为`blogPostFolder`的变量，它将是创建新的降价文件的文件夹的路径。

```
const blogPostFolder = `./blog/${year}/${month}/${day}`; 
```

最后，我将清理标签，并将它们转换为一个列表。

```
const tagsList = tags.split(",").map(t => t.trim()); 
```

## 创建文件&文件夹

现在你已经有了所有的变量，是时候开始创建一些文件和文件夹了。为此，您需要[文件系统模块](https://nodejs.org/api/fs.html)。

```
const fs = require("fs"); 
```

### 在节点中创建递归目录

我们已经为我们的博客文章文件夹位置创建了一个变量，所以让我们从这里开始。您要做的第一件事是检查它是否已经存在，因为如果它存在，我们就不需要创建它。这对我来说几乎是不可能的，因为对我来说，每周拿出 1 英镑已经够难的了，但是让我们谨慎一点，以防有一天我变得雄心勃勃。

```
if (!fs.existsSync(blogPostFolder)) {
  // create directory
} 
```

这是一个棘手的部分，可能会让一些人犯错，而且第一次就让我犯了错。如果你只是创建一个没有选项的目录 [mkdirSync](https://nodejs.org/api/fs.html#fs_fs_mkdirsync_path_options) 就可以了。我的意思是，假设您已经创建了文件夹`blog/2019/04/`，您只需要创建日期文件夹`24`就可以了。如果你需要递归地(超过 1 层深度)创建文件夹，你需要传递一个选项给`mkdirSync`方法。我[写了一篇文章](https://www.danvega.dev/blog/2019/02/20/node-recursive-directories)，如果你感兴趣的话，会更深入一点。

```
if (!fs.existsSync(blogPostFolder)) {
  fs.mkdirSync(blogPostFolder, {
    recursive: true
  });
} 
```

### 身前要紧

在每个 Markdown 文件中，我们使用一种叫做 front matter 的东西来定义博客文章。这些是 YAML 声明块
中的变量

```
---
key: value
--- 
```

为了帮助我们创建前台事务，我们将引入一个名为 [json-to-pretty-yaml](https://www.npmjs.com/package/json-to-pretty-yaml) 的包。

```
const jsToYaml = require("json-to-pretty-yaml");

const yaml = jsToYaml.stringify({
  slug,
  title,
  date: createdOn.toISOString(),
  published: false,
  excerpt: excerpt,
  author: "Dan Vega",
  tags: tagsList,
  cover: ""
}); 
```

### 降价销售

有了我们的前台，是时候创建我们的降价文件了。我将引入一个名为“更漂亮”的包来格式化我们的降价，让它，嗯，更漂亮☺️

```
const prettier = require("prettier");

const markdown = prettier.format(`---\n${yaml}\n---\n`, {
  parser: "markdown",
  singleQuote: true
}); 
```

现在您已经有了文件的内容，剩下要做的就是创建文件。您将再次使用文件系统模块，但这次您将使用`writeFileSync`方法。您将把这个文件写到您之前创建的 blog post 文件夹中，而 slug 将是文件扩展名为`md`的文件名。

```
fs.writeFileSync(`${blogPostFolder}/${slug}.md`, markdown);

log(success(`Post ${title} was created successfully`)); 
```

## 测井

为了给我的终端日志添加一些样式，我使用了一个名为 chalk 的[包。要在本地安装它，运行以下命令:](https://www.npmjs.com/package/chalk) 

```
npm install -D chalk 
```

然后将下面的变量声明添加到脚本的顶部。

```
const chalk = require("chalk");
const log = console.log;
const error = chalk.bold.red;
const success = chalk.bold.green.inverse; 
```

这允许我在想要记录错误或成功时编写下面的语句，并拥有一些时髦的日志语句。

```
log(success(`Post ${title} was created successfully`));
log(error("Please don't provide any arguments to the new post generator")); 
```

## 结论

这里的计划是向你展示如何创建你自己的博客文章生成器，但我希望你在这里学到更多东西。当你在构建这样的项目时，你发现你需要一些东西，你可以自己创造。

如果你写了类似的东西，或者为你的项目解决了一个问题，我很想听听。如果你想查看我的博文生成器的 src 以及我整个网站的代码[，你可以在这里查看](https://github.com/danvega/danvega-dev/blob/master/scripts/newpost.js)。

仅供参考-我用这个脚本创建了你正在阅读的帖子🤯

一如既往...

快乐编码
丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*