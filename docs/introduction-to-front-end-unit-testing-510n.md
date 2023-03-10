# 前端单元测试简介

> 原文：<https://dev.to/christopherkade/introduction-to-front-end-unit-testing-510n>

好了，你已经涉足了 web 开发，做了一些项目，部署了一些应用程序来帮助你掌握前端开发的基本概念，从路由、服务器端渲染、状态管理到异步查询等等。

但是有一件事你还没有付诸实践，或者是因为你有意回避它，或者仅仅是因为你没有看到它的用途:测试。更具体地说，前端单元测试是测试生态系统中非常重要的一部分。

今天早些时候，我把这篇文章贴在了我的博客上，希望得到大家的反馈，请欣赏！

## 单元测试？🧐

我将回顾一下基础知识，以理解单元测试在日常应用中的作用。

本质上，测试前端代码可以分为**三类**:

[![](img/8f889f5794d2126fab105f637ab4b78a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OypgjlFL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/15229355/55113335-24fcff00-50df-11e9-87e8-cdd5e0e6ad3f.png)

**E2E 测试**或端到端测试，是测试应用程序的执行是否从头到尾都符合设计的实践。整个应用程序都在真实场景中进行了测试，包括测试数据库、网络、API 等组件之间的通信。并在各种浏览器中执行您的代码。基本上测试一切。它需要大量的时间来设置，成本最高。

**集成测试**包括测试应用程序元素之间的交互，例如，UI 和 API 之间的通信。它需要更短的时间来设置，并且不太昂贵。

**单元测试**是不同的，因为它包括测试代码的孤立部分，作为单元。这些单元通常采用方法、属性、UI 元素动作等形式。这是实现速度最快、成本最低的方法。

你可能已经注意到，你在我们的金字塔中越高，建立我们的测试所花费的时间和金钱就越多。这就是为什么许多项目倾向于关注单元测试，因为它们可以通过覆盖大多数场景、节省时间和简化部署过程来帮助您了解您的代码是否真正工作。

## 一个单元测试⚙️的例子

在我们开始之前，有必要提一下什么是测试框架。

测试框架允许您轻松设置测试环境并运行测试套件。你可以把测试框架看作是 React 或 Vue 对于 UI 开发的作用，它们给你提供了大量的工具，并通过这样做使你的生活变得更容易。我强烈推荐 [Jest](https://jestjs.io/) ，因为它是大多数项目中常见的，由脸书的一个伟大的工程师团队维护。请注意，我将在我的例子中使用这个框架。

我将讨论一些单元测试的基本例子，所以让我们开始吧。如果你想继续下去，请随意使用一个名为 [TDDBin](http://tddbin.com/) 的网站。

```
// 1\. The method we want to test
function add(x, y) {
  return x + y
}

// 2\. A test suite
describe("add method", () => {

  // 3\. A unit test
  it("should return 2", () => {
    // 4\. An assertion
    expect(add(1, 1)).toBe(2)
  })
}) 
```

让我们来分解代码:

1.  **我们要测试的方法**。正如我们前面提到的，单元测试通常适用于方法或 UI 元素交互。了解要测试什么的一个好方法是从头开始查看应用程序的组件。"我的方法接受什么作为输入，它的输出是什么？"“我的方法会影响组件的状态吗？”，“有哪些边缘案例？”都是好问题找个切入点。
2.  **一个测试套件**，它应该被简要地描述，并对相关的单元测试进行分组。例如，一个测试套件可以包括所有涉及特定方法的测试。你可以声明尽可能多的测试套件，它的主要作用是让你的测试日志更具可读性。
3.  **一个单元测试**，附带一个描述，回调里面的语句就是测试本身。
4.  **一个测试断言**。测试就是断言，将给定的值与预期的值进行比较。这里，我们用 1 和 1 作为参数给出我们的`add`方法的返回值，并期望结果是 2。

### 我们可以添加的其他测试

以下是本例中可以合理添加的一些其他测试:

检测阴性结果:

```
it("should return -2", () => {
  expect(add(0, -2)).toBe(-2)
}) 
```

测试我们的方法的错误处理(当除了数字以外的任何东西作为参数传递时):

```
function add(x, y) {
  // Check if the parameters are numbers
  // If not, throw an error
  if (isNaN(x) || isNaN(y)) {
    throw new Error("Parameter is not a number !")
  }
  return x + y
}

describe("add method", () => {  
  it("should throw an error if NaN is given as parameter", () => {
    expect(add).toThrow()
  })
}) 
```

> **注**:你可能已经看到我们用的是`toThrow()`而不是`toBe()`。Jest 提供了大量的匹配器来检查一个值是否匹配给定的结果。所以你可以检查一个值是否是`null`、`true`、大于或小于等。

## 单元测试🧪的具体例子

好了，我已经展示了一个非常不现实的单元测试的例子，所以让我们从头到尾在一个真正的组件上试一试。

我已经使用`create-react-app`创建了一个项目，它开箱即用，并设置了 Jest。无论您使用什么框架，他们的大多数 CLI 都会为您配置 Jest，所以您只需创建测试文件和编写测试即可！如果你没有使用这些 CLI 或者只是需要从头开始配置 Jest，请随意阅读他们的[入门](https://jestjs.io/docs/en/getting-started.html)文档。

现在，让我们安装 [Enzyme](https://airbnb.io/enzyme/) ，它将允许我们通过渲染组件来测试组件的输出。请注意，有许多众所周知的工具可以用来测试前端应用程序，Jest 和 Enzyme 是其中一些最著名的工具。

让我们按照他们的介绍文档安装必要的包:

`npm i --save-dev enzyme enzyme-adapter-react-16 react-test-renderer`

然后，我们需要通过创建以下文件来设置我们的适配器:

```
// /src/setupTests.js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() }); 
```

> **注意**:如果你正在使用一个旧版本的 React，确保为你正在使用的版本配置正确的适配器，随意阅读他们的[安装](https://airbnb.io/enzyme/docs/installation/#installation)文档。

您可能已经注意到，`create-react-app`创建了下面的单元测试:

```
// App.spec.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
}); 
```

所有测试文件都有相似的格式:`*.spec.js`或`*.test.js`，这取决于您的偏好。我个人总是使用第一种格式。😄

在你的控制台上运行`npm run test`试试看。您应该得到以下输出:

```
 PASS  src/App.spec.js
  ✓ renders without crashing (2ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.097s, estimated 1s
Ran all test suites. 
```

太棒了，我们已经运行了我们的第一个单元测试。

我们现在将构建一个基本的计数器应用程序，让用户单击一个按钮来增加屏幕上的值。源代码可以在[这里](https://github.com/christopherkade/unit-testing-demo)找到，所以可以随意克隆或派生回购来尝试一下。

我们的组件如下所示:

```
class App extends Component {
  state = {
    counter: 0
  }

  handleClick = () => {
    this.setState(state => {
      return {
        counter: state.counter + 1
      }
    })
  }

  render() {
    return (
      <div className="App">
        <header className="App-header">
          <h1>{this.state.counter}</h1>
          <button className="button" onClick={this.handleClick}>
            Click Me !
          </button>
        </header>
      </div>
    );
  }
} 
```

那么我们从哪里开始呢？问问自己我们可以测试组件的哪些方面，在这种情况下:

*   屏幕上最初显示的是什么
*   当用户点击按钮时，计数器增加

### 测试渲染值

Enzyme 的类似 JQuery 的语法和 Jest 的断言使得测试这些情况变得非常容易，我们应该这样做:

```
import React from 'react';
import App from './App';
import { shallow } from 'enzyme'

// 1\. Test suite
describe("[UNIT] Testing the App component", () => {
  let wrapper

  // 2\. A Jest setup helper function 
  beforeEach(() => {
    // 3\. Enzyme's shallow rendering
    wrapper = shallow(<App/>)
  })

  describe("Component validation", () => {    
    // 4\. Our unit test, checking if the initial value is 0
    it('displays 0 as a default value', () => {
      expect(wrapper.find("h1").text()).toContain("0")    
    })
  })
}) 
```

您可能注意到了一些事情，所以让我们来看一下代码。

1.  正如我们前面提到的，Jest 允许我们创建测试套件来组织我们的测试。
2.  有时你想在测试运行前设置一些东西，或者在测试运行后包装其他东西。这就是为什么 Jest 提供安装和拆卸助手功能，你可以在[这里](https://jestjs.io/docs/en/setup-teardown)阅读。你会发现你自己使用的主要的是`beforeEach`和`beforeAll`，因为它们允许你渲染你的组件，这将我们带到第三个。
3.  浅层渲染是 Enzyme 提供的少数几种渲染方法之一。在浅层渲染的情况下，我们渲染组件本身**而不渲染其子组件**。这允许您将组件作为一个单元来测试，这样，如果您修改了一个子组件，它将不会影响当前正在测试的组件。当 Enzyme 第一次出现在你的屏幕上时，把它作为你的组件的一个实例来看，包括它的内部状态，HTML 等等。
4.  我们的第一个测试很简单:我们通过向`find`方法传递一个选择器来查找组件的`h1`标题，并直接访问它的文本；然后，我们使用 Jest 的断言方法检查它是否包含值 0。简单吧？

好了，进行第二项测试。

### 检测事件

多亏了 Enzyme，测试事件变得非常简单，下面是我们测试点击按钮增加计数器的方法:

```
it("should increase counter when the button is clicked", () => {
   wrapper.find("button").simulate("click")
   expect(wrapper.find("h1").text()).toContain("1")
}) 
```

我们使用`button`包装器上的`simulate`方法来触发事件，然后检查我们的标题，看它是否等于 1。

> **注意**:大多数事件类型都可以使用模拟方法进行模拟，包括输入、点击、聚焦、模糊、滚动等。

### 测试代码覆盖率

要掌握的一个重要概念是代码覆盖率，它代表了被测试代码的百分比。

代码覆盖率工具检查以下内容:

*   语句:代码中执行了多少条语句。
*   分支:由条件语句(if/else)创建的分支，可以执行也可以不执行。
*   函数:被调用的函数的数量。
*   行数:测试期间执行的行数的比例。

可能看起来像这样(基于我们前面的例子):

[![](img/bec37d26c1a887c27acfad10d6c956a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8P2PGafo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/15229355/55360258-1d20ce80-54d4-11e9-9cbc-e08a0e437042.png)

我们最常用的代码覆盖工具之一叫做[伊斯坦布尔](https://istanbul.js.org/)，当你运行下面的命令`npm run test --coverage`时，create-react-app 使用它来报告你的应用程序的代码覆盖。

像伊斯坦布尔这样的工具以 HTML 文件的形式生成一个代码覆盖报告，它可以帮助你了解你的代码中哪些部分还没有被测试。它突出显示了在单元测试中没有覆盖的特定行，以帮助您达到 100%的覆盖率。

> 注意:代码覆盖率并不代表一切，100%的覆盖率并不意味着你已经测试了给定组件的每一个场景，所以你应该只在有意义的时候努力去做。
> 
> 正如 [@edaqa](https://twitter.com/edaqa) 所指出的，代码覆盖率可以被视为一个糟糕的指标，因为它可能“通过将执行的代码行等同于测试的代码行来提供一种虚假的安全感”以及其他问题。所以要小心使用它，因为它可以让你对你的代码覆盖了多少有一个总体的了解，并且不要混淆度量和目标。

## 优秀奖👏

以下是一些我没有谈到但值得一提的事情，排名不分先后:

*   Jest 有一个`--watch`选项，允许在测试文件改变时自动运行测试。
*   酶的一个伟大的[备忘单](https://devhints.io/enzyme)。
*   确保检查代码覆盖报告期间生成的文件，准确地知道哪些行没有被覆盖可以节省大量的时间。
*   确保你测试的是需要测试的东西，避免测试第三方包的工作，集中测试你的组件是否符合你的规格。
*   测试驱动开发(TDD)的概念可以描述如下:“测试驱动开发是这样一种行为，首先决定你希望你的程序做什么(规范)，制定一个失败的测试，然后编写代码使测试通过”([链接](https://medium.freecodecamp.org/an-introduction-to-test-driven-development-c4de6dce5c))，如果你不能马上完全理解它，不要担心，但重要的是最终理解它的价值，并知道有一天你可能会被要求实践它。这里有一个伟大的[起点](https://github.com/dwyl/learn-tdd)。

## 关闭思绪🎁

我相信这些信息对于前端测试的简单介绍来说已经足够了，并且应该可以让你从整体上学习很多很多关于单元测试的东西。

现在对你来说，测试可能感觉是一种耗时且无用的实践，但是相信我，你最终会意识到测试一个应用程序是多么的重要。它将帮助你调试和构建你的代码，节省时间，减少技术债务，改善你的工作流程，并从长远来看全面提高你的生产力。

一如既往，非常感谢你花时间阅读这篇文章，我希望你在这个过程中学到了一些东西。

如果您有任何问题，请随时在 Twitter 上发送给我 [@christo_kade](https://twitter.com/christo_kade) ，如果您喜欢这篇文章，关注我会在我上传任何新内容时提醒您！