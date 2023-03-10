# 动态进口的力量

> 原文：<https://dev.to/duranenmanuel/the-power-of-dynamic-imports-4hnd>

*本文原载于[Enmascript.com](https://enmascript.com/articles/2018/10/12/the-power-of-dynamic-imports-in-javascript-and-react)，去那里可以获得更好的阅读体验*。

动态导入很棒，它们已经存在了一段时间，并假设我们对待模块/组件的方式有了很多改进。在这篇文章中，我将解释它们是如何工作的，为什么它们这么好，以及我们通过使用它们可以完成的一些事情。

如果你读过我之前关于 javascript 中的[工厂模式的文章，你可能已经知道在 javascript 中动态创建实例有一些好的方法，*但是*，这并不意味着导入的其他对象*(未实例化的)*也不会被添加到我们的初始包中，因为，毕竟，我们导入了所有的对象，正如我所说的，唯一的区别是*它们是动态实例化的*。如果我告诉你，我们实际上可以动态地导入模块的内容并在以后需要的时候包含它，会怎么样？](https://enmascript.com/articles/2018/10/05/javascript-factory-pattern)

## 引入动态导入

动态导入()是目前处于阶段 3 的一个 [javascript 提案，因为我们知道 ECMAScript 模块是*静态的*，但是由于这个提案，它们并不总是必须如此，简而言之，动态导入()语法允许我们通过使用 promises 或 async/await 语法按需加载模块，这方面的一个示例/实现是:](https://github.com/tc39/proposal-dynamic-import)

```
import('./Module').then(Module => Module.method()); 
```

现在，想象一下你的站点中的应用程序，包括模态、工具提示等组件，SPA 等技术，以及 React 等库中的应用程序，我们可以做很多事情:

1.  组件/模块预取
2.  按需加载代码(Lazyloading)
3.  无需包含未使用代码的条件加载。

动态导入的明显影响体现在我们的包大小的减少、我们的请求的*大小/有效负载响应*以及用户体验的整体改善，尤其是在 SPA 预取代码时。

## 使用 React 和 async/await 实现

假设您有一个组件，可以在特定事件(点击)时延迟加载或获取，该组件需要动态添加到我们的应用程序中，并且需要利用一个允许我们更新 react 应用程序以响应更改的结构，我知道，还有比 React 的状态更好的结构吗？，它允许我们在应用程序中进行动态更改，所以让我们继续使用它。

我将做一个非常简单的实现，当点击一个 div 时，在标题中动态添加到 Github 的链接。

首先让我们开始创建一个非常愚蠢的组件，它只在菜单中呈现 Github 链接，我们称它为 **DynamicComponent.js** ，它的代码是:

```
import React from 'react';
import { GitHub } from 'components/Icons/SocialIcons';

const DynamicComponent = () => (
    <a
        className="github-link"
        target="_blank"
        rel="noopener noreferrer"
        href="https://github.com/enmanuelduran/enmascript">
        <GitHub />
        GitHub
    </a>
);

export default DynamicComponent; 
```

现在，在 **Header.js** 组件中，我将添加代码以在单击某个 div 时动态呈现链接，我将注释代码中的所有内容，以便您在阅读时能够理解:

```
class Header extends React.Component {

    /* Sets the state to save the dynamic component when needed */
    state = {
        DynamicComponent: null,
        ...
    };

    /*
     * Function to be called onClick event.
     * Waits for the import to be completed and stores the default exported
     * value in the state so we can rendered when needed.
     */
    handleOnClick = async () => {
        const DynamicComponent = await import('./DynamicComponent');

        this.setState({
            DynamicComponent: DynamicComponent.default
        });
    };

    render() {
        ...

        /* Gets the dynamically imported component */
        const { DynamicComponent } = this.state;

        return (
            <header className="header">

                {/* Calls the function needed onClick */}
                <div
                    onClick={this.handleOnClick}>
                    Load Github!
                </div>

                <nav>
                    ...

                    {/* Waits for the component to exist and renders it */}
                    {DynamicComponent && <DynamicComponent />}
                </nav>
            </header>
        );
    } 
```

非常简单，这里有一个实现工作的可视化示例，您会注意到，当单击黄色按钮时，包含 Github 链接的组件被添加到菜单中，并被动态获取，如网络请求所示:

[视频](https://enmascript.cimg/2018-10-11-dynamic-imports.mp4)

这个特性为许多可能性和改进打开了大门，不仅是 React，而且是整个 Javascript，希望在阅读本文后，您已经开始发现代码中可以添加动态导入来进行改进的地方。如果你喜欢这篇文章，请在你的社交网络或朋友中分享。你可以在推特上用用户名 [@duranenmanuel](https://twitter.com/duranenmanuel) 或者用我的电子邮件【duranenmanuel@gmail.com[联系我。](mailto:duranenmanuel@gmail.com)

就这样了，伙计们，下次再见！