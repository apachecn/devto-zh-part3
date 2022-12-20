# 升级一个 React 项目！

> 原文：<https://dev.to/hamed_farag/upgrade-a-react-project-46mc>

[![Atlas hold the react logo](img/85817f082fb686ebbcff54b8abb3ca40.png "Atlas hold the react logo")](https://res.cloudinary.com/practicaldev/image/fetch/s---3jjiisw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cbunolmw5m6ffz8ms2l3.jpg)

几天前，我参与了一个升级项目的挑战，这个项目是用 React 和 Redux 库编写的，维护了两年多，我们仍然在维护它，并添加新的功能来扩展它的功能。从 React 15.4 到 16.8，React 本身及其生态系统有很多更新和升级。

我将在下面列出所有我做了什么，使该项目在大量的升级和变通后再次工作。

## 1-升级 React 和 React DOM

有史以来的第一步，只是安装 React 16.8 和 React DOM 16.8，然后尝试运行应用程序。嘣，一个闪亮的白色屏幕在我的浏览器中打开了，有很多错误，但主要错误是

`React.PropTypes.string.isRequired cannot read property isRequired of undefined`

## 2-安装 PropTypes 包

所以第二步是安装 PropTypes 包，因为根据 React 文档，从 React 15.5.0 开始，PropTypes 被提取到它自己的包中。呜😄

所以我重新访问了所有使用 PropTypes 的组件，并基于 PropTypes 包对其进行了调整。

### 2.1“参”

**什么是“参”？** Refs 提供了访问在 render 方法中创建的 DOM 节点或 React 元素的方法。

**什么时候用“Refs”？**

*   管理焦点、文本选择或媒体播放。
*   触发命令式动画。
*   与第三方 DOM 库集成。

**问题出在哪里？**在我的项目中，我们使用旧 API 的“引用”，其中`ref`属性是一个**字符串**，就像`"textInput"`，DOM 节点作为`this.refs.textInput`被访问。[ [React 文档](https://reactjs.org/docs/refs-and-the-dom.html) ]

> 我的项目使用这个遗留 API， **String Refs** ，所以在 React 文档中，他们推荐使用回调模式或 createRef API。

因此，我们重新审视了整个项目，并用 createRef API 替换了遗留 API

### 2.2 引用与还原

我清楚地记得那三天知道为什么父组件不能访问其子组件的引用，为什么？因为父级包含按钮，如果用户单击它，子级中的文本框将被聚焦😄出于某种原因，这个孩子和 redux 有联系😓。

> **注:**
> 
> 如果你还记得我们是如何将 react 组件与 redux 连接起来的，那么 react-redux 库引入的 **connect** 方法就会在你的脑海中形成。
> 
> 从 **connect** 方法出来的新包装组件，默认情况下不会返回包装组件的实例，你需要告诉他你需要返回一个实例，这样你就可以访问你在这个包装组件中创建的任何方法。
> 
> 因此为了解决这个问题，`connect`接受四个不同的参数，都是可选的。按照惯例，它们被称为:
> 
> 1.  `mapStateToProps?: Function`
> 2.  `mapDispatchToProps?: Function | Object`
> 3.  `mergeProps?: Function`
> 4.  `options?: Object`
> 
> 第四个参数是一个对象，你可以发送`{withRef: true}` [ [forwaredRef](https://react-redux.js.org/api/connect#forwardref-boolean) 来解决这个问题。

由于 redux 的升级，`withRef`被弃用，取而代之的是`forwardRef`，这意味着对这个包装器组件的访问也发生了变化。

#### 展柜

```
// Old code and remember, i am react v.15.4
import React from "react";
import ReactDOM from 'react-dom';
import { connect } from "react-redux";

export default class Parent extends React.Component {
    _onClick = () => {
        this.refs["childComponent"].getWrappedInstance()._handleFocus();
    }

    render(){
        return(
            <div>
                <input type="button" value="focus" onClick={() => this._onClick()} />
                <Child ref="childComponent" />
            </div>
        )
    }
}

class ChildComponent extends React.Component {

    _handleFocus = () => {
        var elementToFocus = this.refs["inputToFocus"];
        var commentDOM = ReactDOM.findDOMNode(elementToFocus);
        var elementPosition = commentDOM.getBoundingClientRect()
        elementToFocus.focus();
    }

    render(){
        return <input type="text" ref="inputToFocus" />
    }
}

const Child = connect(null, null, null, { withRef: true })(ChildComponent); 
```

Enter fullscreen mode Exit fullscreen mode

```
// Old code and remember, i am react v.15.4
import React from "react";
import ReactDOM from 'react-dom';
import { connect } from "react-redux";

export default class Parent extends React.Component {
    _onClick = () => {
        // The new ref access
        this.child._handleFocus();
    }

    render(){
        return(
            <React.Fragment>
                <input type="button" value="focus" onClick={() => this._onClick()} />
                {/*the new usage of ref */}
                <Child ref={ele => this.child = ele} />
            </React.Fragment>
        )
    }
}

class ChildComponent extends React.Component {
    _handleFocus = () => {
        this.inputToFocus.focus();
    }

    render(){
        {/*the new usage of ref */}
        return <input type="text" ref={ele => this.inputToFocus = ele} />
    }
}

const Child = connect(null, null, null, { forwardRef: true })(ChildComponent); 
```

Enter fullscreen mode Exit fullscreen mode

很多变化，特别是在访问包装组件方面。

## 3。升级大多数软件包

因为下面的大多数包使用 React 包中的 **PropTypes** ，我把它们更新到最新，因为它们直接使用 **PropTypes** 包。

```
"draft-js":  "^0.10.5",  "draft-js-mention-plugin":  "^3.1.3",  "draft-js-plugins-editor":  "^2.1.1",  "slick-carousel":  "^1.8.1",  "react-appinsights":  "^2.0.2",  "react-click-outside":  "^3.0.1",  "react-dropzone":  "^10.0.6",  "react-notification-system":  "^0.2.17",  "react-notification-system-redux":  "^2.0.0",  "react-router-dom":  "^5.0.0",  "react-select":  "^2.4.2",  "react-textarea-autosize":  "^7.1.0",  "react-tooltip":  "^3.10.0", 
```

Enter fullscreen mode Exit fullscreen mode

但是最痛苦的包是 **redux-form 5.3.3** 和**redux-form-validation 0 . 0 . 8**😞

### 3.1 还原形式 5.3.3

我试图升级 **redux-form 5.3.3** 和**redux-form-validation 0 . 0 . 8**通常像其他包一样，但这个项目有近 6 个表单，如果我升级它们，我将完全重写这些表单，因为 redux-form 已经为 v6 完全重写，因为一个基本的设计更改。

那么解决方法是什么呢？简单地说，我下载了 redux-form 5.3.3，并通过安装 **PropTypes** 和更改 **React 来手动升级。PropTypes** 将直接成为 **PropTypes** ，感谢 redux-form 团队创建单元测试以确保一切正常。当然，我在我的项目中包含了 redux-form 代码，直到我重写了 6 个表单，当然，我安装了 redux-form 依赖于它们的额外的包。

```
"is-promise":  "2.1.0"  "react-lazy-cache":  "3.0.1"  "valid-url":  "1.0.9" 
```

Enter fullscreen mode Exit fullscreen mode

## 4。反应路由器

最大的噩梦， **React 路由器 v4** 是一次彻底的重写，而不仅仅是在之前的版本上增加了增强功能，而且 **react-router** 改成了 **react-router-dom** ，从 **v.3** 到 **v.5** 发生了巨大的变化

我按照主要文章从 **react-router v.3** 升级到 **react-router v.5**

*   [React 路由器 v4 非官方迁移指南](https://codeburst.io/react-router-v4-unofficial-migration-guide-5a370b8905a)

*   [从 v2/v3 迁移到 v4](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/guides/migrating.md)

如果你想升级你的 react-router，这两篇文章是很好的参考。

* * *

#### 当然这次升级后也有一些崩溃，我升级了一些包太像 react-dropzone 之类的由于一些配置的变化和它的用法。

##### 这次升级连续花了我 3 天 30 多个小时。当我在进行这次升级的时候，我想了很多，因为发生了很多崩溃，所以要后退一步。

原帖:[https://hamed-farag.github.io/blog/react-upgrade-v1/](https://hamed-farag.github.io/blog/react-upgrade-v1/)