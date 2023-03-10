# fundamentos JavaScript antes de a prender React

> 原文：<https://dev.to/willianjusten/fundamentos-javascript-antes-de-aprender-react-icl>

## 导言

个人来说，2015 年，我在那里开始了~~，我甚至没有授予~~[一系列关于试剂的帖子](https://willianjusten.com.br/series/#aprendendo-reactjs)。到目前为止，邮局都是超级用户，但已经过时了。借此，我想再写一篇关于此事的文章，直到对内容作出更新，同时也帮助现在开始学习的人。

在系列的[第一篇](https://willianjusten.com.br/comecando-com-react/)中，我讲的是图书馆及其概念，所以如果你今天想阅读的话，还是会奏效的。但在此之前，我认为有必要了解一些小概念，以免在研究试剂本身时迷路。

我所看到的是那些在反应中遇到困难的人，而不是那些在 ES6 语法，高阶函数甚至三元运算符等基本事物上遇到困难的人。这篇帖子将以另一篇英语帖子为基础，该帖子也讲得很多。

如果您已经使用 React，或者已经熟悉 ES6 的新语法以及 JavaScript 基础知识，则此帖子可能会对您非常枯燥且重复，因此如果您不想阅读，则不会有问题。如果你还没开始反应堆，觉得还缺少一些基本的东西，就去看看这个帖子吧。

所涉及的几个概念，主要是 es-6 部分，我在 udemi 上有一门关于它的课程，你可能会感兴趣。

一边写信，一边听着作为瑞典 DJ 创作的《镜框边缘催化剂》等各种酷东西的[【太阳能场】](https://open.spotify.com/artist/7GyhmlEy51sGUE09A5AWzc?si=P3-T0rYMQ7yyvOnDUDWTJg)。这次是我朋友 [Jonas Mendes](https://nipher.io/) 的推荐，他也是程序员。

好吧，来吧，我要把话题分开来，简单一点:

*   [首次接触试剂](#primeiro-contato-com-react)
*   [React e 类无 JavaScript](#react-e-classes-no-javascript)
*   [模板文字](#template-literals)
*   [箭头功能](#arrow-functions)
*   [反应器中作为组分的功能](#funcoes-como-componentes-no-react)
*   [sintax do 反应类组件](#sintaxe-do-react-class-component)
*   [Map，减少 e 滤镜无反应](#map-reduce-e-filter-no-react)
*   反应堆中的三元算子
*   [输入输出无反应](#importando-e-exportando-no-react)
*   [高阶函数](#funcoes-de-ordem-superior)
*   反应器中的高阶函数
*   [析构 e 传播算子](#destructuring-e-spread-operators)
*   结论

## 首次接触试剂

进入 React 世界时，很可能会使用[【create-react-app】](https://github.com/facebook/create-react-app)启动项目，因为对于想要启动 react 项目的人来说，这是最常用的工具之一，而且已经准备好了整个框架。这一切都得到了 React 和 Facebook 团队的支持。或者你也可以去网上的一些教程。

但当你看到这些文件时，很可能会出现类似于:
的代码

```
import React, { Component } from 'react'
import logo from './logo.svg'
import './App.css'

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <a
            className="App-link"
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
      </div>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

很快你就会看到一个‘t0’，也就是 JavaScript 内类的使用。今天，这对于很大一部分人来说应该已经很正常了，但由于类的使用仅随 ES6 一起提供，因此对于一些初学者来说仍然会造成困难。类的使用带来了类定义、类方法和继承等概念。在 React 中，我们不需要只使用类，但在某些时候，您可能需要/看到这些概念，您将需要了解这些概念。

## 反应 e 类无 JavaScript

在 ES6 之前，已经有一种处理遗产和对象的方法，使用了[原型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)，ES6 中的类无非是其中的一种“糖语法”，即在布下也使用了原型。

为了更好地了解班级，我们将在以下代码中不带试剂使用:

```
class Developer {
  constructor(firstname, lastname) {
    this.firstname = firstname
    this.lastname = lastname
  }

  getName() {
    return `${this.firstname}  ${this.lastname}`
  }
}

const me = new Developer('Willian', 'Justen')

console.log(me.getName()) // "Willian Justen" 
```

Enter fullscreen mode Exit fullscreen mode

`Developer`类描述了一个用作“形状”(自己想想那些饼干形状)的实体，为了能够从该实体中创建`instâncias`，我们通常把这些实例称为`objetos`。也就是说，从一个类中，我们可以创建多个对象，这些对象具有“`herdam`”其类的属性和方法。

为了创建基于类的实例，我们使用以下语法:`new Nome_Da_Classe(paramêtros_aqui)`，并将此值标记为变量，如上面的变量`me`。

类可能包含也可能不包含“`constructor`”这种初始方法用于定义该对象将具有的某些属性。在类内，也可以创建自己的方法，直接调用，无需使用`function`，方法`getName`就是这样。

类实例由类内的对象“`this`”及其在类外标记的变量表示，也就是说，对于其它范围，我们的实例将是变量“`me`”，但对于诸如“`getName`”等内部方法，我们将使用“`this`”。

类通常用于继承面向对象编程。而在 JavaScript 中，这是不可能不同的，为了在类之间创造这种继承，我们使用了`extends`这个词。下面是我们创建另一个从`Developer`继承而来的`ReactDeveloper`类的示例。

```
class Developer {
  constructor(firstname, lastname) {
    this.firstname = firstname
    this.lastname = lastname
  }

  getName() {
    return `${this.firstname}  ${this.lastname}`
  }
}

class ReactDeveloper extends Developer {
  getJob() {
    return 'React Developer'
  }
}

var me = new ReactDeveloper('Robin', 'Wieruch')

console.log(me.getName())
console.log(me.getJob()) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，除了新方法`getJob`外，还可以使用`getName`方法，以及其性质`firstname`和`lastname`。

基本上，这就是你要了解 T0 的工作原理。JavaScript 类用于定义 React 组件，但这只有在我们扩展/继承属于`react`包的`Component`的所有内容时才能实现。

```
import React, { Component } from 'react'

class App extends Component {
  render() {
    return (
      <div>
        <h1>Welcome to React</h1>
      </div>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

这就是`React Class Components`中强制使用`render()`方法的原因，因为该方法将指示浏览器在屏幕上显示某种东西。再往前看，您会看到试剂的生命周期方法([生命周期方法](https://reactjs.org/docs/react-component.html#the-component-lifecycle))也只在`React Class Components`或全新`React Hooks`中提供。

好吧，虽然我在这里讲了很多课，并表明它几乎是组成部分的基础，但最可取的做法是只扩展‘t0’，并以作文而不是继承的方式进行更多的工作，在‘t1’官方文档中甚至还有一部分谈到‘T2’。

## 模板文字

另一件非常简单但仍令很多人困惑的事是文字模板，它在字符串连接时非常有用。现在，我们不再需要在千个角落里用那些‘t0’的符号来连接东西，而是用这个反引号，把变量写在这个‘t1’符号上。请看下面或前后:

```
getName() {
  return `${this.firstname}  ${this.lastname}`;
}

// forma antiga
getName() {
  return this.firstname + '  ' + this.lastname;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 箭头功能

es-6 的另一个特点是箭功能。这种情况经常发生，因为有的时候需要钥匙，有的时候不需要‘t0’，有的时候两者都可以省略。下面举例说明:

```
// Função em ES5
function getGreeting() {
  return 'Welcome to JavaScript'
}

// Função em ES6 com {} e por isso o return obrigatório
const getGreeting = () => {
  return 'Welcome to JavaScript'
}

// Função em ES6 sem {} e return implícito
const getGreeting = () => 'Welcome to JavaScript' 
```

Enter fullscreen mode Exit fullscreen mode

如果有单行方法，则不需要关键字和 return，但如果方法稍微大一点，就有这种需要，最好不要混淆。我已经有很多学生因为忘记放‘t0’而停下来，但加入了钥匙。

## 作为试剂中组分的功能

React 使用了不同编程范式中最好的。在面向对象的编程方面，它允许创建“`this.state`”以继承 react API 方法以及诸如“`this.state`”等属性。

另一方面，React 也有几个背后的功能编程概念。允许创造出著名的`stateless components`，这是定义试剂成分的纯函数。

`props`简单得多，基本上是接收参数的函数，即`props`并在屏幕上呈现元素。他们不动州，也不拥有整个 react API。但是，它们是创建反应堆零部件的首选和最有效的方法。以下是创建“`stateless component`”的可能方法。

```
// Função normal
function Greeting(props) {
  return <h1>{props.greeting}</h1>
}

// Arrow Function com {} e return
const Greeting = props => {
  return <h1>{props.greeting}</h1>
}

// Arrow Function sem {} e return implícito
const Greeting = props => <h1>{props.greeting}</h1> 
```

Enter fullscreen mode Exit fullscreen mode

## sintax do React 类组件

反应堆中定义组件的方式总是在不断演变。一开始可以看到使用方法`React.createClass`，但随着 ES6 的到来，我们进一步看到了如上所示的使用方法，从`react`包中延伸出`Component`。

正是因为这种不断的进化，你可能会看到不同的方法来做同样的事情。与状态(“T0”)和方法合作的方法之一是:

```
class Counter extends Component {
  constructor(props) {
    super(props)

    this.state = {
      counter: 0
    }

    this.onIncrement = this.onIncrement.bind(this)
    this.onDecrement = this.onDecrement.bind(this)
  }

  onIncrement() {
    this.setState(state => ({ counter: state.counter + 1 }))
  }

  onDecrement() {
    this.setState(state => ({ counter: state.counter - 1 }))
  }

  render() {
    return (
      <div>
        <p>{this.state.counter}</p>

        <button onClick={this.onIncrement} type="button">
          Increment
        </button>
        <button onClick={this.onDecrement} type="button">
          Decrement
        </button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在此，我们将对象绑定到构造函数中的方法，以便可以将这些方法与模板中的状态和操作一起使用。但随着时间的推移，这种使[绑定](https://medium.freecodecamp.org/react-binding-patterns-5-approaches-for-handling-this-92c651b5af56)的过程最终是重复而乏味的。为了避免这种情况，我们可以使用箭函数，箭函数已经做了总线，而不必在构造函数中进行。事实上，如果我们没有通过任何“`props`”的话，我们甚至可以省略构造函数的用法。变成这样:

```
class Counter extends Component {
  state = {
    counter: 0
  }

  onIncrement = () => {
    this.setState(state => ({ counter: state.counter + 1 }))
  }

  onDecrement = () => {
    this.setState(state => ({ counter: state.counter - 1 }))
  }

  render() {
    return (
      <div>
        <p>{this.state.counter}</p>

        <button onClick={this.onIncrement} type="button">
          Increment
        </button>
        <button onClick={this.onDecrement} type="button">
          Decrement
        </button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## Map，减少 e 滤镜无反应

React 中没有用于处理数组、对象的方法或任何东西。你所使用和需要的只是喜爱的 JavaScript。这样一来`map`、`reduce`和`filter`等一些方法就成了与 React 合作的人的宠儿，但为什么呢？

简单地说，它们是纯粹的方法，它们接受一个值，并以所需的方式返回另一个值，以反应堆非常喜欢的功能方式工作。它们使您可以方便地以所需的方式返回项目(“T0”)、根据所需的任何参数过滤信息(“T1”)，甚至可以对一组值执行操作以仅返回一个已处理的值(“T2”)。

使用“`map`”渲染项目列表的示例如下:

```
import React, { Component } from 'react'

class App extends Component {
  render() {
    var users = [{ name: 'Robin' }, { name: 'Markus' }]

    return (
      <ul>
        {users.map(function (user) {
          return <li>{user.name}</li>
        })}
      </ul>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

为了使其更干净，我们可以利用箭函数，在一行中创造同样的东西:

```
import React, { Component } from 'react'

class App extends Component {
  render() {
    var users = [{ name: 'Robin' }, { name: 'Markus' }]

    return (
      <ul>
        {users.map(user => (
          <li>{user.name}</li>
        ))}
      </ul>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

而且，如前所述，不仅仅是`filter`的帮助，像`filter`这样的功能也是天才的，如下面的例子:

```
import React, { Component } from 'react'

class App extends Component {
  render() {
    var users = [
      { name: 'Robin', isDeveloper: true },
      { name: 'Markus', isDeveloper: false }
    ]

    return (
      <ul>
        {users
          .filter(user => user.isDeveloper)
          .map(user => (
            <li>{user.name}</li>
          ))}
      </ul>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

如果您要阅读有关方法的更多信息，请参阅以下文档:

*   [地图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
*   [过滤器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
*   [减少](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

## 反应堆中的三元算子

在 JSX 中，不能直接使用著名的`if-else`，但可以先创建条件，然后使用空返回停止渲染。这样反应堆就不会在屏幕上显示任何东西。

```
import React, { Component } from 'react'

class App extends Component {
  render() {
    const users = [{ name: 'Robin' }, { name: 'Markus' }]

    const showUsers = false

    if (!showUsers) {
      return null
    }

    return (
      <ul>
        {users.map(user => (
          <li>{user.name}</li>
        ))}
      </ul>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果要在 JSX 中使用 if-else 逻辑，则可以使用三元运算符，如下所示:

```
import React, { Component } from 'react'

class App extends Component {
  render() {
    const users = [{ name: 'Robin' }, { name: 'Markus' }]

    const showUsers = false

    return (
      <div>
        {showUsers ? (
          <ul>
            {users.map(user => (
              <li>{user.name}</li>
            ))}
          </ul>
        ) : null}
      </div>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法，只返回条件的一侧，就是使用运算子`&&`，如下所示:

```
import React, { Component } from 'react'

class App extends Component {
  render() {
    const users = [{ name: 'Robin' }, { name: 'Markus' }]

    const showUsers = false

    return (
      <div>
        {showUsers && (
          <ul>
            {users.map(user => (
              <li>{user.name}</li>
            ))}
          </ul>
        )}
      </div>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

注意，万一`: null`是假的，什么也不会出现，不需要写下以前做的`: null`。

## 进口出口无反应

这让很多人很困惑。我应该怎样进出口东西？有些时候你有钥匙，有些时候你没有，这是什么意思？

我们将从‘t0’开始发言，他们将会直接解释‘t1’。我们有两种`exports`，它们是:

*   `named exports`:用于导出同一文件中的各种方法/值。

```
const firstname = 'Robin'
const lastname = 'Wieruch'

export { firstname, lastname } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们有两个变量，这两个变量正在分别导出。导入这些值时，我们只能使用与导出时相同的名称，或使用别名。进口`named exports`有 3 种方式。

```
// Importando diretamente valor a valor, para isso é necessário o uso de chaves
import { firstname, lastname } from './file1.js'
console.log(firstname) // "Robin"

// Importando todos os valores e atribuindo a um objeto
import * as person from './file1.js'
console.log(person.firstname) // "Robin"

// Importando somente um valor, mas atribuindo um alias para o valor
import { firstname as username } from './file1.js'
console.log(username) // "Robin" 
```

Enter fullscreen mode Exit fullscreen mode

每种进口方式都有其优点。

*   导入整个对象有助于快速识别对象的来源。
*   使用别名可以导入具有不同名称的方法，以避免与具有相同名称的其他方法发生冲突。
*   导入值允许我们不导入我们那时不会使用的东西。

导出方法的另一种方法是`default export`，在该方法中，每个文件只导出一个值。我们在上述反应的一些例子中展示的是我们的`App`。在这种情况下，输入时不必具有相同的名称，例如:

```
const robin = {
  firstname: 'Robin',
  lastname: 'Wieruch'
}

export default robin 
```

Enter fullscreen mode Exit fullscreen mode

并且在进口时，可以使用除`robin`以外的任何名称:

```
import developer from './file1.js'

console.log(developer)
// output: { firstname: 'Robin', lastname: 'Wieruch' } 
```

Enter fullscreen mode Exit fullscreen mode

## 高阶函数

高阶函数(High-order Functions)是程式设计中的一个重要概念，尤其是当您要进入功能端时。在 React 中，了解这种功能是完全有意义的，因为总有一天你会需要和`high-order component (hoc)`一起工作，如果先了解高阶功能的话会容易得多。

也许你不知道，但我们刚刚在那篇帖子里谈到 HOF！没错，`map()`是庭院的例子，无非是`uma função que aceita uma ou mais funções como argumento.`

让我们再看一下地图:

```
const collection = ['Willian', 'Jonas', 'Marcio']

// Usando Função ES5
collection.map(function (person) {
  return `${person} Developer`
  // Output: ["Willian Developer", "Jonas Developer", "Marcio Developer"]
})

// Usando Arrow Function com {} e return
collection.map(person => {
  return `${person} Developer`
  // Output: ["Willian Developer", "Jonas Developer", "Marcio Developer"]
})

// Usando Arrow Function e return implícito
collection.map(person => `${person} Developer`)
// Output: ["Willian Developer", "Jonas Developer", "Marcio Developer"] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们有一个函数是‘t0’，并且它作为参数得到另一个函数，就是这个使用过的参数函数将在数据上工作。这种概念使我们能够更好地抽象动作，从而使动作以多种方式呈现，其中包括一个角色可以用来创建另一个更大的角色，甚至可以用来创建资源。

为了更好地理解这个概念，我建议您阅读一下雄辩 JavaScript 的这一章，其中显示了几个非常有趣的例子。

## 反应中的高阶函数

如上所述，在反应器中，我们可以创建具有简单功能的组件，即所谓的`stateless components`。于是一个`high-order component`无非是一个`componente`接受另一个`componente`作为引数，返回一个`componente`。

例如，您可以创建一个将所有传递的内容都保留为大写的即席。

```
const yell = (PassedComponent) =>
  ({ children, ...props }) =>
    <PassedComponent {...props}>
      {children.toUpperCase()}!
    </PassedComponent>

const Title = (props) => <h1>{props.children}</h1>
const AngryTitle = yell(Title)

<AngryTitle>Whatever</AngryTitle> // Output: <h1>WHATEVER!</h1> 
```

Enter fullscreen mode Exit fullscreen mode

有些细节如`children`和`props`是从反应堆里来的，所以我们就不多谈了。但请理解，“`children`将是组件内过去的内容，在本例中为文本“`Whatever`”。而`props`是通过属性传递给组件的简单对象。

我们在这里有一种叫做“`yell`”的方法，它接收一个组件并使用该组件封装内容，只需将子代的过去值更改为大写即可。

我们还有组件`Title`，它接收属性并在`<h1></h1>`之间以非常简单的方式打印在屏幕上。

而构成这种完整连接的组件，即负责调用方法`yell`的`Title`，在其中传递组件`Title`。

这样，字符串“`Whatever`”传递给上述函数，该函数将该字符串转换为大写并封装在组件“`Title`”中，该组件将在屏幕上打印“`<h1>WHATEVER!</h1>`”。

这可能看起来有点“无用”和杂乱，但在更大的抽象中却容易很多。使用 ad hoc 的一个很好的例子是 Redux，它使用“`store`”将值从“`store`”传递到组件。

另一个有用的功能是，从 React 组件外部提取高阶函数也可以帮助测试隔离状态。下面举一个例子:

```
export const doIncrement = state => ({ counter: state.counter + 1 })

export const doDecrement = state => ({ counter: state.counter - 1 })

class Counter extends Component {
  state = {
    counter: 0
  }

  onIncrement = () => {
    this.setState(doIncrement)
  }

  onDecrement = () => {
    this.setState(doDecrement)
  }

  render() {
    return (
      <div>
        <p>{this.state.counter}</p>

        <button onClick={this.onIncrement} type="button">
          Increment
        </button>
        <button onClick={this.onDecrement} type="button">
          Decrement
        </button>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们有方法`doIncrement`和`doDecrement`在组件外并出口。这样我们就可以有条不紊地单独测试方法。

## 解构 e 传播算子

上面有一个叫`...props`的家伙，在反应堆里用得很厉害，这也是随 S6 而来的奇迹之一。如果您要访问组件的一个`state`或`props`的多个属性，而不是逐一标记变量，则可以使用 destroy 来实现此目的。

```
// sem destructuring
const users = this.state.users
const counter = this.state.counter

// com destructuring
const { users, counter } = this.state 
```

Enter fullscreen mode Exit fullscreen mode

在那里，我们已经创建了变量“T0”和“T1”，而无需逐个执行。而这在与`stateless components`合作时尤其有益，因为我们将始终以我们的角色接收`props`对象。这样我们就可以直接从`props`调用内容，而不是整个对象。

```
// sem destructuring
function Greeting(props) {
  return <h1>{props.greeting}</h1>
}

// com destructuring
function Greeting({ greeting }) {
  return <h1>{greeting}</h1>
} 
```

Enter fullscreen mode Exit fullscreen mode

那些点呢？嗯，这是`spread operator`，它允许您分离对象的各个部分，分别具有不同的特定属性，其馀部分则位于对象上。

```
const { users, ...rest } = this.state 
```

Enter fullscreen mode Exit fullscreen mode

在那里，我们将拥有单独的`users`属性，其馀的则留在`rest`对象上。在上面的例子中，我们想要分开的`children`的值，为了不丢失任何其他属性，我们使用扩频运算符保留了`props`对象的其馀部分。

## 结论

好吧，伙计们，我知道邮局变得很小，但我希望它对你们有用。很多人都说试剂有很大的学习曲线，很难，但大多数情况下，它本身并不是试剂，而是一些语言的基本东西。

如果你觉得遗漏了什么细节或者你有其他法律参考资料，就把它放在评论里吧！=)