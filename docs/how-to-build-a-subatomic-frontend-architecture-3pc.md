# 如何构建亚原子前端架构

> 原文：<https://dev.to/maxwell_dev/how-to-build-a-subatomic-frontend-architecture-3pc>

我目前的工作让我处理一些事情:

*   提高我们网站的可访问性标准
*   帮助人们调试 CSS 问题，主要是 Flexbox 问题
*   在月圆之夜，注意我们的首席执行官买的无头人体模型的活动

这些都很重要，但最重要的一点不在这里:为我们的前端资产制作和管理一个系统。当我到达时，我们当前的是一个分散的资产集合，混合着 Bootstrap。我们想要一个有更好的文档风格、组件组织和对框架更少依赖的。

经过几次尝试，最终我找到了一个能满足我们大部分需求的系统。它依赖于我所谓的**亚原子前端架构或 SAFA** 来管理样式。我们已经慢慢地将它集成到旧的项目中，一个新的项目将它用于整个前端。

我很高兴/松了一口气/接受命运的诱惑，说它大部分都很受欢迎，并帮助我们避免了常见的前端问题，如:

*   意外覆盖
*   比例风格
*   专注于后端的工程师不知道该做什么
*   藜麦

这就是为什么我写这篇文章来描述这种亚原子方法。我的希望是你能得到灵感，亲自尝试，给我信任，这样我就能在夕阳西下的时候最终达成一个小型的 web 开发书籍交易。

所以，加入我的探索，继续阅读吧！

## 什么是亚原子前端架构？

**我所说的 SAFA 基本上就是用 80%原子 CSS，20% BEM** (大部分是原子，所以是亚原子，懂了吗？).以这种方式编写前端有几个关键步骤:

### 1。获得或构建一个原子 CSS 框架

对于那些不知道的人来说，原子 CSS 完全(或大部分)是用助手类来构建样式的。我不会在这里讨论细节(或争议)，但是我建议你参考这个演示，它为原子 CSS 提供了理由。我要说的是，一开始我是持怀疑态度的，但是我和我推荐的每个人都接受了它。

我们的系统使用一个自定义的助手类集合，因为我们想避免过去的依赖问题。如果你不受限于此，那么[尾风](https://tailwindcss.com/docs/what-is-tailwind/)和[超光速粒子](http://tachyons.io/)是很受欢迎的选项。至少看看这些框架是怎么处理变量的，响应类的，涵盖什么风格。根据我现在看到的顺风的处理方式，我会对当前版本的一些部分进行修改。

### 2。尽可能使用原子 CSS 来构建你的网站

一旦原子类就位，就开始使用它们吧！我发现许多组件的标记可以完全用原子 CSS 来编写，并且可以部分样式化其余部分。从长远来看，正确的原子样式可以节省很多时间。

让我们以我的个人网站为例，它使用了 Tailwind。这里是旧的`footer`标记，只使用助手类。

```
<footer class="w-full py-2 text-white bg-teal-darker shadow-lg">
  <div class="flex flex-wrap max-w-md px-4 md:px-6 my-4 mx-auto">
    <div class="w-full md:w-3/4 md:pr-8 lg:pr-12">
      <p class="text-sm">
        I’m Max Antonucci, a front-end developer in New Haven, CT...
      </p>

      <p class="text-sm">
        See all my (hopefully) useful notes on blah, blah, blah...
      </p>
    </div>
  </div>
</footer> 
```

Enter fullscreen mode Exit fullscreen mode

要浏览的类很多，但它是有效的。我可以单独从标记中得到页脚的外观，调整样式就像改变类一样简单，而且我没有编写任何新的 CSS 就做到了。

### 3。在原子 CSS 不支持的地方使用 BEM

尽管原子 CSS 很棒，但助手类并不能涵盖一切。有太多潜在的 CSS 属性和值，用助手覆盖它们要求太多了。

我发现的原子 CSS 的常见例外是:

*   *布局*，有许多潜在的宽度或网格设置。有像 Bootstrap Grid 这样的工具，但是我更喜欢编写自己的布局 CSS 来避免不必要的依赖。
*   *常用组件*，如按钮或输入框。使用的元素越多，就越难保持所有可能的助手类同步。创建可重用组件解决了这个问题，但是除非我 100%确定标记只在一个地方，否则原子 CSS 在这里给我带来了太大的风险。
*   *隐藏样式或定位*，因为在绝对定位中不可能每个百分比值都有一个助手。请不要。

对于这些情况和更多的情况，我回过头来用 BEM 添加额外的样式。如果您还不了解 BEM，请查看[这篇文章以全面了解 BEM](https://seesparkbox.com/foundry/bem_by_example)。简而言之，这是一种编写特定组件类名的方法，基本上与原子 CSS 相反。

您的第一个想法可能是*“那么如果我的原子类不够用，我应该用一个 BEM 类来代替它们吗？”*对此，我透露一下我的读心术，说完全不是那么回事！**亚原子方法使用边界元法作为原子 CSS 的补充，而不是替代。我写的任何额外的类都和助手一起使用。**

最好用一个例子来说明这一点。这是我个人网站导航的简化 HTML。如果您查看类名，您会看到一组潜意识指令和一些与我上一个例子不同的项目。

```
<div class="bg-teal-darker text-white shadow-lg z-10">
  <nav class="flex flex-col max-w-md mx-auto">
    <div class="hidden md:flex items-center py-2 px-2 md:px-4">
      <img class="nav__logo mr-4 rounded-full" src="/asseimg/global/profile.jpg" alt="Maxwell's profile picture" />

      <p class="my-0 text-sm italic">
        {{site.description}}
      </p>
    </div>

    <ul class="nav__main-menu list-reset flex flex-wrap md:flex-col mb-0 md:my-4 text-xs">
      {% for menu_item in site.data.menu %}
        <li class="inline-block mb-0 text-center">
          <a class="block p-2 text-white" href="{{menu_item.link}}">
            {{ menu_item.name }}
          </a>
        </li>
      {% endfor %}
    </ul>

    <ul class="list-reset flex flex-wrap md:flex-col mt-auto mb-0 text-xs">
      {% for social in site.data.social %}
        <li class="inline-block mb-0 p-2 text-center">
          <a class="text-white" href="{{ social.url }}" target="_blank" rel="noopener">
            {{ social.name }}
          </a>
        </li>
      {% endfor %}
    </ul>
  </nav>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

`img`和`ul`标签上有不同的类！顺风课程涵盖了我需要的大约 95%的风格，但是仍然有他们不能给我的风格。所以我用 BEM 在 CSS 的最后一点:一个特定的最大宽度和一个半透明的背景覆盖。

```
.nav__logo {
  max-width: 100px;
}

.nav__main-menu {
  background-color: rgba(#005661, 0.75);
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我为这个导航写的全部 CSS！到目前为止，我的整个网站的自定义 CSS 只需要 10 个 Sass 部分，大多数只有 10-20 行长。

这样，你就有了亚原子方法的基础！

## 亚原子取舍

我不会说这种方法对所有项目都是最好的，尽管它至少值得考虑。但是为了帮助其他人做出这个决定，我将列出我在 SAFA 的经历中发现的主要权衡。只知道这些都是主观意见，可能会随着其他编码人员使用它而有所不同。

### 速度更快，但风险更大

原子 CSS 受益于快速开发和更少的决策。只需添加和删除类，就可以轻松构建和重新设计组件。它比我处理过的替代方案更快:

*   决定一个新元素或模式的类名
*   决定是将这个类放在新的还是现有的分部中(如果是新的，就给它命名)
*   写出常见的样式，如填充和空白，这已经重复了几十次
*   确保这些值在需要的时候使用样式变量，而不是硬编码或幻数
*   尖叫声

当前端代码也由后端开发人员开发时，这种额外津贴会更好，对我来说就是这样。CSS 是提前写好的，他们只需要选择要写的部分，而不是自己写。

但是选择越多，犯错的风险就越大。很容易在标记中的任何地方搞乱几个类。这可能导致通用组件之间的不一致，这在一大堆组件类中很难根除(头韵！)

您可以通过记录常见的助手类组件组合来降低这种风险。我们的风格指南记录了一些小东西，比如徽章和风格化的链接，并收集了合适的助手来重新创建它们。这并不是万无一失的，但是可靠的参考资料也让他们更容易发现其中的错误。考虑到 SAFA，有限的文件是一个危险信号。

### 更大的灵活性，但更多特定于项目的代码

围绕助手构建 CSS 意味着它可以用于许多网站和界面。我们不需要构建特定于项目需求的 UI 元素，比如购物车或预览卡。我们只提前构建几乎任何项目都需要的东西，比如按钮和输入。剩下的要么是纯类，要么大部分是辅助类。

这为最小样式的元素节省了更多的时间。对于只需要填充和边框的组件，我们不需要新的类。添加两到三个助手，你就完成了，没有任何不必要的 CSS 膨胀。

但这也意味着任何 BEM 代码都必须逐个项目地进行管理。在仅使用 SAFA 的三个项目之间，可能有几十个特定于每个项目的 CSS 文件。它们也可以由不同的开发人员编写。这违背了使用框架的一个主要原因，即加强一致性和风格指南。

当然，管理这个的一个方法是良好的文档。以及尽早实施一致的代码标准。但是我发现这很难，即使有好的代码翻译器，而且很大程度上取决于你的团队学习的意愿。因此，如果你正在制作一个被大量项目和开发人员使用的框架，考虑其他选择。

## 包装完毕

SAFA 一直是我选择的 CSS 架构，因为它拥有我喜欢的原子 CSS 的一切，并致力于改善它的一些缺点。在我工作的地方，我们在新的和现有的项目中组织、记录和扩展前端代码的方式有了很大的改进。

我希望其他开发人员发现这值得一试，尽管我认为有些人可能意识到他们已经在编写类似的东西了。但是因为我先写了一篇博文并给它起了个名字，所以我会得到荣誉，因为这就是我心目中的世界。

不管怎样，下次你需要选择前端架构时，请尝试一下亚原子方法！

*[封面图片由 SafeBooru.org](http://safebooru.org/index.php?page=post&s=view&id=2623986)提供*