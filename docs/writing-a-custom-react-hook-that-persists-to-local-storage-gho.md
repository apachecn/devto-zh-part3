# 编写一个保存到本地存储的自定义 react 钩子

> 原文：<https://dev.to/mcrowder65/writing-a-custom-react-hook-that-persists-to-local-storage-gho>

所以，你正在写一些 react 钩子，你对自己说，“我希望这个本地状态能坚持刷新”。

让我们编写一个持续到 localStorage 的自定义钩子！

## 设置

```
$ create-react-app local-storage-hook
$ cd local-storage-hook
$ yarn eject # accept all of the prompts
$ yarn add -D jest-localstorage-mock react-testing-library jest-dom
$ touch src/use-local-storage-set-state.js && touch src/use-local-storage-set-state.test.js && touch src/setupTests.js 
```

然后，打开 package.json，编辑 jest 配置，

将“jest-localstorage-mock”添加到 setupFiles 部分。

所以现在看起来是这样的:

```
"setupFiles": [
   "react-app-polyfill/jsdom",
   "jest-localstorage-mock"
] 
```

然后，将以下属性添加到 jest 配置中，

```
"setupTestFrameworkScriptFile": "<rootDir>/src/setupTests.js" 
```

进行`src/setupTests.js`如下操作:

```
import "react-testing-library/cleanup-after-each";
import "jest-dom/extend-expect"; 
```

(在继续之前，如果您在运行`yarn test`时遇到奇怪的错误，那么请执行`rm -rf node_modules && yarn`，然后这将解决这些问题)。

好吧！让我们写一些测试！

```
// use-local-storage-set-state.test.js
import React from "react";
import { useLocalStorageSetState } from "./use-local-storage-set-state";

test("throws when name is not provided", () => {
  expect(() => useLocalStorageSetState(0)).toThrow();
}); 
```

当运行`yarn test`时，这就失败了，所以让我们实现源代码:

```
// use-local-storage-set-state.js
export const useLocalStorageSetState = (initialValue, name) => {
  if (!name) {
    throw new Error("Name must be provided to persist to localStorage");
  }
}; 
```

现在运行`yarn test`的时候，这个测试通过了！

不幸的是，这并没有多大作用。让我们添加额外的测试来展示我们的目标！

```
// use-local-storage-set-state.test.js
import React from "react";
import { render, fireEvent } from "react-testing-library";
import { useLocalStorageSetState } from "./use-local-storage-set-state";

test("throws when name is not provided", () => {
  expect(() => useLocalStorageSetState(0)).toThrow();
});

test("persists on component unmounts and rerenders", () => {
  function Comp() {
    const [value, setValue] = useLocalStorageSetState(0, "value");

    return (
      <div>
        {value}
        <button onClick={() => setValue(value + 1)}>Add value</button>
      </div>
    );
  }
  const { getByText, rerender, unmount } = render(<Comp />);
  expect(getByText(/0/i)).toBeInTheDocument();
  fireEvent.click(getByText(/add value/i));
  expect(getByText(/1/i)).toBeInTheDocument();
}); 
```

现在让我们添加源代码:

```
// use-local-storage-set-state.js
import React from "react";

export const useLocalStorageSetState = (initialValue, name) => {
  if (!name) {
    throw new Error("Name must be provided to persist to localStorage");
  }

  const [value, setValue] = React.useState(initialValue);

  return [value, setValue];
}; 
```

现在，当运行`yarn test`时，测试通过了！

让我们添加更多的测试，以显示我们想要更多的功能，添加以下内容:

```
unmount();
rerender(<Comp />);
expect(getByText(/1/i)).toBeInTheDocument(); 
```

我们又要失败了！让我们添加适当的源代码。

在编写一些随机代码之前，让我们考虑一下这个问题。
当值改变时，我们希望将该值保存到 localStorage 中。因此，值发生变化，函数需要启动..这正是 useEffect 的用途！
在我们继续之前，让我们安装商店 npm 模块，以实现高效的跨浏览器本地存储支持:
`yarn add store`

下面是带有 useEffect 的源代码:

```
// use-local-storage-set-state.js
import React from "react";
import store from "store";

export const useLocalStorageSetState = (initialValue, name) => {
  if (!name) {
    throw new Error("Name must be provided to persist to localStorage");
  }

  const [value, setValue] = React.useState(initialValue);

  React.useEffect(
    () => {
      store.set(name, value);
    },
    [value]
  );
  return [value, setValue];
}; 
```

仍然失败，我们就快成功了！我们需要从 localStorage 中读取初始值。

```
// use-local-storage-set-state.js
import React from "react";
import store from "store";

export const useLocalStorageSetState = (initialValue, name) => {
  if (!name) {
    throw new Error("Name must be provided to persist to localStorage");
  }
  const actualInitialValue =
    store.get(name) !== undefined ? store.get(name) : initialValue;
  const [value, setValue] = React.useState(actualInitialValue);

  React.useEffect(
    () => {
      store.set(name, value);
    },
    [value]
  );
  return [value, setValue];
}; 
```

而现在，`yarn test`正在过去！

现在，有几个额外的边缘情况，我们在这里错过了，让我在评论中知道，如果你想涵盖这些，但是，你应该能够自己实现这些！

这里有源代码:[https://github.com/mcrowder65/local-storage-hook](https://github.com/mcrowder65/local-storage-hook)