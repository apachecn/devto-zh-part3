# 开玩笑地嘲笑回调

> 原文：<https://dev.to/jhotterbeekx/mocking-with-callbacks-in-jest-2423>

今天我在帮助我的一个同事为他们的组件创建单元测试。该应用程序是用 react 构建的，在 package 上运行，并结合 jest 和 enzyme 进行测试。在我们到达一个使用回调方法传递给另一个组件的组件之前，一切都很顺利。一旦回调被调用，第一个组件的结果就会改变。到那时为止，我已经能够深入了解如何构建单元测试，寻找什么以及如何处理嘲讽，但是这种情况并没有那么直接。当我们在互联网上挖掘例子的时候，我们发现了这个问题，现在我想和你们分享这个结果。

在我开始实际的代码之前，本文希望您对 react、jest 和 enzyme 有基本的了解。我将解释它的一些基本部分，但不是整个基础，因为这将是一个相当大的覆盖面。我开始在 https://codesandbox.io/制作一个演示，但是遇到了 jest.mock 还不被支持的问题。所以我在本地创建了演示，并把它放在 github 上，你可以在[https://github . com/JHotterbeekx/jest-mock-with-callback-demo](https://github.com/JHotterbeekx/jest-mock-with-callback-demo)找到它。

## 回调

让我们从什么是回调的简单解释开始。我假设你对方法很熟悉，所以让我们以这个基本方法为例。

```
function doSomething(argument) {
  console.log(argument);
}
doSomething("I'm awesome") 
```

这是做什么的？它将字符串“我很棒”写入控制台。假设您传入的参数是一个字符串，它会将这个字符串写入控制台。事实上，你可以向它传递任何东西，它会把它写到控制台。但是如果我们给它传递一个方法呢？让我们试试。

```
function doSomething(argument) {
  console.log(argument);
}

function doAnotherThing() {
  console.log("I'm awesome");
}

doSomething(doAnotherThing); 
```

控制台现在告诉你什么？

```
function doAnotherThing() {
  console.log("I'm awesome");
} 
```

好吧，似乎有道理。您将一个方法传递给了另一个方法，因此自然地，记录这个参数会向您展示这个方法。但是等等，如果我调用这个方法呢？

```
function doSomething(argument) {
  argument();
}

function doAnotherThing() {
  console.log("I'm awesome");
}

doSomething(doAnotherThing); 
```

控制台现在告诉你什么？

```
I'm awesome 
```

什么？多棒啊。我们将一个方法传递给另一个方法，后者又调用我们传递的方法。我们能让它更棒吗？是的，我们可以，看这个。

```
function doSomething(argument, whatYouAre) {
  argument(whatYouAre);
}

function doAnotherThing(whatIAm) {
  console.log("I'm " + whatIAm);
}

doSomething(doAnotherThing, "even more awesome"); 
```

控制台现在告诉你什么？

```
I'm even more awesome 
```

我们把它变得更棒了！你看到我们在那里做了什么吗？我们不仅传递了方法，还传递了一个额外的参数，这个参数后来被传递给了方法。您已经看到了回调方法的基本概念。我听到你在想，“但这对我来说没有任何意义！你为什么要这么做？！?"。上面的例子是为了便于阅读而设置的，但是它可能看起来没有太大的意义。让我试着给你举一个更真实的例子。想象一下，doSomething 为您调用一个 API，当调用完成时，它解析结果，现在用结果调用回调方法。现在，传入回调方法并定义它的组件将通过该方法的内容来处理它。你能跟上吗？当你能看见它的时候，它总是更容易，让我们解决它。

## 真实用例

好吧，让我们假设我们有一个应用程序有两个组件。首先是 DataDisplayer，它显示从 DataRetriever 中检索的结果。然而，这个检索器是异步工作的，所以它不能只传回结果。有几种方法可以做到这一点，但在这种情况下，我们将使用回调方法。我在代码中添加了注释，试图解释我们做了什么，让我们看看 DataDisplayer。

```
import React from "react";
import DataRetriever from "./DataRetriever";

export default class DataDisplayer extends React.Component {
  constructor(props) {
    super(props);

    // We initialize the state with a property that contains a boolean telling us if data is
    // available, which will be set to 'true' once the callback method is called. And a data
    // property which will be filled on callback containing a string with a title.
    this.state = {
      dataAvailable: false,
      data: null
    };
  }

  // We use the componentDidMount to trigger the retrieval of the data once the component is
  // mounted. Which means the component first mounts with its default state and than triggers
  // this method so data is retrieved.
  componentDidMount() {
    // We create a new instance of data retriever and call the retrieve method. In this
    // retrieve method we pass a so-called callback method as a parameter. This method will
    // be called inside the retrieve method. As you can see the method expects a title parameter
    // which it will set on the data property in the state and also setting the dataAvailable
    // property to true;
    new DataRetriever().Retrieve(title => {
      this.setState({
        dataAvailable: true,
        data: title
      });
    });
  }

  // This render method will initially render the text 'Data not available', because in the 
  // initial state the property dataAvailable is false. Once data is retrieved and the callback
  // method has been called the state will update, which triggers a re-render, so the render
  // is executed again. Now the dataAvailable will be true and the content in data will be shown.
  render() {
    if (!this.state.dataAvailable) return <div>Data not available</div>;
    return (
      <div>
        Data value: <strong>{this.state.data}</strong>
      </div>
    );
  }
} 
```

好的，让我们看看这个页面的基本功能。它会显示“数据不可用”的页面。在安装组件时，它触发对检索器的调用，传递一个回调方法。当被调用时，这个回调方法获取检索器的结果，将它放入状态并重新呈现显示检索到的标题的组件。

现在让我们看看数据检索器，这是回调方法被传入的地方。

```
export default class DataRetriever {

  // This demo method calls an open API, then translates the response to JSON. Once that is done
  // it calls the passed in callbackMethod with the title property as parameter. So when the API
  // gives us { title: 'myTitle' }, the code will perform callbackMethod('myTitle')
  Retrieve(callbackMethod) {
    fetch("https://jsonplaceholder.typicode.com/todos/1")
      .then(response => {
        return response.json();
      })
      .then(responseJson => callbackMethod(responseJson.title));
  }
} 
```

所以这个 API 调用一个网站调用 jsonplaceholder。它将这个结果解析为一个 JSON 对象，然后用对象的标题作为参数调用回调方法。现在开始有意义了吧？太好了，但是你要怎么测试这些呢？让我们深入研究一下。

## 带回调嘲讽的测试

我们为什么要嘲笑呢？我们正在编写单元测试，单元测试的问题是你希望他们测试一个单元。在这种情况下是一个组件。如果你只是调用 DataDisplayer，它也会使用 DataRetriever，但是这个组件可能已经被测试过了。您实际上希望能够预测您的数据检索器将要做什么，从您的其他组件控制它。您希望这种隔离的另一个原因是，当您中断 DataRetriever 时，您只希望该组件的测试中断，而不是每个可能以某种方式使用它的组件。想象一下，当您更改 DataRetriever 逻辑中的某些内容时，必须更改几十个测试，您不希望这样。

我提到您想要预测另一个组件，在本例中是 DataRetriever，是做什么的。我们通过嘲笑来做到这一点。模仿允许我们用一个假的(或被模仿的)组件替换 DataRetriever 组件，这个组件正是我们想要的。让我们从在测试文件中构建基础 scafolding 开始。

```
import React from "react";
import { mount } from "enzyme";
import DataDisplayer from "./DataDisplayer";
// We want to test DataDisplayer in an isolated state, but DataDisplayer uses DataRetriever.
// To keep the isolation we will need to mock out the DataRetriever. This way we control 
// what this component does and we can predict the outcome. To do this we need to do a manual
// mock, we can do this by importing the component we want to mock, and then defining a mock
// om that import.
import DataRetriever from "./DataRetriever";
jest.mock("./DataRetriever");

describe("DataDisplayer", () => {
  // Before each test we want to reset the state of the mocked component, so each test can
  // mock the component in the way it needs to be mocked. Should you have any default mock
  // needed that is required for every test, this is the place to do this.
  beforeEach(() => {
    DataRetriever.mockClear();
  });
}); 
```

这有道理吗？让我们再复习一遍。这是 DataDisplayer 的测试文件，它使用了 DataRetriever。我们将数据检索器导入到测试中，就像 DataDisplayer 一样。但是在导入之后，我们用一个模仿的组件替换这个组件。为了确保所有的测试都是独立运行的，所以没有一个测试会被另一个测试所做的任何模仿的事情所困扰，我们在每个测试之前清除了模仿。但是我们能预测和控制模拟做什么吗？不，我们还不能，但是我们现在已经准备好了工具来这样做。让我们编写第一个测试。

```
// In this test we will mock the DataRetriever in a way that it will call the callback method
// we pass to it, and call it with "fakeTitle" as argument. This simulates that the API has
// given us a result with { title: "fakeTitle" } in it.
it("Should show the data, When retrieved", () => {
  // We are going to set up a mock implementation on the DataRetriever, we tell it when the code
  // uses DataRetiever instead of the original code it will receive a mocked object. This mocked
  // object has one method call "Retrieve".
  DataRetriever.mockImplementation(() => {
    return {
      // The retrieve method is defined as a method with is own logic. It's a method that gets 
      // another method as argument, the so-called callback method. And the only thing it does
      // is call this method with the argument "fakeTitle". This means that when the code will
      // create a new instance of DataRetriever and calls Retrieve(callback) that the method
      // callback is instantly called with the argument "fakeTitle". Simulating the API returning
      // this result.
      Retrieve: (callback) => callback("fakeTitle")
    }
  });

  // We mount the compont through enzyme. This renders the component with a fake DOM making us
  // able to see the result that would be rendered. Usually in unit tests I'd prefer the shallow
  // mount which doesn't execute lifecycle methods, but in this case part of the logic of our
  // component is in the componentDidMount lifecycle method, so we need mount to make sure this
  // lifecycle is triggerd.
  var wrapper = mount(<DataDisplayer />);
  // Since we fake a result coming back from the retriever, we expect the text to actually show
  // the word "fakeTitle" in the component.
  expect(wrapper.text()).toContain("fakeTitle");
}); 
```

这并不难，对吗？看起来你会在玩笑中遇到的大多数测试，唯一奇怪的部分可能是模拟实现部分。这就是嘲笑这次回调的关键所在。看，通过实现 mock，我们告诉代码，当运行这个测试时，DataRetriever 的任何实例都不是实际的组件，而是我们返回一个已定义的对象，它也有一个 Retrieve 方法。所以代码可以调用这个方法。但是这个 retrieve 方法是我们实现的，我们告诉它只要用包含“fakeTitle”的字符串调用回调就可以了。所以只要实际的代码调用 Retrieve(callback)，回调就会被立即调用，比如 callback("fakeTitle ")。这可能需要一些时间来适应，但只要尝试一下，它确实有意义。

现在我们要测试另一个场景，如果 API 失败了怎么办？或者由于某种原因(还)没有调用回调。让我们为此编写一个测试。

```
// In this test we will mock the DataRetriever in a way that it will not call the callback
// method we pass to it. This simulates tha API not being finished or returning an error.
it("Should show not available, When data has not been retrieved", () => {
  // We are setting up a new mock implementation on the DataRetriever again.
  DataRetriever.mockImplementation(() => {
    return {
      // This is where we made it a little different. Instead of passing a method which does
      // an instant call to the callback we pass an empty method that doesn't do anything. So
      // when the code will create a new instance of DataRetriever and calls Retrieve(callback)
      // nothing is done with this callback. To make it more clear you could also read this line
      // as: Retriever: (callback) => { /* Do Nothing */ }
      Retrieve: () => {}
    }
  });

  //We mount the component again, since we need to use the lifecycle methods.
  var wrapper = mount(<DataDisplayer />);
  // Since we fake no result coming back from the retriever we don't expect any title appearing
  // on the page, but instead we expect to see the text "not available"
  expect(wrapper.text()).toContain("not available");
}); 
```

我们做的唯一“大”改变是交换了 Retrieve()的实现。我们没有直接调用回调方法，而是什么都不做。因此，当代码从这个测试中调用 Retrieve()时，实际的回调永远不会被触发。酷吧？

## 资源

互联网上有很多资源，尽管在这个问题上你必须小心不要使用那些过时的资源。对于玩笑和嘲讽来说，一个很好的起点是他们关于 https://jestjs.io/docs/en/getting-started 的文档，尤其是嘲笑 https://jestjs.io/docs/en/es6-class-mocks 的 ES6 职业 T2 的部分。他们的文档可能是压倒性的，尤其是许多嘲笑事物的方法，但这是一个非常好的文档，所以只要尝试一下。除此之外，只要谷歌一下你想要的，我们就能以这种方式找到它。你会遇到足够多的文章和堆积如山的问题来帮助你，只要观察文章的日期，并尝试寻找更近的文章。

## 承诺和异步/等待

虽然这个例子使用了回调方法，但现在这些方法大多被使用 promises 或较新的 async / await 结构所取代。这并不意味着你不能再使用回调了。只要使用对你的代码有意义的东西。

## 包装完毕

此刻你可能会头晕，不要担心它会停止。或者你可能想知道，这就是全部吗？那我会说，对你有好处！与开发软件中大多数概念一样，你必须在某个地方看到它们，并开始自己去做。然后一遍又一遍地重复。最后让他们坚持下来，改进他们，并教给其他人。所以我很期待有人会写一篇关于如何以他或她的方式做到这一点的文章！你通过阅读来学习，通过实践来学习，通过分享来坚持。