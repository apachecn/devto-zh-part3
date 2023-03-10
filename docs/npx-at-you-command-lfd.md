# npx，听你指挥

> 原文：<https://dev.to/andreasbergqvist/npx-at-you-command-lfd>

从 npm 的 5.2 版本(大约 2 年前发布)开始，你也可以收到命令`npx`。

使用`npx`，你可以为你的项目依赖项运行二进制文件(比如`npx webpack`或者`npx jest`)。

但是您也可以运行 npm 上任何可用包中的任何命令！这里有一些我最喜欢的！

```
npx http-server 
```

在当前文件夹中启动 web 服务器。如果你想快速测试某样东西，这很好。

```
npx create-react-app my-app 
```

使用 create-react-app，无需先全局安装。只需运行`npx`。

```
npx shx touch file # Create a file
npx shx mkdir folder # Create a folder 
```

`shx`命令允许您跨平台运行 shell 命令！如果您想要共享 Mac 和 Windows 用户都可能使用的命令片段，这将非常有用。

```
npx sort-package-json 
```

对 package.json 中的键进行排序(如果当前目录中有 package.json)。

```
npx speed-test 
```

测试您的互联网连接

```
npx typescript index.ts --out index.js 
```

如果你想检查当 TypeScript 把一个. ts 文件转换成一个. js 时的结果，请注意，这可能是针对更简单的文件，并且主要是如果你想研究它是如何工作的，因为你不使用配置文件。

```
npx terser index.js > index.min.js 
```

缩小 Javascript 文件。

```
npx ntl 
```

为您提供可用 NPM 脚本的交互式列表(如果您位于包含 package.json 的文件夹中)。

```
npx puppeteer-cli print https://itiden.se page.pdf
npx puppeteer-cli screenshot https://itiden.se page.png 
```

从 URL 创建 PDF 或截图。注意，当我运行这个命令时，Chromium 被下载，这使得它非常慢。但是如果你很快想要一个网页的 PDF 或截图，它可能会派上用场！

而且，如果您愿意，您可以设置自己的“卡片”在终端中显示。参见:
[https://dev.to/wuz/setting-up-a-npx-username-card-1pip](https://dev.to/wuz/setting-up-a-npx-username-card-1pip)

因此...

```
npx andreasbergqvist 
```

请随意评论更多可能派上用场的命令！