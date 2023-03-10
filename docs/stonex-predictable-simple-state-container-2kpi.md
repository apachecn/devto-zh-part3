# Stonex —可预测的简单状态容器

> 原文：<https://dev.to/js2me/stonex-predictable-simple-state-container-2kpi>

大家好！🙂

我想推荐[我的库](https://github.com/acacode/stonex)，它允许你比其他类似的库更好更简单地管理和存储数据。

使用 React like 语法的可预测状态容器

解决方案我决定创建 stonex 的原因是与另一个类似于库的 stonex 一起工作的经历，我不喜欢那里的大多数方法，我希望创建自己的解决方案。我应该为创建类似于 stonex 模块的结构写太多的代码，还需要附加太多的依赖。正因为如此，我决定创建 stonex，让你的前端开发更容易！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ js2me ](https://github.com/js2me) / [ stonex](https://github.com/js2me/stonex)

### 💡JavaScript/TypeScript 应用程序的简单状态容器💡

<article class="markdown-body entry-content container-lg" itemprop="text">

[![stonex](img/84e7029b29f021c62128f5d371c28403.png)](https://www.npmjs.com/package/stonex)

[![](img/acc0d8fef4bc3a2316ab6b69af11f4d5.png)](https://raw.githubusercontent.com/js2me/stonex/master/./LICENSE)[![](img/4a38e7b6988cc402bd42a66f42173fd2.png)](https://www.npmjs.com/package/stonex)[![](img/1140591ce5ff38093ff5687a6b110e32.png)](https://travis-ci.org/acacode/stonex)[![](img/2362e9a9ec8ff98e7c1c58e21e7c05c7.png)](http://npm-stat.com/charts.html?package=stonex)[![](img/6490e4d034f05b46f046a2c8009eabe2.png)](https://bundlephobia.com/result?p=stonex)[![](img/5b129874b5bf3d05d70d5cb171e3a6c8.png)](https://bundlephobia.com/result?p=stonex)

<g-emoji class="g-emoji" alias="cyclone" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f300.png">🌀</g-emoji>JavaScript/TypeScript 应用程序的状态容器<g-emoji class="g-emoji" alias="cyclone" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f300.png">🌀️</g-emoji>

#### 目录

那是什么？
[如何使用](https://raw.githubusercontent.com/js2me/stonex/master/#-how-to-use)
[文档](https://raw.githubusercontent.com/js2me/stonex/master/#-documentation) :
- [Stonex 商店](https://raw.githubusercontent.com/js2me/stonex/master/#stonexstoresource-link)-[Stonex 模块](https://raw.githubusercontent.com/js2me/stonex/master/#stonexmodulesource-link)
-[state worker](https://raw.githubusercontent.com/js2me/stonex/master/#stateworkersource-link)
-[createStoreBinder](https://raw.githubusercontent.com/js2me/stonex/master/#createstorebindersource-link)
[许可证](https://raw.githubusercontent.com/js2me/stonex/master/#-license)

## ❓ 那是什么？

这是一个用于管理/存储数据的简单易用的库。
它允许您正确地存储和管理数据，还可以将所有业务逻辑组合到单独的`Stonex`模块中。

很容易配置，如果需要，大多数东西都可以被覆盖。

和其他类似的库中一样，每个`Stonex`模块都有自己的**状态**和**动作**。但是，与其他库不同，大多数`Stonex`特性都是“开箱即用”的，例如，创建异步动作。

模块的语法几乎与`ReactJS`组件的语法相同。

另外，目前`Stonex`正在支持与 [ReactJS (react-stonex)](https://github.com/acacode/react-stonex) 的集成

## <g-emoji class="g-emoji" alias="bulb" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4a1.png">💡</g-emoji>如何使用

**1。**需要安装它:

```
  npm i -S stonex
  # or
```

…</article>

[View on GitHub](https://github.com/js2me/stonex)

另外，Stonex 已经得到了 ReactJS 的支持！

# 为什么是 Stonex？

在组件(react、angularjs、vue 等)之间共享数据的应用程序中，知道一个状态应该位于何处可能会令人困惑。理想情况下，组件中的数据应该只存在于一个组件中。因此在兄弟组件之间共享数据变得更加困难。

[![](img/96411e6e025ea2afd144768286679bd3.png)](https://i.giphy.com/media/KKtAZiNVEeU8/giphy.gif)

让我们来看看 ReactJS 组件的例子:

```
class Login extends React.Component {

  changeForm = ({ target }) => {
   this.setState({ [target.name]: target.value })
  }

  render() {
   const { firstName, lastName } = this.state

   return (
     <div>
       <input value={firstName} name="firstName" onChange={this.changeForm} />
       <input value={lastName} name="lastName" onChange={this.changeForm} />
     </div>
   )
  }

} 
```

如果需要在应用程序的完全不同的部分显示这个组件的状态数据，我该怎么办？当然，我们可以使用上下文 API，但它不会是一个通用且不太灵活的解决方案。

但是，如果我们有一个包含所有必要应用程序数据的 stonex reactjs 组件，会怎么样呢？以这种方式工作的 Stonex，所以以这种方式发送应用程序数据将不会有任何困难！

使用 stonex 解决方案会是什么样子:

```
// login component
import { connect } from 'react-stonex'

const Login = ({ firstName, lastName, changeAuthData }) => (
 <div>
   <input value={firstName} onChange={e => this.changeAuthData('firstName', e.target.value)} />
   <input value={lastName} onChange={e => this.changeAuthData('lastName', e.target.value)} />
 </div>
)

export default connect((state, modules) => ({
  firstName: state.auth.firstName,
  lastName: state.auth.lastName,
  changeAuthData: modules.auth.changeAuthData,
}))(Login)

// somewhere in sidebar component
import { connect } from 'react-stonex'

const Sidebar = ({ firstName, lastName }) => (
 <div>
   <span>Hello, {firstName} {lastName}</span>
 </div>
)

export default connect((state, modules) => ({
  firstName: state.auth.firstName,
  lastName: state.auth.lastName,
}))(Sidebar) 
```

如您所见，在组件之间交换应用程序数据并不困难。而且我们来看看 Stonex 的 Auth 模块:

```
export default {
  state: { firstName: '', lastName: '' }

  changeAuthData(name, value) {
    this.setState({
      ...this.state,
      [name]: value
    })
  }
} 
```

看起来很容易，不是吗？😁

# 深潜到斯通克斯

[![](img/7c0e02c610846512390b51dae68fc260.png)](https://i.giphy.com/media/l2Je6HrCWMzM635bW/giphy.gif)

之前，你需要安装 stonex

```
npm i -S stonex
# or using yarn
yarn add stonex 
```

Stonex 目前有出口:`StonexStore`、`StonexModule`、`createStoreBinder`、`StateWorker`。*后两种出口更需要中间产品。*

## [斯通克斯托](https://github.com/acacode/stonex#stonexstoresource-link)

这就是把你所有模块连接在一起，并使美妙的存储容器！😉

> 如果我想创建一个 StonexStore 实例，我需要什么？

几乎没有，只是你的模块的唯一对象！参见示例:

```
// store.js

import { StonexStore } from 'stonex'
import modules from './modules'

const store = new StonexStore(modules) 
```

应该是一个带有键的对象，其中键是你的模块的名字，值是 StonexModule 类的引用(不仅仅是类，它可能只是对象，但关于这一点会写得更详细)。参见示例:

```
// ./modules/index.js
import UsersModule from './UsersModule'
import AuthModule from './AuthModule'
import ModalsModule from './ModalsModule'

const modules = {
  users: UsersModule,
  auth: AuthModule,
  modals: ModalsModule
}

export default modules 
```

如果你想添加一些中间件或者覆盖 stonex 处理状态的地方，你可以使用额外的参数`StonexStore`。更多关于 Stonex middlewares 的信息请点击[这里](https://github.com/acacode/stonex#stateworkersource-link)。

下面是如何添加一些修改器和自定义状态机的例子

```
// store.js

import { StonexStore } from 'stonex'
import modules from './modules'

import Logger from './modifiers/Logger'
import AppStateWorker from './common/AppStateWorker'

const storeConfiguration = {
  modifiers: [ // middlewares
    Logger
  ],
  stateWorker: AppStateWorker
}

const store = new StonexStore(modules, storeConfiguration) 
```

## [斯通克斯模块](https://github.com/acacode/stonex#stonexmodulesource-link)

Stonex 模块它是一个特殊的类/对象，包含所有与特定状态相关的信息/动作。更多关于它的信息请点击。

外观:

```
import { StonexModule } from 'stonex'

class UsersModule extends StonexModule {
  state = []
  addUser = user => this.setState([...this.state, user])
  removeAllUsers = () => this.resetState()
}

export default UsersModule 
```

正如我前面说过的，stonex 模块可以作为类的引用，但并不总是这样。你可以创建一个纯 stonex 模块(*这是相同的，但看起来更容易* ):

```
// ./modules/index.js

// don't worry about context. It links when module attaching to the store!
// Also all methods (like 'this.setState' ) from StonexModule class automatic will been inherited
const pureModule = {
  state: {},
  addItem(item){
   this.setState({...this.state, [item.name]: item })
  }
}

const modules = {
  pureModule
}

export default modules 
```

> 注意:纯模块不支持箭头函数作为属性。

# 结论

正如我所说的这个项目并没有结束，而是已经包含在 NPM。我正在我最喜欢项目中使用它，并将很快在 stonex 上迁移一些企业 web 项目。

目前，我正在编写单元测试的覆盖代码，并在任何可能的地方编写文档。

**感谢阅读！我真的希望这篇文章能帮助你。**

很高兴看到关于这个项目的任何贡献或问题！🙂

编码快乐！🌟✨🎉

### 额外油墨

Github 上有 react 和 stonex 的样例项目[在这里](https://github.com/js2me/react-stonex-webpack-scss-boilerplate)T2:[https://www.npmjs.com/package/stonex](https://www.npmjs.com/package/stonex)T5】Github:[https://github.com/acacode/stonex](https://github.com/acacode/stonex)

[![](img/72214424c0ce50bb1313546b32128b07.png)](https://i.giphy.com/media/Ydvn0G7Agtk9G/giphy-downsized.gif)