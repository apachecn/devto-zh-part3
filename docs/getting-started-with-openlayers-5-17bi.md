# OpenLayers 5 入门

> 原文：<https://dev.to/greenwoodmap/getting-started-with-openlayers-5-17bi>

2019 年 1 月

**[OpenLayers](https://openlayers.org/)** 是一个高级的、功能丰富的客户端 JavaScript 地图渲染库。它是开源的，符合标准。如果你想在网络浏览器中进行复杂的交互式制图，你应该去看看 Openlayers。它很成熟(我从 2006 年就开始使用它)，但它并不“陈旧”——它是一个完全现代化的代码库，具有最先进的功能。

开始使用 OpenLayers 可能会令人生畏。标准的入口是[示例](https://openlayers.org/en/latest/examples/)页面，大约有 150 个示例，顶部有一个很好的搜索工具。当前版本(5.3)的问题是，有一个理解水平的假设，这可能是一个进入的障碍。尽管示例中代码上方有一个漂亮的“复制”图标，但您不能只是粘贴到您的编辑器中，就可以使用工作代码了。

要让这些示例正常工作，需要做一点编辑。这并不是因为例子被破坏了，而是因为 openLayers 库提供了很大的灵活性。至少有三种方法可以在您的应用中使用 OpenLayers，并让复制粘贴示例正常工作:

1.  包括完整的 OpenLayers 库，并调用其构造函数和方法。
2.  使用 webpack 这样的捆绑器将您的代码与所需的 OpenLayers 模块捆绑在一起。
3.  使用浏览器的本地 ES6 模块支持。

第一种方法最简单。但是 OpenLayers 有大约 78，000 行代码(没有最小化，包括注释)，你的应用程序永远不会使用它的所有功能和特性。第二种方法是开发商推荐的路线。捆绑器将只包含您的应用程序需要的 OpenLayers 模块，并提供额外的优化。第三种方法是可能的，因为 OpenLayers 使用符合 ES6 的导出/导入语法。这可能不是一个生产应用的好方法，因为 OpenLayers 有近 700 个模块，即使你使用其中的一小部分，即使你使用 http2，它仍然是一整批模块。但它吸引了我，因为它看起来如此现代和“纯粹”。

让我们来看看使用 OpenLayers 5 . 3 . 0[simple.html](https://openlayers.org/en/v5.3.0/examples/simple.html)的例子需要做些什么来使用这三种方法中的每一种。JavaScript 看起来像:

```
 import Map from 'ol/Map.js';
      import View from 'ol/View.js';
      import TileLayer from 'ol/layer/Tile.js';
      import OSM from 'ol/source/OSM.js';

      var map = new Map({
        layers: [
          new TileLayer({
            source: new OSM()
          })
        ],
        target: 'map',
        view: new View({
          center: [0, 0],
          zoom: 2
        })
      });npm install ol 
```

但是，如果您已经将示例粘贴到编辑器中，然后在浏览器中打开该文件，您将得到错误:“未捕获的语法错误:意外的标识符”

要使用第一种方法，您需要包含完整的 OpenLayers 库，并删除四个导入语句。完整的库可通过 cdn 获得(不推荐用于生产)

```
<script src="https://cdn.rawgit.com/openlayers/openlayers.github.io/master/en/v5.3.0/build/ol.js"></script> 
```

或者更好，下载完整的发行版 [v5.3.0.zip](https://github.com/openlayers/openlayers/releases/download/v5.3.0/v5.3.0.zip) 并将脚本 src 指向您的副本。然后注释或删除导入，如下所示。

```
 <!-- add script element pointing to full openlayers -->
    <script src="path/to/where/you/downloaded/ol.js"></script>
    <script>
      /*  // remove imports
      import Map from 'ol/Map.js';
      import View from 'ol/View.js';
      import TileLayer from 'ol/layer/Tile.js';
      import OSM from 'ol/source/OSM.js';
      */

      var map = new ol.Map({
        layers: [
          new ol.layer.Tile({
            source: new ol.source.OSM()
          })
        ],
        target: 'map',
        view: new ol.View({
          center: [0, 0],
          zoom: 2
        })
      });
    </script> 
```

太简单了！这是一个不费吹灰之力探索 OpenLayers 的好方法。

但是我们可以做得更好。通过使用 Webpack、Rollup 或 package 等 ES6 模块捆绑器，您可以将自己的模块与 OpenLayers 合并，构建更小的发行版，并在更现代的环境中编码。这是使用 OpenLayers 开发生产应用程序的推荐方式。OpenLayers 的下一个版本将于 2019 年 2 月发布，将有使用这种方法的工作示例。但是如果你等不及了，请继续读下去！

Webpack 不使用 [v5.3.0.zip](https://github.com/openlayers/openlayers/releases/download/v5.3.0/v5.3.0.zip) 文件。Webpack 使用随 npm 一起安装的源模块。

github 上有很棒的入门指南:
[OpenLayers+web pack](https://github.com/openlayers/ol-webpack)
[OpenLayers+roll up](https://github.com/openlayers/ol-rollup)
[OpenLayers+package](https://github.com/openlayers/ol-parcel)
然而 OpenLayers 5.3.0 的例子仍然不能很好地解决这些问题。再一次，仅仅几个小的编辑就能让你理解例子。

我们将在最基本的配置中使用 webpack，而不是使用上面的任何入门包。我将假设您比我更了解 webpack(大多数人都知道),并且您已经安装了 nodejs。对于最基本的设置:

```
mkdir ol-test && cd ol-test
npm install ol
npm install --save-dev webpack webpack-cli
mkdir src 
```

将来自[simple.html](https://openlayers.org/en/v5.3.0/examples/simple.html)示例的 JavaScript 粘贴到名为“index.js”的新 JavaScript 文件中，并保存到 src/ directory。因为*地图*、*视图*、*平铺器*、 *OSM* 都是导入的，所以“ol”、“ol.layer”和“ol.source”前缀是不必要的，需要删除。编辑后的 JavaScript src/index.js 文件应该是这样的:

```
 import Map from 'ol/Map.js';
      import View from 'ol/View.js';
      import TileLayer from 'ol/layer/Tile.js';
      import OSM from 'ol/source/OSM.js';

      var map = new Map({
        layers: [
          new TileLayer({
            source: new OSM()
          })
        ],
        target: 'map',
        view: new View({
          center: [0, 0],
          zoom: 2
        })
      }); 
```

运行 webpack 将 src/index.js 与导入的 OpenLayers 模块捆绑在一起。(通常你会使用一个带有“脚本”部分的 package.json 文件，但我们的重点只是让 OpenLayers 示例工作起来)

```
node_modules/.bin/webpack 
```

默认情况下，webpack 会查找。/src/index.js 作为其源代码，并将绑定的输出写入。/dist/main.js 所以从 simple.html 中移除内联 Javascript 并添加一个指向您的包的脚本元素:

```
 <script src="dist/main.js"></script> 
```

幸运的话，当你在浏览器中打开 simple.html 时，你会看到一张地图。

我将在以后的文章中描述使用 OpenLayers 的第三种方式，即在浏览器中本地加载模块。这种方法对于生产来说可能是不实际的，至少在 web 发展的这个阶段是不实际的。旧的浏览器不能导入 ES6 模块，所需的模块数量可能会影响性能。但是它能够实现的事实表明 OpenLayers 代码库是多么现代和干净，并且玩起来真的很有趣。