# 用 Yargs 构建 CLI

> 原文：<https://dev.to/christopherkade/building-a-cli-with-yargs-ip8>

上周，我在我的[网站](https://christopherkade.com/yargs-cli)上发表了这篇文章，之前我创建了一个名为 [randogs](https://www.npmjs.com/package/@christopherkade/randog) 的革命性 CLI，可以在你的终端上显示狗狗(这可能是 20 世纪最伟大的发明)。我想我也应该把它贴在这里，以得到你的一些反馈，因为它总是受到赞赏，我希望你喜欢！

* * *

Yargs 是一个很棒的构建命令行应用程序的库，简单地说，它将使创建在控制台上运行的应用程序的过程变得轻而易举。什么能让它变得更好？它是以海盗为主题的(它被称为 YARgs you guys)，使它正式成为有史以来最好的工具。

你可能知道其他 cli，如 [vue-cli](https://cli.vuejs.org/) 来轻松建立一个 Vue.js 项目或 [create-react-app](https://facebook.github.io/create-react-app/) ，所以这个概念应该为大多数人所熟悉。

在今天的文章中，我们将从头到尾创建一个基本的 CLI，涵盖以下几点:

*   [项目设置](#setup)
*   [创建基本 CLI](#cli)
*   [在 NPM 部署我们的 CLI](#deploy)

## 项目设置

设置项目非常简单，从做以下事情开始:

```
mkdir yargs-project
cd yargs-project
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经创建了项目的文件夹，并启动了包含元数据的`package.json`文件。

以下是创建的文件:

`package.json` :

```
{  "name":  "yargs-example",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "keywords":  [],  "author":  "",  "license":  "ISC"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们需要对这个文件做一些调整，因为我们正在创建一个 CLI。它现在应该是这样的:

```
{  "name":  "yargs-example",  "version":  "1.0.0",  "description":  "A simple Yargs CLI",  "bin":  {  "yargs-example":  "./yargs-example.js"  },  "keywords":  ["cli"],  "preferGlobal":  true,  "author":  "Christopher Kade",  "license":  "ISC"  } 
```

Enter fullscreen mode Exit fullscreen mode

以下是需要注意的重要变化:

*   我们添加了一个`bin`值，它将我们稍后创建的条目文件映射到它的可执行名称(您可以根据自己的喜好设置它)
*   我们已经将`preferGlobal`设置为 true，这意味着我们的包更喜欢在全球范围内安装(例如通过`npm install -g`)

其他的调整包括改变`description`，移除未使用的`scripts`，增加一个`author`名字等等。

在我们开始编写 CLI 代码之前，我们需要安装`yargs`，像这样做:

`npm install yargs`

我们开始吧。

## 创建基本 CLI

Yargs 使得解析命令行参数变得非常容易，许多示例项目可以在[这里](https://github.com/yargs/yargs/blob/master/docs/examples.md)找到。

我们将创建一个基本的 CLI，它接受一个文件作为参数，并计算它的行数。

为此，首先创建我们的主脚本文件。

`touch yargs-example.js`

并填入以下内容:

```
#!/usr/bin/env node const argv = require('yargs')
  .usage('Usage: $0 <command> [options]')
  .help('h')
  .alias('h', 'help').argv 
```

Enter fullscreen mode Exit fullscreen mode

让我们一行一行地介绍所有内容:

1 - `#!/usr/bin/env node`是一个 shebang 行的实例，它告诉我们的系统使用什么解释器来执行文件。

2 - `const argv = require("yargs")`导入`yargs`包。

3 - `.usage('Usage: $0 <command> [options]')`设置调用`--help`命令时将显示的 CLI 使用信息。

4 - `.help('h')`将帮助命令绑定到选项`h`。

5 - `.alias('h', 'help')`为选项`-h`创建一个别名，即`--help`。

如您所见，这第一步非常简单，`yargs`语法非常直观。

接下来我们将添加`count`命令。

只需将以下几行添加到您已经存在的 CLI 中:

```
.command("count", "Count the lines in a file")
.example("$0 count -f foo.js",
  "count the lines in the given file") 
```

Enter fullscreen mode Exit fullscreen mode

让我们再一次一行一行地回顾它们。

1 - `.command("count", "Count the lines in a file")`创建一个名为`count`的新命令，并设置描述。

2 - `.example("$0 count -f foo.js", "count the lines in the given file")`创建一个带有描述的例子，当用户调用`--help`选项或弄乱命令时，就会显示这个例子。

这些都很好，但是现在运行`node yargs-example.js count`并没有太多作用，接下来我们将需要一个文件名，并通过计数和显示它的行数来完成 CLI。

为此，添加以下内容:

```
.alias("f", "file")
.nargs("f", 1)
.describe("f", "Load a file")
.demandOption(["f"]) 
```

Enter fullscreen mode Exit fullscreen mode

您的文件最终应该是这样的:

```
#!/usr/bin/env node const argv = require('yargs')
  .usage('Usage: $0 <command> [options]')
  .command('count', 'Count the lines in a file')
  .example('$0 count -f foo.js', 'count the lines in the given file')
  .alias('f', 'file')
  .nargs('f', 1)
  .describe('f', 'Load a file')
  .demandOption(['f'])
  .help('h')
  .alias('h', 'help').argv 
```

Enter fullscreen mode Exit fullscreen mode

1 - `.alias("f", "file")`为`-f`选项创建别名`--file`。

2 - `.nargs("f", 1)`为该选项(文件名)设置一个参数的要求，否则显示`--help`菜单。

3 - `.describe("f", "Load a file")`添加选项的描述。

4 - `.demandOption(["f"])`因为我们需要一个文件名，所以我们需要选项`-f`。

最后，让我们像这样添加程序的逻辑:

```
const fs = require('fs')

// Create stream with the file
const s = fs.createReadStream(argv.file)

var lines = 0
s.on('data', buf => {
  // Get the number of lines
  lines += buf.toString().match(/\n/g).length
})

s.on('end', () => {
  // Display the number of lines
  console.log(lines)
}) 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们来测试一下。

```
$ node line-count.js -f package.json
21 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们一直这样运行程序，但是如果我们试图通过直接调用它来运行它，我们会得到一个错误。

```
$ line-count count -f package.json
zsh: command not found: line-count 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过使用`npm link`命令全局注册二进制文件(我们之前在`package.json`中定义为`bin`)来解决这个问题。

在你的应用程序的目录下，运行以下:

`npm link`

万岁！你现在可以像这样在本地运行你的脚本:

`yargs-example count -f package.json`

## 将我们的 CLI 部署到 NPM

在部署它之前，我们需要向我们的`package.json`添加一些信息。

```
"homepage":  "YOUR GITHUB REPO OR SITE HERE",  "repository":  {  "type":  "git",  "url":  "git+YOUR GITHUB REPOSITORY HERE"  },  "engines":  {  "node":  ">=8"  }, 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记用你自己的信息替换`homepage`和`repository`信息，这将允许 npmjs.com 网站填满你未来项目的页面。

`engine`值简单地定义了您的项目应该工作的节点的最低版本。将它设置为您的项目所需要的(取决于您最终可能会使用的 JS 特性，比如`async/await`)。

以下是接下来的步骤:

*   在 npmjs.com 上创建帐户
*   运行`npm login`命令并输入您的信息
*   运行`npm publish`命令，这将在几分钟内自动发布它

就是这样！如果您希望将来更新您的项目，您需要在`package.json`文件中更改它的版本号，然后再次运行发布命令。

现在，您已经发布了自己的 NPM 包，社区也可以访问了，恭喜您！

如果有任何问题，欢迎在 twitter [@christo_kade](https://twitter.com/christo_kade) 上提问。

感谢您的阅读:-)