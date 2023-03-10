# 使用 ZingGrid 创建带有远程端点的数据网格

> 原文：<https://dev.to/zinggrid/creating-a-data-grid-with-a-remote-endpoint-using-zinggrid-lkj>

在我的上一篇文章中，我在 [ZingGrid](http://zinggrid.com) JavaScript 库中介绍了数据网格和数据表的基础知识。今天，我将向您展示如何将远程数据源连接到 ZingGrid。查看[视频教程](https://www.youtube.com/watch?v=wKAvQyog5Ac)看看它是怎么做的，或者跟着我一起看这个书面教程。
[https://www.youtube.com/embed/wKAvQyog5Ac](https://www.youtube.com/embed/wKAvQyog5Ac)
在这篇文章中，你将了解到三件主要的事情:

1.  `[src]`属性
2.  远程 JSON 数据
3.  动态端点

您可以使用自己的远程数据集来跟进，但是我们也提供了以下远程端点供您使用:[https://cdn.zinggrid.com/datasets/remote-data.json](https://cdn.zinggrid.com/datasets/remote-data.json)

* * *

## 【src】属性

ZingGrid 提供了`src`属性来从远程数据源获取数据并显示出来。您可以使用相对路径或远程 URL。当您使用`src`属性时，内部会发出一个 AJAX 请求来获取您的数据。

`src`属性可以通过两种不同的方式实现:

1.  使用顶级的`<zing-grid>`标签
2.  使用`<zg-data>`标签

#### 使用顶级`<zing-grid>`标签

使用`src`属性最简单的方法是将其添加到顶级`<zing-grid>`标签中，如下所示:

```
<zing-grid
  src="https://cdn.zinggrid.com/datasets/remote-data.json">
</zing-grid> 
```

#### 使用`<zg-data>`标签

使用`src`属性的更可靠的方法是将它添加到`<zg-data>`标签中。这个方法允许您在`<zg-data>`中嵌套`<zg-param>`标签，这样您就可以调整`src`端点的动作。下面是`<zg-data>`的语法:

```
<zing-grid>
  <zg-data src="https://cdn.zinggrid.com/datasets/remote-data.json"></zg-data>
</zing-grid> 
```

这里有一个使用`<zg-data>` :
[https://codepen.io/zinggrid/embed/ZPaxJE?height=600&default-tab=result&embed-version=2](https://codepen.io/zinggrid/embed/ZPaxJE?height=600&default-tab=result&embed-version=2)
的数据网格的实例。在[我们的文档](https://www.zinggrid.com/docs/component-basics)中了解更多关于标记元素`<zg-data>`和`<zg-param>`的信息。

* * *

## 远程 JSON

`src`属性适用于大多数简单明了的数据结构。然而，数据集并不总是那么简单，您可能并不总是连接到您控制的端点。考虑以下数据结构:

```
{
  "company": {
    "name": "Planet Express",
    "employees": [
      {
        "name": "Philip J. Fry",
        "actor": "Billy West",
        "job": "Delivery Boy",
        "origin": "Earth",
        "gender": "male",
        "species": "Human"
      } ...
    ]
  }
} 
```

默认的`src` fetch 将只尝试访问顶级公司属性。如果您想列出从`company.employees`级别开始的数据呢？在这种情况下，您可能希望将`recordPath`属性添加到`<zg-param>`中，如下所示:

```
<zing-grid>
  <zg-data src="https://cdn.zinggrid.com/datasets/remote-data-recordpath.json">
    <zg-param name="recordPath" value="company.employees"></zg-param>
  </zg-data>
</zing-grid> 
```

这里有一个`recordPath`的实例:
[https://codepen.io/zinggrid/embed/xBPWXE?height=600&default-tab=result&embed-version=2](https://codepen.io/zinggrid/embed/xBPWXE?height=600&default-tab=result&embed-version=2)

要了解更多关于使用`<zg-data>`和`<zg-param>`的信息，请查看 ZingGrid 中可用属性的[列表。](https://www.zinggrid.com/docs/api-elements#zg-param)

* * *

## 动态端点

到目前为止，我已经向您展示了如何将静态端点连接到 ZingGrid，但是如果您想要连接到数据发生变化的动态端点，该怎么办呢？您所需要做的就是以一个时间间隔轮询那个端点，并通过`setData()` API 方法更新您的网格。

```
const zgRef = document.querySelector('zing-grid');
fetch('https://cdn.zinggrid.com/datasets/remote-data-recordpath.json')
  .then(res => res.json())
  .then(data => zgRef.setData(data))
  .catch(err => console.error('--- Error In Fetch Occurred ---', err)); 
```

这就是带有动态端点的演示的样子:
[https://codepen.io/zinggrid/embed/QoOmOP?height=600&default-tab=result&embed-version=2](https://codepen.io/zinggrid/embed/QoOmOP?height=600&default-tab=result&embed-version=2)

* * *

这就是在 ZingGrid 中连接远程数据源的要点。我希望这对您有所帮助，感谢您的任何反馈！如果您有兴趣学习如何在 ZingGrid 🖖中定制列，请阅读本系列的下一篇教程