# 重构真实案例

> 原文：<https://dev.to/carlillo/refactoring-a-real-case-7pj>

* * *

*最初发布于[www . carloscaballero . io](https://www.carloscaballero.io/refactoring-a-real-case/)2018 年 12 月 28 日。*

* * *

由于发布了[打卡/下班
系统](https://carloscaballero.io/clock-in-out-system/)的系列帖子，我需要
编写一个函数，使用 ORM (TypeORM)将数据从一个大的 XLS 文件导入到 Postgres 数据库
。

这个函数很快就被写成了一个脚本，没有过多考虑
代码的质量，只是简单的“工作”。然而，一旦系统开始工作
并且测试套件可用，通过持续的
重构来改进代码是一个好主意。

接下来，我将展示不同的重构步骤，我将这些步骤应用于这个
函数，以提高代码的质量。所以我们
要改进的功能先展示出来。

[![](img/ae223083f8af4267b19623ff19627cef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OCFBWZ_O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A0HhII86DZiJuAzYY89R9nA.png)

*   **normalizedUID** :该函数用于生成每个教师的姓名/uid(字符串)。
*   **getDaysByTeacher** :返回一个数组，其中每个位置代表每个用户的一天。每一天也由一个数组表示。因此，这个函数返回一个数组的数组，在这个数组中我们已经组织了每个用户的每周工作时间(固定时间表)。
*   **factorySchedule** :从另一个由房间、小时和天数组成的对象创建一个时间表对象。

[![](img/551f86dd7edfe57e1ec31840a95b9552.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ziFSulOr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AOCkqygyRWkOkWJ_MZdVuDA.png) 

提取函数后，主要代码如下:

[![](img/541cc5064c70ee6efc8e22dbf540b151.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h19vNIQV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A6HlohEq2Vc6kJF5oZLIB8g.png)

在进入重构的下一阶段之前，我们将删除使用问题领域理解的
变量，同时也将删除
临时变量。

[![](img/4ae379889fb1bac5ad924c8848c11d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GhqEfQqq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AF6BiwJoXb70BWrH1JSGzwg.png)

此时，我们看到我们正在利用 **JavaScript 的最强大的
特性之一，作为 **forEach** 方法中的参数**。
但实际上， **forEach** 方法只是作为语法糖对抗
命令式编程的经典 **for** 。因此，我们将
根据每个 **forEach** 方法执行的
任务，用最合适的方法修改每个**forEach**方法。

我们要处理的第一个 **forEach** 循环如下:

[![](img/a87153376d410aba7c119a00aebf8a42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2_N0RIrI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Aud0fKT3iNVcO8qcADMZSzw.png)

这个循环可以转换成方法 **reduce** ，因为我们希望
在遍历每一天之后得到一个单一的值(**一个数组**)。
另外，可以省略 **schedule** 变量，可以直接调用
**factorySchedule** 方法，因为这个
变量的创建不会在代码中增加语义值。

[![](img/a1db7ddb24ec72a951f18f2a409536d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vg_GCxkG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2ACV2RwATNgzPJ1RrCzMEvHQ.png)

如果我们观察在方法 **reduce、**的主体中执行的函数，它是
一个可以转换成三元运算的 if-else，我们可以将
与潜在的不变性(函数式
编程的另一个特征)结合起来，留下如下代码:

[![](img/73328bcf3ed33b76f5e05a29418f7d33.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Qzkl_ur--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AIPhlkpB_1l5dqtljqQfACQ.png)

重构中的另一个小步骤是在使用 JavaScript 的胖箭头(lambda 函数)时省略 return 的显式调用
，留下如下的
代码:

[![](img/dac75c9ee6431f84f451ec8df8d174ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s2z1iLAo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AHa-yyz_dcAxU8Y87pGqXLg.png)

如果我们看看代码，下一步自然是用另一个方法 **reduce** 替换包含 **reduce** 的 **forEach**
方法。由于我们使用了
fat-arrow 和 spread operator (…)的特性，生成的代码可能看起来
很复杂，如果您以前从未使用过的话，这些特性可能看起来
很抽象(我们将通过创建允许您分解
任务的函数来解决这个问题)。

[![](img/e6905e0f829cacec0fa6c647f8b30f11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qqe1oePw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AFQiTCy4yAv3XV5U_q31PZQ.png)

因此，下一步是构建小函数来执行任务，即在每个**减少**中完成
。但是我们有一个小问题，我们需要
向 reduce 方法的每个回调传递额外的参数。这个
问题很容易在 JavaScript 中用不同的技术解决，比如
覆盖作用域(This)，使用更大作用域的变量，或者你自己的
，比如**绑定**，**应用**或者**调用**方法。

在我们的例子中，我们将创建几个小函数，它们与 bind 方法结合使用，将解决在回调中传递参数的问题。
以这种方式，直到此刻产生的代码将如下:

[![](img/d0657152bc45187d6fe83ad6f4274b83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yrdNI5j2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AH4bqWhBeAcSAA3prCDWH5w.png)

下一步是处理包含整个
主方法的 **forEach** 方法。这个方法的消除将允许我们消除两个充当累加器的
变量(**用户**和**调度器**)。以这样一种
方式，方法 reduce 将接收一个数组作为累加器，该数组
包含两个数组(**调度器**和**用户**)，这两个数组将是非结构化的
，以便能够在 reduce 方法中容易地连接。

重构代码后，我们发现以下版本:

[![](img/6f9b70c817b131e11ff805a8ac76c7c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gSVvzHfs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AOWPC3Ddr96u94EejCCK33w.png)

与前一个案例一样，方法 **reduce** 接收到一个执行 JSON 转换的
回调，最后我们
将文件的内容重命名为一个名为 xls 的变量。所以部分结果
如下:

[![](img/b9fb77d0d7088518db926028e7a67c96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pY7-_T1A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2ASLCL-CRNY_ZXlwX4__XhBg.png)

最后，进行这些更改后，脚本的结果如下所示:

[![](img/c6bf6504f726dae728c393f9484e698a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EpAmxe_a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AzK0E804pPOItRWIt0K14Xw.png)

摘要

1.  提取函数。
2.  函数和变量的名称必须是一个标识值。
3.  使用内置的语言方法供您使用(**forEach**against of**reduce**)。
4.  将代码的复杂性和细节抽象为较低级别的辅助功能。

* * *

*最初发布于[www . carloscaballero . io](https://www.carloscaballero.io/refactoring-a-real-case/)2018 年 12 月 28 日。*

* * *

嗨！我叫卡洛斯·卡瓦列罗，我是博士。来自西班牙马拉加的计算机科学。教开发人员和学位/硕士计算机科学如何成为专家！