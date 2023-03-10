# 使用 ArcGIS API for JavaScript 实现苗条身材

> 原文：<https://dev.to/odoenet/svelte-with-the-arcgis-api-for-javascript-34ej>

如果你还没看过的话，纽约时报的 Rich Harris 做了一个关于最新发布的[苗条](https://svelte.dev/)的[演示。Svelte 已经存在了一段时间，它并不是一个真正的框架。它是一个编译器，可以自省你的代码，并做一些真正有趣的事情，比如知道你什么时候使用一些东西作为你的 DOM 的状态。](https://www.youtube.com/watch?v=AdNJ3fydeao)

当今 JavaScript 领域的另一个有趣之处是，Svelte [不使用虚拟 DOM](https://svelte.dev/blog/virtual-dom-is-pure-overhead) ，它会根据您的使用方式就地更新 DOM。

自从它的最新版本发布以来，我收到了许多关于它如何与 ArcGIS API for JavaScript 一起使用的问题。我喜欢做这些混搭，所以我花了一些时间简单地研究集成。

## 入门

当您第一次安装您的苗条应用程序时，添加 ArcGIS API for JavaScript 非常简单。进入公共文件夹，将脚本标签添加到 index.html 文件的文件头。

```
<head>
    <meta charset='utf8'>
    <meta name='viewport' content='width=device-width'>
    Svelte app
    <link rel='stylesheet' href='global.css'>
    <link rel='stylesheet' href='bundle.css'>
        <link rel="stylesheet" href="https://js.arcgis.com/4.11/esri/themes/light/main.css">
    <script src="https://js.arcgis.com/4.11/"></script>
</head> 
```

Enter fullscreen mode Exit fullscreen mode

一旦你这样做了，你可以直接进入 App.svelte 文件并使用 API 的 AMD loader 来加载你的 mappy 文件。

```
<!-- src/App.svelte -->
<script>
    export let centerText;

    require(["esri/Map", "esri/views/MapView"], function(Map, MapView) {
        const map = new Map({
            basemap: "streets"
        });

        const view = new MapView({
            container: "viewDiv",
            map: map,
            zoom: 4,
            center: [15, 65] // longitude, latitude
        });

        view.watch('center', center => {
            const { latitude, longitude } = center;
            centerText = `Lat: ${ latitude.toFixed(2)} | Lon: ${ longitude.toFixed(2) }`;
        });
    });
</script>

<style>
    #root {
        display: flex;
        flex-direction: column;
        padding: 0;
        margin: 0;
        height: 100%;
        width: 100%;
    }
    #viewDiv {
        padding: 0;
        margin: 0;
        height: 100%;
        width: 100%;
    }
    h1 {
        width: 100%;
        text-align: center;
        color: dodgerblue;
    }
</style>

<div id="root">
    <h1>{centerText}</h1>
    <div id="viewDiv"></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

所以是的，Svelte 有一个文件扩展名。如果你用过 Vue，这可能对你来说很熟悉。你的编辑可能会抱怨没有安装*所需的*类型，但这可以忽略。

*旁注* -我可以看到这对于开发人员来说将他们所有的组件逻辑、样式和 UI 保持在同一个地方的好处，如果你已经在做 css-in-js，你就不能抱怨这个了！我不确定我是不是一个大粉丝，但再次重申，我不会经常使用 Svelte 或 Vue。

在这一点上，它只是工作。你应该有一个像这样的应用程序。

[![svelte and arcgis javascript api](img/d6dc69a69fe613cdeed26c8e167429ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vaTFAisa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xve0myr5v79s033ibnth.gif)

好吧，这很酷...但是它可能会更凉爽。

## 让我们更酷

Svelte 不用 webpack，用的是 [rollup](https://rollupjs.org/guide/en) 。这对我们来说容易多了。

在 rollup.config.js 中，我们将输出改为 amd。

```
// rollup.config.js
...
output: {
  sourcemap: true,
  format: 'amd',
  name: 'app',
  file: 'public/bundle.js'
},
... 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以更新 index.html 页面，使用 ArcGIS API for JavaScript 的 AMD loader 来加载 Svelte 将创建的模块。

```
<script>
  // define app as a dojo package
  window.dojoConfig = {
    packages: [
      {
        name: 'app',
        location: window.location.pathname.substring(0, window.location.pathname.lastIndexOf('/')) + '/'
      }
    ]
  }
</script>
<script src="https://js.arcgis.com/4.11/"></script>
</head>
<body>
<script>
  require(['app/bundle']);
</script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

我将创建一个文件，该文件将处理我的所有 ArcGIS API for JavaScript 代码，与 Svelte 分开。

```
// src/initMap.js
export async function initmap() {
  const [Map, MapView] = await Promise.all([
    import("esri/Map"),
    import("esri/views/MapView")
  ]);

  const map = new Map({
    basemap: "streets"
  });

  const view = new MapView({
    container: "viewDiv",
    map: map,
    zoom: 4,
    center: [15, 65]
  });

  return view;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以在我的苗条档案中使用它。

```
// App.svelte
<script>
    import { initmap } from "./initmap";

    export let centerText = "Loading...";

    const load = async () => {
      const view = await initmap();
      view.watch("center", center => {
        const { latitude, longitude } = center;
        centerText = `Lat: ${latitude.toFixed(2)} | Lon: ${longitude.toFixed(2)}`;
      });
    };

    load();
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我的申请看起来也一样！

你可以在 github 上看到这个应用的[源代码。](https://github.com/odoe/jsapi-svelte)

## 总结

最终，将 ArcGIS API for JavaScript 与 Svelte 集成起来并不困难。我喜欢 Svelte 正在做的事情，我认为它让 JavaScript 开发者思考并让每个人保持警觉。不幸的是，目前它还没有一个好的打字稿故事，但我想他们正在努力！

所以，去吧，试一试，找点乐子，尝试一些新的东西！

请务必[注册我的简讯](https://odoe.net/blog/sign-up/)以了解我的最新内容！