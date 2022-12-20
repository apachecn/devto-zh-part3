# 美丽的拉勒维尔的建筑构件

> 原文：<https://dev.to/__mfarag/building-components-in-the-beautiful-laravel-2b0g>

在美丽的 **Laravel** 中，我们有这么多有用的工具来缩短开发时间，并帮助构建可扩展的现代网络应用。在这篇技术笔记中，我将引导你完成构建和使用**组件**的过程。

#### 1-Laravel 中的组件有哪些？

组件是一组可重用的元素。比如，你可能想创建一个导航条，用于几乎所有的网络应用页面。所以你把你的 Navbar 构建成一个组件，并告诉 laravel 在你需要的时候获取它，想用多少次就用多少次。

[![step by step](img/5e7832ef1c50f2e70fb4e019feee9a7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NjXRqCsI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.pinimg.com/564x/9c/3d/5b/9c3d5bb9a5f2486512ab65c48a9f04cc.jpg)

### 2-如何使用组件构建&？

*2-1*
**首先**让我们在一个 *Laravel 项目*中的 Resources 目录下创建一个新目录，这样在你这边就会有类似这样的结构**(资源/视图/布局/组件)**。

*2-2*
**将**制作成一个新文件，并将其命名为(**navbar.blade.php**)并保存在您刚刚在步骤 *2-1* 中创建的“**组件**目录中。

*2-3*
**用**的任何 IDE，“也许[升华](https://www.sublimetext.com/)”，请将下面的 bootstrap Navbar 代码复制粘贴到你的文件**navbar.blade.php**

> *注意:我已经删除了阅读时间的大部分导航条标签，但是你可以在[引导](https://getbootstrap.com/docs/4.2/components/navbar/)T3】找到完整的标签代码*

*代码*

> `<nav class="navbar navbar-light bg-light">
> <a class="navbar-brand" href="#">Navbar</a>
> </nav>`

*2-4*
**我们已经**创建了组件目录，navbar 文件作为我们新的组件示例，最后将代码添加到**navbar.blade.php**。现在是时候使用它了。我先给你简单的方法，然后再给你专业的方法。

*(简单方式)* :
在你的项目的任何一个页面里面让我们假设它是**index.blade.php**并且确定它在'**视图**'目录里面，请开始使用下面的 blade-directive。

*代码*

> `@component('layouts.components.navbar')
> @endcomponent`

你做到了！！，现在您已经成功地创建并使用了一个 navbar 组件。但是如果你注意到这个导航条完全是硬编码的，这对于构建一个动态项目来说并不好。所以让我们在 navbar.blade.php 的**添加一些对导航条标签的修改。**

*2-4-2*
T3】我将添加一个名为 barndName 的变量，请注意以下代码修改:

*代码*

> `<nav class="navbar navbar-light bg-light">
> <a class="navbar-brand" href="#"> {{$brandName}} </a>
> </nav>`

*2-4-3*

*代码*

> `@component('layouts.components.navbar')
> @slot('brandName')
> value and it could be anything
> @endslot
> @endcomponent`
> 
> 请注意新的指令 **@slot** ，我曾经把我在 navbar.blade.php**创建的**变量**叫做**

**一种更专业的方式**
**我们将**对我们刚刚学过的方式做一点小小的修改。相信我，这并不像听起来那么难。跟着我的脚步走

*2-4-4* 让我们开始吧。

*2-4-5*

请转到以下文件:**(app/providers/appserviceprovider . PHP)**，转到函数 **boot()** ，然后在函数体内添加以下代码

*代码*

> `Blade::component('layouts.components.navbar','navbar');`
> 
> 括号中的第一部分是组件的位置，第二部分是你想用来调用它的名字。这个过程叫做**混叠**。
> 现在在你的 webApp 的任何位置，你只需用 **@navbar** 调用你的组件。
> 
> 别忘了在你的【appServicesProvider.php】**里面导入**刃面**。**

 **或者只在类名前添加这一行。

*代码*

> `use Illuminate\Support\Facades\Blade;`

*2-4-6*

现在，您只需编写以下代码来调用您的 navbar 组件并向其注入数据。

*代码*

> `@navbar(['brandName'=>'Munch any value']) @endnavbar`

**哇，你刚刚调用了你的组件并将数据注入其中**。
请注意以下事项:

> 我们将数据作为一个**关联数组**注入。['名称']= > ['值']对。
> **名称/键**必须与组件中的变量具有相同的拼写，查看步骤 **(2-4-2)** 。
> 如果您在组件中定义了更多的变量，只需将它们添加到您的数组中，如 **['var1'= > 'val1 '，' var2'= > 'val2']** 。

*2-5*
**现在**你可以轻松地构建自己的组件，并在任何一个 Laravel 项目中重用它，只要你需要组件，随时随地重复这个过程

*3*
**感谢您抽出时间**，希望我对您有用。如果你喜欢我的技术说明，请反馈给我或给我一些如何改进的建议。**