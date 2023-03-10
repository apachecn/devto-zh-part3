# 反应的内容、原因和方式(测试)

> 原文：<https://dev.to/mangel0111/the-what-why-and-how-of-react-testing-2702>

大家好，今天我们将讨论开发过程中最重要的部分之一(或者应该是一个部分)，无论是框架、语言还是前端或后端，测试对于验证您的代码是否真的做了编码要做的事情都是至关重要的。在这篇文章中，我们将检查测试 react 应用程序的方法，了解什么是`real coverage`的含义，使用的主要和流行的库，并尝试了解什么时候是测试代码的最佳时机，所以让我们开始吧。

# 测试 React 应用程序意味着什么

在我作为一名开发人员的大部分采访中，他们总是问关于 TDD(测试驱动开发)、BDD(业务驱动开发)、单元测试、自动化测试等等，但同时在我工作过的大多数公司中，他们并不真正进行测试，或者说，他们写一些测试作为一种义务，并不赋予代码任何价值，这通常是因为他们没有测试文化。

所以，你的开发人员可能知道如何测试，他们可能知道为什么要测试，但是测试总是像你不得不做的这种痛苦的任务，没有意识到一个好的测试在生产中是没有错误的(没有错误，这是一个神话)，这适用于任何框架或库，不只是反应，所以让我们看看关于测试的主要概念，并试图理解真正的意思，并验证这是否能帮助你的工作。

单元测试
这是一个非常简单但强大的概念，你需要创建一个测试来检查你的代码的一个独特的部分，目标是如果有一个单元测试来验证一个前提，那么一个被调用的函数将会得到你期望得到的相同的答案。

理解了这一点，你就知道你需要尽可能多的单元测试，以验证你的整个代码没有失败，并完全确定任何改变当前行为或破坏与特定开发任务无关的东西的新代码都会在你的测试电池中发现它，让我们看一个例子，这些是我的前提。

*   我的计算器接收两个数并返回两个数的和。
*   如果我传递一个 no 数作为参数，这个参数就被当作零。

这两个前提是我们的基础，是我们需要的，我们可以说这是我们需要写的代码的验收标准。这里的想法是为每个验收标准创建单元测试，以验证我们的功能始终符合两个验收标准，无论您的代码将来是否更改，这两个前提都应该得到尊重。

*TDD(测试驱动开发)*
这是一个在面试中经常出现的术语，但是什么是 TDD 呢？是一种编程实践，在这种实践中，您先编写测试，然后再编写代码，这意味着您需要了解在开始编写代码之前您必须做什么，这意味着您编写测试以期望得到正确的答案(先测试后编写代码)，创建一个将失败的空函数，然后修复您的代码以返回预期的答案，然后继续下一个任务的过程。让我们试着用 TDD 实现我们的计算器:

1.  我们需要创建一个函数来将两个数字相加，所以让我们在编写代码之前编写测试，测试应该期望正确的答案并创建一个将失败的空函数。

```
// Sum function 
var sum = (a, b) => return 0; // This function always return zero

// Your test 
var shouldAddCorrectly = () => {
    return sum(2,2) === 4; // False
}; 
```

Enter fullscreen mode Exit fullscreen mode

在函数上方的代码中，`shouldAddCorrectly`是我们的测试，并期望收到 4，我们试图添加 2 和 2，但`add`函数失败，并返回`0`，我们有一个错误代码的正确单元测试，我们需要的是修复`sum`函数。

```
// Sum function 
var sum = (a, b) => return a + b; // This function now is working well

// Your test 
var shouldAddCorrectly = () => {
    return sum(2,2) === 4; // true
}; 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的测试和代码现在工作，现在我们没有使用任何库或框架来测试，是纯 javascript。第二个前提是告诉我们，当一个参数不是数字时，我们需要检查，如果它是数字，就使用它，否则，这个参数将是零，所以我们创建测试来验证这一点。

```
// Sum function 
var sum = (a, b) => return a + b; // This function now adding but not filling all the requirements.

// Your tests
var shouldAddCorrectly = () => {
    return sum(2,2) === 4; //true
};

var shouldAddCorrectlyWhenFirstParamIsNotANumber = () => {
    return sum('Something',2) === 2; // false, because is returning "something2"
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们的新测试失败了，但我们的测试也是正确的，答案应该是 2，而不是“something2”，现在我们修复了代码，两个测试都通过了。

```
// Sum function 
var checkNumber = (number) => isNaN(number) ? 0 : number;

var sum = (a, b) => {
    var firstParam = checkNumber(a); 
    var secondParam = checkNumber(b); 
    return firstParam  + secondParam;
}

// Your tests
var shouldAddCorrectly = () => {
    return sum(2,2) === 4; // true;
};

var shouldAddCorrectlyWhenFirstParamIsNotANumber = () => {
    return sum('Something',2) === 2; // true
}; 
```

Enter fullscreen mode Exit fullscreen mode

# TDD 的好处

*   我们可以利用这一点来避免在开发结束时尝试测试一切的不良做法，如果你实现了 TDD，你将在编写代码之前完成所有的测试。
*   在开始之前，你会更好地理解你的代码。
*   这将迫使你把你的功能减少到逻辑的小部分，这总是好的。避免过度复杂代码应该是我们的黄金目标。
*   您可以信任您的代码，并确保您可以在集成之前检测开发过程中的错误。

但是，如果 TDD 这么好，为什么在您的过程中实现起来如此困难呢？那么，TDD 的大问题是写一个测试包括一些项目没有的大量时间和精力，并且大多数团队使用这两个经典借口甚至不尝试 TDD。

*   我们没时间了。
*   我们非常确定我们的代码是有效的。

要真正实现 TDD 或任何其他方法，我们需要的只是一个叫做`culture of testing`的简单东西，我们稍后会谈到它。

*BDD(业务驱动开发)*

BDD 是测试过程的一个发展，TDD 用单元测试来验证一小部分，BDD 编写一个不必要的测试单元测试，来验证业务案例在开发中被考虑，而不仅仅是逻辑。

因为你可以有一个非常好的代码完美地工作，验证代码在多种场景下工作的测试，但最终，代码因为不符合业务需求而失败，所以基本上 BDD 是*验证行为而不是实现*，让我们看一个例子。

我们之前写了代码，但是现在我的业务需要计算器回答“您的参数中有错误，请验证，感谢您使用此计算器！”而不是将 no numbers 参数作为零当您提供一个 no number 参数时，这种变化是一个业务需求，我们需要验证它是否有效。

```
// Sum function 

var sum = (a, b) => {
    if(isNaN(a) || isNaN(b)) {
        return "There's an error in your parameters, please verify, and thanks for use this calculator!";
    }
    return a + b;
}

// Your tests
var shouldAddCorrectly = () => {
    var answer = 4;
    return sum(2,2) === 4; // true
};

var shouldAddCorrectlyWhenFirstParamIsNotANumber = () => {
    var answer = 2;
    return sum('Something',2) === "There's an error in your parameters, please verify, and thanks for use this calculator!"; // true
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有了一个验证业务的测试，而不仅仅是实现，我们可以使用 BDD 进行更复杂的测试，例如，在 *How* 部分我们将看到如何在 React 中进行测试

*功能测试、自动化测试和集成测试*
功能测试是由 QA 对人(如果我们可以称之为 QA 的人)进行的测试，QA 现在是验证整个应用程序的人，他们需要验证所有的需求和场景，自动化测试是相同的功能测试，但这次是由工具执行，selenium 是目前运行自动化测试最流行的框架之一。

集成测试是为了验证操作和部署的功能，我们需要理解单元测试是为了关注单个实现的细节，而其他测试是为了验证流程。

*测试文化*
那么，我们定义了一些最重要的测试概念，现在我们需要谈谈测试文化，正如我们之前所说的，测试的问题是大多数开发人员不觉得编写测试是开发的一部分，相反是一个额外的任务，是一个阻碍你继续开发的无聊任务。

我们需要的是一种测试文化，只有当开发人员觉得测试是有价值的，而不是更多的工作时，这才能实现，我们需要做的是遵循这些规则，很快我们就会看到测试的真正价值。

*   *认为在编码之前*，测试是理解你将要编码的东西的最好方法，试着在开始之前识别问题，而不是思考如何实现某事，试着理解什么因素会使你的代码失败，这是一种改变思维，它会给你力量去理解你到底在做什么以及如何做得更好。
*   当我的代码编译和运行时，我可以完成一项任务，但没有测试我们就无法完成整个故事你可以完成所有的任务，编写所有的代码，但在所有事情都完成之前，工作还没有准备好，这意味着一切都应该正常工作，没有测试你不知道这一点，所以你无法知道你的代码是否正常工作。
*   测试失败并不是一件坏事，我们人类在心理上需要看到一切都好，失败意味着我们错了，但有时失败的测试也可能是一件好事，因为这阻止了你去融合错误的东西，你应该感谢测试，以避免成为阻碍所有人的人的耻辱。
*   *化繁为简*，有很多方法和模式可以帮助你写出更好的代码，比如 YAGNI(你不需要它)或者 KISS(保持简单愚蠢)，但是使用 TDD 是最好的方法。
*   真正的目标不是覆盖率，我们经常看到团队的规则是拥有超过 80%的覆盖率或任何其他他们认为合适的数字，但是我们需要理解的是，覆盖率并不意味着什么，因为我们有糟糕的测试，所以不要试图满足一定数量的覆盖率，而是尝试编写真正的测试来验证重要的业务和逻辑。
*   *我们的测试需要防弹，*如果我们可以删除代码中的一行，并且测试无论如何都通过了，那么我们的测试就是不合格的。

这些简单的规则将帮助你在所有的团队中创造一种测试文化。

# 为什么测试中会有反应。

您需要测试，因为您将拥有:

*   质量代码(Quality Code):你确定代码做了我们期望的事情。
*   *设计关注需求*:你理解需求，基于需求进行设计，并在此基础上进行思考。
*   更少的调试更多的编码:更多的测试，更少的错误，你可以专注于更复杂和有趣的任务。

# 测试中如何反应

现在我们开始反应，如何测试我们的应用程序？不仅仅是为了获得覆盖率而进行测试，相反，我们将看到如何进行真正的测试，并完全确定我们的覆盖率是有意义的。我们将使用 React [Jest](https://jestjs.io/) 上最流行的框架进行测试，这个库不仅可以用于 React，而且工作得非常好，我们还将使用 [Enzyme](http://airbnb.io/enzyme/) 这是一个测试 React 应用程序的实用程序，它允许创建我们组件的模拟和阴影，还有(Istambul)[[https://istanbul.js.org/](https://istanbul.js.org/)]帮助我们收集覆盖率。

首先，我们需要理解我们的 React 环境的哪一部分可以被测试，因为我们可以根据元素的范围或类型来划分我们的测试。

## 如何测试组件

React 是一个帮助我们创建封装视图的库，在这里我们可以处理他的状态，并根据组件的需要添加许多逻辑，所以让我们从头开始，看看 React 组件的基本概念

*理解生命周期*
所有的组件都是从一个`mounting`过程开始的。如果组件更新有一个`updating`过程，当组件被移除时有一个“卸载”过程，理解这一点很重要，因为一个好的测试应该验证你的组件在所有状态或生命周期中的行为。每个进程将调用不同的方法，这些方法在某个时刻我们将需要`mock`或调度。

当创建(安装)一个组件时，调用这些方法

*   *建造者*将获得一些道具并开始状态
*   *getDerivedStateFromProps* 几乎从不使用的是静态的，没有访问组件的属性或状态
*   渲染魔法发生的地方
*   这是请求数据最常用的方法

当一个组件检测到他的属性或状态的变化(更新)时，这些方法被调用

*   *getDerivedStateFromProps* 也静态。
*   这个函数用于避免更新后的重新渲染，如果你在你的组件中包含了那种你应该测试的逻辑。
*   *再次渲染*魔力。
*   componentDidUpdate 是请求任何类型数据的最佳位置。

最后，当组件被移除时，这个函数被调用:

*   组件卸载这是用来清理 DOM 和取消所有组件可能的订阅请求。

*注意:*这些是目前在 2018 年 9 月 React 16 中使用的方法，此流程可能会发生变化，一些方法可能在未来被弃用或在 React 的早期版本中不可访问。
*注 2:* 理解每种方法背后的原因对正确使用它们很重要，理解了原因你就能理解什么是测试

*理解状态和道具*
组件也有`states`和`props`，道具是父组件提供的信息，调用他的那个人，而`state`是在构造中声明的，拥有组件的信息，是组件唯一应该操纵他的状态的人，而且道具是神圣的，永远不应该改变。

*阴影和安装*

操纵状态的变化，如果测试 react 组件的方法之一，react 组件具有绑定到其元素的功能，如输入上的“onChange”或按钮上的“onClick ”,因此您可以创建组件的`shadow`或`mount`,然后您应该能够单击并更改输入或模拟真实环境的任何其他事件。

一个`Shadow`是你的组件的一个隔离，你将只渲染你的组件而没有他的孩子，而一个`Mount`将再现所有的渲染流，要使用`mount`你将需要为测试声明 DOM，你可以使用 [JSDOM](https://github.com/jsdom/jsdom) 。

我们需要做的是用酶创建一个组件的阴影或挂载，这将允许您拥有一个组件的挂载和更新过程，在那里您可以更改输入并单击按钮，基本上与您的组件进行所有可能的交互，验证您的状态并调用您的任何方法，这样您就可以通过测试来证明您的用例。

用 Jest 模仿
你可以模仿一些组件来避免你的测试解决外部依赖的复杂性，要模仿你的组件只需在导入声明之后写这个:

```
jest.mock('the relative or absolute path of the js file that you want mock', () => `Mocked JS or whatever`); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，你可以模仿任何东西并返回你需要的任何东西，你也可以使用 Jest 的默认模仿，只传递组件的路径来模仿如果你想了解更多关于这个功能的信息[阅读这个](https://jestjs.io/docs/en/manual-mocks)

现在你知道了 React、Jest 和 Enzyme 的一些基础知识，让我们看看如何编写一些测试。

第一件事是安装所有的依赖项:

```
npm install --save-dev jest react-test-renderer enzyme enzyme-adapter-react-16 enzyme-to-json 
```

Enter fullscreen mode Exit fullscreen mode

*注意*:如果你使用 Babel，async 来获取或键入脚本，你将需要为你的编译器包括 jest 插件，像 [babel-jest](https://github.com/babel/babel-jest) ， [async 来获取](https://github.com/leebyron/async-to-gen#jest)或 [ts-jest](https://github.com/kulshekhar/ts-jest) 。

在开始之前，你需要在你的项目上创建 2 个东西，一个配置文件和一个设置文件，让我们从设置文件开始，它将在我们的`src`文件夹中被称为`jestSetup.js`，在这个文件中，我们将初始化酶适配器。这将有助于我们使用 React 16 的酶，对于旧版本，您需要检查使用什么适配器，这是我的:

```
import Enzyme from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

Enzyme.configure({ adapter: new Adapter() }); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要定义在哪里放置我们的测试，你可以有一个`test`文件夹，我们将在那里为你的代码创建所有的测试，或者你可以把你的测试放在你的测试文件所在的相同位置，Jest 将把所有以`.test.js`或`.spec.js`结束的测试文件作为测试文件运行。

*注意:*你可以在`textRegex`上更改这个，稍后我会展示给你看。

所以你可以有很多你想要的测试，并按你的意愿排序，我们将把它放在同一个文件夹中，但这取决于你。

现在轮到配置文件了，你可以有一个外部文件并在你的测试命令中包含标志`--config=jestconfig.json`，或者你可以只在你的`package.json`中包含一个`jest`键。反正配置应该是这样的:

```
{
    "collectCoverageFrom": [
      "src/**/*.{js,jsx,ts,tsx}",
      "!src/**/*.d.ts"
    ],
    "resolver": "jest-pnp-resolver",
    "setupFiles": [
      "react-app-polyfill/jsdom"
    ],
    "testMatch": [
      "<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}",
      "<rootDir>/src/**/?(*.)(spec|test).{js,jsx,ts,tsx}"
    ],
    "testEnvironment": "jsdom",
    "testURL": "http://localhost",
    "transform": {
      "^.+\\.(js|jsx|ts|tsx)$": "<rootDir>/node_modules/babel-jest",
      "^.+\\.css$": "<rootDir>/config/jest/cssTransform.js",
      "^(?!.*\\.(js|jsx|ts|tsx|css|json)$)": "<rootDir>/config/jest/fileTransform.js"
    },
    "transformIgnorePatterns": [
      "[/\\\\]node_modules[/\\\\].+\\.(js|jsx|ts|tsx)$",
      "^.+\\.module\\.(css|sass|scss)$"
    ],
    "moduleNameMapper": {
      "^react-native$": "react-native-web",
      "^.+\\.module\\.(css|sass|scss)$": "identity-obj-proxy"
    },
    "moduleFileExtensions": [
      "web.js",
      "js",
      "web.ts",
      "ts",
      "web.tsx",
      "tsx",
      "json",
      "web.jsx",
      "jsx",
      "node"
    ],
    "setupTestFrameworkScriptFile": "<rootDir>/src/setupTests.js",
    "snapshotSerializers": [
      "enzyme-to-json/serializer"
    ]
  } "roots": ["test", "src"]
  } 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我使用 react-scripts 2 提供的默认配置，最后两行，`setupTestFrameworkScriptFile`将指示我们的设置文件，而`snapshotSerializers`将帮助我们避免 javascript 上的内存泄漏问题。

您可以[查看 Jest 配置文档](https://jestjs.io/docs/en/configuration.html)以更好地了解您的设置文件中包含的内容。

为了包含覆盖率,我们需要在命令行上添加`npm run test -- --coverage`,以允许 jest 和 Istambul 生成覆盖率报告。

# 最后写一个测试

如果您到了这一部分，那么您已经完成了所有的配置，您可以开始编写您的测试了。

# 匹配快照

Jest with React 的基本示例是 [shallow a component，操纵他的事件并匹配快照](https://jestjs.io/docs/en/tutorial-react)，这个测试是可以的，你将编写你的组件来改变他的属性，比如类名，或者每个事件的一些数据属性。

在 Jest 的例子中，他们创建了一个`Link`组件，他的类名被绑定到状态:`className={this.state.class}`。然后，他们模拟用户在组件上进入(悬停)和离开(模糊)，并为每个事件创建一个快照。

第一次运行测试时，jest 将创建基本快照，如下所示:

```
// __tests__/__snapshots__/Link.react.test.js.snap
exports[`Link changes the class when hovered 1`] = `
<a
  className="normal"
  href="http://www.facebook.com"
  onMouseEnter={[Function]}
  onMouseLeave={[Function]}>
  Facebook
</a>
`;

exports[`Link changes the class when hovered 2`] = `
<a
  className="hovered"
  href="http://www.facebook.com"
  onMouseEnter={[Function]}
  onMouseLeave={[Function]}>
  Facebook
</a>
`; 
```

Enter fullscreen mode Exit fullscreen mode

下一次运行测试时，jest 将验证每个测试中的组件是否创建了相同的快照，如果由于某种原因组件创建了不同的快照，将标记为失败的测试。您可以覆盖以前的快照，但需要在更新前验证失败的原因。

*优点*

*   您可以验证您的组件保持相同的行为，并且每次呈现时都返回相同的 HTML。
*   这将验证执行没有问题，没有抛出异常
*   您应该能够创建通过多个道具的快照，并检查渲染的内容。

*缺点*

*   改变一个组件的属性来反映一个状态并不是一个常见的场景或实践，因此通过测试获得多个快照并不常见。
*   渲染组件并创建快照将通过许多行的测试，这将增加您的覆盖率，但*这并不意味着您正在测试您的组件*，在这种情况下，您只是渲染组件，而不是创建验证业务或功能案例的测试。

# 测试真实案例。

在大多数情况下，你需要做的是创建一个测试来验证你所期望的工作，但是我们需要写一个测试来验证一个真实的案例吗？例如，我们需要一个 DOM 或者至少是一个模拟的 DOM，在那里我可以操作我的组件，基本上模拟真实的交互。

*   如果我创建一个表单，我应该测试向输入添加值，提交或取消表单，并验证提供给输入的值。
*   如果我创建了一个仪表板，可以点击图标并重定向到某个地方，我应该测试点击图标。

听起来很傻也很简单，但是写测试就是这样。

# 我们一起去测试一下吧！

我将使用我不久前写的一个项目作为例子，其中屏幕上显示了多个侏儒的仪表板，你应该能够按名称过滤。
你可以在这里获得源代码[。](https://github.com/mangel0111/brastlewark)

# 为仪表板和搜索输入编写一个测试。

那么，我们需要什么？对此有什么期望？让我们从使用 BDD 的测试开始，首先需要它定义我们期望在多种场景中发生什么

*   没有任何文字的搜索所有的侏儒在仪表板上应该可以看到一个图标。
*   如果我写了一些东西，并与一些配置文件匹配，只有符合该名称的侏儒配置文件应该显示。
*   如果我写的东西不符合任何配置文件，没有配置文件应该显示。

因此，对于这个组件，我们有 3 个功能和业务案例要测试。在这个项目中，我们将测试两种文件。

*   在我所有的 react 视图中，我将只测试仪表板(包括 gnome 列表)，Gnome 框，它有一个白色的框，在那里我可以看到 Gnome 显示的细节和一个 Gnome 细节。
*   saga 是 a 进行应用程序所有交易的地方，我也将向你展示如何测试它。

这将代表我们的应用程序的最重要的部分，并且是应该测试它的人，以确保我们的代码如我们预期的那样工作。

# 测试仪表板

我创建了一个简单的组件，它接收一个侏儒列表，并在一个`GnomeDetails`中显示每个侏儒，有一个过滤器修改当前的列表。一种在很多地方使用的非常普通的部件。

```
export class DashboardPanel extends Component {
    constructor(props){
        super(props);
        this.state = {
            filterText: ''
        };
    }

    filter(){
        const { gnomes }= this.props;
        const { filterText } = this.state;
        const gnomesFiltered = gnomes.filter(gnome => {
            if(filterText){
                return gnome.name.toLowerCase().includes(filterText.toLowerCase());
            }
            return true;
        });
        return gnomesFiltered;
    }

    render(){
        const { filterText } = this.state;
        const gnomesFiltered = this.filter();
        return (
            <Dashboard>
                <Options>
                    Gnomes
                    <Filter>
                        <Input
                            type="text" 
                            width="150px"
                            isFilter
                            title="Filter"
                            value={filterText}
                            onChange={({target})=> this.setState({ filterText: target.value })}
                        />
                    </Filter>
                </Options>
                <GnomesList>
                    {gnomesFiltered.length !== 0 ? gnomesFiltered.map(gnome => 
                        <GnomeBox 
                            key={gnome.id} 
                            gnome={gnome}
                        />):                         <p>No gnomes to display</p>
                    }
                </GnomesList>
            </Dashboard>
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个推荐的组件测试是快照，我们可以使用 jest `toMatchSnapshot`并生成一个快照，它将备份第一次测试中呈现的内容，如果有任何变化，这个快照将失败，这是一个正常的测试，我使用`beforeEach`来加载每个测试的道具，并使用一个简单的测试来创建和验证快照，如下:

```
import React from 'react';
import { mount } from 'enzyme';
import DashboardPanel from 'components/DashboardPanel';
import GnomeBox from 'components/GnomeBox';
import Input from 'components/Input';

let props = {
};

describe('Dashboard Panel', ()=> {
    beforeEach(()=> {
        props = {
            gnomes: [
                {'id':0,'name':'Tobus Quickwhistle','thumbnail':'http://www.publicdomainpictures.net/pictures/10000/nahled/thinking-monkey-11282237747K8xB.jpg','age':306,'weight':39.065952,'height':107.75835,'hair_color':'Pink','professions':['Metalworker','Woodcarver','Stonecarver',' Tinker','Tailor','Potter'],'friends':['Cogwitz Chillwidget','Tinadette Chillbuster']},
                {'id':1,'name':'Fizkin Voidbuster','thumbnail':'http://www.publicdomainpictures.net/pictures/120000/nahled/white-hen.jpg','age':288,'weight':35.279167,'height':110.43628,'hair_color':'Green','professions':['Brewer','Medic','Prospector','Gemcutter','Mason','Tailor'],'friends':[]},
                {'id':2,'name':'Malbin Chromerocket','thumbnail':'http://www.publicdomainpictures.net/pictures/30000/nahled/maple-leaves-background.jpg','age':166,'weight':35.88665,'height':106.14395,'hair_color':'Red','professions':['Cook','Baker','Miner'],'friends':['Fizwood Voidtossle']},
                {'id':3,'name':'Midwig Gyroslicer','thumbnail':'http://www.publicdomainpictures.net/pictures/10000/nahled/1-1275919724d1Oh.jpg','age':240,'weight':40.97596,'height':127.88554,'hair_color':'Red','professions':['Carpenter','Farmer','Stonecarver','Brewer','Tax inspector','Prospector'],'friends':['Sarabink Tinkbuster','Tinadette Wrongslicer']},
                {'id':4,'name':'Malbin Magnaweaver','thumbnail':'http://www.publicdomainpictures.net/pictures/10000/nahled/zebra-head-11281366876AZ3M.jpg','age':89,'weight':43.506973,'height':101.6974,'hair_color':'Black','professions':['Smelter',' Tinker'],'friends':['Fizkin Fussslicer','Cogwitz Chillwidget']},
                {'id':5,'name':'Zedkin Quickbuster','thumbnail':'http://www.publicdomainpictures.net/pictures/10000/nahled/1-1193219094.jpg','age':273,'weight':38.742382,'height':91.54829,'hair_color':'Red','professions':['Cook'],'friends':['Libalia Quickbooster','Whitwright Mystwhistle']},{'id':6,'name':'Emmadette Gimbalpower','thumbnail':'http://www.publicdomainpictures.net/pictures/20000/nahled/stingray.jpg','age':212,'weight':40.681095,'height':98.701645,'hair_color':'Green','professions':['Mason'],'friends':['Ecki Gyrobuster','Zedkin Nozzlespackle','Milli Clankswhistle','Libalia Magnatink']},
                {'id':7,'name':'Twizzle Chrometossle','thumbnail':'http://www.publicdomainpictures.net/pictures/10000/nahled/1-1275919724d1Oh.jpg','age':85,'weight':38.953087,'height':96.0678,'hair_color':'Red','professions':['Baker','Brewer','Tax inspector'],'friends':['Libalia Mystbooster','Zedkin Gyrotorque']},
                {'id':8,'name':'Malbert Tinkbuster','thumbnail':'http://www.publicdomainpictures.net/pictures/10000/velka/1-1248161543llOC.jpg','age':186,'weight':41.159805,'height':118.27941,'hair_color':'Gray','professions':['Baker','Mason'],'friends':[]},
                {'id':9,'name':'Kinthony Nozzlebooster','thumbnail':'http://www.publicdomainpictures.net/pictures/20000/nahled/baby-lamb.jpg','age':233,'weight':41.024612,'height':113.56545,'hair_color':'Red','professions':['Smelter','Miner','Tax inspector','Carpenter'],'friends':['Zedkin Clankstorque','Midwig Magnarivet']}]
        };
    });
    it('should match snaptshot dashboard', () => {
        const dashboardPanel = mount(<DashboardPanel {...props}/>);
        expect(dashboardPanel).toMatchSnapshot();
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这里我有一个测试，发送道具到一个组件，渲染它，一切看起来都很好，但是我们能说我们真的测试了我们的组件吗？我们需要检查我们的覆盖率报告，以真正了解遗漏了什么，如果您运行包含`npm test -- --coverage`的测试，您将会创建一个新文件夹`coverage`和您的根项目，您将会找到这个文件:`/coverage/lcov-report/index.html`，请在您最喜欢的浏览器上打开它，您将会看到您的项目的覆盖率状态。

让我们搜索我们的 DashboardPanel 组件，并尝试理解所报告的内容。

*状态协变*

[![Status with only snapshot test](img/76921d81ed862ec8ca12afc973f23f77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dyaBZCpa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7qktuda9yyfxoruzy0n9.PNG)

哇！我对行和语句有 90%的覆盖率，在函数中我们超过了 80%，它们是很大的数字，分支有点低，但平均来说我们还好，对吗？如果我们作为一个团队决定有 80%的覆盖率，我完全理解这个测试，但是我的组件真的被测试了吗？让我们看看我的代码状态:

[![Code status](img/36be67bbfa14182376f780340f4554de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xOoNuxL5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q314raqko82156z9ig3y.PNG)

如你所见，代码和我之前的数字有所不同，我有一部分代码是红色的，这意味着我的测试没有通过，还有一部分是黄色的，这意味着我有一个条件，如果它没有通过测试。因此，我们可以看到，我的测试并没有真正测试过，我有覆盖率，但我不相信我的代码工作。

让我们做一个真正的测试，我从一个空的 gnomes 列表开始，然后接收它，作为一个正常的流程，然后我们接受输入，用不同的输入模拟 onChange 函数，并验证状态是否正确改变。

```
it('should render dashboard panel',()=> {
        // Mount Dashboard with none list of gnomes because the normal is that the first time never receive anything because the server was no called yet.
        const dashboardPanel = mount(<DashboardPanel gnomes={[]}/>);
        expect(dashboardPanel.find(GnomeBox).length).toEqual(0);

        // Mock the response of the server with 10 gnomes, the component will receive these props and validate that the 10 GnomeBox components are rendered.
        dashboardPanel.setProps(props);
        expect(dashboardPanel.find(GnomeBox).length).toEqual(10);

        //Find the filter component.
        const input = dashboardPanel.find(Input);

                // We mock the user iteration and send to the input an valid change event, and also we validate that the state change accordely, the filter text in the state  and is only one GnomeBox displayed.
        input.at(0).props().onChange({ target: { value: 'Tobus'}});
        expect(dashboardPanel.state('filterText')).toEqual('Tobus');
        dashboardPanel.update();
        expect(dashboardPanel.find(GnomeBox).length).toEqual(1);  

                // Then we validate the case where I just pass a letter and when we reset the filter to nothing again.
        input.at(0).props().onChange({ target: { value: 'a'}});
        expect(dashboardPanel.state('filterText')).toEqual('a');
        dashboardPanel.update();
        expect(dashboardPanel.find(GnomeBox).length).toEqual(4); 

        input.at(0).props().onChange({ target: { value: ''}});
        expect(dashboardPanel.state('filterText')).toEqual('');
        dashboardPanel.update();
        expect(dashboardPanel.find(GnomeBox).length).toEqual(10); 
    }); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们再来看看状态:
[![Status after my real test](img/db8195707699e2a9239efa3de022efbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J4PcT0Fr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a0qtpioeh02hi6ve2526.PNG)

一切都是 100%的覆盖率，但更重要的是，我测试了我的组件的所有可能的行为，就像普通用户会使用它一样。现在我可以相信，如果每个人都修改了代码，并且基础行为发生了变化，我的测试将会发现它。

我们需要明白的是，覆盖率只是一个数字，真正的覆盖率是我们需要得到的，而不仅仅是传递一个随机数。一个组件可以有更复杂的行为，但最终，我们需要做的是理解生命周期并使用它。

# 测试中间件

今天，react 应用程序变得越来越大，我们现在需要在我们的应用程序中包含额外的逻辑，有时我们会包含中间件来处理我们不想(也不应该)包含在我们的组件中的事务，为此我们可以使用 redux-thunk、sagas 或任何东西。我将向你解释如何测试传奇，但这适用于任何[生成器函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)

让我们检查一下我的传奇文件`gnomes`，你可以在传奇文件夹中找到它。我有两个函数，但是让我们测试一下第一个，`fetchGnomesSaga`这个函数负责从服务器获取侏儒，看起来像这样:

```
export function* fetchGnomesSaga(option) {
    yield put(isLoading(true));
    const result = yield call(fetchGnomes, option);
    yield put(isLoading(false));
    if(!result.error) {
        yield put(gnomesFetched(result));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个测试来调用这个函数，模拟事务的行为，发送答案并验证其正确性。让我们从一个生成器函数的基本概念列表开始。

*   生成器是一个 javascript 函数，在名字后面用星号标识，比如这个`function* fetchGnomesSaga(option)`，它将执行代码，但会在每个`yield`中停止，直到得到一个答案。
*   `yield`是我们的交易步骤。
*   我们需要根据每一步可以接收到的内容来验证每一个可能的事务响应。

```
import { fetchGnomesSaga } from './gnomes';

describe('Saga Gnome test', ()=> {
    it('should fetch the gnomes correctly',()=> {
                // Set the Generator function in a constant
        const generator = fetchGnomesSaga({}); // We send nothing because we don't care this right now
        const isLoading = generator.next(); // The first stop is when the saga change the state to Loading
        expect(isLoading.value).toEqual(
            {'@@redux-saga/IO': true, 'PUT': {'action': {'payload': true, 'type': 'IS_LOADING'}, 'channel': null}}
        ); // Now we validate that the state is the correct.
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的测试中，生成器在第一个 yield 上开始和停止，这个 yield 将改变应用程序的`Loading`状态，然后，我调用`generator.next()`函数来模拟 redux 的响应，指示动作已经完成，我可以在`next`上传递值来指示动作发送了一些参数，在这种情况下，redux 只是改变状态，不返回任何东西，这就是为什么是空的。

要完成一次`Ok`旅程，我们需要完成所有的收益，就像这样:

```
it('should fetch the gnomes correctly',()=> {
        // Set the Generator function in a constant
        const generator = fetchGnomesSaga({}); // We send nothing because we don't care this right now
        let isLoading = generator.next(); // The first stop is when the saga change the state to Loading
        expect(isLoading.value).toEqual(
            {'@@redux-saga/IO': true, 'PUT': {'action': {'payload': true, 'type': 'IS_LOADING'}, 'channel': null}}
        ); // Now we validate that the state is the correct.

        // The next stop is the fetchGnomes API
        const callGnomes = generator.next();
        expect(callGnomes.value.CALL.fn).toEqual(fetchGnomes);

        // The next stop before receive the gnomes is disable the loading, in this step is where the data is received, so we send the data on the next
        isLoading = generator.next({ status: true, data: [1,2,3]});
        expect(isLoading.value).toEqual(
            {'@@redux-saga/IO': true, 'PUT': {'action': {'payload': false, 'type': 'IS_LOADING'}, 'channel': null}}
        );

        // We received the data already, but now we call the redux action who change the state with the payload received [1,2,3]
        const gnomesReceived = generator.next();
        expect(gnomesReceived.value).toEqual(
            {'@@redux-saga/IO': true, 'PUT': {'action': {'payload': {'data': [1,2,3], 'status': true}, 'type': 'GNOMES_FETCHED'}, 'channel': null}}
        );

        // The next step and the last one has just finished the generator, we need to validate it to avoid extra steps before the end.
        const endGenerator = generator.next();
        expect(endGenerator).toEqual({'done': true, 'value': undefined});
    }); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的测试中，您可以看到我将事务模拟成一条快乐的路径，如果有人修改了代码，并包含了修改结果的额外步骤，我也应该能够捕捉到它。

现在让我们看看当 API 返回一个错误时如何处理一个不愉快的路径:

```
it('should fetch the gnomes but fails ', ()=> {
        // Set the Generator function in a constant
        const generator = fetchGnomesSaga({}); // We send nothing because we don't care this right now
        let isLoading = generator.next(); // The first stop is when the saga change the state to Loading
        expect(isLoading.value).toEqual(
            {'@@redux-saga/IO': true, 'PUT': {'action': {'payload': true, 'type': 'IS_LOADING'}, 'channel': null}}
        ); // Now we validate that the state is the correct.

        // The next stop is the fetchGnomes API
        const callGnomes = generator.next();
        expect(callGnomes.value.CALL.fn).toEqual(fetchGnomes);

        // The next stop before receive the gnomes is disable the loading, here the fetch fails, we don't care the error, but we need to hanlde it.
        isLoading = generator.next({ error: true });
        expect(isLoading.value).toEqual(
            {'@@redux-saga/IO': true, 'PUT': {'action': {'payload': false, 'type': 'IS_LOADING'}, 'channel': null}}
        );

        // We received the data already, but now we call the redux action who change the state with the payload received [1,2,3]
        const gnomesNotReceivedAndDone = generator.next();
        expect(gnomesNotReceivedAndDone).toEqual({'done': true, 'value': undefined});
    }); 
```

Enter fullscreen mode Exit fullscreen mode

我基本上改变 API 函数来接收错误，当有错误时，我的代码不会更新 gnomes 状态。如果我对任何服务器、解析器逻辑或任何其他场景有不止一个调用，我应该包括一个测试来验证每个场景，假设在任何时候都有可能失败，如果我们编码时认为我们的代码是脆弱的，我们将能够理解并防止未来的问题，这就是为什么我应该有这么多测试的原因。

# 结论

*所以，我们应该测试一切？*
我试图解释我们的 react 应用程序的两个常见位置，在那里我们可以有许多逻辑、组件和中间件，但我们不应该测试所有东西，因为这是规则，我们应该测试所有处理影响我们业务场景的逻辑的东西。

*这个报道是骗人的？*
没有，但是只信任在覆盖范围内没有检查测试的质量是一样的，不做任何测试。代码审查的一部分应该包括验证测试对于想要测试的内容是可以的，高覆盖率意味着测试，但在我们的代码失败的小细节中，覆盖率并没有说我们在这个意义上是可以的。

我应该只说笑话吗？
Jest 非常强大但不是唯一的一个，你可以使用 chai、mocha 或其他任何框架，这个库只是一个帮助，在我们的第一个例子中我们不使用任何框架，一个测试的质量不是针对所使用的工具，是测试本身谁能保证。

如何在我的团队中创造一种测试文化？
很难，因为没有人喜欢写测试，但教如何测试，并解释值应该是第一步。

如何进行更好的测试？
写出更好的代码，如果你应用好的模式和原则，测试应该是简单的，如果我们意识到一个测试花费了我们这么多的时间，而且过于复杂，也许问题出在我们的代码而不是测试本身。*分而治之*

我应该嘲笑吗？
取决于你试图测试什么，在你试图测试一个消耗多个服务的代码时，最好只是模仿那个服务，复制真实的响应。单元测试应该被隔离。

好吧，我希望这篇文章能帮助你对测试过程有更多的了解，不仅仅是如何测试，还有为什么要测试。希望你喜欢阅读。

# 在我们信任的代码中

查看这篇文章的第二部分:

*   [什么，为什么和如何反应(用钩子测试)](https://dev.to/mangel0111/the-what-why-and-how-of-react-testing-with-hooks-2aog)

查看我以前的帖子

*   [什么，为什么和如何反应(风格)](https://dev.to/mangel0111/the-what-why-and-how-of-react-styles-2a0k)
*   [什么，为什么以及如何反应(路由器)](https://dev.to/mangel0111/the-what-why-and-how-of-react-routers-41b)
*   [什么、为什么以及如何反应(高阶组件)](https://dev.to/mangel0111/the-what-why-and-how-of-react-high-order-components-3ko1)
*   [什么时候 SCRUM 不是正确的选择？](https://dev.to/mangel0111/when-scrum-is-not-the-correct-choice-ae8)