# 我在为 JS 和 HTML 页面编写测试后学到的东西

> 原文：<https://dev.to/snowleo208/things-i-learned-after-writing-tests-for-js-and-html-page-4lja>

在这几周里，我一直想学习用 Jest、Mocha 或其他东西写测试。不幸的是，目前我还没有任何想要测试的 React 应用程序，但是我有机会弄清楚如何用纯 JavaScript 测试 HTML 页面。

我发现大多数教程都使用 Jest 和 React 或其他 JavaScript 框架。真的可以用 Jest 测试 HTML 页面吗？是啊！

# 如何开始

[点击这里](https://snowleo208.github.io/test-js-example/)查看我的示例 HTML 页面的演示。只是一个简单的获取 JSON 的页面，显示一个基于它的列表和一个显示/隐藏翻译的按钮。

在你的根文件夹中，创建一个像这样的`package.json`，并在控制台中运行`npm install`。

```
{  "scripts":  {  "test":  "jest --watch",  "coverage":  "jest --coverage"  },  "devDependencies":  {  "jest":  "^23.6.0"  }  } 
```

完成后，您可以开始测试您的应用程序！创建一个文件`<YOUR-FILENAME>.spec.js`并开始测试，如:

```
const fs = require('fs');
const path = require('path');
const html = fs.readFileSync(path.resolve(__dirname, '../index.html'), 'utf8');

jest
    .dontMock('fs');

describe('button', function () {
    beforeEach(() => {
        document.documentElement.innerHTML = html.toString();
    });

    afterEach(() => {
        // restore the original func after test
        jest.resetModules();
    });

    it('button exists', function () {
        expect(document.getElementById('disable')).toBeTruthy();
    });
}); 
```

保存后运行`npm test`！

[在这里查看我的 GitHub 代码](https://github.com/snowleo208/test-js-example)

# 我学到了什么

### 1。测试 HTML 和 React 页面的区别

在 React 中，您可以使用酶来`shallow`组件并获取测试状态。但是在 HTML 和 JavaScript 页面中，唯一可以测试的是类、内容和函数输出。当你习惯于按状态测试时，测试 HTML 页面可能就不那么方便了。

由于 pure JS 没有导出任何东西供 Jest 测试，您还需要在末尾添加这个:

```
if (typeof exports !== 'undefined') {
    module.exports = {
        getItem,
        setItems,
        triggerItem
    };
} 
```

然后 Jest 可以导入/导出函数进行测试。

对于 HTML，不能像 React 组件一样直接导入。您需要在测试之前添加这个代码片段，以导入整个 HTML:

```
const fs = require('fs');
const path = require('path');
const html = fs.readFileSync(path.resolve(__dirname, '../index.html'), 'utf8');

jest
    .dontMock('fs'); 
```

或者在测试中编写内联 HTML。例如:

```
const html = document.createElement('div');
html.innerHTML = `<div class="lang-grid" id="language">This is a sample</div>`;
document.body.appendChild(div); 
```

### 2。测试异步代码的特殊方法

测试基本的东西还是很容易的，比如用 Jest 检查菜单按钮是否会出现在移动视图中。喜欢:

```
 it('menu button exists', function () {
        expect(document.getElementById('menu-btn')).toBeTruthy();
    }); 
```

但是对于异步代码，比如 Promise，您需要使用不同的方法。

最重要的是每次测试都要加`done()`。

```
 it('get same items from json', function (done) {
        fetch.mockResponse(JSON.stringify(json))
        const {getItem} = require('../scripts/main.js');

        getItem().then(res => {
            expect(res).toEqual([{
                "phase": "Entschuldigung!",
                "trans": "Excuse me. [as in may I have your attention]."
            },
            {
                "phase": "Sprechen Sie Englisch?",
                "trans": "Do you speak English?"
            }])

            expect(res.length).toEqual(2);
            done();
        })
        .catch(err => console.log(err))
    }); 
```

就像 [Jest 文档](https://jestjs.io/docs/en/asynchronous)说的，在测试中加入`done()`很重要。否则，可能会产生错误的结果。

在您添加了`done()`之后，它将等待您的异步调用被解析并得到预期的结果。

* * *

### 3。使用 Jest 检查覆盖率

Jest 内置了覆盖函数，可以用`jest --coverage`调用。然后您可以在`coverage/lcov-report/index.html`中看到您的报告。这个图表非常有用，它可以告诉你哪些代码没有经过测试。

[![Coverage tool](img/3e5b0363db937eb271075c91c9fb5059.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xPW40IWO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xwt4bi0qkp4my37t0mxz.jpg) 
*(为什么不是 100%在分行？因为我最后跳过了导出模块语句的测试。)*

我以前没有使用过覆盖率工具，这就是为什么当我看到我的代码从红色变成绿色时，我很有动力！

* * *

# 测试好玩吗？

嗯，这可能并不有趣，但当我看到我的代码从红色变成绿色时，肯定是令人满意的。

你对我的流程有什么建议吗？或者对测试有什么想法？欢迎在这里给我留言:)