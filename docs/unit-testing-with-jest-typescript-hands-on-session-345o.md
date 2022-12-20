# Jest/TypeScript 动手练习单元测试

> 原文：<https://dev.to/lexlohr/unit-testing-with-jest-typescript-hands-on-session-345o>

# <small>或者:如何准备一次实践课</small>

最近，我和我的同事们进行了一次有趣且有启发性的实践活动，用 [Jest](https://jestjs.io/) / [TypeScript](https://www.typescriptlang.org/) 教他们进行单元测试，我想和你们分享这次活动及其准备工作。因此，如果您有兴趣为您的同事准备一次实践会议，或者想了解 Jest/TypeScript 的一些更有趣的特性，这篇文章可能会让您感兴趣。

## 最初的想法和准备

您希望您的与会者尽快开始工作，所以您应该准备一个小的包来帮助他们开始工作，其中包含一个小的自述文件(在 markdown 中)、一些任务文件和尽可能少的脚手架(packages.json、configs)来帮助他们开始工作。

我想用 Jest 和 TypeScript 的基础知识来设置它们，所以我创建了一个小项目:

```
mkdir jest-hands-on
cd jest-hands-on
yarn init -y -p
yarn add --dev typescript jest @types/jest ts-jest 
```

Enter fullscreen mode Exit fullscreen mode

因为我们希望这个会话是关于测试的，所以我在 package.json 中添加了一个小脚本:`"scripts: { "test": "jest" },`——为您的会话花费一些心思在最少的有用的搭建上是值得的。

最后，我决定添加一个 src 文件，其中包含供他们测试的功能单元，每个单元都有我希望他们解决的问题的注释。

> **自我提醒:**你*肯定会*低估它完成所有任务所需的时间。这并不是因为你的与会者比你差。他们可能还不熟悉这个话题，在开始之前需要一些时间来适应。

我的 README.md 只是告诉人们什么是什么，并运行`yarn`和查看`src/testable.ts`来找到他们的任务和所需的阅读材料。

我想让他们发现 Jest 的一些伟大特征:

*   自动创建大量测试
*   快照和序列化有助于发现错误和变化(后来我为了好玩加入了 XSS 任务)
*   自定义匹配器
*   异步测试
*   简单的监视和嘲笑

于是我写了几个函数，在自己解决任务的过程中进行测试，提炼，评论。这是`testable.ts`后来的样子:

```
// Task: create multiple tests at once using it.each
// see https://jestjs.io/docs/en/api#testeachtable-name-fn-timeout
export const add = (...numbers) => {
  return numbers.reduce((sum, item) => sum + item, 0);
};

// Task: create DOM snapshot using expect(...).toMatchSnapshot()
// see https://jestjs.io/docs/en/snapshot-testing
// and don't forget to have a look at the snapshot:
// the snapshot will show a mistake. Fix it and re-run the test
// Task: try to exploit lastPoint in a test for XSS & spy on window.alert to catch it
// see https://jestjs.io/docs/en/jest-object#jestspyonobject-methodname
export const createDom = (lastPoint = 'c') => {
  const container = document.createElement('div');
  container.innerHTML = `<ul><li>a</li><li>b</li></li>${lastPoint}</li></ul>`;
  document.body.appendChild(container);
  container.querySelector('li:nth-child(odd)').setAttribute('test', 'true');
  return container;
};

// Task: write custom matcher for a 32bit number with 11 digits
// Task: use new matcher and expect to validate the config
// see https://jestjs.io/docs/en/expect#expectextendmatchers
const createRandomId = () => (1e16 + Math.random() * 1e15).toString(32);
export const config = {
  id: createRandomId(),
  started: new Date()
};

// Task: write an async test using async/await
// see https://jestjs.io/docs/en/tutorial-async#async-await
// Task: create a new file tester.ts which includes this function from testable.ts
// and mock it to run the callback synchronously
// see https://jestjs.io/docs/en/mock-functions
export const resolveWhenever = () => new Promise((resolve) => setTimeout(resolve, Math.floor(Math.random() * 4e3))); 
```

Enter fullscreen mode Exit fullscreen mode

我还创建了一个最小的`tsconfig.json`和`jest.config.js`，但是我不想用细节来烦你，因为你可以很容易地自己创建它们，因为我主要使用默认值(仅仅包括对`tsconfig.json#compilerOptions.lib`的承诺)。我压缩了脚手架文件，很快就忘了包括一个文件夹，所以我必须提醒我的与会者为这个任务创建一个文件夹，事后看来，这个任务本来可以准备得更好。

几个小时后，动手练习开始了。

## 遇到问题

正如你可能想象的那样，它并不像我想象的那样顺利。让我们来看看突然出现的问题。

### 第一个任务

我按计划开始了会议，分发了 zip 文件，让他们打开并安装它。我的与会者应该使用`it.each(...)`和一组测试数据在一个语句中编写多个测试。我原以为这项任务应该很简单，但没有预料到几件事:

1.  `it.each`的数组结构的复杂性成了一个问题，尽管通过对每个测试使用一个数组，将结果放在第一位，并对被加数使用 spread 运算符，这个问题很容易解决。
2.  spread 操作符的使用让我的同事们非常困惑，他们似乎希望用数组而不是多个参数来调用`add(...)`。
3.  使用可变数量的参数使得格式化测试字符串的能力变得无用，这导致了我的同事们的进一步困惑，他们最初认为这是他们的错。

> **自我提醒:**对你来说显而易见的解决方案对你的与会者来说可能并不那么显而易见。思考那些对你来说显而易见的事情，并准备给出暗示。如果您想将它们添加到您的任务中，使其不立即可见，请使用 base64 编码使它们不可读。

在给出一些小提示后，解决方案看起来像这样:

```
describe("add", () => {
  it.each([[6, 1, 2, 3], [998, 999, -1, 1, -1], [33, 1, 1, 2, 3, 5, 8, 13]])(
    "it will add up the numbers",
    (expected, ...numbers) => {
      expect(add(...numbers)).toBe(expected);
    }
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 第二个任务

第二个任务包含一个将元素注入文档的脚本。它的第一部分旨在展示快照在玩笑中的价值，而且出乎意料地顺利。解决方案如下:

```
describe("createDom", () => {
  it("will create a serialized snapshot of a DOM", () => {
    expect(createDom()).toMatchSnapshot();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在快照很容易地显示出列表的最后一个点不在它自己的`li`元素中，因为它没有被打开:

```
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`createDom will create a serialized snapshot of a DOM 1`] = `
<div>
  <ul>
    <li
      test="true"
    >
      a
    </li>
    <li>
      b
    </li>
    c
  </ul>
</div>
`; 
```

Enter fullscreen mode Exit fullscreen mode

你也能发现它吗？是的，看着你的快照和/或它的变化是它们如此有价值的原因。脸书的 Jest 团队花了很多时间来微调他们的序列化程序，所以好好利用它们来回报他们。

但还有第二部分:我认为滥用 Jest 来捕捉 XSS 攻击会很有趣(XSS 是跨站点脚本的缩写:让外部代码在你的环境中运行的能力，通常是从表单参数、cookies 或类似的渠道添加的，在这种情况下，类似于函数参数)。我的与会者中并不是每个人都听说过 XSS 攻击，所以我可能应该添加另一个提示，例如一个 XSS 有效载荷和在 jsDOM 中触发它的方法，这是 Jest 附带的。

> **自我提醒:**你很容易被自己感兴趣的东西转移话题，但这不在你实际操作课程的主题之内。努力集中注意力！

经过大约 10 分钟的解释，大多数与会者有了这样一个解决方案:

```
describe("createDom", () => {
  // ...
  it("will (not?) allow XSS payloads", () => {
    const alertSpy = jest.spyOn(window, "alert").mockImplementation();
    const XSSPayload = '<span onmouseover="window.alert(1)"></span>';
    const container = createDom(XSSPayload);
    // simulate DOM event
    const ev = document.createEvent("HTMLEvents");
    ev.initEvent("mouseover", false, true);
    container.querySelector("span").dispatchEvent(ev);
    // add .not to fail if alertSpy was called
    expect(alertSpy).toHaveBeenCalled();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

通常，我们的 XSS 有效负载会包含一些 CSS 来确保鼠标悬停事件总是通过在屏幕上分层来触发。因为我们手动选择它来创建事件，所以在这种情况下没有必要这样做。

### 第三个任务

在第三个任务中，有一个要测试的配置对象，它具有不同的属性。如果包含的匹配器不够用，Jest 允许你用你自己的来扩展它们。

幸运的是，用 Jest 编写这样一个自定义匹配器很简单。您用一个包含 matcher 的对象调用`expect.extend({…})`, matcher 只是一个函数，它获取预期的值和任何其他参数。

这次任务的困难来自于 TypeScript，因为`@types/jest`的定义并没有神奇地将这些扩展添加到它的类型声明中，所以为了不被类型错误所阻止，您必须自己去做。容易被忽略的一点是，第一个参数是由 expect 隐式添加的，因此它不会出现在您的声明中。

由于这种复杂性是主题的一部分，我认为我不应该在这种情况下添加提示，但最终，我放弃了，并向他们发送了一个自定义匹配器的示例 TypeScript 声明，非常类似于下面的解决方案:

```
expect.extend({
  toBeRandomId(expected: string) {
    const pass =
      typeof expected === "string" && /^[0-9a-v]{11}$/.test(expected);
    return {
      message: () =>
        `expected ${expected}${
          pass ? " not" : ""
        } to be an 11-digit 32bit lower case random id`,
      pass
    };
  }
});

declare global {
  namespace jest {
    // required for expect(expected).toBeRandomId()
    interface Matchers<R> {
      toBeRandomId(): object;
    }
    // required for { id: expect.toBeRandomId() }
    interface Expect {
      toBeRandomId(): object;
    }
  }
}

describe("config", () => {
  // you can check for the property
  it("will contain a random id", () => {
    expect(config.id).toBeRandomId();
  });

  // or the complete object
  it("will contain a random id and the current date", () => {
    expect(config).toEqual({
      id: expect.toBeRandomId(),
      started: expect.any(Date)
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

> **自我提醒:**你不可能为所有可能发生的事情做好计划。如果事情没有像预期的那样运行，试着不要像我一样在它上面浪费太多时间。

### 第四个任务

最后一个，我想在完成更复杂的任务后稍微休息一下，事后看来，这出乎意料地顺利。我的与会者应该使用一个发出一个`Promise`的函数，它在随机延迟后自动解决，并以不同的方式测试它。

Jest 将接受`Promise`作为测试函数的返回值，并将异步处理测试，因此解决方案很容易找到:

```
describe("runWhenever", () => {
  it("will run after a random time", async () => await resolveWhenever());
}); 
```

Enter fullscreen mode Exit fullscreen mode

该任务的第二部分旨在通过模仿我们刚刚测试的功能来展示 Jest 的模仿功能，以便同步运行测试，这是 Jest 在易用性方面的另一个亮点:

```
// tester.ts
import { resolveWhenever } from "./testable";

export const useResoveWhenever = () =>
  resolveWhenever().then(() => console.log("now"));

// tester.test.ts
jest.mock("./testable", () => ({
  resolveWhenever: () => ({ then: cb => cb() })
}));

import { useResoveWhenever } from "./tester";

describe("tester", () => {
  it("logs after resolve", () => {
    const logSpy = jest.spyOn(console, "log").mockImplementation();
    useResoveWhenever();
    expect(logSpy).toHaveBeenCalled();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，我们已经超时了，所以我的与会者中只有较快的一半完成了这项任务。我最初试着在房间里四处走动，帮助我发现与会者很慢的地方。那些帮助的尝试被帮助更快的参与者解决更大问题的必要性所阻止。

> **自我提醒:**不要试图跟踪所有的与会者，即使只有极少数人。这会让他们紧张，如果他们人多了，你可能也帮不上什么忙。

下一次，我将尝试给我的与会者几分钟时间自己解决任务，然后在每个人面前创建解决方案，同时为那些尚未完成的人解释它。

## 结论

通过阅读这篇文章，你可能会觉得我犯了所有可能的错误。至少我准备了脚手架，在任务中投入了一些想法，并事先解决了它们，以确保我在我的与会者之前发现问题。其余的，正如我当老师的妻子所说的，是实习老师的典型错误，只要多一点经验就很容易避免。

我希望你觉得这是有帮助的，或者至少是有趣的。在我的下一篇文章中再见。