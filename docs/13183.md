# JavaScript match media API 背后被低估的力量

> 原文：<https://dev.to/duranenmanuel/the-underestimated-power-behind-matchmedia-31ep>

基于媒体查询的交互是我们应用程序中非常重要的一部分，原因有几个，其中最重要的一个原因是，它们允许我们根据用户使用的设备为用户提供更好的体验。通常，当你想显示/隐藏或改变某些设备的某些东西时，你只需要在 *CSS* 中定义一些媒体查询就可以了，但是...当我们需要在响应式网站中用 javascript 创造动态体验时会发生什么？目前，这个问题最流行的答案是使用一个 resize 事件监听器，当`window.innerWidth`或`window.innerHeight`满足我们的条件时，它会执行一些代码，比如:

```
// Do something for some specified breakpoints
function doSomething() {
    if (window.innerWidth > 768) {
        /* do Something */
    }
}

// Add a listener somewhere
window.addEventListener('resize', doSomething); 
```

但这还不是全部，因为`resize`事件对于浏览器来说是一个*昂贵的*操作，我们可能最终会在处理程序中添加一个*节流*或*去抖*函数来释放一些压力，这间接意味着必须使用像 *lodash* 这样的库或实现我们自己的函数来实现它，该死，如果我们只有一个 API 来处理 *JavaScript* 中的媒体查询，就像处理*CSS*一样...而不必担心每个断点的额外验证、去抖动和所有这些荒谬的限制...**

## 介绍 matchMedia API

[matchMedia](https://developer.mozilla.org/es/docs/Web/API/Window/matchMedia) 是一个[非常受支持的 API](https://caniuse.com/#feat=matchmedia) ，它允许我们将监听器附加到媒体查询，而不是经典的 resize 事件，允许我们只添加我们想要执行的代码，并使我们从所有额外的验证中解脱出来。此外，它比传统方法具有更好的性能，因为它不经常执行检查或调用函数，它只在满足媒体查询条件时执行一次。很好，这听起来很棒，但是，我们如何实现它呢？不再多说，这里举个例子:

```
// Define a MediaQueryList Object with your media query passed like in CSS
const mediaQueryListObject = window.matchMedia('(min-width: 768px)');

// Define the code to execute
function doSomething() {
    /* Some code to execute */
}

// Attach the event listener with the function to execute
mediaQueryListObject.addListener(doSomething);

// Execute your function once in case your current screen size
// already mets the media query conditions
doSomething(); 
```

这就是删除监听器的方法:

```
mediaQueryListObject.removeListener(doSomething); 
```

我想让你注意到一些东西，检查我们将媒体查询传递给`matchMedia`函数`(min-width: 768px)`的方式，就像它是 *CSS* 一样，这难道不漂亮吗？这意味着我们可以随心所欲地对媒体查询进行创新和具体化，甚至更好地帮助我们同步 CSS 和 JavaScript 断点。

### 一些需要考虑的折衷和一个能让你生活更轻松的库

1.  如果你想删除一个事件监听器，你现在需要访问变量`mediaQueryListObject`和函数`doSomething`的引用，为什么这是一个问题？因为很多时候，您希望在不同于添加侦听器的作用域中删除侦听器，这将最终迫使您声明全局变量或类属性，这将使您的应用程序容易发生变化。

2.  如果您再次检查代码，您会意识到我们现在有更多的步骤来执行函数和添加侦听器，这对您来说可能不是一个大问题，因为我们获得了性能提升，但是认知负载是需要考虑的。

现在，如果我告诉你有一个替代方案，你也不用担心前面提到的这些问题，会怎么样？

## MediaQuerySensor，一款强大而轻便的救援解决方案

[查看演示视频](https://enmascript.com/articles/2018/10/31/the-underestimated-power-behind-window-matchmedia#mediaquerysensor-a-powerful-and-lightweight-solution-to-the-rescue)

[此演示页面可在此处获得](https://enmascript.com/code/mediaquerysensor)。

[MediaQuerySensor (MQS)](https://enmascript.com/code/mediaquerysensor) 是一个围绕`matchMedia`创建包装器的库，它负责所有额外的责任，以便您可以专注于重要的事情，您只需指定一个引用 id 来标识您的侦听器、媒体查询断点和对给定媒体查询执行的操作，然后它将使用这些数据创建一个我们称之为*的传感器对象*，它允许您删除并验证从代码中任何位置添加的侦听器，而不必担心上下文、引用、样板文件和验证，哇哦！

让我们看一个代码示例，演示如何添加一个监听器/传感器(摘自[文档](https://github.com/enmanuelduran/mediaquerysensor) ):

```
const consoleLogger = () => console.log('Between 480px and 990px');

MQS.add({
    ref: 'uniqueId',
    mediaQuery: '(min-width: 480px) and (max-width: 990px)',
    action: consoleLogger
}); 
```

就是这样，你不需要做其他任何事情，你可以随意添加、移除和检查传感器，下面是一些通过 MQS 的 API 可用的其他功能:

```
// Removes individual listeners, in this case the one with ref "uniqueId"
MQS.remove('uniqueId');

// Removes all the listeners
MQS.empty();

// Shows an object with all the active sensors/listeners
MQS.get();

// Shows the sensor object with ref "uniqueId"
MQS.get()['uniqueId']; 
```

如你所见，我们只定义重要的事情，我们有一个非常完整和简洁的 API 供我们使用，这将允许我们满足我们的需求。

我希望这篇文章对你有所帮助，也许你甚至发现了你的代码库中可以改进的地方，如果你喜欢这篇文章，请与你的朋友和队友分享，如果你有什么想分享或添加的东西，你可以在下面的评论中做，你可以在 twitter 上用 [@duranenmanuel](https://twitter.com/duranenmanuel) 找到我，我的电子邮件是【duranenmanuel@gmail.com】T2。

下次再见，亲爱的未来开发者们。

最初发布于[Enmascript.com](https://enmascript.com/articles/2018/10/31/the-underestimated-power-behind-window-matchmedia)