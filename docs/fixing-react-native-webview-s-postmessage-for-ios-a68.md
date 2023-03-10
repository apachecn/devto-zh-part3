# 修复 iOS 版 React Native WebView 的 postMessage

> 原文：<https://dev.to/charlesstover/fixing-react-native-webview-s-postmessage-for-ios-a68>

2016 年，GitHub 用户 Robert Roskam (raiderrobert) [在 React 原生存储库上打开了一个问题](https://github.com/facebook/react-native/issues/10865)，报告了错误“*在 WebView 上设置 onMessage 覆盖了 window.postMessage 的现有值，但定义了以前的值*”。自那以后的两年中，在 WebView 的内部 React 本机实现中没有做任何事情来解决它。

React 原生社区已经[将 WebView](https://github.com/react-native-community/react-native-webview) 专门作为第三方包来维护，并修复了许多正在发生的问题。然而，为了实现这些第三方包，您必须能够链接 React 本地包— `react-native link react-native-webview`。如果你能够并且愿意这样做，你的问题就解决了。WebView 社区版的安装说明非常简单:

```
yarn add https://github.com/react-native-community/react-native-webview
react-native link react-native-webview 
```

Enter fullscreen mode Exit fullscreen mode

**注:**为了`react-native link ...`，你必须先`yarn global add react-native`。

不幸的是，如果你不能或不愿意这样做，这个问题就没有解决方案。好几年了！

例如，Expo 的用户将不得不放弃他们的项目，编写他们自己的本地的、非 JavaScript 的特性实现。理论上，Expo 将在未来的版本中使用这些社区版包；但是离发布时间只有几周了，我和我的团队不愿意再等了。

## 解💡

如果你更关心现在就解决这个问题*而不是它是如何工作的，这一节是为你准备的。*

 *使用`npm install rn-webview`或`yarn add rn-webview`将 [`rn-webview`包](https://www.npmjs.com/package/rn-webview)添加到您的项目中。

无论你在哪里使用`import { WebView } from 'react-native'`，简单地用`import WebView from 'rn-webview'`代替它。然后就像使用 React 本地内部实现一样使用新的 WebView 组件，包括使用`onMessage`道具。`rn-webview`包只是内部 React 本机实现的包装器，它通过不同于内部`onMessage`属性的通道拦截消息，但用自己的`onMessage`属性处理它，给人一种你实际上在使用内部`onMessage`并获得预期结果的错觉。

## 告诫🤕

`rn-webview`包的工作原理是将`window.postMessage`的流量导向`history.pushState`。React Native 的 iOS 实现虽然不能正确处理`window.postMessage`，但是可以处理导航状态变化。因此，导航状态更改事件是消息在 WebView 和本机应用程序之间传输的通道。

如果历史状态的操作是应用程序的一个重要方面，这个解决方案可能不适合您的需要。请随意在 GitHub 上分叉项目，以提供替代解决方案。

## 实现🔨

### 导出🚢

首先也是最重要的，WebView 的`ref`道具是特别重要的一个。因此，我们不希望用户失去对它的访问。我们从一个`forwardRef`实现开始这个包，其中`WebViewPostMessage`是这个包使用的类名。

```
export default React.forwardRef((props, ref) =>
  <WebViewPostMessage
    {...props}
    forwardedRef={ref}
  /> ); 
```

Enter fullscreen mode Exit fullscreen mode

### 渲染🎨

该组件的输出将是 WebView 的 React 本机内部实现，稍加调整。我们不会给它一个`forwardedRef`属性，因为这只是用来给父节点访问`ref`的权限，对内部 WebView 来说毫无意义。最重要的是，我们不会给它`onMessage`道具，因为那是我们所有问题的根源——它不被 iOS 支持！

```
render() {
  const props = {...this.props};
  delete props.forwardedRef;
  delete props.onMessage;

  return (
    <WebView
      {...this.props}
      onNavigationStateChange={this.handleNavigationStateChange}
      ref={this.handleRef}
    />
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个自定义的导航状态更改监听器，因为这是我们监听消息的通道。

我们有一个自定义的引用处理程序，因为我们 1)需要在这个组件中访问它，2)需要通过`forwardedRef`属性将引用传递回父容器。

### Ref👋

当内部 WebView 给我们它的 ref 时，我们将它存储在实例(`this.ref = ref`)中以备后用。如果父节点也请求了 ref，我们会转发它。

```
handleRef = ref => {
  this.ref = ref;

  // If the caller also wants this ref, pass it along to them as well.
  if (this.props.forwardedRef) {
    this.props.forwardedRef(ref);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 注入 window.postMessage💉

现在，`window.postMessage`的定制实现需要存在于 WebView 中的任何页面上。每当导航状态改变时，如果它已经完成加载，我们就向它注入 JavaScript 来覆盖`window.postMessage`所做的。

```
handleNavigationStateChange = e => {

  /* We'll do something here later. */

  // If this navigation state change has completed, listen for messages.
  if (
    !e.loading &&
    this.ref
  ) {
    this.ref.injectJavaScript(injectPostMessage);
  }

  /* We'll do something here later. */
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了可读性，我在不同的文件中定义了和`importedinjectPostMessage`。

```
export default `
(function() {
  var EMPTY_STATE = Object.create(null);
  var escape = function(str) {
    return str.replace(/'/g, '\\\\\'');
  };
  var postMessage = window.postMessage;
  window.postMessage = function() {
    if (postMessage) {
      postMessage.apply(window, arguments);
    }
    history.pushState(
      EMPTY_STATE,
      document.title,
      location.href +
      '#window.postMessage(\\\'' +
      escape(arguments[0]) +
      '\\\')'
    );
  };
})();
`; 
```

Enter fullscreen mode Exit fullscreen mode

它是一个[立即调用的函数表达式](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression),以确保我们的变量不会与网页冲突。

`EMPTY_STATE`是推入历史的内容，因为我们不会为事件监听器使用状态对象。

`escape`函数对字符串中的撇号进行转义，以便我们可以将该字符串放在撇号中。由于我们推送的导航状态不是真正的 JavaScript，也不会通过任何类型的 JavaScript 解释器传递，所以这一步并不完全必要。它只是允许我们推送的状态更接近真实的 JavaScript。

`postMessage`变量检查`postMessage`函数是否已经存在。如果是这样，我们将希望在任何`window.postMessage`调用中也执行它。

我们定义了自己的`window.postMessage`函数。它做的第一件事是执行之前的`window.postMessage`函数，如果它存在的话。

接下来，我们推进到历史状态。我们没有状态对象，所以我们使用前面提到的空对象。文档的标题不变，所以我们只使用当前的标题。文档的位置本身也没有改变:我们只是添加了一个散列。

这个哈希，我们稍后会听到，是`window.postMessage('the message')`。从设计上看，它很像 JavaScript，但不会被任何真正的 JavaScript 解释器评估。我们只需要一个唯一的散列，它不会与真实的文档内散列冲突。

### 邮件监听器📬

现在我们有了自己的`window.postMessage`事件发射器，我们需要监听它。这是位于`handleNavigationStateChange`方法顶部的代码。

```
const postMessage = e.url.match(/\#window\.postMessage\('(.+)'\)$/);
if (postMessage) {
  if (
    e.loading &&
    this.props.onMessage
  ) {
    this.props.onMessage({
      nativeEvent: {
        data: unescape(postMessage[1])
      }
    });
  }
  return;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们检查新的 URL 是否匹配我们之前定义的`postMessage`散列。如果是这样，我们将进入`return`,这样其余的导航状态改变事件监听器就不会触发。这是一个消息事件，而不是导航状态的改变(撇开技术不谈)。

每个`postMessage`事件将触发两次导航状态改变——一次针对`loading: true`,另一次几乎紧接着针对`loading: false`。我们只监听`loading: true`事件，因为它首先发生。`loading: false`事件被忽略，因为它只是一个副本。

只有当父组件传递了一个`onMessage`事件处理程序时，我们才使用包含消息的模拟事件调用该处理程序。我们在传递消息之前对其进行了转义，因为我们之前对撇号进行了转义。

unescape 函数被定义在文档的顶部，因为它是常量(不依赖于实例)并且不需要成为组件的方法。如果你喜欢代码分割，你可以导入它。

```
const unescape = str =>
  str.replace(/\\'/g, '\''); 
```

Enter fullscreen mode Exit fullscreen mode

### onnavigationstachange🕵

以上涵盖了我们拦截`window.postMessage`和用自己的`onMessage`事件监听器处理它所需的一切。我们原来的问题已经解决了——`onMessage`使用这个网络视图。然而，由于我们已经覆盖了内部的`onNavigationStateChange`监听器，父节点不再接收导航状态改变事件。

在`handleNavigationStateChange`事件监听器的底部，添加以下内容:

```
if (this.props.onNavigationStateChange) {
  return this.props.onNavigationStateChange(e);
}
return; 
```

Enter fullscreen mode Exit fullscreen mode

如果父元素包含了一个`onNavigationStateChange`属性，调用它，并给它这个导航状态改变事件。

空返回只是个人偏好——我不认为函数应该有条件地返回，即使它在功能上等同于隐式返回。

## 结论🔚

提醒一下，你可以通过安装来自 NPM 的 [`rn-webview`包](https://www.npmjs.com/package/rn-webview)来包含刚才概述的组件。也可以在 GitHub 上叉叉。

如果你喜欢这篇文章，请随意给它一个心形或独角兽。又快又简单，还免费！如果你有任何相关评论，请在下面的评论中留下。

要阅读更多我的专栏，你可以在 [LinkedIn](https://www.linkedin.com/in/charles-stover) 、 [Medium](https://medium.com/@Charles_Stover) 和 [Twitter](https://twitter.com/CharlesStover) 上关注我，或者[在 CharlesStover.com](https://charlesstover.com/)上查看我的作品集。*