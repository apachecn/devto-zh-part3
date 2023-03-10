# 在 Next.js 中自动生成 sitemap.xml

> 原文：<https://dev.to/embiem/auto-generate-sitemapxml-in-nextjs-2nh1>

万岁！您为自己漂亮且高性能的 Next.js 网站创建了所有的组件和样式。现在怎么办？

在你导出的包的根目录下有一些你想要服务的关键文件，但是 Next.js 只支持从`/static`文件夹中开箱复制文件。但是如何添加一个`sitemap.xml`，即使是以一种自动的并且总是最新的方式？

让我向您展示如何为一个名为“yarn export”的 Next.js 项目设置这个。

## 基本 sitemap.xml 结构

首先，我们需要看一看基本站点地图需要的信息。

一列；一栏...

*   每个可用页面的 URL，
*   和一个伴随的日期，让搜索引擎机器人知道在哪里可以找到一个页面，以及它最后一次更改的时间。

就是这样！如果你想了解更多信息，你可以查看谷歌的“建立并提交网站地图”网站。

## 收集所需信息

在我们将文件写入导出/输出文件夹之前，我们必须实际获取我们需要的信息:页面 url 和最后修改日期。

为此，我构建了这个函数，它返回/pages 文件夹中所有文件的路径:

```
module.exports = () => {
  const fileObj = {};

  const walkSync = dir => {
    // Get all files of the current directory & iterate over them
    const files = fs.readdirSync(dir);
    files.forEach(file => {
      // Construct whole file-path & retrieve file's stats
      const filePath = `${dir}${file}`;
      const fileStat = fs.statSync(filePath);

      if (fileStat.isDirectory()) {
        // Recurse one folder deeper
        walkSync(`${filePath}/`);
      } else {
        // Construct this file's pathname excluding the "pages" folder & its extension
        const cleanFileName = filePath
          .substr(0, filePath.lastIndexOf("."))
          .replace("pages/", "");

        // Add this file to `fileObj`
        fileObj[`/${cleanFileName}`] = {
          page: `/${cleanFileName}`,
          lastModified: fileStat.mtime
        };
      }
    });
  };

  // Start recursion to fill `fileObj`
  walkSync("pages/");

  return fileObj;
}; 
```

这会返回一个对象，在我写的时候我的网站是这样的:

```
{
  "/blog/auto-generate-sitemap-in-next-js": {
    "page": "/blog/auto-generate-sitemap-in-next-js",
    "lastModified": "2018-10-03T00:25:30.806Z"
  },
  "/blog/website-and-blog-with-next-js": {
    "page": "/blog/website-and-blog-with-next-js",
    "lastModified": "2018-10-01T17:04:52.150Z"
  },
  "/blog": {
    "page": "/blog",
    "lastModified": "2018-10-03T00:26:02.134Z"
  },
  "/index": {
    "page": "/index",
    "lastModified": "2018-10-01T17:04:52.153Z"
  }
} 
```

如你所见，我们已经拥有了构建网站地图所需的所有信息！

## 导出时创建文件

在 Next.js 中，当您创建静态文件包时，通常会运行`yarn build && yarn export`。我们希望在导出后挂接，在/out 文件夹中创建 sitemap.xml 文件。

为了挂钩到 package.json 中定义的任何脚本，我们可以添加另一个同名的脚本，但以“post”为前缀；

新的 package.json 脚本部分将如下所示:

```
...  "scripts":  {  "dev":  "next",  "build":  "next build",  "start":  "next start",  "export":  "next export",  "postexport":  "node scripts/postExport.js"  },  ... 
```

我选择创建一个新文件夹“scripts ”,并在那里创建“postExport.js”文件。这个脚本现在将在每次“纱线导出”调用后运行。

## 生成 sitemap.xml 内容

这个`scripts/postExport.js`文件将利用我们之前创建的函数来获取所有需要的信息:

```
const pathsObj = getPathsObject(); 
```

然后，我们将创建 sitemap.xml 内容和文件:

```
const sitemapXml = `<?xml version="1.0" encoding="UTF-8"?>
<urlset > ${Object.keys(pathsObj).map(
    path => `<url>
    <loc>https://embiem.me${path}</loc>
    <lastmod>${
      formatDate(new Date(pathsObj[path].lastModified))
    }</lastmod>
  </url>`
  )} </urlset>`;

fs.writeFileSync("out/sitemap.xml", sitemapXml); 
```

就是这样！差不多吧。我确实使用了一个 formatDate 函数来获得我们的日期所需的字符串格式。

你可以只做一个`.substr()`，因为`pathsObj[path].lastModified`已经包含了一个 ISO 格式的日期，或者使用像 [date-fns](https://date-fns.org/) 这样的库。我决定从网上复制一个可行的解决方案:

```
module.exports = function formatDate(date) {
  var d = new Date(date),
    month = "" + (d.getMonth() + 1),
    day = "" + d.getDate(),
    year = d.getFullYear();

  if (month.length < 2) month = "0" + month;
  if (day.length < 2) day = "0" + day;

  return [year, month, day].join("-");
}; 
```

现在运行`yarn export`，在`out/sitemap.xml`出现一个文件！

## 挑战！创建机器人. txt

基于此，你现在应该很容易用你想要的内容创建一个 robots.txt。

如果你想知道我是如何做到的，可以查看我网站的回购中的[脚本文件夹。](https://github.com/embiem/embiem-website/tree/master/scripts)

让我知道你是否会用不同的方法解决这个问题。

## 后记

这是我在这个社区的第一个帖子👋。我计划在未来发布更多内容。你可以在我的[博客](https://embiem.me/blog/auto-generate-sitemap-in-next-js/)上找到原帖。