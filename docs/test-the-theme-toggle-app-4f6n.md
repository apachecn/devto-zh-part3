# 测试主题切换应用程序

> 原文：<https://dev.to/email2vimalraj/test-the-theme-toggle-app-4f6n>

在我之前的[文章](https://dev.to/email2vimalraj/toggle-theme-using-react-hooks-2ibe)中，我们已经构建了一个具有主题切换功能的应用。在本文中，让我们测试一下这个特性。

我将使用 [react-testing-library](https://github.com/testing-library/react-testing-library) 和 [jest-dom](https://github.com/testing-library/jest-dom) 作为`react-testing-library`的伙伴库，利用 jest 的定制 dom 元素匹配器。

让我们将这两个库作为开发依赖项来安装。

```
npm install -D react-testing-library jest-dom 
```

自从我们使用了`create-react-app`，在他们的文档站点[这里](https://facebook.github.io/create-react-app/docs/running-tests)有一篇很好的文章开始运行测试。我强烈建议我的读者通读一遍文档。

正如这里提到的，让我们在`src`目录下创建一个`setupTests.js`。

```
import "react-testing-library/cleanup-after-each";
import "jest-dom/extend-expect"; 
```

*   第一次导入确保在每次测试运行后移除由`react-testing-library`渲染的组件。
*   第二个导入让我们使用`jest-dom`的自定义匹配器，我们主要用它来断言。

在编写测试之前，让我们在`header`中添加一个属性`data-testid`，在`App.js`组件中添加`button`组件，如下所示:

```
<header className="App-header" style={{ backgroundColor: theme.backgroundColor,
color: theme.color }} data-testid="header">
  <button type="button"
    onClick={toggle} style={{ backgroundColor: theme.backgroundColor, color: theme.color, outline: 'none' }} data-testid="toggle-theme-btn" > 
... 
```

我们将使用`data-testid`属性来查找测试中的元素。

现在让我们创建一个新的测试。去删除`App.test.js`上的所有内容，进行以下测试:

```
import React from "react";
import { render } from "react-testing-library";
import App from "./App";

test("renders with light mode default", () => {
  const { getByTestId } = render(<App />);
  expect(getByTestId("toggle-theme-btn")).toBeInTheDocument();
  expect(getByTestId("header")).toHaveStyle("background-color: white");
}); 
```

在我们的第一行，我们渲染我们的`App`组件，然后我们断言是否有一个`Toggle theme`按钮，默认的`background color`是使用各自的`data-testid`的`white`

让我们运行测试，看看运行是否成功。

```
npm run test 
```

万岁。！！测试运行成功！

现在，让我们添加另一个测试来验证单击切换按钮是否会改变背景颜色。

```
test("toggles the theme", () => {
  const { getByTestId } = render(<App />);
  const toggleBtn = getByTestId("toggle-theme-btn");
  fireEvent.click(toggleBtn);
  expect(getByTestId("header")).toHaveStyle("background-color: black");
  fireEvent.click(toggleBtn);
  expect(getByTestId("header")).toHaveStyle("background-color: white");
}); 
```

确保从`react-testing-library`中导入`fireEvent`。

让我们看看运行结果。新添加的测试失败了 [![😟](img/d41db192203e25b1f81485fe4ddd11ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2xnchDwe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f61f.png) 。为什么？

```
✕ toggles the theme (29ms)

  ● toggles the theme

    expect(element).toHaveStyle()

    - Expected

    - background-color: black;
    + background-color: white;

      13 | const toggleBtn = getByTestId('toggle-theme-btn')
      14 | fireEvent.click(toggleBtn)
    > 15 | expect(getByTestId('header')).toHaveStyle('background-color: black')
         | ^
      16 | fireEvent.click(toggleBtn)
      17 | expect(getByTestId('header')).toHaveStyle('background-color: white')
      18 | })

      at Object.toHaveStyle (src/App.test.js:15:33) 
```

执行堆栈跟踪显示`background-color`仍然是`white`。这意味着在触发一个`click`事件后，`localStorage`的`setItem`方法没有被正确触发。

为了实现这一点，我们应该模拟我们的`localStorage`，因为测试不能在真正的浏览器上执行，也没有`Storage`。现在打开你的`setupTests.js`,添加下面几行:

```
let store = {};

// Mock the `localStorage.getItem` method to return the value stored in the given key
jest.spyOn(Storage.prototype, "getItem").mockImplementation(key => {
  return store[key];
});

// Mock the `localStorage.setItem` method to insert a given value into the given key
jest.spyOn(Storage.prototype, "setItem").mockImplementation((key, value) => {
  return (store[key] = value + "");
});

// Mock the `localStorage.clear` method to clear the `store`
jest.spyOn(Storage.prototype, "clear").mockImplementation(() => {
  store = {};
}); 
```

现在让我们运行我们的测试，看看结果。还是因为同样的原因失败。让我们用`theme-context.js`中著名的`console.log`语句添加一些调试点。

我在我们的`ThemeProvider`函数中的`toggle`方法中添加了以下语句:

```
console.log(">>> clicked"); 
```

让我们看看运行结果，并注意该语句是否被打印出来。意外的是，不是！

原因是测试中的`App`组件没有用`ThemeProvider`包装。我们应该把它包起来。就这么办吧。

一旦您完成，您的测试必须通过，添加的`console.log`语句应该打印两次！！！

干得好。但是我们需要在每一个测试中用`ThemeProvider`包装`App`组件吗？我听到你说那很痛苦。但是我们也有解决方案。

所以我在[推特](https://twitter.com/email2vimalraj/status/1131496659685220352)上向[肯特](https://twitter.com/kentcdodds)提出了这个问题。我立即得到了回应。

> 没错。我建议包装，但移动也可以。在这里了解更多关于无痛苦包装的信息:[https://testing-library . com/docs/react-testing-library/setup # custom-render](https://testing-library.com/docs/react-testing-library/setup#custom-render)

因此，根据上述内容，让我们创建一个测试实用程序文件来定义我们自己的渲染方法。创建一个名为`src/test-util.js`的文件，并添加以下内容:

```
import React from "react";
import { render } from "react-testing-library";
import { ThemeProvider } from "./theme-context";

const AllMyProviders = ({ children }) => {
  return <ThemeProvider>{children}</ThemeProvider>;
};

const MyCustomRender = (component, options) =>
  render(component, { wrapper: AllMyProviders, ...options });

// re-export everything from the `react-testing-library`
export * from "react-testing-library";

// export our custom render method
export { MyCustomRender as render }; 
```

让我们进行测试，并做一些更改:

替换

```
import { render, fireEvent } from "react-testing-library"; 
```

随着

```
import { render, fireEvent } from "./test-utils"; 
```

然后，在所有测试中移除`App`组件的`ThemeProvider`包装。现在执行您的测试，看看。

测试应该运行良好。今天到此为止。如果你觉得这篇文章有用，请点击赞并分享。