# 从角度理解内容投影

> 原文：<https://dev.to/angular/understanding-content-projection-in-angular-2ld9>

### 内容投影，是什么？

> *内容投影(也称为 transclusion)是一种从组件外部导入 HTML 内容
> 并将该内容插入组件模板
> 中指定位置的方法。*

因此，使用内容投影，您可以获得下图所示的结果。

[![](img/00fce30354d4c2af63f4597368fca251.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HKmLqrdx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2A4WsQ3ww9rk2K7zmY.png)

请注意，该组件像镜子一样工作。另一个更复杂的例子
如下图所示，其中配置了一个带有用户数据的字母
。

[![](img/eae9c10178a603524745a2308900eda2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8-ezy0YN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AK-a_YvW5BmDIqe2c.png)

我看到一个容器，那是什么？

> Angular ng-container 是一个分组元素(语法元素),它不会干扰样式或布局，因为 Angular 没有把它放在 DOM 中。

我们可以使用**结构指令**。

**结构…什么？**

> *结构化指令通过添加、删除或
> 操作 DOM 元素来改变 DOM 布局。*

然后我可以…？

[![](img/e368fd3aaa3f5731f0d731137bc95ede.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5gliJhQV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AVoCmlu_pAqH6XFMX.png)

其中`am-icon`是以下组件:

[![](img/ea4753d85278dbdd21f0dd9c57ecb642.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DZJFsK_4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AGPArrg3qDWVEsM2L7hHkzA.png)

结果是一个图标，而不是四个，正如我们所料。💔

[![](img/362440ed67c03df389ded1047c868a60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---M_1G-uE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2Ahi2BHFo3lj7A15Yv.png)

### ng-content 是如何工作的？

*   **ng-content** 不产生内容。它只是投射现有的内容
*   期望的一致性👀
*   表演🔥

*   根据前面的规则，它既不能创建也不能破坏计划的组件(生命周期)。
*   只有最后一个 ng-content 投影内容。

还有……我能做什么？ **这里 ng-template 来了救兵……**

[![](img/d3392730d9cec1d565d2b072a523f074.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9IfoOt5l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2ATdpQI3h19CwsHolb.png)

**什么是 ng 模板？**
顾名思义，它是一个模板元素，
是一个可以实例化的模型，因此可以将模板设置为组件的
输入，这是非常有用的。

**星号(*)语法**

> 星号是稍微复杂一点的语法糖。

你通常会看到*ngIf，*ngFor，*anyDirective，…

[![](img/17b1f81d9c239082f42589a1eb6f61b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0ezN4DXO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AIt_YBGVJBxRvIdqC.png)

**那么我们的重复成分呢？**

[![](img/74dc2957e650fc522d9a72aba5d428a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z7qi2P90--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AXI4UgqiHedvIr33D.png)

[![](img/f12ecc63423a7c88de6f2f26301fa66c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZmXZqDJR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AvzylM6hLR6skusX3.png)

给我解释一下 ngTemplateOutlet 是什么！

指令**ngtemplateeoutlet**接收一个 ng-template，它负责
创建实例并将其插入 DOM。

[![](img/4a35911403c907f7513fc3be5cba6c8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yydEnly1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2A0nf7-yJglL59RXkR.png)

### 耶，上下文像在 JS 里！

我们可以给实例一个对象，这个实例是由你的 ng-template 的
**ngTemplateOutlet** 创建的。该对象可以包含您想要传递给模板的任何内容。在这里，我们展示了许多库
组件的魔力(数据表、角度材料、ng-bootstrap 等等)。

### 我们可以做牛逼的事情！

[![](img/94e36b374fbbf1c401b9ec6befbadc0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kqLJj8ni--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2Apk5PbZUuW8FGwFmS.png)

### 越多越多……

*   [演示文稿](https://caballerog.github.io/ng-transclusion/slides/)
*   这个岗位的 GitHub 分支是[https://github.com/Caballerog/ng-transclusion/](https://github.com/Caballerog/ng-transclusion/)

*最初发布于[https://carloscaballero . io](https://www.carloscaballero.io/understanding-content-projection-in-angular/)2019 年 6 月 6 日。*