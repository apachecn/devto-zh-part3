# NPM 快速提示

> 原文：<https://dev.to/codingcatdev/npm-quick-tips-3o6>

> 原帖:[https://ajonp.com/lessons/npm/](https://ajonp.com/lessons/npm/)

[https://www.youtube.com/embed/eWc0bg9KMQQ](https://www.youtube.com/embed/eWc0bg9KMQQ)

> 请注意，为了让您运行命令，您**必须**更新您的。bash_profile
> 然后你可以通过关闭并重启或者`exec bash`来重启终端。

```
export PATH="$PATH:/usr/local/bin/"
export PATH="/Users/ajonp/.npm-packages/bin/:$PATH" 
```

Enter fullscreen mode Exit fullscreen mode

新来的`npm`结帐[的 NodeSource 博客](https://nodesource.com/blog/an-absolute-beginners-guide-to-using-npm)

# NPM 全局安装失败

在运行 npm install -g 之后，有没有遇到过可怕的 EACCES 错误？你不是一个人！

您会注意到 npm 正在尝试将其软件包安装到以下路径:

缺少对/usr/local/lib/node_modules 的写访问权限

我们需要将此更改为您有权更新的更好的路径。

## [T1。npmrc 更新](#npmrc-update)

使用 vim 或 nano 更新您的本地。npmrc 文件

```
vim ~/.npmrc 
```

Enter fullscreen mode Exit fullscreen mode

用下面的内容更新此文件，这将告诉 npm 在本地将软件包安装到。npm 包

```
prefix=${HOME}/.npm-packages 
```

Enter fullscreen mode Exit fullscreen mode

## NPM 全球安装成功

一旦你改变了。npmrc 文件，您将开始安装软件包到~/。npm 包。不再有问题

[![npm install success](img/9c6a65f8e231797bcf05abb2490be718.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wj-zs7Fc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1543984849/ajonp-ajonp-com/3-lesson-npm/npm-packages.jpg)

# NPM 初始化默认值

如果您经常使用 npm 来启动项目，您会希望默认一些创作项目。基本语法是`npm config set init.*`

> 如果您在不同的目录中使用 npm 配置集进行更新，请不要紧张，这仍然会在~/中进行更新。npmrc

```
npm config set init.author.name "Alex Patterson"
npm config set init.author.email "developer@ajonp.com"
npm config set init.author.url "https://ajonp.com/"
npm config set init.license "MIT"
npm config set init.version "0.0.1" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的全部。npmrc 将看起来像:

```
prefix=/Users/ajonp/.npm-packages
init.author.name=Alex Patterson
init.author.email=developer@ajonp.com
init.author.url=https://ajonp.com/
init.license=MIT
init.version=0.0.1 
```

Enter fullscreen mode Exit fullscreen mode

执行 npm init 只需按 enter 键就会产生以下结果。

```
{  "name":  "npm-sample",  "version":  "0.0.1",  "description":  "Sample NPM",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "author":  "Alex Patterson <developer@ajonp.com> (https://ajonp.com/)",  "license":  "MIT"  } 
```

Enter fullscreen mode Exit fullscreen mode

# 设置 NPM 注册表

在工作中，我们有一个 VSTS(又名 Visual Studio，又名 DevOps)私有 npm 注册表，因此使用`npm config set registry "https://<company>.pkgs.visualstudio.com/_packaging/<company>/npm/registry/"`变得很重要

这将导致更新。带
的 npmrc

```
registry=https://<company>.pkgs.visualstudio.com/_packaging/<company>/npm/registry/ 
```

Enter fullscreen mode Exit fullscreen mode

有一篇关于建立 VSTS 国家预防机制的文章。

# 设置 NPM loglevel

可能我最喜欢的设置是`npm config set loglevel="warn"`，它允许我看到任何输出，并且只显示警告。
在 [npm 文档](https://docs.npmjs.com/misc/config)中有几个不同的级别，你可以通过运行类似`npm i -g ionic -s`
的东西来执行其中的任何一个

```
-s, --silent: --loglevel silent
-q, --quiet: --loglevel warn
-d: --loglevel info
-dd, --verbose: --loglevel verbose
-ddd: --loglevel silly 
```

Enter fullscreen mode Exit fullscreen mode