# Patrón 模板方法

> 原文：<https://dev.to/mangelsnc/patron-template-method-19m4>

[![](img/9cd93aa0442e26e0da61f9e12ce1ea62.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7ZycNyg---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AijO7IgrrLENhUgmkKipRUw.jpeg)

### 导言

前几天，我和一位同事一起复习一个项目，当我们开始看一系列有点奇怪的课程时，他将开始与我合作:每个课程都有一段代码，在所有课程中都是完全一样的。

这种代码重复不是偶然的:这些类中的每一类都使用第三方库来执行基于策略的输入数组验证，从而帮助定义这些策略并检测错误。然后，它会处理验证错误以与所有验证错误形成例外，或传回 true，表示没有违反验证条件。

看起来很像这样: