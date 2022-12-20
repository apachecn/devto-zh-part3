# 用 ZingGrid 创建您的第一个数据网格

> 原文：<https://dev.to/zinggrid/creating-your-first-data-grid-with-zinggrid-2288>

在本文中，我将向您展示使用 ZingGrid 在不到五分钟的时间内创建数据网格或数据表是多么简单快捷。观看视频教程，了解这些步骤的实际操作，或者跟随这篇博客文章，了解开始使用 ZingGrid 所需的语法。
[https://www.youtube.com/embed/I2zzfv2tLRk](https://www.youtube.com/embed/I2zzfv2tLRk)
在本教程中，你将学习如何做三件主要的事情:

1.  导入 ZingGrid 库
2.  引用 ZingGrid 库
3.  渲染网格

* * *

## 导入 ZingGrid 库

要导入该库，您需要首先将其作为资源下载，您可以通过三种方式之一来完成——通过 CDN 链接、通过包管理器或通过直接下载。

#### CDN 链接

CDN 链接是可以用来导入 ZingGrid 库的最快捷的设置选项。

```
<script src="https://cdn.zinggrid.com/zinggrid.min.js"></script> 
```

#### 包管理器

如果你更愿意使用包管理器，你可以运行`npm install zinggrid`来通过 npm 安装这个库。

```
<script type="module">
  import ZingGrid from 'ZingGrid';
</script> 
```

#### 直接下载

你的第三个选择是从我们的[网站](https://www.zinggrid.com/download)或者从我们的公共 [GitHub 库](https://github.com/ZingGrid/zinggrid)下载 ZingGrid 库。

```
<script type="module">
  import ZingGrid from './index.js';
</script>
<!-- fallback for no module support -->
<script nomodule src="./dist/zinggrid.min.js"></script> 
```

* * *

## 引用 ZingGrid 库

要使用这个库，您需要在您的环境中引用这个脚本。在这个例子中，我们使用的是 CDN 链接，所以我们只需将它添加到`<head>`标签中，并添加`defer`属性，以防止页面解析期间出现呈现阻塞脚本。

我们推迟这个脚本是因为在解析组件之前不需要加载这个库。一旦脚本被注册，页面上的所有`<zing-grid>`实例将自动被实例化。

```
<!DOCTYPE html>
<html>
<head>
  <!--Script Reference-->
  <script src="https://cdn.zinggrid.com/zinggrid.min.js" defer></script>
</head>
<body>
  <!--Grid Component-->
  <zing-grid></zing-grid>
</body>
</html> 
```

您可以使用的一个稍微不同的方法是将`<script>`标签放在文档的底部，这实际上与上面的`defer`过程相同。

```
<!DOCTYPE html>
<html>
<head>
</head>
<body>
  <!--Grid Component-->
  <zing-grid></zing-grid>
  <!--Script Reference-->
  <script src="https://cdn.zinggrid.com/zinggrid.min.js"></script>
</body>
</html> 
```

* * *

## 渲染网格

创建第一个网格就像创建一个`<zing-grid>`标签并添加一个`data`属性一样简单。`data`属性期望网格数据采用有效的 JSON 格式，所以这就是我们将样本数据添加到这个网格的方式。

在这个例子中，我们还使用了`caption`属性将标题添加到数据网格中。

一旦将 JSON 数据添加到`data`属性:
中，您的标记应该是这样的

```
<!DOCTYPE html>
<html>
<head>
  <!--Script Reference[1]-->
  <script src="https://cdn.zinggrid.com/zinggrid.min.js" defer></script>
</head>
<body>
  <!--Grid Component Placement[2]-->
  <zing-grid
    caption="Hello Futurama"
    data='[{
        "name": "Philip J. Fry",
        "origin": "Earth"
      },
      {
        "name": "Turanga Leela",
        "origin": "Earth"
      },
      {
        "name": "Bender Bending Rodriguez",
        "origin": "Earth"
      },
      {
        "name": "Amy Wong",
        "origin": "Mars"
      },
      {
        "name": "Doctor John Zoidberg",
        "origin": "Decapod 10"
      },
      {
        "name": "Lord Nibbler",
        "origin": "Earth"
      }
    ]'>
  </zing-grid>
</body>
</html> 
```

下面是这个网格的现场演示:
[https://codepen.io/zinggrid/embed/vbQNXN?height=600&default-tab=result&embed-version=2](https://codepen.io/zinggrid/embed/vbQNXN?height=600&default-tab=result&embed-version=2)
zing grid 的所有主库特性通常都是添加到顶级`<zing-grid>`标签的属性。您可以在我们的 [API 参考文档](https://www.zinggrid.com/docs/api-elements#zing-grid)中找到可用属性和特性的完整列表。

* * *

这就是使用 ZingGrid 库构建第一个网格所需要做的全部工作——感谢您的关注！请继续关注本系列的下一篇教程，我将带您了解津格里德·🖖的数据基础知识