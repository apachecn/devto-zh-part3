# 一个整洁的自己动手制作滚动动画的解决方案(适用于任何框架)

> 原文：<https://dev.to/bholmesdev/a-neat-diy-solution-to-animating-on-scroll-for-any-framework-24lc>

> **更新 4.10.19** 下面最近的一个评论炸了我的心思名字掉路口观察者 API。这实际上正是为了本文的目的而构建的，允许您在元素位于屏幕上的某个位置时触发回调！本文后面关于用存储集中逻辑的部分仍然完全适用于此，但是交集观察者在 requestAnimationFrame 上做了大量的清理工作，同时也更具性能。但是，请注意，直到最近才出现 IE 和 Safari 支持。好吧，享受这篇文章的其余部分😊

在万维网上寻找灵感，我发现每当我滚动到某些元素时，我喜欢的许多网站都包含有趣的小“揭示”动画。虽然很微妙，但这些额外的触摸让页面感觉不那么静态，更具响应性。问题是...实现这一点的最佳方式是什么？

浏览 CodePen 的例子，我发现[时间](https://codepen.io/syedrafeeq/pen/yEJKn)和[时间](https://codepen.io/letsbleachthis/pen/zewKYE)人们正在寻找可以为他们处理它的包罗万象的库。在卷轴上制作动画有数不清的选择，最流行的是名副其实的 [AOS](https://github.com/michalsnik/aos) 。我自己也希望🌶我的网站上有一些滚动动画，所以我很自然地想到求助于 AOS 图书馆。然而，随着我的实现变得越来越专业(例如如何避免在滚动到 iFrame 之前加载它？)我开始怀疑...

我不能自己做吗？

## 也许吧。让我们看看如何

从基本的、普通的 JS 开始，没有框架，这种方法实际上非常简单。我们所需要的是一个`onScroll`处理程序和任何我们想要激活的元素。从基础开始，假设我们有一个想要触发动画的特定 ID 的元素。正如你可能想象的那样，我们可以通过 DOM 窗口的`onScroll`事件来计算出我们的元素在屏幕上的位置，只要你滚动:

```
window.onScroll = ({target}) => {
    const element = document.getElementById('animate-me')
    const elementTop = element.getBoundingClientRect().top
    if (elementTop < document.body.clientHeight) {
        element.classList.add('scrolled-to')
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为此，我们需要获取一些嵌套的对象属性。首先，我们需要获得元素顶部在屏幕上的像素值。有一些有效的方法可以找到这一点，但是通过快速的互联网搜索，似乎`getBoundingClientRect()`是跨浏览器做到这一点的最可靠的方法。

有了这个，我们应该对照文档的固定高度。这基本上只是你的浏览器窗口的高度，作为`clientHeight`。如果我们元素的顶部小于这个高度，那么它的某个部分一定在屏幕上。现在，我们只需在`.animate-me.scrolled-to`上将关键帧添加到 CSS 中，就可以开始了👍

### 很好，我们基本上重新创建了一个 MDN 帮助页面示例...

这样一来，我们就可以在现实世界中使用它了。首先，如果你感到好奇，并在那里抛出了一个`console.log`语句，那么无论何时你扭动滚轮，你都可能得到这个。

[![Console log of on scroll handler](img/f5709b2fd32449c1c71dd237ab6845e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DXW4KZm7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/defuaddw82tdkyrzkfpo.png)

这反映了分析每个滚动事件实际上是多么昂贵。我们正在为我们滚动的每个像素执行一个函数，当我们开始使这个函数变得更健壮时，这可能会开始导致滞后和口吃。

解决这个问题的一个方法是使用一个`requestAnimationFrame`来决定我们的回调何时被触发。这是另一个窗口级别的函数，在这里你可以为浏览器调用的回调进行排队。当它觉得已经准备好执行这些功能而不会影响你的滚动体验时，它就会启动这些功能。值得庆幸的是，这种方法已经让[相对较高的浏览器采用率](https://caniuse.com/#search=requestanimationframe)。我们所需要的是一个包装器，包装我们的`onScroll`处理程序到`requestAnimationFrame`，以及一个`boolean`标志，让我们知道我们之前的回调是否已经执行完毕:

```
let waitingOnAnimRequest = false

const animChecker = (target) => {
    // Our old handler
    const element = document.getElementById('animate-me')
    const elementTop = element.getBoundingClientRect().top
    if (elementTop < document.body.clientHeight) {
        element.classList.add('scrolled-to')
    }
}

window.onScroll = ({target}) => {
    if (!waitingOnAnimRequest) {
        window.requestAnimationFrame(() => {
            animChecker(target)
            waitingOnAnimRequest = false
        })
        waitingOnAnimRequest = true
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们的通话应该更有效率一点。但是让我们来解决一个更紧迫的问题:我们如何让这个为文档中的任何元素工作呢？

为我们需要的每个可能的 ID 或类名添加回调当然没有意义，那么为什么不创建一个集中式数组，我们可以将所有的元素选择器附加到这个数组中呢？

## 某些循环的时间

这种添加利用`querySelectorAll`相当简单。只需创建一个全局数组，其中包含所有应该激活的选择器(id 或类)，并像这样循环遍历它们:

```
let animationSelectors = ['#ID-to-animate', '.class-to-animate']

const animChecker = (target) => {
    // Loop over our selectors
    animationSelectors.forEach(selector => {
        // Loop over all matching DOM elements for that selector
        target.querySelectorAll(selector).forEach(element => {
            const elementTop = element.getBoundingClientRect().top
            if (elementTop < bodyHeight) {
                 element.classList.add('scrolled-to')
            }
        })
    })
}
... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的滚动动画检查器应该能够处理我们扔给它的任何元素了！

## 整齐！但是我使用 X 框架，我不认为我可以使用它，因为 Y

现在站在那里别动。我知道每个人的工具都有自己的怪癖，所以让我们试着解决其中的一些。

### 我使用一个组件系统，那么我如何集中这个逻辑？

虽然有一个简洁的我们想要激活的类和 id 的列表是很好的，但是组件，尤其是有作用域的 CSS 解决方案，使得这个列表很难保持可读性和可扩展性。

幸运的是，这个解决方案只需要一个字符串数组就可以工作，所以我们可以使用一个全局存储，每个组件都可以用它们想要激活的 DOM 选择器进行更新。我在最近一个基于 SvelteJS 的项目中使用了这个，它使用了一个基于订阅的全球商店。为了更新`animationSelectors`，我刚刚创建了一个商店...

```
export const animationTriggers = writable({}) 
```

Enter fullscreen mode Exit fullscreen mode

...并在创建组件时添加了组件的类名。

```
import { animationTriggers } from '../stores'

onMount(() => {
    animationTriggers.set([
      ...$animationTriggers,
      '.wackily-animated-class',
      '#section-id',
    ])
  }) 
```

Enter fullscreen mode Exit fullscreen mode

这同样适用于通用的全局状态解决方案，如 Redux 和 React Context。Redux 的实现因中间件的不同而有很大差异，所以我在这里省略了多文件的例子，但是这里有一个使用 React 上下文的选项(在普通 React 中有效):

```
// store.js
...
const AnimationTriggerContext = React.createContext()

class StoreWrapper extends React.Component {
    constructor() {
        super()
        this.state = {
            selectors: []
        }
    }
    render() {
        return (
            // create a provider to wrap our components in at the parent level
            <AnimationTriggerContext.Provider value={{
                // make our array of selectors accessible from all children
                selectors: this.state.selectors,
                // add a helper function to update our array
                addSelector: (selector) => {
                    this.setState({
                        selectors: [...this.state.selectors, selector],
                    })
                }
            }}>
                {this.props.children}
            </AnimationTriggerContext.Provider>
        )
    }
}

//childManyLayersDeep.js
...
class Child extends React.Component {
    componentDidMount() {
        this.context.addSelector('special-class')
    }
    render() {
        return <div className="special-class"></div>
    }
}

//wrap the child with a 'withContext' so it can be accessed
export default withContext(Child) 
```

Enter fullscreen mode Exit fullscreen mode

自然地，这个方法可以扩展到 VueJS、RxJS observables，以及基本上任何你可能使用全局存储的地方。

### 好的，那很漂亮...但是我不会用基本的 CSS 选择器。这些都是组件！

好吧公平点；这可能会使大多数基于组件的框架变得复杂。最简单的妥协是在我们的“add”函数中传递对元素本身的引用，而不是类名，这样我们就可以避免 DOM 查询。总的来说，React 或 Vue 中不起眼的`ref`属性，而不是一个类或 ID 选择器，应该可以做到这一点。

### 另外，我正在使用 CSS-in-JS，不想检查类名来开始动画。我有什么选择？

这是目前相当常见的模式，并且倾向于更多地依赖于类名切换的适当传递。幸运的是，我们已经有了几乎所有的逻辑来根据我们的商店找出这些道具。我们所需要的只是传入的选择器上的一个额外的对象属性，比如一个`scrolledTo`标志，它可以设置为“真”或“假”。

为此，我们将修改添加到存储中的内容，使其从一个字符串(或引用)变成一个对象...

```
{
    selector: 'class-name',
    scrolledTo: false,
} 
```

Enter fullscreen mode Exit fullscreen mode

...并在滚动到时更新其标志。

```
const animChecker = (target) => {
    ...
        if (elementTop < bodyHeight) {
            animationTriggers[currentIndex].scrolledTo = true
        }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以订阅我们的 animationTriggers 数组(或者获取上下文，这取决于您的实现)并将我们的`scrolledTo`标志作为道具传递给组件的样式。

## 总结

所以，在你抗议你可以在阅读这篇文章的时候让你最喜欢的动画库工作之前...我明白了。但是我想说，将这个特性作为一个有趣的小挑战来扩展自己，对于理解如何制作圆滑、高效的 DOM 侦听器非常有帮助。这也意味着在你的包中你将少了一个需要担心的依赖项，所以没有突破性的改变，并且在添加新特性方面有很大的灵活性！

为了看到这个解决方案的实际应用，我们佐治亚理工学院俱乐部的主页上到处都在使用它:Golden Swarm Games。访问网站 [https://gsg.surge.sh](https://gsg.surge.sh) 或 [the repo](https://github.com/Holben888/gsg-site) ，看看我们的滚动动画是如何在幕后工作的。

## 学点小东西？

滑头。如果你错过了，我发布了一个[我的“网络魔法”时事通讯](https://tinyletter.com/bholmesdev)来探索更多像这样的知识金块！

这个东西解决了 web 开发的[【首要原则】](https://www.swyx.io/first-principles-approach/)。换句话说，是什么让我们所有的 web 项目运转起来的所有 janky 浏览器 API、弯曲的 CSS 规则和半可访问的 HTML？如果你正在寻找超越框架的*，这是给你亲爱的网络巫师的🔮*

 *[立即在此订阅](https://tinyletter.com/bholmesdev)。我保证永远教，永远不会垃圾邮件❤️*