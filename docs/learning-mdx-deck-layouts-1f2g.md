# 学习 MDX 资料:布局

> 原文：<https://dev.to/davefollett/learning-mdx-deck-layouts-1f2g>

欢迎回来👋。在本期*学习 MDX 平台*中，我将介绍一下 [MDX 平台的](https://github.com/jxnblk/mdx-deck)内置布局。也可以创建自己的布局。我不会在这里介绍它，但是你可以在 [MDX 平台文档](https://github.com/jxnblk/mdx-deck/blob/master/docs/layouts.md)中读到它。所以让我们开门见山吧。这里是 MDX Deck 为开箱即用的[布局](https://github.com/jxnblk/mdx-deck/blob/master/docs/components.md#layouts)提供的内容。

*   默认
*   转化的
*   裂开
*   向右拆分
*   全屏模式
*   水平的

MDX 平台的布局是 React 组件。要使用 one，您首先需要导入它。

```
import { LayoutName } from 'mdx-deck/layouts' 
```

因为每张幻灯片可以使用不同的布局，所以也可以一次导入多个布局。

```
import { LayoutName1, LayoutName2, LayoutName2 } from 'mdx-deck/layouts' 
```

导入布局后，将内容放入其组件标记中，如下所示，确保在内容项周围放置换行符。不使用换行符会导致幻灯片显示不正确。或者，您可以将内容放在布局组件的上方或下方。

```
# SLIDE TITLE

<LayoutName>

Add your content here.

</LayoutName> 
```

现在我们知道了如何使用 MDX 面板布局，让我们看看所有内置选项。

## 默认

没有指定布局组件将提供从上到下垂直堆叠幻灯片上所有内容的默认行为。

```
# LAYOUT: Default

1\. All content stacks
1\. Top to bottom
1\. On the slide
1\. This is an ordered list 
```

<figure>

[![Example of MDX Deck's Default Layout Rendered](img/1cb24641500c2243a37927570f6fdc84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4u8Wfynf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davefollett.io/2019/06/05/mdx-deck-layouts/layout-default.png)

<figcaption>Example of MDX Deck's Default Layout Rendered</figcaption>

</figure>

## 反转

`Invert`布局像默认布局一样垂直堆叠内容，但是它颠倒了主题的前景色和背景色。

```
import { Invert } from 'mdx-deck/layouts'

<Invert>

# LAYOUT: Invert

The foreground and background colors are inverted from the theme.

</Invert> 
```

<figure>

[![Example of MDX Deck's Invert Layout Rendered](img/74ab6f90ffb41ea06333a35cfd6eed27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mz83mrLb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davefollett.io/2019/06/05/mdx-deck-layouts/layout-invert.png)

<figcaption>Example of MDX Deck's Invert Layout Rendered</figcaption>

</figure>

## 分割

`Split`布局将幻灯片分成两个垂直栏；左和右。第一个项目将显示在左侧，所有其他项目将垂直堆叠在右侧。

```
import { Split } from 'mdx-deck/layouts'

# LAYOUT: Split

<Split>

![](https://media.giphy.com/media/1CrejqXxVZs9q/giphy.gif)

* Otter Elevates
* For The 🏀 Dunk

</Split>

First item displayed to the ⬅️, all remaining to the ➡️ 
```

<figure>

[![Example of MDX Deck's Split Layout Rendered](img/6d42d8cc896f1452ffa62c10d330ff93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tLAxkmho--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davefollett.io/2019/06/05/mdx-deck-layouts/layout-split.png)

<figcaption>Example of MDX Deck's Split Layout Rendered</figcaption>

</figure>

## 向右拆分

`SplitRight`布局与`Split`布局相反。第一个项目将显示在右侧，所有其他项目将垂直堆叠在左侧。

```
import { SplitRight } from 'mdx-deck/layouts'

# LAYOUT: SplitRight

<SplitRight>

![](https://media.giphy.com/media/vjKrEyy2NVblS/giphy.gif)

* So Much Otter
* Cuteness 😍

</SplitRight>

First item displayed to the ➡️, all remaining to the ⬅️ 
```

<figure>

[![Example of MDX Deck's SplitRight Layout Rendered](img/22d6bba3e086f48b0539fe1d6b2baf8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--miagCVNk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davefollett.io/2019/06/05/mdx-deck-layouts/layout-splitright.png)

<figcaption>Example of MDX Deck's SplitRight Layout Rendered</figcaption>

</figure>

## 全屏编码

`FullScreenCode`布局将全屏显示由三个反勾号包围的代码块。

```
import { SplitRight } from 'mdx-deck/layouts'

<FullScreenCode>

```js
   const arrayOfNumbers = [0, 1, 2, 3, 4, 5];

   const sum = arrayOfNumbers.reduce(function (accumulator, currentValue) {
     return accumulator + currentValue;
   });

   console.log("The sum is: " + sum);
```

</FullScreenCode>

```

<figure>

[![Example of MDX Deck's FullScreenCode Layout Rendered](img/1f48a4faa2bfca76a85ec5a5f12bdb18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iUkMIfKG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davefollett.io/2019/06/05/mdx-deck-layouts/layout-fullscreencode.png)

<figcaption>Example of MDX Deck's FullScreenCode Layout Rendered</figcaption>

</figure>

## 水平

`Horizontal`布局与默认布局相反。在垂直堆叠内容(从上到下)的内部，它从左到右水平堆叠内容。

```
# LAYOUT: Horizontal

<Horizontal>

![](https://media.giphy.com/media/26gssIytJvy1b1THO/giphy.gif)

![](https://media.giphy.com/media/26gssIytJvy1b1THO/giphy.gif)

![](https://media.giphy.com/media/26gssIytJvy1b1THO/giphy.gif)

</Horizontal>

Each item displayed into its own column

👋 Hi Mr. Otter 👋 
```

<figure>

[![Example of MDX Deck's Horizontal Layout Rendered](img/f4a01a78055f48cc65e45a9940b079f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fAmXubrN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://davefollett.io/2019/06/05/mdx-deck-layouts/layout-horizontal.png)

<figcaption>Example of MDX Deck's Horizontal Layout Rendered</figcaption>

</figure>

## 🏁结论

如您所见，MDX Deck 提供了一组可靠的布局，可以满足大多数演示需求。本文使用的所有代码都可以通过以下参考资料查看。

### 代码手册。我

[https://codesandbox.io/embed/learning-mdx-deck-layouts-i87ih](https://codesandbox.io/embed/learning-mdx-deck-layouts-i87ih)

### GitHub 资源库

[https://github.com/davefollett/learning-mdx-deck-layouts](https://github.com/davefollett/learning-mdx-deck-layouts)

### 部署到网络

[https://learning-mdx-deck-layouts . davefollett . io](https://learning-mdx-deck-layouts.davefollett.io)

## 🔜下一个

在 *Learning MDX Deck* 系列中，我们将探索 MDX Deck 的内置主题选项。发表的时候我会在这里加一个链接。