# React w/ Redux 中 Javascript 生成器的一个简单应用

> 原文：<https://dev.to/hliutongco/a-simple-ish-application-of-javascript-generators-in-react-w-redux-l53>

大约有一年的时间，我知道什么是 Javascript 生成器，但是没有在野外使用它们的经验。在开发一款名为[状态引用](https://github.com/hliutongco/status-quote-v2-frontend)的游戏时，我看到了一个尝试它们的机会，这款游戏会一个接一个地显示一系列视频剪辑。

这篇博文假设您理解生成器函数的基本语法，所以如果您对这个主题不熟悉，请查看我的描述生成器基础知识的博文。

## 目标

最终目标是在遍历视频集合时使用生成器函数。暂停功能的能力(通过 **yield** 关键字)将方便地暂停迭代，以便允许每个视频在移动到下一个视频之前结束播放。

## 设置

所有这些代码都写在一个名为 GameContainer 的组件中。该组件使用生成器函数来呈现 VideoPlayer 组件的数组。

首先，我们从一个单独的文件中导入一个对象数组，并将该数组赋给一个名为“clips”的变量。数组中的每个对象包含一个视频剪辑的信息:`import {clips} from '../clips'`

其次，我们在状态中保存两个键:

```
 state = {
    counter: 0,
    generatedObj: {value: null}
  } 
```

*   计数器将用于跟踪我们希望在 VideoPlayer 组件数组中获取的元素；这个数字实际上是数组中当前元素的索引号。
*   generatedObj 键将跟踪从生成器对象返回的普通对象。换句话说，这个键存储。在生成器对象上调用 next()时。

我们将使用三种生命周期方法:**componentdimount、componentDidUpdate 和 render** 。

我们还将创建两个助手方法:一个用于创建生成器对象，另一个用于使用生成器对象。

## 创建生成器对象

让我们从创建一个名为“createVideoGenerator”的助手方法开始。

在这个方法中，我们首先要创建的是 VideoPlayer 组件的数组。我们映射“剪辑”数组，以便创建一个新的组件数组:`const videoPlayers = clips.map((clip) => <VideoPlayer key={clip.id} clip={clip}/>)`

接下来是生成器函数本身。让我们发布完整的代码，然后逐行分解。

```
 createVideoGenerator = () => {
    const videoPlayers = clips.map((clip) => <VideoPlayer key={clip.id} clip={clip}/>)

    function* nextVideo(array){
      while(this.state.counter < array.length) {
        this.setState({ counter: this.state.counter + 1 })
        yield array[this.state.counter]
      }
    }
  } 
```

让我们从第一行开始:`function* nextVideo(array){`

这只是函数声明。生成器函数被命名为 nextVideo。当我们稍后调用这个函数时，我们传入的数组参数将是 videoPlayers 数组。

接下来:`while(this.state.counter < array.length) {`

这是我们将使用我们在 state 中保存的计数器的地方。如果计数器小于数组的长度，这意味着仍有更多的 VideoPlayer 组件需要呈现到页面上。

接下来:`this.setState({ counter: this.state.counter + 1 })`

在 while 循环中，我们将计数器加 1，然后将这个新数字保存到 state 中。

最后:`yield array[this.state.counter]`

最后，我们使用 yield 关键字来表示代码何时应该暂停。在这种情况下，代码应该在返回数组中的当前元素后暂停 while 循环。

#### //警告

您可能已经注意到最后两行代码有些奇怪。毕竟，setState 是异步的。这意味着在这一行代码中:`yield array[this.state.counter]`，我们没有使用更新的计数器，而是使用 setState 结束运行之前的计数器。比如:

```
// this.state.counter => 0 

this.setState({ counter: this.state.counter + 1 })
// after setState: this.state.counter => 1

yield array[this.state.counter]
// this.state.counter => 0 
```

这仍然有效，因为我们希望在递增计数器之前返回数组。实际上，如果我们能够颠倒这两行代码的顺序，会更准确:

```
yield array[this.state.counter]
this.setState({ counter: this.state.counter + 1 }) 
```

在使用 setState 递增计数器之前，我们希望首先使用计数器的当前值。不幸的是，如果通过数组递增导致重新呈现，那么这将不起作用。在我的应用程序中，通过数组递增会导致 Redux 状态发生变化，从而导致在 GameContainer 组件中重新呈现。这意味着 yield 之后的任何代码都不会运行。

我的解决方法是利用 setState 函数的异步特性。因为它是异步的，所以 yield 将总是在 setState 被解析之前运行。所以从某种意义上说，我们还是在 yield 之后使用 setState。这是一个小黑客，但它的工作！

#### //结束警告

createVideoGenerator 函数的最后一部分包括两个步骤:

*   绑定下一个视频生成器函数的上下文
*   调用生成器功能

在 nextVideo generator 函数内部，当我们使用“this”关键字(例如，this.state)时，“this”的值需要是 GameContainer 组件。因此我们需要使用。bind 为了将 nextVideo 函数绑定到 GameContainer 的上下文:`this.nextVideo = nextVideo.bind(this)`

最后，我们调用 nextVideo 函数，将 videoPlayers 数组作为参数传入。这一行也将是 createVideoGenerator 函数的返回值，因为生成器函数返回一个生成器对象。

这是我们的 createVideoGenerator 函数的完整代码:

```
 createVideoGenerator = () => {
    const videoPlayers = clips.map((clip) => <VideoPlayer key={clip.id} clip={clip}/>)

    function* nextVideo(array){
      while(this.state.counter < array.length) {
        this.setState({ counter: this.state.counter + 1 })
        yield array[this.state.counter]
      }
    }

    this.nextVideo = nextVideo.bind(this)
    return this.nextVideo(videoPlayers)
  } 
```

## 使用生成器对象

接下来，我们将创建另一个助手函数，它使用我们在 createVideoGenerator 中创建的 generator 对象。让我们称这个函数为 useGenerator。下面是这个函数的完整代码:

```
 useGenerator = () => {
    this.setState({ generatedObj: this.createVideoGenerator().next() }, () => {
      if(!this.state.generatedObj.value){
        this.props.handleChange("ENDED")
      }
    })
  } 
```

在声明了 useGenerator 函数之后，我们使用 createVideoGenerator 作为辅助函数来设置 State，以访问 Generator 对象。让我们仔细看看作为 setState 的第一个参数传递的对象:

`{ generatedObj: this.createVideoGenerator().next() }`

首先，我们调用 createVideoGenerator 函数。返回值是生成器对象。生成器对象可以访问该函数。接下来，它允许生成器函数中的代码在从`yield`关键字暂停后继续运行。

那么这一整行代码的返回值是什么:`this.createVideoGenerator().next()`？又是一个普通的物体！该对象可能看起来像这样:`{ value: <VideoPlayer/>, done: false }`

如您所见，这个对象有一个名为“value”的键，它保存我们在生成器函数中使用的任何值。在这种情况下，value 键将包含以下两种内容之一:

*   视频播放器组件
*   空

当生成器函数完全遍历完 videoPlayers 数组时，该值为 null。然后，我们将这个对象保存在 generatedObj 键中。

让我们看看传递给 setState 的第二个参数:

```
() => {
      if(!this.state.generatedObj.value){
        this.props.handleChange("ENDED")
      }
    } 
```

这是一个使用 generatedObj 的值的回调。如果 generatedObj 为 null，我们将数据发送到 Redux 状态。这个数据实际上是向其他组件发出信号，表明我们已经完成了所有视频的显示。

就是这样！概括一下，这里是 createVideoGenerator 和 useGenerator 的代码:

```
 createVideoGenerator = () => {
    const videoPlayers = clips.map((clip) => <VideoPlayer key={clip.id} clip={clip}/>)

    function* nextVideo(array){
      while(this.state.counter < array.length) {
        this.setState({ counter: this.state.counter + 1 })
        yield array[this.state.counter]
      }
    }

    this.nextVideo = nextVideo.bind(this)
    return this.nextVideo(videoPlayers)
  }

  useGenerator = () => {
    this.setState({ generatedObj: this.createVideoGenerator().next() }, () => {
      if(!this.state.generatedObj.value){
        this.props.handleChange("ENDED")
      }
    })
  } 
```

## 使用辅助方法

既然我们已经构建了 helper 方法，是时候实际使用它们了！对于这一部分，我们将利用 componentDidMount 和 componentDidUpdate 生命周期方法。

总体思路是在组件挂载时(第一个视频)以及当道具发生变化时(第一个视频之后的每个视频)调用 userGenerator 函数。

代码是这样的:

```
 componentDidMount(){
    this.useGenerator()
  }

  componentDidUpdate(){
    if(this.props.changeNextVideo){
      this.props.toggleChangeNextVideo(false)
      this.useGenerator()
    }
  } 
```

在 componentDidUpdate 中，changeNextVideo 是以 Redux 状态存储的布尔值。我做了一些设置，以便每当视频结束时，changeNextVideo 在 VideoPlayer 组件内切换为 true。在上面的 if 语句中，它切换回 false。最后，我们再次调用 useGenerator()来检索 videoPlayers 数组中的下一个 VideoPlayer 组件。

## 总结

让我们回顾一下我们所做的一切:

#### 创建生成器对象

```
 createVideoGenerator = () => {
    const videoPlayers = clips.map((clip) => <VideoPlayer key={clip.id} clip={clip}/>)

    function* nextVideo(array){
      while(this.state.counter < array.length) {
        this.setState({ counter: this.state.counter + 1 })
        yield array[this.state.counter]
      }
    }

    this.nextVideo = nextVideo.bind(this)
    return this.nextVideo(videoPlayers)
  } 
```

*   我们创建了一个名为 createVideoGenerator 的助手函数。这个函数包含一个生成器函数。
*   生成器函数接受一个数组作为参数。它包含一个 while 循环，在每次迭代过程中递增一个计数器，只要计数器不大于或等于数组参数的长度，它就会继续运行。
*   在 while 循环中，我们递增计数器并将其保存到 state。然后，生成器函数`yield`是数组的一个元素，使用计数器作为索引号。
*   最后，我们将`this`的上下文绑定到 GameContainer 组件，然后调用生成器函数，将 VideoPlayer 组件的数组作为参数传递。
*   这个函数的返回值是生成器对象。

#### 使用生成器对象

```
 useGenerator = () => {
    this.setState({ generatedObj: this.createVideoGenerator().next() }, () => {
      if(!this.state.generatedObj.value){
        this.props.handleChange("ENDED")
      }
    })
  } 
```

*   我们创建另一个助手函数，以便检索 createVideoGenerator 中返回的生成器对象。
*   我们获取生成器对象并调用。下一个方法，并将生成的普通对象保存到我们的状态。
*   这个简单的对象允许我们访问在生成器函数中`yield` -ed 的值(即一个视频播放器组件)。如果这个值为 null，这意味着我们已经遍历了整个 videoPlayers 数组，我们最终可以结束这个功能。

#### 使用辅助方法

```
 componentDidMount(){
    this.useGenerator()
  }

  componentDidUpdate(){
    if(this.props.changeNextVideo){
      this.props.toggleChangeNextVideo(false)
      this.useGenerator()
    }
  } 
```

*   当组件挂载时以及 changeNextVideo 属性(处于 Redux 状态)从 false 切换到 true 时，我们调用 useGenerator 函数。
*   这允许 VideoPlayer 数组中的第一个 VideoPlayer 组件在 GameContainer 挂载时正确呈现，也允许其余的 video player 组件一个接一个地呈现。

这是 React with Redux 中生成器函数的一个应用！当然，有许多不同的(也可能更简单的)方法可以在不使用生成器函数的情况下实现相同的功能。这个小实验的目的不是编写最有效的代码，而是满足我在实际的 web 应用程序中使用生成器函数的好奇心。我希望你受到启发，尝试在自己的应用程序中使用生成器功能！