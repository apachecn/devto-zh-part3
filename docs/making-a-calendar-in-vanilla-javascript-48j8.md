# 用普通 Javascript 制作日历

> 原文：<https://dev.to/knheidorn/making-a-calendar-in-vanilla-javascript-48j8>

可以说，时间是 Javascript 中最好和最差的内置函数。在我最近的项目中，我有了制作一个低劣的谷歌日历的想法。我天真地认为，制作一个通用的、功能性的日历会很简单，并且让我有足够的时间来构建我的网站。我**所以**错了。

### 从哪里开始？

很早的时候，在导师的指导下，我决定在 HTML 中硬编码一个月。这让我可以为我的日历设计出我想要的总体结构和风格。关于使用`<div>`还是`<table>`会是更好的实践，有相互矛盾的文献。我最终决定用一张桌子。

如果你曾经花时间真正研究过月历，它的结构非常简单。通常在顶部有一个标题，表示月份和年份。下面是 7 列，概括了一周中的每一天。最后，最多有 6 行来填充给定月份的天数。

下面是我的硬编码 HTML 的快照。

[![Hard-coded HTML Calendar](img/ce6ee8c26f88a976fca0b2c58645a085.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jpa2Gxq9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vU5ethQ.jpg)

同样，这允许我修改 CSS 样式表来呈现我想要的日历的整体外观。

### 利用时间的力量

现在是困难的部分。我需要使用内置的时间函数来填充给定月份和年份中的准确天数。在做了一些研究之后，我了解到 Javascript 将返回一个数字，该数字对应于一年中月份的相对索引:即一月是 0，二月是 1，等等。这可以使用 Javascript 内置的`getMonth()`函数来调用。

Javascript 也对一周中的每一天做同样的事情，从星期天开始。这使得星期日为 0，星期一为 1，等等。这是使用 Javascript 中的`getDay()`函数调用的。

所以，问题变成了我如何使用这两条至关重要的信息:1。我如何知道一个月的开始是星期几？2.我如何计算一个月有多少天？

回答第一个问题很简单。 [Javascript 文献](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)帮助概述了内置函数的各种选项。不可思议的是，`new Date(year, month)`非常强大，会自动呈现给定年份和月份参数的第一天。在这里使用`.getDay()`函数允许我获得月初的日索引。

[![Start of Month Code](img/18736fc662c408f9c63fb6874418b3e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qF_hJgNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ZBTvzTd.jpg)

接下来，我需要获得给定月份的天数。我再次使用全能的`new Date()`开始，并使用一些高中代数，我逆向工程了一种计算总天数的方法。`new Date()`函数可以接受大量的参数，精确到毫秒。我不需要细化我的日历，但是我想我可以使用月、年和日参数。假设任何一个月的最大天数是 31 天，那么在`new Date()`中输入 32 天就会得到下个月的相对日期。例如，七月(一年的指数 6)一个月有 31 天。如果我将`new Date(2019, 6, 32)`输入我的主机，我会期望主机返回 2019 年 8 月 1 日。
[![July Snapshot of Console](img/1be0cd784513b91cf66001c9e96cb366.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q93RNvOz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/NyFABME.jpg)

`new Date()`函数还有一个`getDate()`函数，它将返回给定日期的天数。使用这个返回的数字，我将它从 32 中减去，得到给定月份中的准确天数。

[![End of Month Code](img/69473f459a261a6cf8564328ee3402ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ca6W3NIv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/eDSgUJP.jpg)

为了进一步证明我的概念，我在我的主机中使用了 2019 年 2 月。将 2019 年作为年，1 作为月，32 作为天，我应该预计上面的等式会返回 28，因为 2019 年 2 月有 28 天。

[![Feb Snapshot Console](img/d601446a514f7e0acbc4684149e57af9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8XUNzvrR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/AW2bhi7.jpg)

一个障碍过去了，现在我必须使用这些数据来填充和呈现我的表。

### 普及野兽

从表中删除硬编码的 HTML 数据后，我需要用当前呈现的月份和年份填充*月份*跨度和*年份*跨度。这相当简单，因为我将大部分 HTML 代码留在了标题中。在使用`document.findElementById()`找到各个跨度元素之后，我将`textContent`更改为给定的*月*和*年*。

[![Populating Month Year](img/78daa2d908943fed28c4e01aabdc6ce9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K57uHgdL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fzJ0ZJR.jpg)

在抓取了 table 元素之后，再次使用`document.getElementById()`，我需要添加一些`for`循环来呈现我的行和列。我也知道我需要在日历上打印实际的日期。出于明显的原因，我从 1 开始计数，并将这个 1 赋给一个叫做`renderNum`的可用变量。(注意:这个变量将随着每个列循环递增 1。但我一会儿会谈到这一点)

[![Get The Variables](img/febb9e3ed682f0b4fa2a41c90ce225cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1M2ctNG6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/CGvy8GC.jpg)

接下来，我生成了 row `for`循环，因为它将保存所有单独的日期块。(更确切地说，我将把我的表数据`<td>`元素追加到我的行`<tr>`。)因为我知道我最多需要 6 行，所以我从传统的 0 开始我的`for`循环，并在 *i* 到达 6 时停止。由于该行没有文本内容，所以我需要做的就是创建`<tr>`元素，并将其分配给一个可用的变量。

[![First For Loop](img/819ed6fb66a85febb7fcbe0b29174341.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4dUh5GpV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/8Zne8uO.jpg)

如前所述，我想在每一行中添加 7 个表格数据元素。这意味着我需要一个嵌套的`for`循环。这个第二个`for`循环也从 0 开始，当计数(本例中为 c)达到 7 时结束。这很简单，但是下一步需要一些逻辑推理。记得`new Date(month, year).getDay()`给了我星期几的索引，我需要设置空的`<td>`以便计数和日历呈现在正确的日期开始。为此，我在我的列`for`循环中添加了一个`if`语句，它将检查: <sub>1。</sub>循环在第一行(又名`i===0`)，而 <sub>2。</sub>计数循环值小于`new Date(month, year).getDay()`值(又名`c < startOfMonth`)。

如果这两个条件都为真，我希望循环创建一个`<td>`元素，向新呈现的`<td>`添加一个*空*类，并将其追加到当前行元素。

[![Empty Column Data](img/da18b811221cde46a4753dd3e3636800.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ayN09Ej7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/scpykiX.jpg)

接下来，我想确保我在每个新的表数据元素上填充了正确的日期。修改我的*空 td 元素*方法，我为每个新的一天添加了一个`<td>`元素，添加了`textContent`，并追加了行。这种情况下的`textContent`将是前面定义的`renderNum`变量。为了确保`renderNum`打印出正确的日期，我将变量增加了 1。

[![Populated Column Data](img/fc8a5d84c1b61feffaecdf3b1b8fddf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qu5NSw0_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ehYSSyh.jpg)

利用*真实度*的力量，我使用了一个`else if`语句来阻止日历增加和添加超过当月最后一天的数据。

[![Else If Statement](img/f827f52fb3b32984d38e69b8a5d3643b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1VgRFHNE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/IRQMv0B.jpg)

一旦我到达了`break`并脱离了`for`循环，我需要确保用新形成和填充的行追加表体。(*我确实根据变量和我的个人偏好重新排列和分组了代码。显然，这是没有必要的。这只是个人喜好。*)

[![Final Code](img/f49189c657ee877eae8224bc169b4474.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1sIcrk7w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/c1kWv4W.jpg)

最后，我在点击箭头时添加了`EventListeners`,以切换到日历快照前后的月份。鉴于这篇文章已经很长了，我就饶了你。但是如果你在这一步有困难，我推荐阅读大量关于 T1 T3 的 T2 文献。

下面是呈现的日历的快照。一旦项目完成，我一定会用 github 链接更新这篇文章。

接下来，约会，时区和日程安排。祝我好运！

[![Final Calendar](img/a6dab2128d850fde7e336d74064b813d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XoTlnZn2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/3dlwWWo.jpg)