# 驯服具有联合类型的复杂反应状态

> 原文：<https://dev.to/joefiorini/taming-complex-react-state-with-union-types-1l97>

我在许多 React 项目中看到的一个反模式是在组件状态上有多个布尔标志，用于控制同一个组件的不同方面。这些标志经常会相互矛盾，导致非常微妙的错误，等到发现时已经太晚了。让我们看一个这种反模式的例子，看看 TypeScript 的丰富类型系统如何允许我们以一种更易于团队成员理解的方式来表达我们的代码，并让我们相信我们的代码不会意外中断。

### 一个预警组件

web 应用程序的一个常见需求是在页面上显示一个警告，确认操作成功或显示一条错误消息。最简单的开始方式是使用一个布尔标志来跟踪这个状态。让我们考虑一个在用户将数据保存到服务器的操作之后显示警告的例子。

```
// UsernameForm.tsx

import React from 'react'

function UsernameForm() {
  const [isShowing, setIsShowing] = useState(false)
  const [isError, setIsError] = useState(false)
  const [alertMessage, setAlertMessage] = useState('')
  const [username, setUsername] = useState('')

  async function saveUsername() {
    const response = await saveUsernameViaApiCall()

    if (response.errorMessage) {
      setAlertMessage(response.errorMessage)
      setIsError(true)
    } else {
      setAlertMessage('Your username has been updated.')
    }
    setIsShowing(true)
  } catch (e) {
    setIsError(true)
  }
  }

  return (
    <section>
      <h1>Change Your Username</h1>
      {isShowing ?
        <Alert status={isError ? 'error' : 'success'}>
          {alertMessage}
        </Alert>
      : null}
      <form onSubmit={saveUsername}>
        <div>
          <label htmlFor="username">Username</label>
          <input
            type="text"
            id="username"
            name="username"
            onChange={e => {
              setUsername(e.currentTarget.value)
            }}
          />
        </div>
      </form>
      <button>Save</button>
    </section>
  )
} 
```

。CSS-OAM BGA { text-align:left；行高:1.7；背景色:# ebf 3 ff；填充:9px 18px 边框-半径:9px 边框:纯色# b2bdce 2px}

<aside>注意上面的文件名有`.tsx`扩展名。这个扩展允许我们用 TypeScript 编写 React 组件。更多信息参见打字手册的 [React & Webpack 部分。](https://www.typescriptlang.org/docs/handbook/react-&-webpack.html)</aside>

在上面的例子中，我们有一个允许用户更改用户名的表单。提交表单后，我们进行 API 调用来更新用户名，一旦完成，我们就将`isShowing`状态设置为`true`。我们在这里假设我们的 API 返回一个带有`"errorMessage"`属性的错误，如果我们得到一个`errorMessage`的值，我们使用它作为消息显示在警报中。在表单上方，我们显示一条警告，通知用户他们的用户名已成功更新，如果保存不成功，则显示错误消息。

### 解除警报

然后反馈开始出现。产品了解到用户希望能够消除此警报。当然，我们的产品经理希望看到有多少用户真正点击了“关闭”按钮来关闭它，因此我们需要跟踪它被点击的次数。有许多方法可以实现这一点(这超出了本文的范围)，但经过仔细考虑，我们的团队决定我们应该将解雇作为警报的另一种状态进行跟踪，这样我们可以在工作器中使用后台脚本来定期将该属性传递给我们的分析跟踪工具。因此，我们添加了另一个要在我们的状态中跟踪的值。

```
function UsernameForm() {
  const [isShowing, setIsShowing] = useState(false)
  const [isError, setIsError] = useState(false)
  const [alertMessage, setAlertMessage] = useState('')
  const [isDimissed, setIsDismissed] = useState(false)

  const closeAlert = () => {
    setIsShowing(false)
    setIsDimissed(true)
  }

  async function saveUsername() {
    try {
      const response = await saveUsernameViaApiCall()

      if (response.errorMessage) {
        setAlertMessage(response.errorMessage)
        setIsError(true)
      } else {
        setAlertMessage('Your username has been updated.')
      }
      setIsShowing(true)
    } catch (e) {
      setIsError(true)
    }
  }

  return (
    <section>
      <h1>Change Your Username</h1>
      {isShowing && !isDismissed ? (
        <Alert
          status={isError ? 'error' : 'success'}
          onClose={closeAlert}
          message={alertMessage}
        />
      ) : null}
      <form onSubmit={saveUsername}>
        <div>
          <label htmlFor="username">Username</label>
          <input
            type="text"
            id="username"
            name="username"
            onChange={e => {
              setUsername(e.currentTarget.value)
            }}
          />
        </div>
      </form>
      <button>Save</button>
    </section>
  )
} 
```

这里我们添加了一个回调到我们的`Alert`组件，当关闭按钮被点击时通知我们。然后我们调用一个`closeAlert`函数来设置`isShowing`和`isDimsissed`来消除警报。

### 定义复杂度

这是一个相对简单的例子，但是希望它已经开始变得越来越复杂。每次显示警报时，我们必须记住设置 3 个状态值，当警报关闭时，我们必须设置其中的两个。然而，所有这些值都是紧密耦合的:即。随时`isError`是`true`，`isShowing`也是`true`；`isShowing`和`isDimsissed`是互斥的，它们不应该同时为真。

查看代码时，很容易不小心引入一个非常微妙的错误。由于显示警报的复合条件(`isShowing && !isDismissed`)，如果我们在关闭警报时不将`isShowing`重置为`false`，它仍然会像我们希望的那样消失。因此，想象有人对这个组件进行一些重构并删除`setIsShowing(false)`行并不太牵强，因为从技术上来说这是不需要的。几个月后，假设另一个开发人员正在修复一个错误，并在再次显示警告之前引入一些代码来重置`isDismissed`。因为`isShowing`从未被重置为`false`，所以警报现在同时显示和解除。像这样的矛盾状态有助于进行伟大的物理实验，但这只是成千上万的剪纸中的一小部分，它们会使大型代码库的维护变得非常困难和昂贵。

你如何避免这种情况？我们从非常简单的东西开始，但是随着我们的需求变得越来越复杂，我们将这种复杂性引入到我们的代码中。乍一看，这个解决方案似乎并不复杂，但是请记住，simple 的一些同义词是“清晰”和“可理解”。这个解决方案隐藏了这些值之间如此重要的关系，这使得它并不简单。避免这种情况的方法是当你注意到复杂性开始出现的时候进行重构。

我们现在已经到了一个大问题:我们如何以这样一种方式来建模这个状态，使得我们不可能出现意外的状态，并且仍然满足需求？如果我们将 3 个布尔标志合并成一个名为`alertStatus`的字符串，可能值为`"not_shown"`、`"success"`、`"error"`和`"dismissed"`，会怎么样？

```
function UsernameForm() {
  const [alertStatus, setAlertStatus] = useState('not_shown')
  const [alertMessage, setAlertMessage] = useState('')

  async function saveUsername() {
    try {
      const response = await saveUsernameViaApiCall()

      if (response.errorMessage) {
        setAlertMessage(response.errorMessage)
        setAlertStatus('error')
      } else {
        setAlertMessage('Your username has been updated.')
        setAlertStatus('success')
      }
    } catch (e) {
      setAlertMessage(
        'An unknown error occurred trying to update your username. Please try again later.',
      ),
        setAlertStatus('error')
    }
  }

  return (
    <section>
      <h1>Change Your Username</h1>
      {alertStatus !== 'not_shown' && alertStatus !== 'dismissed' ? (
        <Alert
          status={alertStatus}
          onClose={() => {
            setAlertStatus('dismissed')
          }}
          message={alertMessage}
        />
      ) : null}
      <form onSubmit={saveUsername}>
        <div>
          <label htmlFor="username">Username</label>
          <input
            type="text"
            id="username"
            name="username"
            onChange={e => {
              setUsername(e.currentTarget.value)
            }}
          />
        </div>
      </form>
      <button>Save</button>
    </section>
  )
} 
```

通过合并这些标志，我们使得警报不可能同时处于两种不同的状态。我们还将显示警报所需的值的数量减少了三分之一，使得在呈现警报之前忘记设置所有必要值的可能性大大降低。

#### 我们还有一个问题

我们可能已经从代码中消除了矛盾的状态，但是仍然有一个问题。因为我们对`alertStatus`使用了字符串，所以很容易将其设置为无效值；没有什么可以阻止某人调用`setAlertStatus("cat")`并导致警告显示错误。感谢 JavaScript 的动态特性，无需在`Alert`组件周围实现额外的条件，没有绝对的方法来防止这种情况。您*可以*使用 React 的`prop-types`库在浏览器中发出警告，当它收到一个无效的`status`值时。然而，根据我的经验，道具类型的警告经常被忽略；你可以配置一个[测试套件在无效道具类型警告](https://www.npmjs.com/package/jest-prop-type-error)时失败，或者确保每个人都在他们的编辑器中集成了 eslint，并使用 [React eslint 插件](https://github.com/yannickcr/eslint-plugin-react)在可能的情况下验证道具类型。

### 短暂涉足打字稿

如果您想要一种更简单的方法来帮助您的团队成员(很可能是您未来的自己)正确使用您的代码，TypeScript 提供了一些功能，当这些功能一起使用时，可以很好地防御无效状态。如果您已经熟悉了类型别名、字符串文字类型和联合类型，可以随意跳到下一节。否则，在继续之前，请继续阅读这些概念的介绍。

#### 键入别名

> 类型别名为类型创建新名称。–<cite>[打字手册:高级类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-aliases)</cite>

例如，您可以将`string`类型重命名为`ID`,以显示特定的字符串代表记录的唯一标识符。

```
type ID = string 
```

#### 字符串文字类型

> 字符串文字类型允许您指定字符串必须具有的确切值。–<cite>[打字手册:高级类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#string-literal-types)</cite>

```
const message = 'Hello world' 
```

在上面的例子中，`message`可能看起来将具有类型`string`，但是如果你在一个具有良好的类型脚本支持的编辑器中查看它，它实际上具有类型`"Hello world"`。这是一个字符串类型的例子。在这种情况下，类型是根据该字符串的初始值推断出来的，但是因为字符串文字类型是类型，所以可以使用类型别名使它们可重用。

```
type Message = 'Hello world'

const message: Message = 'blah' 
```

[打字操场上的视图](https://www.typescriptlang.org/play/index.html#src=type%20Message%20%3D%20'Hello%20world'%0D%0A%0D%0Aconst%20message%3A%20Message%20%3D%20'blah')

使用上面的链接，你可以看到给`message`赋值一个文字值而不是`"Hello World"`会产生一个编译错误，从而允许你将字符串限制为一个特定的可能值。

此时，您可能想知道这些特性将如何帮助我们的警报示例。我们已经看到了如何使用类型强制执行单个字符串值，但是我们的示例有四个互斥的值。我们需要的是一种方法来执行一系列价值观中的一个。这就是工会类型的用武之地。

#### 引入工会类型

> 联合类型描述可以是几种类型之一的值。我们使用竖线(|)来分隔每种类型，所以`number | string | boolean`是值的类型，可以是数字、字符串或布尔值。–<cite>[打字手册:高级打字](https://www.typescriptlang.org/docs/handbook/advanced-types.html#union-types)</cite>

在 JavaScript 中围绕 [联合类型已经有了](https://github.com/paldepind/union-type)[很多](https://dev.to/avalander/union-types-with-javascript-4emo) [的解释](https://medium.com/@justintormey/write-beautiful-js-with-union-types-ddd11e5e9241) [。如果你对联合类型背后的理论感兴趣，我推荐](https://medium.com/fullstack-academy/better-js-cases-with-sum-types-92876e48fd9f)[这篇关于不同语言中联合类型实现的概述](https://github.com/paldepind/union-type) <sup id="fnref-1">[1](#fn-1)</sup> 。让我们看一个使用联合类型的快速示例，然后我们将回到我们的常规主题。

想象一下一个名为`padLeft`的函数，它用一个特定的字符或多个空格填充一个字符串。

```
padLeft('example', 5) // => " example"
padLeft('example', '?') // => "?example" 
```

这个函数有两个不同的类型签名，一个接受一个`string`和一个`number`，另一个接受两个`string`，你也可以说它有一个单一类型签名:它接受一个`string`和一个参数，这个参数可以是`string`或`number`。第二个参数表示联合类型。

```
function padLeft(value: string, padding: string | number) {
  // ...
} 
```

您可以使用任何有效的 TypeScript 类型作为联合类型的一部分:记录、数组、函数、字符串、数字，甚至字符串文字类型——这正是我们的警报所需要的！

### 回到我们的例子

回到我们的警报示例，我们现在知道我们可以组合类型别名、字符串类型和联合类型，以确保我们的组件永远不会处于无效状态。

```
// Alert.tsx

export type AlertStatus = 'not_shown' | 'success' | 'error' | 'dismissed'

function Alert({ status, message }: { status: AlertStatus; message: string }) {
  return <div className={`alert alert--${status}`}>{message}</div> }

export default Alert 
```

这里我们创建一个基本的`Alert`component，它接受一个名为`status`的`AlertStatus`类型的道具；它可以有四个可能的值，通过用`|`分隔它们，我们创建了一个只能是四个值之一的联合类型。我们使用状态来确定警报的 CSS 类。我们还稍微修改了接口，使 TypeScript 更容易理解:它还采用了类型为`string`的`message`道具，并在`div`中显示它。注意，我们还导出了`AlertStatus`类型；这使得它可以在其他地方使用。

```
// UsernameForm.tsx
import Alert, { AlertStatus } from './Alert'

function UsernameForm() {
  const [alertStatus, setAlertStatus] = useState('not_shown' as AlertStatus)
  const [alertMessage, setAlertMessage] = useState('')

  async function saveUsername() {
    try {
      const response = await saveUsernameViaApiCall()

      if (response.errorMessage) {
        setAlertMessage(response.errorMessage)
        setAlertStatus('error')
      } else {
        setAlertMessage('Your username has been updated.')
        setAlertStatus('success')
      }
    } catch (e) {
      setAlertMessage(
        'An unknown error occurred trying to update your username. Please try again later.',
      ),
        setAlertStatus('error')
    }
  }

  return (
    <section>
      <h1>Change Your Username</h1>
      {alertStatus !== 'not_shown' && alertStatus !== 'dismissed' ? (
        <Alert
          status={alertStatus}
          onClose={() => {
            setAlertStatus('dismissed')
          }}
          message={alertMessage}
        />
      ) : null}
      <form onSubmit={saveUsername}>
        <div>
          <label htmlFor="username">Username</label>
          <input
            type="text"
            id="username"
            name="username"
            onChange={e => {
              setUsername(e.currentTarget.value)
            }}
          />
        </div>
      </form>
      <button>Save</button>
    </section>
  )
} 
```

现在我们在我们的`UsernameForm`组件中使用`AlertStatus`类型。我们需要做的唯一改变是添加一个[类型断言](https://www.typescriptlang.org/docs/handbook/basic-types.html#type-assertions)。这给 TypeScript 编译器提供了一个提示，我们期望`useState`来处理`AlertStatus`类型。

现在，如果我们试图用一个它不明确支持的值来调用`setAlertStatus`(即。`"warning"`)我们在尝试构建代码时会得到一个编译器错误。然而，发送到浏览器的代码将与我们编写的代码相对相同(除了标准的 Babel 转换)——TypeScript 将删除类型声明和断言，我们有信心我们的代码将按预期工作。

### 包装完毕

在这篇文章中，我们看到了一种使用 TypeScript 的联合类型来简化 React state 中常见的复杂示例的方法。如果您不能(或不想)使用 TypeScript(如果您还没有使用过，我强烈建议您尝试一下),我将在最后包含一些为运行时联合类型提供选项的链接；这需要一点时间来适应，但是类型允许代码中的表达性和安全级别，否则你根本无法获得)。本文中的例子可以很容易地快速说明问题，但是如果您是一个很难维护的大型代码库，您可能会在许多地方发现这种反模式。对抗复杂性的关键是在你看到它到来的时候立即重构。我希望这篇文章有助于激发你的直觉，让你注意到自己的状态何时变得复杂，以免为时过晚。

* * *

1.  如果您查看其中的一些链接，您会发现 JavaScript 中有一些库实现提供了联合类型，而没有使用 TypeScript。我更喜欢 TypeScript 方法，因为它提供了强大的构建时功能，而不需要运行时的额外代码。↩