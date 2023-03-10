# 使用 React 创建门户创建通知

> 原文：<https://dev.to/lisaveras/creating-notifications-with-react-create-portals-5c>

请直接跳到我的[代码本](https://codepen.io/verasveras/pen/MZOVyy)的例子。这篇文章也出现在我的个人[博客](http://lisaveras.com/easy-modals-notifications-and-chat-bubbles-in-react-with-react-create-portals/)上。

使用组件开发 UI 时，倾向于从外到内进行思考。尤其是 React，它几乎总是从一个应用外壳组件、页面组件开始，然后是更小的组件。当您必须构建“跳出”这种范式的元素时，这会使它变得具有挑战性。你把这些组件放在哪里？

React 门户为这个问题创建了一个简单的解决方案。它允许你在应用程序之外思考，并在应用程序外壳之外呈现组件，但仍然让这些组件与你的应用程序共享状态。

在本教程中，我将带您构建一个简单的通知组件，您可以将它用作任何通知、模态或聊天气泡式 UI 元素的基础。

你需要做的第一件事是在你的应用程序中创建一个新的 HTML 元素，你的通知将在其中呈现。

```
 <div id="root">
        <!-- This is your app root. You have this (maybe with a different id) already. -->
    </div>
    <div id="portal">
        <!-- This is new! It's where your new notification element will be rendered. -->
    </div> 
```

您需要做的第二件事是为您的通知创建一个容器元素。这是最酷的部分！这是您在应用程序中声明的元素，在应用程序根之外呈现。

```
 class NotificationContainer extends React.Component {
      render() {
        // grab the new element you declared above
        const domElement = document.getElementById('portal');
        return ReactDOM.createPortal(
          this.props.children,
          domElement
        );
      }
    } 
```

现在，您的通知容器需要一些内容。让我们为此制作一个组件。

```
 class Notification extends React.Component {
        render() {
            return (
            <div> The world says hello! </div>
            )
        }
    } 
```

好了，我们有了所有新的必要组件，所以让我们实际使用它们。

```
 // this is the app root that's being rendered inside that div with id="root".
    class App extends React.Component {
        render() {
            return (
                <div>
                    <h1>Hello world!</h1>
                    <NotificationContainer>
                        <Notification/>
                    </NotificationContainer>
                </div>
            );
        }
    } 
```

恭喜，你刚发了通知！当然，你可能想在它上面添加一些样式，但是基本的东西已经在那里了。

以这种方式构建通知有一些很酷的好处:

*   您可以使用应用程序的状态来控制通知。这可以让您做以下事情:
    *   延迟通知/用计时器计时。
    *   换出 NotificationContainer 的子级，以便可以呈现不同的组件
    *   在中轻松显示您的应用状态信息
*   这稍微简化了 CSS。在你的应用程序内部争论通知的 CSS 可能会很烦人，这通过将 div 与你的应用程序根放在同一级别来使它变得更容易。

如果你想看一个使用状态来延迟通知弹出的例子，这里是我的构建这个例子的代码笔。

[https://codepen.io/verasveras/embed/MZOVyy?height=600&default-tab=result&embed-version=2](https://codepen.io/verasveras/embed/MZOVyy?height=600&default-tab=result&embed-version=2)