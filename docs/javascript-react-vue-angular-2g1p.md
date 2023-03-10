# JavaScript :关于框架 React/Vue/Angular

> 原文：<https://dev.to/gumi/javascript-react-vue-angular-2g1p>

用于前端开发的受欢迎的框架(库)有[React](https://reactjs.org/) 和[Vue.js](https://jp.vuejs.org/index.html) 以及[Angular](https://angular.io/) 。 简要介绍了这些框架在前端开发中的作用，以及三者之间的区别。

# 数据绑定和组件化

React、Vue.js 和 Angular 框架都将 HTML 元素( DOM )与数据相关联(数据绑定)，以便根据数据的变化动态组织页面。

DOM 的操作曾经使用过 jQuery (至今仍有不少使用)。 但是，如果摩登浏览器是支持的 ECMAScript 2015(ECMAScript 6)或更高版本，标准 JavaScript 也不难操作 DOM。

动态构成页面的内容的典型例子是单页面 APP ( spa )。 如果尝试用 jQuery 或标准 JavaScript 动态组装 DOM，则在处理新数据时，您将了解需要更改的 DOM 的现状，确定下一个状态应该如何，然后按原样更改 DOM 配置。 数据数量越多，配置组合就越多。 如果不注意编写代码，就不会有性能，或者每个浏览器的行为都有差异，所以很难很好地处理 DOM。

三个框架将 DOM 确定为模板，并将其与数据绑定。 然后，根据数据如何配置 DOM，要写入模板中。 这意味着 DOM 的配置将与数据处理分离。 此外，框架只查找需要更改的 DOM，并通过渲染差异进行优化。

然后，将 APP 显示的功能细分为各个数据的处理和模板汇总而成的组件。 把几个小组件组合起来，就能做成一个系统。 代码只要按每个组件确认即可，因此容易阅读，也容易维护。

三个框架提供以下功能:

*   **状態とビューの同期**

    表示状态的数据与视图绑定并同步。

*   **テンプレート機能**

    预先确定根据状态的视图是模板。

*   **再利用できるコンポーネント**

    根据功能划分组件，便于再利用。

# 三个框架的特征

前款介绍了三个框架共同的作用和机制。 接下来，来确认 3 个特征和差异吧。 试着总结在下表中了。 为了便于比较，有些细节都写得很粗略，请谅解。

|  | 反应 | 某视频剪辑软件 | 有角的 |
| --- | --- | --- | --- |
| Kaiyuanyuan | Facebook +社区 | 社区主体 | 谷歌+社区 |
| 初始发行年份 | [2013/07](https://github.com/facebook/react/releases?after=v0.5.0) | [2013/12](https://github.com/vuejs/vue/releases?after=v0.7.2) | [2010/10](https://github.com/angular/angular.js/releases?after=v0.9.1)(Angular . js)[2016/09](https://github.com/angular/angular/releases?after=2.2.0-rc.0)(Angular 2) |
| 最新版本 | [v16.8.6](https://github.com/facebook/react/releases) | [v2.6.10](https://github.com/vuejs/vue/releases) | 7.2.13 |
| function | 用户界面 | 用户界面 | 完整堆栈 |
| 路由选择 | [反应路由器](https://reacttraining.com/react-router/) | [检视路由器](https://router.vuejs.org/ja/) | standard facility |
| 适当的开发规模 | Small | Xiaozhong | big |
| 生态系统 | maximum | 中+有日语 | big |
| 学习成本 | middle | 略低~中 | tall |
| 编写代码的特点 | [JSX](https://reactjs.org/docs/introducing-jsx.html) | [单一组件文件](https://jp.vuejs.org/v2/guide/single-file-components.html#ad) | [打字稿](https://www.typescriptlang.org/) / [RxJS](https://rxjs-dev.firebaseapp.com/) |

### 做出反应

React 是用于创建用户界面的库。 其他功能(如路由)必须在单独的库中进行补充。 为此的信息和生态系统是三者中最丰富的。 也可以在保守规模的开发中采用，然后扩大。 充分利用虚拟 DOM，功能也很高。 [React Native](https://facebook.github.io/react-native/) 等新技术的发展也在积极推进，虽说开始得很早，但版本还差一点就要成为 v17 了。

学习成本不是很高。 如果控制规模开始的话，门槛会降低吧。 在推进学习的过程中，丰富的信息和生态系统也应该会起到很大的作用。 设计和代码的做法既牢固又兼具灵活性。 具备经得起大规模开发的坚固性和严密性，团队开发也很容易进行吧。 毛色稍有变化的是 JSX 代码的写法。

JSX 是将 HTML 标记直接写入 JavaScript 代码中的语法。 这意味着模板由 JavaScript 代码确定。 以下代码引用了雏形。 一开始可能会有违和感。 这是基于开发人员认为标记和 View 逻辑应该是密集的(“关于 React.js 的组件”“[Separation of concerns？ 参照](https://qiita.com/koba04/items/4f874e0da8ebd7329701#separation-of-concerns))。

```
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

另外，JSX 也可以在 Vue 中使用。 但是，在 React 中，语法原则上被认为是 JSX。 而且，关于 CSS 也提倡使用库写入 JavaScript 中的“[CSS-in-JS](https://reactjs.org/docs/faq-styling.html#what-is-css-in-js) ”的样式(参照“[React: CSS in JS](https://speakerdeck.com/vjeux/react-css-in-js) )

### 视图

Vue 也是用于创建用户界面的框架。 与 React 一样，路由等其他功能将由其他库补充。 Vue 的特征是可以很小地开始。 之后一边添加功能，一边可以将网站扩大。 或者，也可以将使用 jQuery 的站点一点一点地迁移到 Vue。

信息和生态系统比其他两个少。 但是，官网上有日语版。 更新也和原文的英语网站几乎没有时间差。 如果缩小到最新的日语信息来看，应该不会那么逊色于其他的框架吧。

学习成本不高。 从小开始，一点一点地前进，就能逐步学到东西。 不过，官网似乎很重视开始的容易性，简易语法的样本很多，为了正式开发的方式似乎有必要重新考虑。 以下代码来自单组件文件( VUE )的雏形。 模板、JavaScript 代码和样式表都是单个组件的文件。

```
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  components: {
    HelloWorld
  }
}
</script>

<style>
/* ...[略]... */
</style> 
```

Enter fullscreen mode Exit fullscreen mode

### 棱角分明

Angular 的特征，不管怎么说是完全堆栈“全部进入”。 它提供了 web APP 所需的所有功能，包括路由。 从开发的规模来看，适合大项目。 从一开始就要好好设计，按 Angular 流程进行。 稍微试一下这个使用方法很难，需要鼓足干劲努力。 坚固性和严密性是三个中最高的吧。

虽然花在学习上的工夫和时间很多，但是考虑到可以“全部”的话，性价比并不是很低。 只是说“果然还是放弃了”时的埋没费用会变高。 相关库、工具、信息等生态系统可以说很丰富。

代码用 TypeScript(TS )编写，模板( HTML )和样式表( CSS )是单独的文件。 分工可能很容易。 以下代码引用自雏形 TypeScript 文件: [除了译码器函数](http://www.fumiononaka.com/Business/html5/FN1611003.html)(`@`)外，和 ECMAScript 2015 没有差别。

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

另外，TypeScript 在 React 和 Vue 的开发中，也可以进行设定后使用。 Angular 的原则是使用 TypeScript。

介绍了三个框架的特征。 各有不同的优点，反过来也会导致缺点。 一般来说，没有什么能说这是最好的框架。 从项目、团队、或者公司的开发推进方法和重视的功能等中，选择合适的技术比较好。

# gumi 采用 React

gumi 在三个框架中采用了 React。 主要理由如下。

## 丰富的信息和生态系统

信息和相关库、工具等最丰富。 官方网站的信息也明确了与 Vue 把开始的容易性放在前面相比，向开发者推荐的风格。 团队开发大型 APP 时，也很容易调整体制吧。 因为不像 Angular 那样“全部进入”，所以必须选择合作的库。 在那个时候，信息和库以及工具的数量也是一大优势。

## 开发规模和导入灵活

开发规模从小到大可以灵活应对。 Angular 在小规模的服务中使用容易过于规格。 React 以小规模开始，之后扩大也不难。 此外，与 Vue 相比，它的设计更能支撑大型 APP 应用程序。

与必须从一开始就记住 TypeScript 和 RxJS 等的 Angular 相比，React 具有以较小的学习成本开始并根据需要添加技术和工具的灵活性。 对 JSX 感到违和感的开发者不在少数。 但是，这大概也有很大的习惯因素吧。 如果还引入了“CSS-in-JS”，则可以将组件合并为一个模块。

## 经得起大规模开发的坚固性

仅限于大规模开发的话，Angular 应该有份儿吧。 但是，React 即使是小的 APP 也很容易制作，可以设计成经得起大规模开发的坚固的设计。 另外，不“全部进入”也有利于根据目的选择库。 评价了坚固性和柔软性的平衡。

# 用数字比较 3 个框架的评价

用数字比较一下三个框架的评价。 在 bestofjs.org“2018 JavaScript rising stars”“[Front-end Frameworks](https://risingstars.js.org/2018/#section-framework) ”中，Vue 排名第一。

[![front_end_frameworks_2018.png](img/23b95c9416f563b477ee4c2f41fb0b66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hraLQpSj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yvbhesypxxbrbxfpw79i.png)

[从 GitHub 的 Star 数推移](https://star-history.t9t.io/#facebook/react&angular/angular&vuejs/vue)来看，Vue 的增长率很大，最近超过了 React。

[![github_stars.png](img/de00622e2558ea189b15003ad037945c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fPkBuNKd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8p8qd2h53cazw9dk08xi.png)

在 bestofjs.org「 [UI Framework](https://bestofjs.org/tags/framework) ”中，Vue 也是最受欢迎的。 但是，在趋势上，React 更受关注。

[![ui_framework.png](img/dfc90f45cc4fc232c51a2eadf7fcccee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NrK7iQe8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aq0k3d53fj5ni73c80ea.png)

在 Stack Overflow 的“Developer Survey Results 2019”中，作为“Most Popular Technologies”有关于“[Web Frameworks](https://insights.stackoverflow.com/survey/2019#technology-_-web-frameworks) ”的调查 React.js 仅次于 jQuery，排名第二，稳居第三。 Vue 排在第七位。

| cis-position | Mingqian | 百分比 |
| --- | --- | --- |
| one | jQuery | 48.7% |
| Two | React.js | 31.3% |
| three | Angular/Angular.js | 30.7% |

此外，在[Most Loved，Dreaded，and Wanted Web Frameworks](https://insights.stackoverflow.com/survey/2019#technology-_-most-loved-dreaded-and-wanted-web-frameworks) 的“Wanted”中，React 被选为第一。 可以看出开发者的期待很高(另外，“Loved”中 React 也是第一位，Vue.js 是第二位。 Angular/Angular.js 将排名降低到第 9 位)。

| cis-position | Mingqian | 百分比 |
| --- | --- | --- |
| one | React.js | 21.5% |
| Two | view . js-检视. js | 16.1% |
| three | Angular/Angular.js | 12.2% |

npm trends 的下载量以 React 为首位。

[![npm_trends.png](img/e197823091efabb9fb88e0c1a2e7b0c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PaiOKr11--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zre1kscjhi9xdfax34he.png)

还有一个关于性能的测试结果。 据说在《面向工程师的微交互入门》的“[性能比较](https://speakerdeck.com/clockmaker/enziniaxiang-kefalsemaikurointarakusiyonru-men?slide=45)”中，React 在内存使用量和绘图性能方面都是最好的。

[![1812002_001.png](img/58eee043f3112cbaf86efcafaf34404c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ifuyXeyT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/262045/a5ab76e9-7670-8ceb-6c86-8924c6c324bc.png)

另外，在第 69 届 HTML5 之类的学习会“UI 框架最前线”上，小组讨论“[Angular，Vue.js，React 的主持人们所说的，JavaScript 框架开发的最前线【 另外，还可以从不同的视角评价三个框架吧。](https://logmi.jp/tech/articles/302650)