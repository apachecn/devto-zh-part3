# 我在 React 元素上放了 18 个事件处理程序

> 原文：<https://dev.to/ma5ly/i-put-18-event-handlers-on-a-react-element-j55>

[![Synthetic Event Party!](img/b8e577f8cc49c2f6beb5c8155e4d6e34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WWQ1nUZv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z84rzkapzmzgpytkcg9j.jpg)

如果你曾经开发过 React 应用，你可能会遇到一个`SyntheticEvent`。很有可能是`onChange`，但也许你更有冒险精神，是`onKeyDown`。

从技术角度来说，`syntheticEvent`是 React 的一部分。它取代了您可能从普通 Javascript 中了解到的本机事件处理程序。

假设我们有一个`<button />`，我们希望当用户点击它时会发生一些事情。在普通的 Javascript 中，我们会将`onclick`添加到元素中。这在 React 中不起作用。相反，该库提供了自己的处理程序，这些处理程序模拟了功能，并使它们在不同的浏览器上同样工作。不过，它们看起来很像本地的处理程序。比如:React 里的`onclick`就是`onClick`。

你可以在文档中读到更多关于他们的信息。

## 火大了！

现在，我们可以浏览整个事件列表，并逐一解释它们，但是为了真正了解当您向元素添加这些处理程序之一时会发生什么，让我们先将它们连接起来。

我选了其中的 18 个。还有更多，但这些是最常见的。我们将把它们添加到一个`<input />`元素中。

因为目标是了解他们，所以让我们试着回答两个问题:

*   他们什么时候开火？
*   他们多久开火一次？

我们要回答的第一个问题是在开火时给出视觉提示，第二个问题可以通过记录来回答。让我们开始建造吧。

合成事件处理程序接受函数。因此，我们将为所有 18 个处理程序添加一个函数。

```
<input
  onCopy={() => this.addEvent("onCopy")}
  onCut={() => this.addEvent("onCut")}
  onPaste={() => this.addEvent("onPaste")}
  onKeyDown={() => this.addEvent("onKeyDown")}
  onKeyPress={() => this.addEvent("onKeyPress")}
  onKeyUp={() => this.addEvent("onKeyUp")}
  onFocus={() => this.addEvent("onFocus")}
  onBlur={() => this.addEvent("onBlur")}
  onChange={() => this.addEvent("onChange")}
  onClick={() => this.addEvent("onClick")}
  onDoubleClick={() => this.addEvent("onDoubleClick")}
  onMouseDown={() => this.addEvent("onMouseDown")}
  onMouseEnter={() => this.addEvent("onMouseEnter")}
  onMouseLeave={() => this.addEvent("onMouseLeave")}
  onMouseMove={() => this.addEvent("onMouseMove")}
  onMouseOver={() => this.addEvent("onMouseOver")}
  onMouseUp={() => this.addEvent("onMouseUp")}
  onSelect={() => this.addEvent("onSelect")}
/> 
```

Enter fullscreen mode Exit fullscreen mode

您可能会注意到，有一个匿名的内嵌函数实际上调用了真正的`this.addEvent`函数。我们必须这样做，因为我们想把一个参数传递给函数；事件的名称。

下一步是编写实际的`addEvent`函数。在写之前，让我们记住我们需要做什么。我们需要一个事件触发时的视觉提示，我们需要保持每个事件被触发的计数。让我们实际上从后者开始，看看有多少个事件触发。这可能会影响我们对视觉线索的想法。

### 记日志

我们的计数日志是一段根据用户输入而变化的数据。这意味着我们将使用`state`。我们将使用的特定数据结构是一个内部有`objects`的`array`。每个`object`将代表每种类型的合成事件，并且将具有一个`name`属性和一个`amount`属性。看起来应该是这样的:

```
[{ name: "onChange", amount: 1 }, { name: "onClick", amount: 5 }] 
```

Enter fullscreen mode Exit fullscreen mode

因为我们从一个没有任何计数的空数组开始，所以每次触发函数时，我们需要做的第一件事是检查是否需要向数组添加一个新事件。然而，如果我们发现事件已经被添加到数组中，我们只需要增加计数。

```
addEvent = event => {
  const existingEvent = this.state.counts.filter(c => c.name === event)[0];
  const amount = existingEvent ? existingEvent.amount + 1 : 1;
  const count = this.state.counts.map(c => c.name).includes(event)
    ? Object.assign({}, existingEvent, { amount })
    : { name: event, amount };
}; 
```

Enter fullscreen mode Exit fullscreen mode

所以`existingEvent`要么包含数据，要么保持为空。有了这些信息，我们就可以确定`amount`属性。最后，我们要么更新现有的对象，要么准备一个新的。

有了这些，我们需要更新`state`。因为我们的`counts`数据是一个数组，我们现在有一个对象，我们需要找到并替换一个现有的对象，或者只是将新的对象标记到数组上。

```
 const counts = produce(this.state.counts, draftState => {
    if (existingEvent) {
      const index = this.state.counts.findIndex(c => c.name === event);
      draftState[index] = count;
    } else {
      draftState.push(count);
    }
  });

  this.setState({counts}) 
```

Enter fullscreen mode Exit fullscreen mode

现在您可能会在这里看到一个不熟悉的函数:`produce`。这不是我自己写的函数，是我从一个叫`immer`的库中导出的。如果你从事数据变异的工作，但是喜欢不可变的数据结构，我强烈推荐你[去看看那个库](https://github.com/mweststrate/immer)。`immer`允许你处理你的数据，就像你直接改变它一样，但是通过一个‘草稿状态’来保持你的新旧状态分开。

有了它，我们现在有了一个新版本的`counts`状态，我们可以把它放在当前版本的`counts`状态中。剩下唯一要做的事情就是将这些数据呈现到页面上，这样我们就可以实际看到计数了。

在我们的`render()`函数中，我们可以将`counts`数组映射到一个列表中。

```
const counts = this.state.counts.map(c => {
  return (
    <li key={c.name}>
       {c.name} <strong>{c.amount}</strong>
    </li>
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`return`中，我们可以将商品添加到我们的`<ul />`中。

```
 <ul>{counts}</ul> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们应该能够看到我们的合成事件弹出各自的计数。试试看你能不能把 18 个都发动起来。

你可能会注意到像`onMouseMove`这样的事件比其他事件更容易发生。这告诉我们，对于我们的视觉线索，我们必须对此有所注意。说到视觉暗示，我们来设置一下。

### 聚会时间

我的想法是在每次触发时，将事件的名称渲染在屏幕上的随机位置，并在一两秒钟后使其再次消失。为了更清楚地说明哪些事件触发，我们将为每个事件添加特定的样式。让我们先做那部分。

```
function getStyle(event) {
  let style;
  switch (event) {
    case "onCopy":
      style = {
        fontFamily: "Times New Roman",
        fontSize: 50,
        color: "red"
      };
      break;
    case "onCut":
      style = {
        fontFamily: "Tahoma",
        fontSize: 40,
        color: "blue"
      };
      break;
    case "onPaste":
      style = {
        fontFamily: "Arial",
        fontSize: 45,
        color: "salmon"
      };
      break;
  }
  return style;
} 
```

Enter fullscreen mode Exit fullscreen mode

为简明起见，这 18 起案件并非全部如此。你可以在[完整代码](https://codesandbox.io/s/qqpl1kjy8q)中找到这些，但是你会得到它的要点。基于该事件，我们返回一个具有唯一字体大小、字体系列和颜色的样式对象。

接下来的部分是获得屏幕上的随机位置。

```
function getRandomNumber(min, max) {
  return Math.random() * (max - min) + min;
}

function getPosition() {
  return {
    left: getRandomNumber(0, window.innerWidth - 120),
    top: getRandomNumber(0, window.innerHeight - 120)
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

`getPosition`函数返回一个 style 对象，它是一个介于 0 和屏幕宽度或高度之间的随机数。我已经扣除了 120 个像素，所以事件不会落在屏幕上。

有了这些助手，让我们考虑一下如何让事件真正显示在我们的屏幕上。我们已经实现了`counts`,所以我们对如何实现有点概念。不同的是，这一次我们希望将每个事件保存为一个单独的对象，我们可以在屏幕上呈现，只需在 2 秒钟后删除该对象。这意味着对于每个事件，我们需要更新状态两次。

让我们从只更新一次状态开始。

```
 const id = shortId.generate();
  const position = getPosition();
  const style = getStyle(event);
  const events = [...this.state.events, { id, event, position, style }]; 
```

Enter fullscreen mode Exit fullscreen mode

我们首先使用`shortid`库为每个事件生成一个唯一的`id`。这样做的原因是，在事件被添加到状态后，我们需要能够再次找到它，这样我们就可以删除它。

接下来，我们得到我们的位置和样式对象，稍后我们将需要它在屏幕上呈现事件。最后，我们创建一个新版本的`events`状态。

如果我们现在更新我们的状态并不断触发事件，我们将得到一个充满事件的巨大数组，这将阻塞屏幕。所以，我们需要不断地清理数组。怎么做呢？

一个有效的窍门就是使用`setTimeOut`，这是一个小定时器。每次更新后，我们等待 2 秒钟，获取我们刚刚添加的事件的`id`，并再次删除它。

```
 this.setState({ events }, () => {
   setTimeout(() => {
     const events = this.state.events.filter(e => e.id !== id);
     this.setState({ events });
   }, 2000);
 }); 
```

Enter fullscreen mode Exit fullscreen mode

我们从常规的`setState`开始，在这里我们更新刚刚创建的`events`数组。但是作为第二个参数，我们添加了一个新的匿名函数。通过在`setState`的第二个参数中这样做，我们确保了`events`的初始更新已经被应用。

在回调函数中，我们将超时设置为 2 秒，并用现在更新的`state`创建一个更新版本的`events`。因为我们仍然在同一个`addEvent`函数中，我们知道`id`，我们可以很容易地过滤掉它。然后我们第二次设置我们的状态。

现在，如果我们要记录`this.state.events`,我们应该看到它被填满和清空。但是在我们的屏幕上看到它更有趣。毕竟，我们有一个具有随机位置和独特格式的样式对象。所以让我们在组件中再做一次`.map`，看看结果如何。

```
const events = this.state.events.map(event => {
  return (
    <div
      key={event.id}
      style={{
        position: "absolute",
        left: event.position.left,
        top: event.position.top,
        zIndex: -1,
        opacity: 0.5,
        ...event.style
        }}
     >
      {event.event}
     </div>
   );
}); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们都将每个`event`对象的位置和样式添加到元素中。我们现在只需要将变量`events`添加到我们的`return`中。

现在，我们的屏幕上有了一个不错的合成事件派对。除了我们刚刚创造的视觉乐趣，我希望你也能感受到每个事件触发的时间。并不是每个事件都与你的日常工作密切相关，但是有时候知道`onMouseLeave`何时触发或者只是意识到`onDoubleClick`的存在是很有用的。

点击查看完整代码[。开心地在](https://codesandbox.io/s/qqpl1kjy8q)[推特](https://twitter.com/ma5ly)上聊天