# 蛋糕是个谎言！

> 原文：<https://dev.to/thekashey/the-cake-is-a-lie-3e54>

所以，让我们想象一下——你有一个朋友，他没有被计算机科学玷污，从未尝试过自动化，从未玩过 Factorio，从未写过一行代码。

所以，让我们想象一个正常的人类。

让我们称他为比尔。

[![bill](img/9d0f883efddb59d64cc87ec8e8526f4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kp-eGbBN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AesRUHR89un2peZ7drOdcLA.png)

他数学不是很好，只是“不好”，但是**他爱吃糖果**！

你的任务是教比尔一些你每天都要做的神奇的事情。最简单的。破译秘密知识。

你该怎么办？

# 喂他！

[![Feed with a cake](img/086fa31987be8c069cac31d185cf4b48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8C2s9Gks--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AUMLY_xPgspHUj8l1X4sfnw.jpeg)

长话短说，但是有一天安德烈·亚历山大问比尔:

> —安德烈:嗨，比尔，你想要块糖吗？比尔:是的，当然！

然后比尔拿到了糖果，从糖果包装中取出糖果，吃下了 T2 的有效载荷。
简单？而且我们用了 2 个*魔咒*！

===

仅仅几天后，沃思问比尔:

> —沃斯:嗨，比尔，你想要块糖吗？比尔:没错！当然可以！嘿，我还有一个，你也想要吗？
> —比尔:是的！当然可以！

这是一个**条件控制循环**，或者说是一个**循环**。

> —沃思:嘿，我还有一个，你也想要吗？比尔:没错！当然可以！

...但是没有糖果，比尔得到了一个普通的空指针异常。

* * *

小心——如果 Alexandrescu 突然回来，继续给 Bill 喂糖果——就会发生**缓冲区溢出**。只要比尔总是接受新的糖果。

[![buffer overflow](img/ed93138e84941a55f28cc3e08c268dbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---udMwJEh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Aa8DoAqe6ThdY3EVUq-2SUQ.jpeg)

* * *

第二天沃思准备好了。他有一大包糖果。

> —沃思:好的，让我检查一下我的包……沃思:我还有一颗糖果。比尔，你想要一块糖吗？比尔:是的！当然可以！

在这种情况下，沃思将提供一个糖果，如果他有一个。这里我们得到了一个有先决条件的**循环。**

* * *

但是有一天，比尔来到沃思的家，喊道:

> —比尔:给我一颗糖！没有什么是永恒的！你什么都吃了！
> —比尔:但是无论如何我都要拿！！！
> —比尔:你有糖果吗？？？

这是一个带有后置条件的周期。悲伤的故事。感谢上帝警察就在附近。

[![no food](img/f27051258b7660263242557d34f1ee35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6R6QgsGj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AKfTBl4hAMEMlJMQvJi89JA.jpeg)

* * *

几十年后，丹·阿布拉莫夫来到比尔面前，把他所有的糖果都放在桌子上:

> —丹:无语的凝视着比尔。比尔:无语地盯着第一颗糖果
> ——丹:比尔，你想在这些糖果上放一颗吗？
> —比尔:是的，当然。

## 那是一个**的集控回路**。

就在这之后，丹拿了另一个袋子，把所有的糖果放在桌子上，数了数，然后放回袋子里。

> —丹:比尔，看起来我还有一颗糖果。那么，你想吃糖吗？比尔:是的，当然。

这次是一个**计数控制回路**。

## T2】

但比雅尼·斯特劳斯特鲁普迟早会来喂比尔。

> —比约恩:比尔，你想要颗糖吗？比尔:是的，当然。比尔，你想要一颗糖吗？
> ——比尔:— — —

比尔正在咀嚼糖果。他很忙，没有什么能打扰他。他抛出了一个互斥锁，线程被锁定，时间停止了…

## T2】

比约恩非常聪明，也非常有礼貌——他找到了一个解决办法:

> —比约恩:比尔，你想要颗糖吗？比尔:————
> ——比约恩:好吧，我会等你吃完再吃……

## 这里我们得到了一个**线程同步**。

好的，这次我们将呼叫彼得·诺顿。

> —彼得:比尔，你想要一块糖吗？比尔:没错！当然可以！彼得:我会提供一整包。你自己嚼吧。

于是比尔从袋子里一颗一颗地拿出糖果，一颗一颗地吃了起来。这叫作**后进先出**——后进先出。只要包底的糖果最后吃。
同时，比尔的消化是**先进先出**——先进先出。(抱歉，但你之前必须知道)。

[![You had to know it before](img/875fe99ae22c0eeecfb1bbbec19dddb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QieCUPcR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AYX9ijeMhcD2d1E2vTD-qqw.png)

* * *

唐纳德·克努特也加入了喂食队伍。但是有一天，他厌倦了给比尔买新糖果，于是让一家餐饮公司代他买。

> —唐纳德:嘿，我的朋友，你能在最近的商店里买最好的糖果并把它们送给比尔吗？这是比尔的地址和钱…

从那以后，比尔总是从唐纳德那儿得到新糖果。

在这种情况下，餐饮公司是一个**功能**，地址和钱是**参数**。而一颗糖果就是一个**返回值**。

> 如果，对于相同的地址和相同的钱数，比尔将总是得到相同的糖果——这将被称为**纯函数**。

[![minion!](img/0d5f09977e593ac5926ef3f83355ca81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lq2lspLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AFUDllp1SIMbDUl98pn7VOQ.jpeg)

但是丹尼斯·里奇甚至更聪明！

> —丹尼斯:嗨，比尔，你想要块糖吗？比尔:你知道的！丹尼斯:这是地址。你会在那里找到你想要的糖果。

那是一个**指针**。

但是，当比尔到达那个地址时，他发现了一个秘密军事目标。不管怎样——他得到了一个地址，并试图不惜一切代价达到他的目标，但是，你知道，安全就是安全。
[![violation](img/306d0f00759a312d63de480508f20e0f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--eiBnP2sB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AFNg5NUuHvpsAlCv0dxWTBw.png)

几个月后，他越狱后，试图重复这一尝试，但再次未能达到"目标"。有什么东西在远处阻止了他——无形的、不可摧毁的**边界检查墙**。

* * *

但是有一天…甚至，说实话——晚上——网络恶魔来到比尔的家，大喊:

> —网络恶魔:要不要来颗糖？比尔:是的！在白天，然后星星闪耀！永远！

所以，比尔又得到了他的糖果。但这是一个有点悲伤的故事，只要他忘记检查有效载荷的类型或执行任何其他**数据验证**。☠️不信任守护进程。

[![fire!](img/92913280642fbf20c2ff0f9321c44e49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3x7EWNKC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A1Y2a8P7rK4sOSF00kL8COQ.jpeg)

* * *

在那之后，比尔在虚空中醒来，听到:

> —宇宙:比尔！你想要一颗糖吗？比尔:是的！
> —宇宙:比尔！你想要一颗糖吗？
> —比尔:是的！
> —宇宙:比尔！你想要一颗糖吗？
> —比尔:是的！

## 这就是 **ping** 的工作原理。

在具有非零**延迟**的分布式系统中，情况可能是这样的:

> —宇宙:比尔！你想要一颗糖吗？
> —宇宙:比尔！你想要一颗糖吗？
> —宇宙:比尔！你想要一颗糖吗？
> —比尔:是的！
> —比尔:是的！
> —比尔:是的！

但是这种情况只对 TCP/ **UDP** 有效。在 TCP/ **IP** 的情况下，它可能会改变

> —宇宙:比尔！你想要一颗糖吗？比尔。你想要一颗糖吗？
> —宇宙:比尔！你想要一颗糖吗？
> —比尔:是的！是啊！是啊！

因为长的兄弟分组将通过时间或大小因子( **MTU** )来连接。

[![ping](img/3c3394582352508247c8fd435bd896e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ddn6M4Rc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AP0hnvFZhcMew_dvQuLwICQ.jpeg)

# 结束

我有许多尚未披露的故事，比如比尔和四人组，或者**非跨浏览器糖果**。让我来讲最后一个，关于不可分割的糖果:
比尔吃了几百次，但它还是会回到他的手里，*没有嚼过*，甚至仍然是甜的！
所以`Indivisible candy`可能是 **singleton** 也可能是**内存泄漏**，只要垃圾收集器消化不了。还有比尔。

[![Have a good time, Bill](img/8c566528fcf79935b338328d187f69e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YaJDUzZU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AmIjltkQfakAxh4SZHP5d3w.jpeg)

那么，**你能描述一下其中的一些东西吗**？从抽象的计算机世界用一颗糖，一块蛋糕或其他东西…

> 最初的文章“The Val's Candies”是在 8 年前发布的，从那时起，它获得了许多新的用户评论示例——递归、委托、接口、堆栈溢出、过程与函数、类、B 树等等。但是...在另一种语言和另一个维度。

[![foood](img/de889f6a51f30555d9095624b6c1b4db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vICJ4Oxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ARCd0hPQUkAbtHutD3jM2VA.png) 
比尔仍然很饿，他快饿死了。**你得喂他。给你。在评论里。**