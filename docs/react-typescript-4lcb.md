# 反应和键入脚本

> 原文：<https://dev.to/thefinnomenon/react-typescript-4lcb>

## 动机

JavaScript 是一种**动态类型的**语言，这意味着解释器在运行时根据变量在特定时间的值给变量分配一种类型。这意味着当你写下面的代码时，

```
let myVar = "cat" 
```

你不必像在强类型语言(如 Java)中那样将它定义为某种类型的变量(本例中为字符串)。你只需在运行时将它声明为变量&，解释器意识到‘cat’是一个字符串&，因此将`myVar`视为一个字符串。

这很好，因为它让你不必真正关心类型的整体概念，但这样做，将程序打开了一个全新的运行时错误的世界。

```
let a = "1"
let b = 5

// '1' + 5
console.log(a + b) // '15' 
```

JavaScript 使用**类型强制**，即变量在特定上下文中隐式改变类型的能力，将 5 变为‘5’，&将两个字符串组合在一起。虽然这个例子不会导致错误，但这很可能不是程序员想要做的。

拥有静态类型不仅有助于避免任何类型相关的运行时错误，而且它还使开发人员能够实时捕捉类型问题，快速确定什么类型应该在哪里，甚至自动完成建议。显然，添加类型是一种胜利，所以让我们开始吧！

## 设置

Typescript 是微软开发的 JavaScript 的开源类型化超集。它基本上增加了类型&在你已经知道和喜欢的 JavaScript 之上增加了一些新特性。

### 创建 React App

为了开始使用 Typescript & React，Create React 应用程序团队已经做得很好&很简单，

```
yarn create react-app my-app --typescript 
```

这创建了标准的 React starter 应用程序，但现在以 TypeScript 为特色。

> 请注意，而不是。js 或者。jsx，文件扩展名现在是。tsx

除了主要的应用程序文件。tsx，一个 tsconfig.json 文件是用很好的默认值生成的，所以我们现在可以让它保持原样。

### 额外工装(可选)

*   ESLint:帮助执行编码和样式规则的林挺工具。
*   更漂亮:代码格式化工具。
*   Husky:编写 git 挂钩(git 事件上要做的事情)
*   Lint-Staged:对 git staged 文件运行 linters、tests 等
*   跨环境:设置环境变量而不用担心平台

这些工具一起使用，可以使您的代码在本地保持良好的格式&在您的存储库中。

#### 安装

```
yarn add eslint eslint-config-react-app @typescript-eslint/parser
@typescript-eslint/eslint-plugin prettier
eslint-config-prettier eslint-plugin-prettier husky lint-staged cross-env --dev 
```

#### 设置

*/.eslintrc.js*

```
module.exports = {
  parser: "@typescript-eslint/parser",
  extends: [
    "plugin:@typescript-eslint/recommended",
    "react-app",
    "prettier/@typescript-eslint",
    "plugin:prettier/recommended",
  ],
  plugins: ["@typescript-eslint", "react", "prettier"],
  rules: {
    "@typescript-eslint/explicit-function-return-type": 0,
    "@typescript-eslint/prefer-interface": 0,
    "@typescript-eslint/no-use-before-define": 0,
    "no-use-before-define": 0,
  },
} 
```

explicit-function-return-type 的规则覆盖是这样的，我们不会因为没有编写函数组件和 jest 测试的返回类型而收到警告。首选接口覆盖是因为我喜欢使用类型。因为我不想在我的 Props 类型之上定义我的 defaultProps，所以在定义之前不使用被覆盖。

*/.prettierrc.js*

```
module.exports = {
  semi: true,
  trailingComma: "all",
  singleQuote: true,
  printWidth: 120,
  tabWidth: 2,
} 
```

*/package.json*

```
...
"scripts": {
  ...
  "precommit": "lint-staged",
  "test:staged": "cross-env CI=true react-scripts test --findRelatedTests"
  ...
},
... 
```

*/.huskyrc.js*

```
module.exports = {
  hooks: {
    "pre-commit": "yarn precommit",
  },
} 
```

*/。lintstagedrc*T2】

```
{
  "linters": {
    "**/*.+(js|jsx|ts|tsx)": [
      "eslint --fix",
      "prettier --write",
      "yarn test:staged",
      "git add"
    ]
  }
} 
```

#### VSCode

如果你使用 VSCode，你应该安装 ESLint & Prettier 扩展。

```
code --install-extension dbaeumer.vscode-eslint
code --install-extension esbenp.prettier-vscode 
```

我建议您也将 ESLint 设置为保存时自动修复，这样您的代码在每次保存后都会被正确格式化。

*settings.json(在 VSCode 中)*T2】

```
...
"eslint.autoFixOnSave": true,
"eslint.validate": [
  "javascript",
  "javascriptreact",
  "typescript",
  "typescriptreact",
],
... 
```

## 反应特定的打字稿的古怪之处

*   不支持常量枚举和命名空间
*   包含 JSX 的文件必须有。tsx 扩展

## -只能在 JSX 使用‘as’型断言(没有尖括号)

必须在 tsconfig.json 中指定 JSX 模式(默认即可)

*   保存:发出 JSX，输出。jsx
*   react:发出 React.createElement，输出。射流研究…
*   发出 JSX，输出。射流研究…

> 该模式可以在 tsconfig.json 中设置，也可以使用—jsx 命令行标志指定。

## 道具类型

获得正确的 prop 类型将使向组件添加 TypeScript 变得轻而易举。这里有一张你可能在某个时候需要的常见道具类型的清单，

```
type Props = {
  message: string
  count: number
  disabled: boolean
  / **array of a type** /
  names: string[]
  / **string literals to specify exact string values w/ union type** /
  status: "waiting" | "success"
  /**any object as long as you dont use its properties (uncommon)**/
  obj: object
  / **same as above** /
  obj2: {}
  / **object w/ defined properties** /
  obj3: {
    id: string
    title: string
  }
  / **array of objects** /
  objArr: {
    id: string
    title: string
  }[]
  /**any function as long as you don't invoke it (uncommon)**/
  onSomething: Function
  / **function that doesn't take or return anything** /
  onClick: () => void
  / **function with named prop** /
  onChange: (id: number) => void
  / **optional prop** /
  optional?: OptionalType
  / **excepts all types of children** /
  children: React.ReactNode
  / **function as a child render prop** /
  functionChildren: (name: string) => React.ReactNode
  / **pass through style props** /
  style?: React.CSSProperties
  / **form event** /
  onChange?: React.FormEventHandler<HTMLInputElement>
  /* all the props of a button element without its ref **/
  props: Props & React.PropsWithoutRef<JSX.IntrinsicElements["button"]>
} 
```

## 类组件

```
import React, { Component } from "react"

// Define a type for props
type Props = {
  animal: string
} & typeof defaultProps

// Define default props
const defaultProps = Object.freeze({
  quantity: 0,
})

// Define a type for the state
type State = {
  currentQuantity: number
}

// Pass in Props & State types to the Component
export class PetStore extends Component<Props, State> {
  // Set initial state using props
  public readonly state = { currentQuantity: this.props.quantity }

  // Create handlers for button clicks
  public onDecrement = () => {
    // Stop currentQuantity from going negative
    if (this.state.currentQuantity > 0) {
      this.setState({ currentQuantity: this.state.currentQuantity - 1 })
    }
  }
  public onIncrement = () =>
    this.setState({ currentQuantity: this.state.currentQuantity + 1 })

  public render() {
    // Grab ‘animal’ prop
    const { animal } = this.props
    const { currentQuantity } = this.state

    return (
      <div>
        <div>
          There are {currentQuantity} {animal}.
        </div>
        <button onClick={this.onDecrement}>-</button>
        <button onClick={this.onIncrement}>+</button>
      </div>
    )
  }

  public static defaultProps: Readonly<{ quantity: number }>
}

// Set the component’s defaultProps to the ones we declared above
PetStore.defaultProps = defaultProps

// Export the component
export default PetStore 
```

## 功能组件

作为功能组件编写的相同组件

```
import React, { useState } from "react"

// Define a type for props
type Props = {
  animal: string,
} & typeof defaultProps

// Define default props
const defaultProps = Object.freeze({
  quantity: 0,
})

// Define initialState (not used in this example)
// const initialState = Object.freeze({});

export const PetStore = (props: Props) => {
  const { animal, quantity } = props
  const [currentQuantity, updateQuantity] = useState(quantity)

  // Ternary operator checks if currentQuantity is 0 & stops it from going negative
  const onDecrement = () =>
    updateQuantity(currentQuantity === 0 ? 0 : currentQuantity - 1)
  const onIncrement = () => updateQuantity(currentQuantity + 1)

  return (
    <div>
      <div>
        There are {currentQuantity} {animal}.
      </div>
      <button onClick={onDecrement}>-</button>
      <button onClick={onIncrement}>+</button>
    </div>
  )
}

// Assigns default props to our Component
PetStore.defaultProps = defaultProps

export default PetStore 
```

## 默认道具

Typescript 对 React 的 defaultProps 的支持以前很缺乏但现在真的很好。我发现我在示例中使用它们的方式是使用它们的最清晰的方式。需要注意的一点我认为不是很明显，那就是你永远不要在 Props 类型中将一个带有 defaultProp 的 prop 设置为可选的，因为那样的话它将被赋予一个带有 undefined 的 unioned 类型(比如 number | undefined)。相反，您可以在没有可选属性的情况下包含它&如果它有一个声明的 defaultProps，或者(我的首选方式)将 defaultProps 的类型合并到 Props 类型中，Typescript 会将它视为可选的。

```
// Best way
type Props = {
  animal: string,
} & typeof defaultProps

const defaultProps = Object.freeze({
  quantity: 0,
})

// Okay too
type Props = {
  animal: string,
  quantity: number,
}

const defaultProps = Object.freeze({
  quantity: 0,
})

// Not okay
type Props = {
  animal: string,
  quantity?: number, // number | undefined
}

const defaultProps = Object.freeze({
  quantity: 0,
}) 
```

## 类型与接口

类型和接口之间有一些不同，但是在我们的例子中，当定义我们的道具时，使用任何一个都可以。我更喜欢使用类型，因为对于我的用例来说，它们更加灵活和简洁。

```
type FirstType = { foo: string, bar?: number }
type SecondType = { baz: boolean }
type MergedType = FirstType & SecondType
type EitherType = FirstType | SecondType

const mergedObject: MergedType = { foo: "I am foo", bar: 12, baz: true }
const eitherObject: EitherType = { foo: "I am still foo" }
const anotherEitherObject: EitherType = { baz: false } 
```

## 抓取构件的道具类型

没有必要导出组件属性类型，相反，如果需要从另一个文件中访问它们，可以使用`React.ComponentProps`。

```
type ButtonProps = React.ComponentProps<typeof Button> 
```

## 为库查找类型

有些库不包括类型&你必须单独添加它们。通常这个包只是@types/library-name，但是如果不是这样，你可以尝试用， [TypeSearch](https://microsoft.github.io/TypeSearch/) 来搜索

## 结果

[![App screenshot](img/03795ac6731b14b30f00aee55e2a89f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jiCYO2a9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thefinnternet.com/static/68bee42898625c43566fddf48c70a9c6/135ae/app_screenshot.png)

[帖子的代码库](https://github.com/thefinnomenon/react-and-typescript)