# JavaScript 中没有 TypeScript/Flow 的类型

> 原文：<https://dev.to/yeion7/tipos-en-javascript-sin-typescriptflow-48ci>

JavaScript 并不是严格意义上的式语言，许多项目在没有使用式的情况下进行了相当不错的升级，但现实情况是，随着一个项目的增长，它的复杂性增加了，只是有很多细节我们再也不能想到了。

类型通过多种方式帮助我们降低这种复杂性，其中包括:

*   *避免常见错误*，因为知道我们使用的模块的输入/输出或接口有助于我们正确使用它们。
*   *文档*能够清楚地了解模块接受或返回的数据类型，而无需去寻找其定义是相当有用的，尤其是在大型项目的情况下。
*   *ide 支持*，ide/editor 的集成和建议在编程时相当有用。
*   *重构*，能够在不倒退的情况下修改部分代码，或者渗透到正在使用更改内容的地方，是类型提供的最佳优势之一。参数以下是将类型集成到您的项目中的好主意的一些原因，一般来说，这将有助于开发者体验并能够预防

> 如果您不使用定义类型的方法，那么很多项目都可以在没有这种情况下运行，如果您不觉得有问题，请不要将它们集成，因为这只会给您的开发者增加另一层复杂性。

## JSDOC + TSC

在 JavaScript 中集成类型时，两种最常见的替代方法是使用 flow 或 [TypeScript](https://www.typescriptlang.org/) ，两者都有各自的优点和缺点，但两者都会在代码中添加一个透明层，并可能不会很容易地将其集成到您的开发流程中。

> 如果您在类型化语言中有背景，则需要花费时间和经验来高效处理类型

另一种方法是使用 [JSDoc](http://usejsdoc.org/) ，这是在 JS 中向代码添加文档的标准方法，与 TSC 或 TS(仅用于类型检查)一起使用，我们可以具有使用 type 的相同优势。

使用这种方法的一个原因是，它不需要或要求额外的透明步骤，您编写的代码仍然是 JS，您不需要迁移或更改您在开发中使用的工具。

即使使用[【vscode】](https://code.visualstudio.com/docs/languages/javascript)，此支持 JSDoc 以便能够使用 intelliSense，从而提高了自我完成性、参数信息等。

## 配置

要在 VSCode 中启用此功能，有两种方法:

首先，默认情况下，在所有 JS 文件中，在“设置”下选择“添加”

```
"javascript.implicitProjectConfig.checkJs":  true 
```

二是在项目根目录中添加一个文件`jsconfig.json`，其设置如下

```
{
  "compilerOptions": {
    "target": "es2017",
    "allowSyntheticDefaultImports": true,
    "jsx": "react",
    "noEmit": true,
    "strict": true,
    "noImplicitThis": true,
    }
  },
  "exclude": ["node_modules", "build"],
} 
```

你可以更详细地阅读所有的配置选项 [![aquí](img/4f7c14b1d90e7763c3c3c43f2a15c7b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vytu3GHr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://code.visualstudio.com/docs/languages/jsconfig) 。

您可能还希望有一个 CI 步骤来检查类型，只需在“`package.json`”中添加一个脚本即可

```
 "type-lint": "tsc --pretty", 
```

使用这种类型审查形式的大型项目之一是 [webpack](https://github.com/webpack/webpack/blob/master/package.json#L125)

## 第三方书店类型

一旦你开始以这种方式或通过 TypeScript 使用类型，你会发现你的依赖项需要帮助来了解它们的类型，因为并非所有的依赖项都是随这些一起发布的，为此 TS 社区有一个很好的工具 [![TypeSearch](img/f1e284497739972657254d6832827097.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F90w2-vQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/TypeSearch/) 帮助你找到你依赖项的类型。

## 类型我可以用吗？

基本类型包括

*   空
*   不明确的
*   布尔型
*   数字
*   线
*   数组或[]
*   对象或{}

要定义变量类型，可以使用`@type`

```
/**
 * @type {number}
 */
const age = 1

/**
 * @type {string}
 */
const name = "yeison" 
```

> 这种情况是不必要的，因为为变量指定一个数字会导致插入类型

例如，在数组中，我们可以定义数组中元素的类型。

```
/**
 * @type {Array<number>} 
 */
const randomNumbers = [] 
```

> 另一个选择是我们的圣诞礼物

使用物件，您可以定义每个性质的类型。

```
/**
 * @type {{age: number, name: string}}
 */
const person = {age: 1, name: 'yeison'} 
```

另一种方法是将每个特性设置为单独的行

```
/**
 * @property {number} age
 * @property {string} name
 */
const person = {age: 1, name: 'yeison'}

person.name = 1 // Te va a mostrar un error 
```

如果一个属性是可选的，我们可以使用`[]`向属性名称的作者声明，

```
/** 
 * @typedef {Object} Options The Options to use in the function createUser.  
 * @property {string} firstName The user's first name.  
 * @property {string} lastName The user's last name.
 * @property {number} [age] The user's age.
 */
/**
 * @type {Options} opts
 */
const opts = { firstName: 'Joe', lastName: 'Bodoni' } // no va a mostrar error 
```

## 定义 tipos 个性化

我们可以创建自定义类型，这是一种创建自定义类型的方法，我们可以重复使用它们。

申报人的个人资料`@typedef`

```
/**
 * @typedef {{age: number, name: string}} Person
 */

/**
 * @type {Person} 
 */
const person = {age: 1, name: 'yeison'}

/**
 * 
 * @param {Person} person 
 * @returns {string}
 */
const getUpperName = (person) => person.name.toUpperCase() 
```

### 方法和功能

宣告函数时，我们可以定义函数要接收和传回哪些值，我们有几种语法可以使用。

#### jsdoc 标准语法

```
/**
 * 
 * @param {number} a 
 * @param {number} b 
 * @returns {boolean}
 */
const gte = (a, b) => a > b 
```

#### 最接近 TS 的语法

```
/**
 * @type {function(number, number): boolean}
 */
const gte = (a, b) => a > b 
```

#### 克隆式语法

```
 /**
 * @type {(a: number, b: number) => boolean}
 */
const gte = (a, b) => a > b 
```

## 通用

`@template`

```
/**
 * @template T
 * @param {T} i 
 * @return {T}
 */
function identity(i) { 
  return i
} 
```

在这种情况下`identity`会收到任何种类的，但收到的种类是必须返回的。

## 重要提示

我们还可以在文件之间导入类型，因此 JSDoc 标准不允许这样做，但 VSCode 允许使用“`import`”导入类型定义。

```
/**
 * @typedef {import('moment').Moment} initialDate
 */ 
```

也可以从文件中导入它们(无需声明任何导出子句)

```
/**
 * @typedef {import('../utils').File} File
 */ 
```

## 交点和接头

我们可能要做的是扩展一个我们已经拥有的定义，为此，我们可以使用交集(“T0”)使我们能够将多个类型组合成一个

```
/**
 * @typedef {{name: string, cc: number, tel: number}} Person
 * @type {Person & {addres: string}}
 */
const person = { name: 'yeison', cc: 1, tel: 12, addres: 'asd' } 
```

或者，我们也可能需要某种类型，我们可以使用连接(“T0”)

```
/**
 * @type {{isValidCitizen: true, cc: number} | {isValidCitizen: false, ce: number}}
 */
const person = { isValidCitizen: true, cc: 1 } 
```

## 反应 con JSDoc

一旦我们知道如何使用 JSDOC 的基本部分，我们就可以将这些部分应用到我们使用 React 的项目中，在本例中，我们将定义每个组件的‘t0’和‘t1’的类型。

如果要使用的组件已定义了其类型，则在使用该组件时，编辑器将向我们提供有关您希望使用的组件及其类型的信息。

*   作为函数的元件

```
import React from 'react'

/**
 * @param {{name: string}} Props
 */
const Hello = ({ name }) => (
  <h1>Hello {name}</h1> )

<Hello name={1}> {/* muestra error */} 
```

*   具有类的组件

在我们用类声明的组件中，我们必须定义自己的组件和状态，为此，类正在从“`Component`”扩展，要在 JSDoc 中写入此内容，我们必须使用“`@extends`”

```
import React, { Component } from 'react'

/**
 * @typedef {{ user: string, password: string }} State
 * @typedef {{ login: (data: State) => Promise }} Props
 * @extends {Component<Props, State>}
 */
class Login extends Component {
  state = {
    user: '',
    password: ''
  }

  /**
   * @param {React.ChangeEvent<HTMLInputElement>} ev
   */
  handleChange = (ev) => {
    const { value, id } = ev.target

    this.setState({[id]: value})
  }

  /**
   * @param {React.FormEvent} ev
   */
  handleSubmit = (ev) => {
    ev.preventDefault()

    this.props.login(this.state)
  }

  render () {
    return (
      <form>
        <fieldset>
          <label htmlFor='user'>
            Password
            <input type='text' id='user' />
          </label>
          <label htmlFor='password'>
            Password
            <input type='password' id='password' />
          </label>
        </fieldset>
        <input type='submit' value='Enviar'/>
      </form>
    ) 
  }
} 
```

## 奖金

许多项目都使用别名，因此无需编写大路径即可导入表单，问题在于您的编辑器将无法识别这些路径，要解决此问题，我们可以在“`jsconfig.json`”中添加别名设置

```
{  "compilerOptions":  {  "module":  "es2015",  "esModuleInterop":  true,  "moduleResolution":  "node",  "baseUrl":  "./src",  "paths":  {  "utils":  ["utils/"],  "api":  ["api/"],  "actions/*":  ["actions/*"],  "stores/*":  ["stores/*"],  }  }  } 
```

## 结语

我个人认为，在一个项目中使用 JSDoc 是一种相当不错的、摩擦较少的方法，这样可以改善开发人员的体验，并具有使用类型的优点，而不必从一种类型迁移到 TS/Flow 类型。