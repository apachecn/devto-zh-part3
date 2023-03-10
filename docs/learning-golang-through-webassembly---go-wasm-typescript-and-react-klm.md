# 通过 WebAssembly 学习 Golang-Go、WASM、打字和反应

> 原文：<https://dev.to/aaronpowell/learning-golang-through-webassembly---go-wasm-typescript-and-react-klm>

## 构建应用程序

这实际上是我在博客上写的一系列文章中的最后一篇，它彻底颠覆了 Go 的 WebAssembly 方法。如果你想看整个系列[，从这篇文章](https://www.aaron-powell.com/posts/2019-02-04-golang-wasm-1-introduction/)开始。

到目前为止，我们已经看到了许多最终将成为应用程序的小部分，是时候解决这个问题了，是时候构建一个 web 应用程序了。

我已经决定，对于这个应用程序，我们将拼凑一些您可能经常使用的其他工具，我们将使用 React 作为 UI 库，使用 TypeScript 作为编译到 JavaScript 的语言。但没有理由你不能用 Vue、Angular 或任何其他 UI 库替换 React，并放弃“普通的旧 JavaScript”的 TypeScript。你会在我的 GitHub 上找到演示应用[。](https://github.com/aaronpowell/go-wasm-experiments)

## 设置我们的应用

为了开始，我们将使用 [`create-react-app`和 TypeScript](https://facebook.github.io/create-react-app/docs/adding-typescript) ，我不会重复做那个设置，React 文档为我做了很好的工作。你不必使用`create-react-app`，这只是一个非常简单的引导方法，但是如果你有信心不用它，那就跳过这一步。

一旦你创建了一个应用程序，我们将需要[弹出`create-react-app`](https://facebook.github.io/create-react-app/docs/available-scripts#npm-run-eject) ，因为我们需要能够修改`webpack.config.js`文件，这只能在你弹出`create-react-app`时才能完成。

## 得到所有 WASM-y

我们将从使用`npm`或`yarn` :
添加在上一篇文章中创建的[加载器开始](https://www.npmjs.com/package/golang-wasm-async-loader)

```
npm install --save-dev golang-wasm-async-loader
# or
yarn add golang-wasm-async-loader 
```

Enter fullscreen mode Exit fullscreen mode

然后编辑`configs/webpack.config.js`文件来添加我们的加载程序(按照文件中的说明来放置它):

```
{
    test: /\.go$/,
    loader: 'golang-wasm-async-loader'
}, 
```

Enter fullscreen mode Exit fullscreen mode

## 添加我们的 WASM

我要做一个小应用程序，显示至少 2 个数字输入字段，并将所有的值相加得到一个和，代码如下:

```
package main

import (
    "strconv"
    "syscall/js"

    "github.com/aaronpowell/webpack-golang-wasm-async-loader/gobridge"
)

func add(i ...js.Value) js.Value {
    ret := 0

    for _, item := range i {
        val, _ := strconv.Atoi(item.String())
        ret += val
    }

    return js.ValueOf(ret)
}

func main() {
    c := make(chan struct{}, 0)
    println("Web Assembly is ready")
    gobridge.RegisterCallback("add", add)
    <-c
} 
```

Enter fullscreen mode Exit fullscreen mode

非常简单，我们使用`range`来检查`js.Value`的分布，将每一个从一个字符串转换成一个数字，对它们求和，然后返回装箱的`js.Value`。

接下来，在我们的输入字段中，我为这个
创建了一个文件`NumberInput.tsx`

```
import * as React from 'react';

export interface NumberInputProps {
    value: number
    onChange: (value: number) => void
}

const NumberInput : React.SFC<NumberInputProps> = ({ value, onChange }) => (
    <input type="number" value={value} onChange={(e) => onChange(parseInt(e.target.value, 10))} /> );

export default NumberInput; 
```

Enter fullscreen mode Exit fullscreen mode

它是一个无状态组件，接收两个属性，一个是输入字段的值，另一个是在输入字段发生变化时执行的回调。

最后，我们将制作`<App />` :

```
import * as React from 'react';
import wasm from './main.go';
import NumberInput from './NumberInput';

const { add } = wasm;

interface State {
    value: number[]
    result: string
}

class App extends React.Component<{}, State> {
    constructor(props: {}) {
        super(props);

        this.state = {
            value: [0, 0],
            result: '0'
        };
    }

    async updateValue(index: number, value: number) {
        //todo
    }

    render() {
        return (
            <div>
                <p>Enter a number in the box below, on change it will add all the numbers together. Click the button to add more input boxes.</p>
                {this.state.value.map((value, index) =>
                    <NumberInput key={index} value={value} onChange={i => this.updateValue(index, i)} />
                )}
                <button type="button" onClick={() => this.setState({ value: [...this.state.value, 0]})}>More inputs!</button>
                <p>Value now is {this.state.result}</p>
            </div>
        );
    }
  }

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

好的，非常基本，它是带状态的组件(抱歉，这里没有 redux 或 hooks😝)其中 state 包含输入值和当前总和的数组。`render`将对输入值进行循环，用该值创建我们的`<NumberInput />`组件，并给它一个函数，当完成时该函数将调用`updateValue`。说明它已初始化为 2 个输入，但您可以通过屏幕上显示的按钮添加更多输入。

在文件的顶部，你会看到我们正在从上面导入`main.go`文件，并使用[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)来得到`add`函数，或者更准确地说，从加载程序为我们创建的`Proxy`中引用它。

现在是时候完成我们的`updateValue`方法了。但是事实证明，使用`add`函数可能有点棘手。当然，我们可以把它定义为 WASM 的`any`财产，但是如果我们想在它的表现方式上更有智慧呢？

```
 async updateValue(index: number, value: number) {
        let newValues = this.state.value.slice();
        newValues[index] = value
        let result = await add<number, string>(...newValues);
        this.setState({ value: newValues, result });
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 使用类型与我们的代理

我们如何确保 TypeScript 知道我们要传递给一个不存在的函数的参数是什么类型的？最终我们想要摆脱`any`，取而代之的是我们想要使用 TypeScript 泛型！

我们可以用两种方法中的一种来实现，第一种方法是创建一个定义文件，为我们的 WASM 导入创建一个显式接口:

```
declare module "*.go" {
    interface GoWrapper {
        add: (...params: number[]) => Promise<string>
    }

    var _: GoWrapper
    export default _
} 
```

Enter fullscreen mode Exit fullscreen mode

我已经创建了一个名为`definitions.d.ts`的文件，它位于`App.tsx`文件旁边，通过为`*.go`声明模块，这意味着这个声明文件适用于任何导入的 Go 文件。我们也可以删除一般的参数，这很好，但如果我们想开始添加更多的 Go 函数，就有问题了，我们必须不断地编辑这个文件来包含它们。

所以疯狂使用 generic 怎么样！

```
declare module "*.go" {
    interface GoWrapper {
        [K: string]: <T = any, R = any>(...params: T[]) => Promise<R>
    }

    var _: GoWrapper
    export default _
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，请听我解释:

*   我们说我们有(`GoWrapper`)类型的键，它们是带有`[K: string]`的字符串
*   每个键都有一个类型，它接受两个通用参数，一个输入和一个输出，这就是`<T = any, R = any>`
*   这些进入一个函数，其中`T`是一个`params`数组，用`(...params: T[])`表示
*   返回类型是使用指定返回类型`Promise<R>`的`Promise`

所以当我们做`add<number, string>`时，它说我们传入了不确定数量的参数，这些参数都是数字，它将异步返回一个字符串。

这种强制类型从我们的状态流下来，又流回来，这一切都是通过 TypeScript 类型的魔力实现的！

如果你在函数的参数中使用混合类型，我们可以这样做:

```
let result = await something<string | number, string>("hello", 1, "world"); 
```

Enter fullscreen mode Exit fullscreen mode

使用`|`告诉 TypeScript 函数的参数是字符串*或*数字类型，而不是函数、布尔值等..相当疯狂吧！

## 部署我们的应用

我们完了！在本地有效！现在是时候把它部署到某个地方了。

我将使用 [Azure DevOps Pipelines](https://azure.microsoft.com/en-au/services/devops/?WT.mc_id=devto-blog-aapowell) 来构建，然后将其部署为一个 [Azure Blob 静态网站](https://docs.microsoft.com/en-au/azure/storage/blobs/storage-blob-static-website?WT.mc_id=devto-blog-aapowell)。

### 建筑

要构建，您需要运行以下步骤:

*   安装我们的 Go 依赖项
*   安装我们的 npm 包
*   运行 webpack
*   复制所需的文件作为构建工件

我在 [GitHub repo](https://github.com/aaronpowell/go-wasm-experiments/blob/master/azure-pipelines.yml) 中创建了一个 [Azure DevOps YAML 版本](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started-yaml?view=azdevops&WT.mc_id=devto-blog-aapowell)。它是在标准 Node.js 管道上建模的，但是我添加了具体的执行步骤。

需要注意的是，你需要用`go get`安装合适的 Go 包。要使用我为加载程序创建的`gobridge`，你还需要设置`GOOS`和`GOARCH`:

```
- script: |
    GOOS=js GOARCH=wasm go get "github.com/aaronpowell/webpack-golang-wasm-async-loader/gobridge"
  displayName: 'install  gobridge' 
```

Enter fullscreen mode Exit fullscreen mode

您还需要确保`GOPATH`和`GOROOT`是加载程序可用的环境变量。默认情况下，这些没有在代理中设置为环境变量，我只是内嵌设置了:

```
- script: |
    npm install
    GOPATH=$(go env GOPATH) GOROOT=$(go env GOROOT) npm run build
  displayName: 'npm  install,  run  webpack' 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以为所有任务创建它们:

```
variables:
  GOBIN:  '$(GOPATH)/bin' # Go binaries path
  GOROOT: '/usr/local/go1.11' # Go installation path
  GOPATH: '$(system.defaultWorkingDirectory)/gopath' # Go workspace path 
```

Enter fullscreen mode Exit fullscreen mode

这是一个完整的构建！(忽略之前所有失败的😆)

### 发布

在撰写本文时，我们还不支持 Azure DevOps 管道的 YAML 文件版本。我使用 [Azure 文件复制任务](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azdevops&WT.mc_id=devto-blog-aapowell)将所有文件复制到我正在运行的存储帐户中，然后使用 [Azure CLI 任务](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/deploy/azure-cli?view=azdevops&WT.mc_id=devto-blog-aapowell)在 WASM 文件上设置 WASM 内容类型，否则无法正确提供:

```
az storage blob update --container-name "$web" --name "hello.wasm" --content-type "application/wasm" --account-name gowasm 
```

Enter fullscreen mode Exit fullscreen mode

*记得把`hello.wasm`改成你的文件名！*眨眨眼:

这是一个完整的版本！

## 结论

我们完了，伙计们！从不知道什么是 WebAssembly 或如何编写 Go 开始，我们已经进行了大量的探索，了解它是如何工作的，作为一名 web 开发人员，是什么使 Go 的 WebAssembly 方法变得有点棘手，以及最终我们如何将 Go 引入到我们现在熟悉的构建 web 应用程序的工具链中。

如果你用围棋和 WASM 构建了什么令人兴奋的东西，请让我知道！