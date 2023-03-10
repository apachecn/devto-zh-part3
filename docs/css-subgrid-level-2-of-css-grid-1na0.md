# CSS 子网格—CSS 网格的第 2 级

> 原文：<https://dev.to/ruphaa/css-subgrid-level-2-of-css-grid-1na0>

[![Title image](img/5041df57b0a332a401938fa9f5f7f718.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f_2Gc7KC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k7bu3vqcyt3y2u226pdk.png)

网格布局即将发生惊人的变化。CSS 网格的第二级规范，叫做 **CSS 子网格**，即将登陆浏览器，它将把已经很棒的 CSS 网格提升到一个全新的水平。

## 有什么大惊小怪的？

最近，我看到了 Rachel Andrew 的文章，她介绍了 CSS 子网格，我对 CSS 子网格带来的可能性感到非常兴奋和激动。

简而言之，CSS 子网格使得在 CSS 网格中构建嵌套网格成为可能。

现在(没有子栅格),嵌套栅格的轨迹与父栅格上的轨迹没有关系。简单地说——我们有一个带有`display: grid`属性的父容器，因此它的子容器成为网格项。这些网格项也可以成为网格容器。但是**为父网格容器定义的网格轨迹不会与子网格容器的网格轨迹对齐，因为它们都独立于其内容的布局**。**在某些情况下，多个网格项的内容和布局需要相互对齐。**

使用 CSS 子网格，**一个网格容器可以从它的父网格容器**继承它的行和列的定义，通过使网格项目成为子网格。子网格能够将网格的属性传递给嵌套元素，并将基于内容的大小调整信息传递回父网格。

这是 CSS 子网格的两个主要优势—

*   子网格可以使用在父网格上定义的网格轨迹，用于行、列或行和列。
*   子网格也将继承父网格的间隙。子网格中项的大小可以改变父轨迹的大小。

这是一个没有子网格的嵌套网格如何工作的例子—

[https://codepen.io/ruphaa/embed/gyzPBY?height=600&default-tab=result&embed-version=2](https://codepen.io/ruphaa/embed/gyzPBY?height=600&default-tab=result&embed-version=2)

但是，如果我们希望嵌套网格项目的轨迹与父网格的轨迹对齐，该怎么办呢？

这就是 subgrid 的用武之地。当我们将属性`grid-template-rows: subgrid`添加到嵌套的网格项时，它们继承了父网格的 row 属性，并自动相互对齐。

如果应用了该属性，它应该是这样的。

[![CSS Subgrid](img/a1334024ad9e66a80e08b6553d0de1a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3m84I6gH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/2Pr78Mk/Screen-Shot-2019-04-20-at-7-30-26-PM.png)

注意:目前，浏览器不支持子网格，所以我创建了一个插图来解释这个行为。

## 为列和行的子网格

在这种情况下，子网格将位于列和行上。这意味着两个维度都与父栅格的轨迹对齐。
[![Subgrid-Column-Row](img/bc7a9d60f825de10b35443dd46385dab.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--PTB9oonG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/4WnLP9N/subgrid-row-column.png)

## 仅用于列的子网格

在这种情况下，subgrid 用于列，这意味着在网格项中将有一个单独的行隐式跟踪，但是它的列跟踪与父网格对齐。
[![Subgrid-Column](img/7ef482d82a1bcd32d1607988d650697d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--X60MYxCY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/6BnvdcG/subgrid-column.png)

## 仅用于行的子网格

在这种情况下，子网格应用于行，它与父网格的行轨迹对齐，但它的列大小与父网格无关。
[![Subgrid-Row](img/5ca6fb17f8669b4c39c208b010a67232.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--cWM2YPPp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/crDPjkh/subgrid-row.png)

## 资源

[CSS 网格第二级:下面是子网格](https://www.smashingmagazine.com/2018/07/css-grid-2/)
[网格一路向下](https://noti.st/rachelandrew/g2qkOz/grids-all-the-way-down#s12eg0L)
[CSS 网格布局模块第二级](https://www.w3.org/TR/css-grid-2/)
[为什么我们需要 CSS 子网格](https://dev.to/kenbellows/why-we-need-css-subgrid-53mh)

* * *

我急切地等待浏览器支持子网格*(希望很快)*，这样我就可以试验它，找出有趣的用例。

反馈？留下评论:)