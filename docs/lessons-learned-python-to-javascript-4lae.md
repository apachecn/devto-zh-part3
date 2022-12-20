# 经验教训:从 Python 到 JavaScript

> 原文：<https://dev.to/dan_starner/lessons-learned-python-to-javascript-4lae>

随着我对 JavaScript 世界的深入研究，以下是我发现的一些有趣的事情。

## 一些背景

在彭博，我和一个开发运维/基础架构团队一起工作，他们主要从事硬件、网络和脚本方面的工作。这意味着我团队中的每个人都非常擅长 Bash、Ansible 和 Python，并且可能在一天之内背出比几秒钟还多的网络缩略语。

在我加入后不久，我们开始考虑一个 web 仪表板，它将管理和自动化我们的许多手动流程和任务。我们计划包括所有的功能，它将会*非常酷*...除了我是团队中唯一一个了解 web 开发的人，即使这样也是一种松散的联系。

我以前的经验是为网络公司编写后端服务和 API，但我真的没有前端方面的经验。对于这个 web dashboard 项目，我想真正投入进去，学习正确的反应方式，自己构建一个完整的应用程序。

## 我学到了什么

以下是我从 Python 和后端的角度了解到的或对 JavaScript、React 和前端开发感兴趣的一些事情。请注意，我正在构建一个 React 应用程序，因此许多这些东西都围绕着库。

### NPM 发展依赖 vs 依赖

直到我开始为我的 Express-React 应用程序构建 Docker 图像，我才真正理解这一点。在 Python 中，我总是有一个包含我的测试和林挺库的`dev-requirements.txt`文件和一个包含所有其他依赖项的`requirements.txt`文件。这两个都留在`package.json`真的很好，清理了项目结构，使自动化安装更容易。

### 结构无所谓，只要管用就行

在开发时，我不断地移动文件和文件夹，试图实现最佳的项目结构。我总是有这样的想法:*如果我添加更多的东西，这还能工作吗？*，而且它通常会导致一个永无止境的项目结构管理而不是编码的兔子洞。

我从网上不同的帖子和我自己的经验中学到的是，React 不在乎你使用什么样的项目结构，你也不应该这样。显然，尽量保持整洁有序，但是除此之外，如果有些东西对你有用，不到万不得已不要重构它。我喜欢我现在的文件夹结构，它看起来像下面这样。请注意，我省略了文件。

```
.
├── __mocks__
├── bin                # Scripts needed to run Docker image
├── certs              # Any certs I need for local dev. These are mounted to container
├── dist               # Build directory
├── screenshots
├── src
│   ├── assets         # Non-JS or styling assets
│   │   ├── images
│   │   └── jss        # I use MaterialUI, which styles using JSS
│   ├── components     # General use components
│   │   └── hoc        # Higher order components
│   ├── config         # Configuration file for server that loads env to object
│   ├── core           # Anything that is crucial to the React app
│   │   ├── layout     # Core layout components
│   │   │   ├── header
│   │   │   └── sidebar
│   │   ├── pages      # Not Found / Landing pages
│   │   ├── state      # Core managed state, aka users and theme
│   │   │   ├── auth
│   │   │   └── meta
│   │   └── util       # Any usable themes
│   ├── pages          # List of top level React Router routes as 'pages'
│   ├── server         # All code pertaining to the Express server
│   └── tests          # All tests
└── webpack            # Webpack configs for server and client 
```

Enter fullscreen mode Exit fullscreen mode

### 状态管理/清洁器组件

Python 中的状态管理对我来说并不存在，尤其是如果它是为了更多的脚本目的。我通常更喜欢作文而不是遗传，但这总是因为我在学校学到的东西。编写 React 组件确实让这个想法脱颖而出。

组件由较小的、可能可重用的组件组成，层次结构中的每一层都负责呈现和/或维护应用程序的特定部分。减少代码行真的是一种很酷的感觉，因为我认识到组件或者是相关的，或者是可以由另一个组成的聪明的方法。

一旦你理解了正在发生的事情，瀑布效果的整体概念和从父组件到子组件的状态看起来真的很酷。起初我并不理解这一点，但是随着我理解了适当的状态管理，我的代码和组件之间的关系变得更好了。

### 承诺一开始超级迷惑

来自一个同步的 Python /脚本世界，JavaScript promises 直到 3 天前对我来说还是毫无意义，所以如果我下面的例子仍然不好，请不要讨厌。很长一段时间，我试图让承诺同步，我会很困惑，为什么像下面这样的事情会返回一个承诺。

```
function fetchSomething() {
  const fetchURL = '/something';
  return axios.get(fetchURL);  // returns a promise
}

// Handling the axios call like a synchronous
// function leds to tons of horrible callback 
// and uncaught promise exceptions 🤷🏼‍♂️
function getSomethingHandler(callback) {
  fetchSomething()
    .then(response => { callback(response.data) })
} 
```

Enter fullscreen mode Exit fullscreen mode

但是现在我明白了整个`then(callback)`、`catch(errCallback)`流程，这让*变得更有意义*。在下一次迭代中，我把它重写为下面这样，稍微好一点:

```
function fetchSomething() {
  const fetchURL = '/something';
  return new Promise((resolve, reject) => {
    axios.get(fetchURL)
      .then(response => { resolve(response.data) })
      .catch(error => { reject(error) })
  });
}

// Handling the axios call like a synchronous
// function leds to tons of horrible callback 
// and uncaught promise exceptions 🤷🏼‍♂️
function getSomethingHandler(callback) {
  fetchSomething()
    .then(data => { res.send(data) }) })
} 
```

Enter fullscreen mode Exit fullscreen mode

这个版本停止了许多回调和承诺异常。这也允许我的处理函数期望数据已经被提取函数整理成我想要的格式。最后我开始用`async`和`await`。我仍然不确定这些，所以我道歉，如果以下不是 100%正确的。

```
async function fetchSomething() {
  const fetchURL = '/something';
  try {
    // Wait for the axios promise to resolve
    const response = await axios.get(fetchURL);
    return response.data;
  } catch (err) {
    console.log(err)
    return err;
  }
}

function getSomethingHandler(callback) {
  fetchSomething()
    .then(data => { res.send(data) }) })
} 
```

Enter fullscreen mode Exit fullscreen mode

我仍然在积极地学习更多关于这个异步工作流的知识，但是到目前为止它还是很棒的。

### 加成 CSS: FlexBox 厉害了

更多的是关于 CSS 的东西，但是阻止我更快进入前端设计的主要因素之一是处理元素对齐和大小。无论我输入什么样的`margin`或`align`值，似乎都没有效果。这是我第一次真正体验在 CSS3 中使用 Flexbox，这让世界变得不同。在`flex-grow`、`flex-basis`、`justify`和`align-items`之间，在 HTML 中定位东西变得容易多了。

### 关闭起来

虽然还有很多东西我可以谈论，但这些是我在过去几个月中使用 JavaScript、Express 和 React 的一些主要经验。随着项目的成熟，我可能会写第二篇文章。

感谢阅读👍😃