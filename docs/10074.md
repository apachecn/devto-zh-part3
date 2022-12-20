# 用 Jest 测试异步生命周期方法

> 原文：<https://dev.to/jhotterbeekx/testing-asynchronous-lifecycle-methods-with-jest-13jo>

最近在帮助一位同事时，我们漫步进入了承诺之地，慢慢偏离了黄砖异步路，陷入了破碎承诺的交换中，等待我们的命运解决。如果您有一些用 javascript 编写异步代码的经验，您可能已经获得了参考资料。如果没有，不要担心，我稍后会深入介绍，这样您也可以了解承诺和 async / await，但主要是当您的生命周期方法使用异步代码时，您如何编写单元测试。因为这是我们最后被困的沼泽。不管有多慢，但我们确实走出去了，我很乐意与你分享我的经历。

在我开始实际的代码之前，本文希望您对 react、jest 和 enzyme 有基本的了解。我将解释它的一些基本部分，但不是整个基础，因为这将是一个相当大的覆盖面。我在本地创建了一个演示，并把它放在 github 上，你可以在 https://github . com/JHotterbeekx/demo-jest-testing-with-async-life cycle 找到它。

## 承诺

什么是承诺？而且我说的不是那些你对配偶说的话，也不是那些只要十指交叉就不需要保留的话。我说的是 javascript 承诺。它们实际上与你在现实生活中做出的承诺没有太大的不同，你做出了承诺，它们可以被实现，我们称之为解决，或者它们可以被打破，我们称之为拒绝。promise 方法是一种定义它将要执行的动作以及如何处理该动作的结果和/或失败的方法。实际操作尚未执行，但将会执行。开始困惑了吗？让我们看一些代码来阐明这一点。让我们拿出控制台日志！

```
function add(x, y) {
  return x + y
}
add(12, 13); 
```

这是做什么的？它将数字 25 写入控制台。这个没什么特别的。让我们把它变成一个承诺。

```
function add(x, y) {
  return new Promise((resolve) => resolve(x + y));
}
console.log(add(12, 13)); 
```

我们现在在控制台上看到了什么。如果你很快，非常快，我的意思是真的像闪电侠一样快，或者有时间减慢的能力，矩阵中的人似乎有，你会看到一个快速日志显示:

```
Promise {<pending>} 
```

这意味着我们承诺它会做一些事情，在这种情况下，添加数字，但它没有履行承诺。现在，如果你等待微秒的承诺将解决。您会注意到日志发生了变化。变了？是的，改变了，因为它仍然是相同的承诺，这一切都将发生在同一个控制台日志。你的超级英雄自我将会看到所有其他人几乎立刻看到的东西:

```
Promise {<resolved>: 25} 
```

这意味着承诺被解决，它返回的值是 25。但是，如果我们想记录这个值，我们如何得到这个值呢？等等，我们会让你进入矩阵，或者让我们处理承诺的结果。

```
function add(x, y) {
  return new Promise((resolve) => resolve(x + y));
}
add(12, 13).then((sum) => console.log(sum)); 
```

你现在看到了什么？25?哇，那是魔法吗？很接近，但不是真的，虽然这是我向你保证的下一个最好的事情。你看到我们在这里改变了什么吗？我们接着补充道。then()部分在 add()之后。这就告诉引擎，一旦解决了承诺的结果，该如何处理，在本例中就是将它记录到控制台。很棒吧？！？但是我感觉这个问题在你的舌尖上燃烧，就像我们在万维网上有一种精神联系，就像你在问我；“你能让它更棒吗？拜托了。！?"。是的，我们可以！看这个:

```
function add(x, y) {
  return new Promise((resolve) => resolve(x + y));
}
add(12, 13)
  .then((sum) => {
    console.log(sum)
    return sum + 10;
  })
  .then((sum) => {
    console.log(sum)
    return sum + 7;
  })
  .then((sum) => {
    console.log(sum)
  }) 
```

你的控制台现在显示什么？会不会是？

```
25
35
42 
```

我们找到了生命的意义！但这怎么可能呢？这就是所谓的承诺链的魔力。这意味着当您从 resolve 处理程序返回一个结果(这是 then 中的代码)时，您会产生另一个 resolve。它可以被处理并产生另一个，等等。在这种情况下，我们不断增加结果的值，这显示在日志中。

是为了承诺吗？差得远，但这足以作为本文的基础。如果你想阅读更多，我很乐意推荐你这篇文章:[https://codeburst.io/javascript-learn-promises-f1eaa00c5461](https://codeburst.io/javascript-learn-promises-f1eaa00c5461)或者使用谷歌既然 promises 已经有一段时间了，你可以找到很多资料。对我们来说，我们进入了下一个层次，异步/等待。

## 异步/等待

虽然承诺已经有一段时间了，但大约两年前随着 ES2017 的发布，我们从圣诞老人那里得到了一份新礼物。很多人抱怨使用承诺、嵌套承诺和承诺链时代码混乱。有些人永远不会满足，你得到了可怕的异步代码的可能性，他们滥用它，使它再次混乱...但他们是对的，事情确实会很快变得一团糟，想象一下，不得不按照一定的顺序处理两三个承诺，多么可怕！这就是为什么我们有 async / await。让我们看一个有三个承诺的例子，在这里我写了最后一个例子来继续使用同一个承诺:

```
function add(x, y) {
  return new Promise((resolve) => resolve(x + y));
}
add(12, 13)
  .then((sum) => {
    console.log(sum);
    return add(sum, 10);
  })
  .then((sum) => {
    console.log(sum);
    return add(sum, 7);
  })
  .then((sum) => {
    console.log(sum);
  }); 
```

虽然结果令人惊讶，但阅读代码并不容易，不是吗？让我们使用 await，看看有什么变化。

```
function add(x, y) {
  return new Promise(resolve => resolve(x + y));
}

async function showMeaningOfLife() {
  let sum = 0;
  sum = await add(12, 13);
  sum = await add(sum, 10);
  sum = await add(sum, 7);
  console.log(sum);
}

showMeaningOfLife(); 
```

这干净多了，对吧？为了能够使用 await，我们不得不将异步调用封装在一个方法中，但是所有的链接和嵌套都消失了，这使得它更容易阅读。

基本上没有什么需要异步/等待的，这是一种让承诺更容易和更可读的方式。如果你想了解更多关于差异和优势的信息，我建议你阅读:[https://code burst . io/JavaScript-es-2017-learn-async-await-by-example-48 ACC 58 bad 65](https://codeburst.io/javascript-es-2017-learn-async-await-by-example-48acc58bad65)。我们暂时就此打住，是时候去看看测试了！

## 真实用例

好吧，让我们假设我们有一个应用程序有两个组件。首先是 DataDisplayer，它显示从 DataRetriever 中检索的结果。然而这个检索器是异步工作的，所以我们不能只使用结果，我们必须正确地处理它。我在代码中添加了注释，试图解释我们做了什么，让我们看看 DataDisplayer。

```
import React from "react";
import RetrieveData from "./DataRetriever";

export default class DataDisplayer extends React.Component {
  constructor(props) {
    super(props);

    // We initialize the state with a property that contains a boolean telling us if data is
    // available, which will be set to 'true' once the data is available. And a data
    // property which will be filled with a title.
    this.state = {
      dataAvailable: false,
      data: null
    };
  }

  // We use the componentDidMount to trigger the retrieval of the data once the component is
  // mounted. Which means the component first mounts with its default state and than triggers
  // this method so data is retrieved. We make the method asynchronous so we are able to use
  // await. This gives us a better readable and debuggable way to handle the promise received
  // from RetrieveData().
  async componentDidMount () {
    // We call the retrieve method and wait for the promise to resolve, the result of this resolved
    // promise will be the title, which is placed in the variable title. We validate if we indeed
    // got a title before updating the state and marking the data as available.
    const title = await RetrieveData();
    if(title){
      this.setState({
        dataAvailable: true,
        data: title
      });
    }
  }

  // This render method will initially render the text 'Data not available', because in the 
  // initial state the property dataAvailable is false. Once data is retrieved and the callback
  // async code has resolved the state will update, which triggers a re-render, so the render
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

好了，让我们看看这个页面的基本功能。它呈现带有“数据不可用”的页面，在安装组件时，它触发对检索器的调用。这是一个异步方法，所以我们选择使用 await 来等待结果。因为我们想使用 await，所以我们也需要使 componentDidMount 异步。

现在，我们也可以处理承诺，而不是等待。为此，componentDidMount 的内容应该是这样的:

```
RetrieveData().then(title => {
      if(title){
        this.setState({
          dataAvailable: true,
          data: title
        });
      }
    }); 
```

几乎相同的代码，只是在函数中嵌套了处理函数。

让我们看一下 DataRetriever，它为我们异步检索数据。

```
// This demo method calls an open API, then translates the response to JSON. Once that is done
// it returns the 'title' property from this data. So when the API gives us { title: 'myTitle' },
// the code will return 'myTitle'. Since we want to use await to give us a readable way to handle
// the promises we encounter, we have to make the method itselfs asynchronous. Which results in 
// the actual return value being a promise that resolves to the title.
export default async() => {
  const todoData = await fetch("https://jsonplaceholder.typicode.com/todos/1");
  const todoDataJson = await todoData.json();
  return todoDataJson.title;
} 
```

这段代码读起来很简单，对吗？我们调用一个 rest API，等待结果。我们将结果转换为 JSON，等待它完成。剩下的唯一一件事就是从它那里把标题还回来。我们可以把它写成一个承诺，这样看起来就像这样。

```
// This demo method calls an open API, then translates the response to JSON. Once that is done
// it calls the passed in callbackMethod with the title property as parameter. So when the API
// gives us { title: 'myTitle' }, the code will perform callbackMethod('myTitle')
export default () => {
  return fetch("https://jsonplaceholder.typicode.com/todos/1")
    .then(response => {
      return response.json();
    })
    .then(responseJson => responseJson.title);
} 
```

可读性仍然不错，但不如 await 语法漂亮。但你现在已经看到了两种选择。是时候弄清楚如何测试了。

## 测试异步生命周期方法

无论我们使用 promise 风格的处理还是 async / await，componentDidMount 中的东西都是异步的。虽然我通常用酶来触发生命周期方法，但在这种情况下不起作用。为什么没用？很棒的问题！lifecycle 方法将在挂载时被触发，但是由于代码是异步调用的，所以执行将继续，而不是等待承诺解决。因此，当您到达 expect 调用时，承诺仍在解析，数据不会被处理。

那我们怎么能等承诺解决呢？其实也没那么难。让我们弄清楚。我们将从搭建测试脚手架开始。

```
import React from "react";
import { shallow } from "enzyme";
import DataDisplayer from "./DataDisplayer";

// We want to test DataDisplayer in an isolated state, but DataDisplayer uses DataRetriever.
// To keep the isolation we will need to mock out the DataRetriever. This way we control 
// what this component does and we can predict the outcome. To do this we need to do a manual
// mock, we can do this by importing the component we want to mock, and then defining a mock
// om that import.
import * as DataRetriever from "./DataRetriever";
DataRetriever.default = jest.fn();

describe("DataDisplayer", () => {
  beforeEach(() => {
    // Before each test we want to reset the state of the mocked component, so each test can
    // mock the component in the way it needs to be mocked. Should you have any default mock
    // needed that is required for every test, this is the place to do this.
    DataRetriever.default.mockClear();
  });
}); 
```

这有道理吗？让我们再复习一遍。这是 DataDisplayer 的测试文件，它使用了 DataRetriever。我们将数据检索器导入到测试中，就像 DataDisplayer 一样。但是在导入之后，我们替换了组件模拟方法的默认导出。为了确保所有的测试都是独立运行的，所以没有一个测试会被另一个测试所做的任何模仿的事情所困扰，我们在每个测试之前清除了模仿。但是我们能预测和控制模拟做什么吗？不，我们还不能，但是我们现在已经准备好了这样做的工具。让我们编写第一个测试。

```
// In this test we will mock the DataRetriever in a way that it will create a promise for us
  // which it will resolve with "fakeTitle" as argument. This simulates that the API has
  // given us a result with { title: "fakeTitle" } in it. We make the test asynchronous, since
  // we want to be able to use await in the code to wait for a promise to resolve.
  it("Should show the data, When retrieved", async () => {
    // We are going to set up the mock value that DataRetriever resolves to, we tell it when the 
    // code uses DataRetiever instead of actually calling it and fetching data from the API. It
    // instantly resolves to a value 'fakeTitle'.
    DataRetriever.default.mockResolvedValue('fakeTitle');

    // We shallow mount the component through enzyme. This renders the component with a fake DOM 
    // making us able to see the result that would be rendered. We specifically use the shallow
    // mount in this case. Not only is this the preferred render for unit tests, since it isolates
    // the component completely when rendering, we also use it because we don't want to trigger
    // the lifecycle methods. Since our lifecycle method handles code asynchronously, we want
    // to be able to wait for that code to complete, this requires manually calling this method.
    var wrapper = shallow(<DataDisplayer />);
    // We need to get the actual instance from the virtual DOM, so we can call any method that 
    // is available on it.
    const instance = wrapper.instance();
    // Now we call the componentDidMount event, telling the component that it mounted. But because
    // we called it manually we are able to await for it to resolve. This makes sure the promise
    // for the method is completed before going on with the code.
    await instance.componentDidMount();
    // Since we fake a result coming back from the retriever, we expect the text to actually show
    // the word "fakeTitle" in the component.
    expect(wrapper.text()).toContain("fakeTitle");
  }); 
```

看起来很像任何单元测试，对吗？让我们走一遍。首先，我们模仿 resolve 值。这意味着对 DataRetriever 方法的任何调用都会产生一个承诺，该承诺会解析为字符串“fakeTitle”。接下来，我们浅安装组件。为什么肤浅？我们需要生命周期方法，对吗？是的，我们做，但我们想打电话给他们手动，你会看到为什么。在下一行，我们从酶中取出组件的实例，这样我们就可以与它进行交互。为下一条线做准备，那里会发生奇迹。我们手动调用生命周期方法 componentDidMount，但是我们使用 await，所以它等待承诺解决。很棒吧？现在我们知道状态处于我们想要的状态，所以我们可以看看是否能找到组件中呈现的“fakeTitle”。

现在我们要测试另一个场景，如果 API 失败了怎么办？或者它不返回标题？让我们试一试。

```
// In this test we will mock the DataRetriever in a way that it will return a different promise
  // which resolves without value. This simulates the API returning unexpected data.
  // We make the test asynchronous, since we want to be able to use await in the code to wait 
  // for a promise to resolve.
  it("Should show not available, When data has not been retrieved", async () => {
    // We are going to set up the mock value that DataRetriever resolves to, we tell it when the 
    // code uses DataRetiever instead of actually calling it and fetching data from the API. It
    // instantly resolves to an undefined value, so we can handle nothing coming back from the API.
    DataRetriever.default.mockResolvedValue(undefined);

    // We are shallow mounting the component again, using its instance, calling the 
    // componentDidMount and waiting for it to resolve. Only this time it will resolve to a value
    // of undefined.
    var wrapper = shallow(<DataDisplayer />);
    const instance = wrapper.instance();
    await instance.componentDidMount();
    // Since we fake no result coming back from the retriever we don't expect any title appearing
    // on the page, but instead we expect to see the text "not available"
    expect(wrapper.text()).toContain("not available"); 
```

所以我们又做了同样的魔术。我们准备好 mock 的 resolve 值，浅化组件，获取实例并手动触发 componentDidMount，然后等待它完成。现在我们知道它没有返回任何内容，所以我们仍然希望文本“不可用”是可见的。没那么难，对吧？

## 资源

而 jest、enzyme、async 和 unit testing 的资料在互联网上随处可见。这个解决方案实际上很难找到。我们找到了很多非常复杂的方法来实现这一点，但都没有成功。最后，我们偶然发现了一个 stackoverflow 帖子，它让我们找到了这个解决方案。[https://stack overflow . com/questions/51895198/jest-enzyme-mock-async-function-in-life cycle-method](https://stackoverflow.com/questions/51895198/jest-enzyme-mock-async-function-in-lifecycle-method)。当然，你可以查看这篇文章中的演示应用，你可以在[https://github . com/JHotterbeekx/demo-jest-testing-with-async-life cycle](https://github.com/JHotterbeekx/demo-jest-testing-with-async-lifecycle)上找到。小提示，最终结果是 async / await 语法，但是如果您查看历史记录，也可以找到 promise 实现。

## 包装完毕

我们越来越多地采用异步方式，但这确实给我们带来了一些额外的挑战。这是唯一的办法吗？没有。这是做这件事的最好方法吗？大概不会。把你读到的东西，变成你自己的，当你完成后，试着把你的解决方案教给别人。记住，你通过阅读来学习，通过实践来学习，通过分享来坚持。