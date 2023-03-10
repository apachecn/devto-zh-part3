# WordPress 数据库取代简单的方法

> 原文：<https://dev.to/perlatsp/wordpress-database-replacing-the-easy-way-288k>

你好，
我发布了一个 NPM 包，一个小的 cli 工具，它可以为 WordPress 网站轻松地导出和替换 URL。您可以在 10 秒钟内导出和替换(取决于数据库的大小和您键入的速度)。

[![demo](img/ac46c07479d789a7d23a6a4572b77bf1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DizV_3HZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/perlatsp/devild/HEAD/demo.gif)

TLDR；
[NPM 链接](https://www.npmjs.com/package/@perlatsp/devild)T3[Github 链接](https://github.com/perlatsp/devild)

* * *

我开始的时候是一个供个人使用的项目，但后来我决定把它做成一个 NPM 包，供公众访问。我们都知道 WordPress 开发的惯例。

*   使用 Docker 容器开发本地
*   使用 git commit、git push(或者为老派中坚分子使用 FTP 上传)来部署
*   打开 phpmyadmin，找到数据库，导出数据库
*   在下载文件夹中找到文件(从`database (2).sql`或`database (3).sql`或`database (5).sql`中找到哪个是最新的，因为你删除了(4)
*   将本地 URL 替换为暂存/生产 URL，您可以通过以下方式完成:
    *   使用任何文本编辑器搜索/替换 URL 或
    *   使用插件来导入和替换数据库中的 URL
        *   安装插件
        *   配置插件
        *   运行搜索/替换并等待完成
        *   在项目目录中完成或执行以下操作:
*   运行`devild db:export`并按照说明将数据库导出到当前目录中
*   运行`devild db:replace`并按照指示替换 URL，并创建另一个文件以保留原来的文件。
*   完成(大约 10 秒⏳)

这个软件包可以在 [NPM 注册中心](https://www.npmjs.com/package/@perlatsp/devild)获得，你可以通过运行`yarn global add @perlatsp/devild`或`npm i -g @perlatsp/devild`来安装它

反馈和公关是非常受欢迎的。:D

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[德维德](https://github.com/perlatsp/devild)

<article class="markdown-body entry-content container-lg" itemprop="text">

## <g-emoji class="g-emoji" alias="smiling_imp" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f608.png">😈</g-emoji> Devild - Wordpress DB 导出&替换简易方式

sql 文件的数据库导出和搜索替换

[![](img/ba422a859566df0fc5891e30ec3ab36f.png)](https://raw.githubusercontent.com/perlatsp/devild/master/readme.md/./demo.gif)

## 如何使用

*   `npm i -g @perlatsp/devild`将其作为全局软件包安装。
*   您现在可以通过运行`devild <command>`来访问它
*   **注意！**如果您使用 devilbox / docker 容器，您需要在容器内完成上述操作。

### 可用命令

*   `devild`或`devild help` -将显示可用命令的帮助信息
*   `devild db:export` -将询问凭证导出数据库
*   `devild db:replace` -将询问问题以替换导出的`sql`文件中的 url
*   `devild install` -将创建示例配置文件以创建项目存储库(名称、类型、url)-
*   `devild project:start` -将提出问题，根据设定的配置创建新项目
*   `devild project:add` -将询问一些问题，以将新项目添加到您的配置文件中

* * *

### 待办事项

*   在当前目录中搜索替换
*   创建项目(WP…

</article>

[View on GitHub](https://github.com/perlatsp/devild)