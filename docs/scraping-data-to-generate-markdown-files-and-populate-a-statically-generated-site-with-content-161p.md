# 抓取数据以生成降价文件，并用内容填充静态生成的站点

> 原文：<https://dev.to/parkeragee/scraping-data-to-generate-markdown-files-and-populate-a-statically-generated-site-with-content-161p>

### **最初发布于[https://blog . Parker agee . com/post/scraping-data-to-generate-markdown-files-and-populate-a-static-generated-site-with-content/](https://blog.parkeragee.com/post/scraping-data-to-generate-markdown-files-and-populate-a-statically-generated-site-with-content/)

[![Demo](img/83f4b9730c6b0a51780b53b5a9494b79.png "Demo")](https://res.cloudinary.com/practicaldev/image/fetch/s--zD3SXTYd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ucarecdn.com/4a2ca4c5-6909-483c-8927-e49b5eb02d64/)

在这篇文章中，我将向你展示我是如何通过创建一个脚本高效地向我的一个客户网站添加 300 多个网页内容的，这个脚本将抓取网页并从这些数据中生成降价文件。

该客户是一家假发经销商，他们需要他们网站上所有可用假发的图片和名称。因此，我创建了一个脚本，从制造商的网站上获取所有信息，而不是手动创建每个页面，复制并粘贴图像和名称。

我们开始吧..

### 先做第一件事

我们需要创建一个目录，我们的脚本将被添加到其中。继续运行`mkdir content-scraper && cd $_`。这将创建我们的目录并将我们移入其中。

接下来，我们想运行`npm init -y`来设置我们项目的 package.json 文件。

一旦我们创建了 package.json 文件，我们需要安装一些节点包来帮助我们实现目标。运行`npm install --save path json2md axios cheerio fs-extra chalk`安装所需的软件包。

现在，让我们创建将要工作的文件- `touch index.js`

### 让我们在 index.js 中构建我们的 scipt

##### 添加节点包

首先，让我们引入所有的节点包。

```
const path = require('path');
const json2md = require("json2md")
const axios = require('axios');
const cheerio = require('cheerio');
const fs = require('fs-extra');
const chalk = require('chalk'); 
```

现在，我们想要创建一个函数，它将在我们启动脚本时运行。让我们下拉并添加以下内容:

```
async function go() {
    console.log('Running...');
}

go(); 
```

现在，如果我们运行`node index.js`，我们应该在终端中得到`Running...`。

##### 获取网页的 HMTL

接下来，我们将使用`cheerio.js`来获取我们想要抓取的网页的 HTML。让我们为代码创建一个新函数。

将新函数添加到您的文件中。

```
async function getHtml(url) {
    const { data: html } = await axios.get(url);
    return html;
} 
```

这将使用`axios`发出请求，并获取我们传递给它的 URL 的 HTML 内容。我们将把这个 HTML 返回给下面的`go()`函数。

在我们已经添加的`go()`函数中，让我们初始化`getHtml()`函数并把我们的 URL 传递给它。将以下内容添加到您的`go()`函数中:

```
async function go() {
    const url = process.argv[2];

    if (url === undefined) {
        console.log(chalk.white.bgRed.bold('Please provide a URL to scrape.'));
        console.log('Try something like:');
        console.log(chalk.green('node index.js https://www.hairuwear.com/raquel-welch/products-rw/signature-wig-collection-2/'));
        return;
    }

    const html = await getHtml(url);
    console.log(html);
} 
```

我们正在检查是否通过终端传递了一个 URL。如果没有，我们会在终端显示一条错误消息，解释如何运行脚本。如果我们传递了一个有效的 URL，那么在运行脚本之后，您应该会在您的终端上看到该页面的 HTML。

##### 从 HTML 中抓取数据

现在我们已经从网页上获得了 HTML，我们需要收集降价文件所需的数据。让我们创建一个新函数来获取 HTML 并查找我们的数据。

```
async function getwigs(html) {

    // Load the HTML as a cheerio instance
    const $ = cheerio.load(html);

    // Find the products list elements
    const wigSpan = $('.products li');

    // We want to make a new directory for our markdown files to live
    const directory = path.join('.', 'wig-pages');
    await fs.mkdirs(directory);

    // Loop through wigs and get data
    for (let i = 0; i < wigSpan.length; i++) {

        // Giving ourselves a little feedback about the process
        console.log(`Getting ${i} of ${wigSpan.length - 1}`);

        // Get the DOM elements we need
        const wigLinkSpan = $(wigSpan[i]).find('a')[0];
        const wigNameSpan = $(wigLinkSpan).find('h3')[0];

        // Get wig link and name data
        const wigLink = $(wigLinkSpan).attr('href');
        const wigName = $(wigNameSpan).text();

        console.log(wigLink, wigName);
    }
} 
```

现在，让我们用我们的 HTML 在`go()`函数中初始化那个函数。您的`go()`函数现在应该是这样的:

```
async function go() {
    const url = process.argv[2];

    if (url === undefined) {
        console.log(chalk.white.bgRed.bold('Please provide a URL to scrape.'));
        console.log('Try something like:');
        console.log(chalk.green('node index.js https://www.hairuwear.com/raquel-welch/products-rw/signature-wig-collection-2/'));
        return;
    }

    const html = await getHtml(url);
    const data = await getWigs(html);
    console.log(data);
} 
```

现在，您应该可以在页面上看到每个假发的链接和名称。

##### 从假发页面获取高分辨率图像

如果你注意到[我们正在看的这个页面](https://www.hairuwear.com/raquel-welch/products-rw/signature-wig-collection-2/)，这些图片的分辨率非常低。但是，如果你点击每一个假发，它会带你到一个关于那个特定假发的详细页面，上面有更高分辨率的照片。因此，我们现在需要做的是，对于该页面上的每个假发，我们还需要获取详细页面的 HTML，并从该页面中提取高分辨率照片添加到我们的数据中。

我们将通过进入我们的`for`循环来做到这一点，在这里我们获得 wig 链接和名称，并在那里添加代码。应该是这样的:

```
async function getWigs(html) {

    // Load the HTML as a cheerio instance
    const $ = cheerio.load(html);

    // Find the products list elements
    const wigSpan = $('.products li');

    // We want to make a new directory for our markdown files to live
    const directory = path.join('.', 'wig-pages');
    await fs.mkdirs(directory);

    // Loop through wigs and get data
    for (let i = 0; i < wigSpan.length; i++) {

        // Giving ourselves a little feedback about the process
        console.log(`Getting ${i} of ${wigSpan.length - 1}`);

        // Get the DOM elements we need
        const wigLinkSpan = $(wigSpan[i]).find('a')[0];
        const wigNameSpan = $(wigLinkSpan).find('h3')[0];

        // Get wig link and name data
        const wigLink = $(wigLinkSpan).attr('href');
        const wigName = $(wigNameSpan).text();

        const wigDetailsHtml = await getHtml(wigLink);
        const wigHtml = cheerio.load(wigDetailsHtml);
        const imgSrc = wigHtml('div.images > a > img').attr('src');

        console.log(wigLink, wigName, imgSrc);
    }
} 
```

你会注意到我们在这里添加了 3 行代码来获得高分辨率图像。

```
const wigDetailsHtml = await getHtml(wigLink);
const wigHtml = cheerio.load(wigDetailsHtml);
const imgSrc = wigHtml('div.images > a > img').attr('src'); 
```

我们将重用我们的`getHtml()`函数，并将 wig 细节页面链接传递给它。然后，我们将找到高分辨率图像的 DOM 元素，并获取`src`属性的值。现在我们有了高分辨率的图像源数据。如果你运行`node index.js`，你会注意到脚本运行得稍微慢了一点，因为我们发出了额外的请求，但是我们得到了我们需要的所有数据。

##### JSON 要降价了

现在，我们将通过`json2md`将所有这些结合在一起。让我们创建一个新的函数，它将获取我们收集的数据，并为每个假发创建一些降价。

```
async function generateMarkdown(data) {
    const heading = `---\ntitle: ${data.name}\nthumbnail: '${data.imgSrc}'\n---\n\n`;

    const md = await json2md([
        {
            h1: data.name
        },
        {
            link: {
                title: data.name,
                source: data.link,
            }
        },
        {
            img: {
                title: data.name,
                source: data.imgSrc,
            }
        }
    ]);

    return `${heading}${md}`;
} 
```

我们需要为每个需要页面的假发运行这个函数。因此，我们将把它添加到`getWigs()`函数的`for`循环中。你的`getWigs()`函数现在应该是这样的:

```
async function getWigs(html) {

    // Load the HTML as a cheerio instance
    const $ = cheerio.load(html);

    // Find the products list elements
    const wigSpan = $('.products li');

    // We want to make a new directory for our markdown files to live
    const directory = path.join('.', 'wig-pages');
    await fs.mkdirs(directory);

    // Loop through wigs and get data
    for (let i = 0; i < wigSpan.length; i++) {

        // Giving ourselves a little feedback about the process
        console.log(`Getting ${i} of ${wigSpan.length - 1}`);

        // Get the DOM elements we need
        const wigLinkSpan = $(wigSpan[i]).find('a')[0];
        const wigNameSpan = $(wigLinkSpan).find('h3')[0];

        // Get wig link and name data
        const wigLink = $(wigLinkSpan).attr('href');
        const wigName = $(wigNameSpan).text();

        // Get high-res photo from detail page
        const wigDetailsHtml = await getHtml(wigLink);
        const wigHtml = cheerio.load(wigDetailsHtml);
        const imgSrc = wigHtml('div.images > a > img').attr('src');

        // create markdown here
        const markdown = await generateMarkdown({
            name: wigName,
            link: wigLink,
            imgSrc,
        });

        console.log(markdown);
    }
} 
```

现在，当您运行`node index.js`时，您应该会得到如下所示的降价:

```
---
title: If You Dare
thumbnail: 'https://www.hairuwear.com/wp-content/uploads/RW-ifyoudare.jpg'
---

# If You Dare

[If You Dare](https://www.hairuwear.com/product/if-you-dare/)

![If You Dare](https://www.hairuwear.com/wp-content/uploads/RW-ifyoudare.jpg) 
```

接下来，我们只需要创建以降价为内容的文件。在前一次添加之后添加这两行代码:

```
const file = path.join('.', 'wig-pages', `${wigName.split('  ').join('-')}.md`);
await fs.writeFile(file, markdown); 
```

所以我们的`getWigs()`函数现在应该是这样的:

```
async function getWigs(html) {

    // Load the HTML as a cheerio instance
    const $ = cheerio.load(html);

    // Find the products list elements
    const wigSpan = $('.products li');

    // We want to make a new directory for our markdown files to live
    const directory = path.join('.', 'wig-pages');
    await fs.mkdirs(directory);

    // Loop through wigs and get data
    for (let i = 0; i < wigSpan.length; i++) {

        // Giving ourselves a little feedback about the process
        console.log(`Getting ${i} of ${wigSpan.length - 1}`);

        // Get the DOM elements we need
        const wigLinkSpan = $(wigSpan[i]).find('a')[0];
        const wigNameSpan = $(wigLinkSpan).find('h3')[0];

        // Get wig link and name data
        const wigLink = $(wigLinkSpan).attr('href');
        const wigName = $(wigNameSpan).text();

        // Get high-res photo from detail page
        const wigDetailsHtml = await getHtml(wigLink);
        const wigHtml = cheerio.load(wigDetailsHtml);
        const imgSrc = wigHtml('div.images > a > img').attr('src');

        // create markdown here
        const markdown = await generateMarkdown({
            name: wigName,
            link: wigLink,
            imgSrc,
        });

        // Create new markdown file and add markdown content
        const file = path.join('.', 'wig-pages', `${wigName.split('  ').join('-')}.md`);
        await fs.writeFile(file, markdown);
    }
} 
```

现在，我们应该有一个名为`wig-pages`的目录，里面装满了包含我们抓取的内容的 markdown 文件。您可以将该文件夹复制到网站的内容目录(取决于您的静态站点生成器)中，然后部署您的更改🎉。

这只是如何抓取数据并用内容填充静态生成的站点的一个例子。请随意采用这种方法，并将其应用于您自己的需求。

### 资源

*   [该脚本的 Github repo】](https://github.com/parkeragee/content-scraper)
*   [jsonmd](https://github.com/IonicaBizau/json2md)
*   [轴](https://github.com/axios/axios)
*   [再见](https://github.com/cheeriojs/cheerio)
*   [fs-extra](http://adilapapaya.com/docs/fs-extra/)
*   [粉笔](https://github.com/chalk/chalk)