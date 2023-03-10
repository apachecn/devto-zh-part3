# 流与类型脚本从流到类型脚本。为什么？

> 原文：<https://dev.to/wemake-services/from-flow-to-typescript-why-1k7m>

**最初发表于我的博客**:【https://sobolevn.me/2019/03/from-flow-to-typescript T2】

这一切都开始于大约两年前。我讨厌我的应用程序中不断出现的愚蠢的 javascript 错误，比如`undefined is not a function`。所以，我决定添加可选的静态类型。

两年前，javascript 领域完全不同。`Flow`和`TypeScript`都有很多缺点:糟糕的库支持、从没有到几乎没有 IDE 支持、类型问题和限制。我选择了`Flow`,只是因为它更容易起步。插入你的 [`.babelrc`](https://github.com/wemake-services/wemake-vue-template/blob/9dc664c36f30c610ee5bdaecfb586968a3ed57bf/template/.babelrc#L8) 开始工作。

大约 6 个月前，我决定将我们所有的前端项目从`Flow`转移到`TypeScript`。在我的脑海里，这是一场艰苦的战斗。所以，我决定把它写下来，帮助其他人选择合适的工具。不要犯我的错误。

这些工具非常相似，都为普通 javascript 提供了类型安全。这篇文章[不是关于类型](https://github.com/niieani/typescript-vs-flowtype)或者结构子类型和名义子类型之间的区别。它更多的是关于这些工具的当前状态。

你很少因为类型而更换打字工具。

## 炒作

我将从所有技术决策中最重要的方面开始。是的，这是关于炒作驱动的发展。

[![Hype ORLY](img/731695aa1aef52bff23d1feced105ad9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ny1zT1RU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8lx11zrlckdw8vltbv8j.jpeg)

请不要这样做你的脸。我会解释它是如何工作的，也许你会改变主意。

我经常[谈论](https://sobolevn.me/talks/)我们的堆栈，写关于它的文章，等等。每次我说我们在使用`Flow`时，其他开发者都会问我:“为什么不用`TypeScript`？每次我都要解释我的选择。深入细节、历史和工具。有时甚至解释当前项目的状态和我们的工作流程。当你不主张某事时，这不是一次愉快的经历。我只是个用户。我真的不喜欢其中一个。一遍又一遍地进行这样的对话感觉很奇怪。

此外，我们为客户提供服务，[雇佣](https://wemake.services/meta/rsdp/job-application/)其他开发人员。他们中的一些人真的想和`TypeScript`一起工作，不想和`Flow`一起工作。为什么？因为他们听说了`TypeScript`以及它有多棒。而围绕`Flow`的炒作列车并没有那么海量。

> 我:嗨，我们在这个项目中使用 X，Y，Z 和`Flow`
> 
> Dev: `Flow`？为什么不是`TypeScript`？
> 
> 我:哦，开什么玩笑？🤦

如果你不想对抗这种炒作列车，但让它为你工作，最好是靠边站，使用什么是炒作。请记住，你目前的工作流程没有大的变化。

## 基础设施

[Vue 3.0 会开箱支持](https://medium.com/the-vue-point/plans-for-the-next-iteration-of-vue-js-777ffea6fabf) `TypeScript`，Nuxt 已经支持`TypeScript`。有很多项目将类型与源代码一起发布。`axios`、`vuex`、`vue-router`、`lodash`不一而足。

`Flow`支持呢？Vue 目前使用`Flow`进行打字(从 3.0 开始切换到`TypeScript`，但是这些类型只是开发用的。您不能在自己的项目中使用它们。

也许有不同的类型？是的，`Flow`有自己的类型库。问题是安装类型是一个全新的额外步骤。您必须配置`postinstall`钩子，以确保在每次`npm install`调用后类型也被重置(是的，它们使用`git rebase`)。

当你深入研究`flow-typed`包时，你会发现它主要是面向反应的。`Flow`甚至在标准库中有很多 React 原语。我觉得很奇怪。

但是 Vue 具体类型呢(既然我们大量使用 Vue)？是的，你可以找到一个人维护的 [`@vue-flow-type`包](https://github.com/sobolevn/vue-flow-typed)。可悲的是，我就是这个单身的人。我真的厌倦了为几个流行的项目维护类型。正如你所想象的，会有错误、类型改变、新版本等等。

为我赢得这场比赛。它的分配系统不会强迫我做额外的工作。装个东西就行了。因为`types/`子文件夹是通过`npm`和源代码一起发货的。不需要额外的步骤。库作者维护`types/`文件夹以及代码库的其余部分。他们可以确保一切正常。

## [这里](#ide)

让我们讨论一下 IDE 对`Flow`项目的支持。或者我会说“没有 IDE 支持”。

这没什么大不了的，我可以只用`nano`来写我的代码。但是我在[文字编辑](https://github.com/sobolevn/dotfiles)里度过了我生命中的大部分时间，我希望他们友好。可悲的是，`Flow`所有主流 ide(和文本编辑器)的插件都有问题并且不可靠。例如， [VSCode 插件](https://github.com/flowtype/flow-for-vscode)根本不工作。

并且同时 VSCode 以一流的`TypeScript`支持著称。具有智能感知、类型检查和自动完成功能。

[![vetur plugin for vscode](img/966a4c6909cd85a4b9c368caf40db016.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1XwqOrIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.githubusercontent.com/assets/4033249/25200022/ea76fef4-251a-11e7-9e18-348b76b97424.png)

看看我们的 [VSCode + TS + Vue](https://github.com/wemake-services/wemake-vue-template/blob/master/template/.vscode/settings.json) 设置。

有了这个简单的特性，您的开发工作流程开始变得更加灵敏，并且反馈循环时间显著减少。

## 未修复的 bug

另一件破坏我`Flow`体验的事情是`Flow`本身未修复的 bug 数量。

例如，当你安装`Vuex`时，所有的`Vue`组件都被扩展了一个额外的属性，这个属性可以通过`this.$store`访问。问题是没有办法告诉`Flow`添加了`Vuex`。而[这个 bug](https://github.com/facebook/flow/issues/396) 是 2015 年开的，到现在 4 年了！

当然你还是可以自己写类型:

```
// @flow

import Vue from 'vue'
import type { Store } from 'vuex'

import type { State } from '~/types/vuex'

/**
* Represents our extended Vue instance.
*
* We just use the annotations here, since properties are already injected.
* You will need to add new annotations in case you will extend Vue with new
* plugins.
*/
export default class CustomVue extends Vue {
  $store: Store<State>
} 
```

Enter fullscreen mode Exit fullscreen mode

但是现在你要自己维护自己的类型。是否要添加`this.$router`属性？请自行添加。Nuxt 具体类型？你只能靠自己了。

与标准的 `TypeScript`方法
进行比较

```
import Vue, { ComponentOptions } from "vue";
import { Store } from "./index";

declare module "vue/types/options" {
  interface ComponentOptions<V extends Vue> {
    store?: Store<any>;
  }
}

declare module "vue/types/vue" {
  interface Vue {
    $store: Store<any>;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现有类型可以用特殊声明来扩展。图书馆的作者会为你做这些。还记得我说过的类型分布吗？这个特性使得发行版更好。

来自 2015 年的[第二个众所周知的错误](https://github.com/facebook/flow/issues/452)是你不能注释`this`，即使你必须这样做。有些库有奇怪的 API。有了`Flow`，你什么也做不了，打字在那里丢失了。但是有了`TypeScript`，你可以在每一个上下文中注释`this`的意思。对于许多使用案例来说，这是非常好的。

为什么这些 bug 没有被修复？我不知道。这些年来，他们引起了很多人的注意。很多人都想要这些东西，但是`Flow`团队并不认同他们对项目的愿景。他们发布他们想要的东西，而不是社区。

## 发布

谈到发布，我必须提到他们的政策:“只发布东西，让用户修改他们的代码”。这里是[发布历史](https://github.com/wemake-services/wemake-vue-template/pulls?utf8=%E2%9C%93&q=is%3Apr+flow-bin)和它对我的项目做了什么。几乎每次发布都会破坏我的代码。考虑到它是一个几乎没有代码的模板——这真的很可怕。

顺便说一下，`Flow`团队并不遵循 SemVer，他们只是增量发布。一次释放后`jsx`里面的`.vue`文件停止了工作。我无法在新版本中再次修复它。我走了一条懒惰的路:锁定了版本，并在这次事件后忽略了更新。

`TypeScript`有[明确的发布政策](https://github.com/Microsoft/TypeScript/wiki/Roadmap)，永远，社会各界广泛关注。长期维持会好很多。

## 结论

我们已经做出了选择，[对](https://github.com/wemake-services/wemake-vue-template/releases/tag/end-of-flow)和`Flow`说了“再见”。现在我们所有的项目和项目模板都支持`TypeScript`。我们不后悔！

顺便说一下，我们的模板真的很棒。它支持:

*   用于服务器端呈现和样板文件隔离的 Nuxt
*   到处都是类型脚本:代码、测试、配置
*   Jest 用于单元测试，TestCafe 用于 E2E 测试
*   开发和生产码头工人
*   涵盖项目各个方面的令人惊叹的文档

[试试看](https://github.com/wemake-services/wemake-vue-template)！