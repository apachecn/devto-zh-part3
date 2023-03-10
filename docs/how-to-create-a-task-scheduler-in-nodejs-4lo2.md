# 节点调度器如何在 NodeJS 中创建任务调度器

> 原文：<https://dev.to/andre347/how-to-create-a-task-scheduler-in-nodejs-4lo2>

### 韦斯·博斯发布了一个非常有用的[视频](https://www.youtube.com/watch?v=rWc0xqroY4U)解释了如何用 NodeJS 从网络上抓取数据。在他的第二个[视频](https://www.youtube.com/watch?v=9dIHjegGeKo)中，他解释了如何为这个特殊的任务建立一个时间表。这是我以前从未在 Node 中做过的事情，所以我想这在未来可能会有用，因此我应该写一篇关于它的快速博客文章。

在 Wes 的视频中，他从自己的社交媒体页面获取数据，而我将创建一个小应用程序，它按照时间表运行，并在每天下午 6 点下载随机图像。我知道没错，谁不想每天都有一个随机的图像出现在他或她的磁盘上呢？！

我们需要先安装的几样东西:

```
 // create dir, go into it and install packages
    mkdir image-downloader && cd image-downloader &&
    npm i node-cron node-fetch esm 
```

Enter fullscreen mode Exit fullscreen mode

对您刚刚安装的内容进行快速分解:

1.  **node-cron** :这是任务调度器的包。它允许您设置自动执行某事(通常是执行一个功能)的时间表。
2.  **node-fetch** :使用 fetch api 的一种方式。这是一个本地浏览器 API——但是当我们使用 node 时，我们没有浏览器。你也可以在这里使用另一个包。Axios 是很受欢迎的一款。它只允许你下载一个 url 后面的内容。通常，您使用它来连接到 API 或抓取 web。
3.  esm :我以前没用过这个，但是它超级有用。它允许你像在 Vue 或 React 等客户端 JavaScript 中一样编写代码。也就是说你有进出口权。要启用此 esm，您必须安装它，然后将其添加到您的运行脚本中。在我的 package.json 文件中，我添加了这一行作为“开始”脚本:

```
 "scripts": {
        "start": "node -r esm index.js"
      }, 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过执行`npm run start`来运行这个脚本。

## 创建下载器

现在我们已经安装了必要的包，是时候创建第一个文件了，在这个文件中我们将只获取一个图像:fetch.js

```
// fetch.js

import fetch from "node-fetch";
import fs from "fs";

// create a function that grabs a random image

const fetchingData = async () => {
  const res = await fetch("https://picsum.photos/200?random");
  const date = Date.now();
  const dest = fs.createWriteStream(`./image-${date}.png`);
  res.body.pipe(dest);
};

// export the function so it can be used in the index.js file

export default fetchingData; 
```

Enter fullscreen mode Exit fullscreen mode

为了在每次执行这个脚本时得到一个随机的图片，我使用了 [Picsum](https://picsum.photos/) 。这个网站允许你生成一个固定宽度和高度的随机图像。您可以将这些维度附加到 url 上。我还用当前日期创建了一个变量。该日期将被附加到文件名后，以防止文件被覆盖。因为我们在这里处理承诺，所以我使用了 async/await。

如果你想测试这个文件，你可以用`node -r esm fetch.js`来运行它

## 设置日程

接下来，您需要创建一个 index.js 文件。这将是主入口文件，其中包含 node-cron 函数:

```
import cron from "node-cron";

cron.schedule("* * * * *", () => {
  console.log(`this message logs every minute`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常小的应用程序，如果你执行它会记录一条消息到控制台。这条消息将每分钟重复一次。很酷，但不是很有用。让我们通过导入来添加图像提取器。index.js 文件看起来应该是这样的:

```
import cron from "node-cron";
import fetchingData from "./fetch";

cron.schedule("* * * * *", () => {
  console.log(`one minute passed, image downloaded`);
  fetchingData();
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是，这将每分钟运行图像下载程序。我们可以通过更改添加到调度函数中的第一个参数来更改 cron 作业。您看到的五颗星表示该函数将每分钟运行一次。您可以通过以下方式修改它(取自此处的[和](https://github.com/node-cron/node-cron)):

```
 # ┌────────────── second (optional)
 # │ ┌──────────── minute
 # │ │ ┌────────── hour
 # │ │ │ ┌──────── day of month
 # │ │ │ │ ┌────── month
 # │ │ │ │ │ ┌──── day of week
 # │ │ │ │ │ │
 # │ │ │ │ │ │
 # * * * * * * 
```

Enter fullscreen mode Exit fullscreen mode

一开始，我真的不明白这是什么意思。在谷歌搜索了一会儿之后，我发现了下面这个网站，它真的是一个非常有用的备忘单；[克朗塔古鲁](https://crontab.guru/)

这意味着你可以为任何时间设定一个时间表。也许一年一次？或者一月和七月的每周二上午 8 点。真的没有限制。接下来，我设置了一个时间表，让它在每天下午 6 点下载，设置为:`0 18 * * *`

完整且最终的`index.js`文件是:

```
import cron from "node-cron";
import fetchingData from "./fetch";

cron.schedule("0 18 * * *", () => {
  console.log(`one minute passed, image downloaded`);
  fetchingData();
}); 
```

Enter fullscreen mode Exit fullscreen mode

想看看完整的应用程序还是克隆它？前往我的 Github [这里](https://github.com/andre347/nodejs-task-scheduling)！

Dev.to so 温柔的第一篇帖子。最初发布于 [andredevries.dev](https://andredevries.dev/posts/node-task-scheduler/)