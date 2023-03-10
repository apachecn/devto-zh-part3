# 使用 useEffect 挂钩

> 原文：<https://dev.to/leighhalliday/using-the-useeffect-hook-3193>

这篇文章最初发表在 https://www.leighhalliday.com/use-effect-hook 的我的博客上

[useEffect](https://reactjs.org/docs/hooks-effect.html) 意在处理任何种类的“副作用”(对某个外部系统进行更改、登录控制台、发出 HTTP 请求等...)由组件数据的更改或对组件呈现的反应触发。它代替了`componentDidMount`、`componentDidUnmount`和`componentDidReceiveProps`，或者一些在你的状态改变时运行的代码。掌握其使用的细微差别可能很有挑战性，但通过了解它何时运行以及如何控制它，它会变得更容易理解。

在这篇文章中，我们将看看如何让一个效果在每次渲染后运行，只是一次，或者当一个特定的数据发生变化。我们还将了解效果本身的区别，以及如何在效果之后进行清理。

[https://www.youtube.com/embed/Y0zkFkbtoeA](https://www.youtube.com/embed/Y0zkFkbtoeA)

本文引用的代码可以在 https://github.com/leighhalliday/use-effect-example[找到](https://github.com/leighhalliday/use-effect-example)

## 在每次渲染时运行效果

对于最小的例子，我们有一个典型的例子,`useEffect`在每次渲染后将`count`的值记录到控制台。需要注意的是:`useEffect`是在渲染之后运行**的。始终认为:先渲染，后效果。** 

```
import React, { useState, useEffect } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log(`The count is ${count}`);
  });

  return (
    <div>
      <p>Count is {count}</p>
      <button
        onClick={() => {
          setCount(count + 1);
        }}
      >
        increase
      </button>
    </div>
  );
} 
```

为什么这个例子在每个渲染完**之后运行？原因是没有参数作为第二个参数传递给`useEffect`。React 使用第二个参数来确定是否需要执行传递给 useEffect 的函数...通过不传递任何内容，React 每次都会运行效果。**

这可能会导致性能问题，或者有点过火，所以让我们看看如何在运行效果函数时添加一些额外的控制。

## 只运行一次效果

假设我们只希望效果运行一次...把这个当做`componentDidMount`的替代。为此，将一个`[]`作为第二个参数传递给 useEffect:

```
import React, { useEffect } from "react";

export default function Mounted() {
  useEffect(() => {
    console.log("mounted");
  }, []);

  return <div>This component has been mounted.</div>; } 
```

## 运行数据变化时的效果

如果您真正想要的是仅在特定值改变时运行效果...比方说，要更新一些本地存储或触发一个 HTTP 请求，您需要将那些您正在观察变化的值作为第二个参数传递。这个例子将在每次更新(由输入的 onChange 触发)后将用户名写入本地存储。

```
import React, { useState, useEffect } from "react";

export default function Listen() {
  const [name, setName] = useState("");

  useEffect(
    () => {
      localStorage.setItem("name", name);
    },
    [name]
  );

  return (
    <div>
      <input
        type="text"
        onChange={e => {
          setName(e.target.value);
        }}
        value={name}
      />
    </div>
  );
} 
```

## 清除你的影响

有时候你需要撤销你做过的事情...当组件要被卸载时，自己进行清理。为此，您可以从传递给`useEffect`的函数中返回一个函数...这听起来有点拗口，但是让我们来看一个真实的例子，将`componentDidMount`和`componentDidUnmount`组合成一个效果。

```
import React, { useEffect } from "react";

export default function Listen() {
  useEffect(() => {
    const listener = () => {
      console.log("I have been resized");
    };
    window.addEventListener("resize", listener);

    return () => {
      window.removeEventListener("resize", listener);
    };
  }, []);

  return <div>resize me</div>;
} 
```

## 避免在未安装的组件上设置状态

因为效果是在组件完成渲染之后运行的，并且因为它们通常包含异步代码，所以当异步代码解析时，组件可能已经不存在了！当调用`setData`函数来更新状态时，您会收到一个错误，提示您不能在一个卸载的组件上更新状态。

我们可以解决上述问题的方法是使用一个局部变量，并利用从我们的效果函数返回的“清理”函数。通过将它作为`true`开始，我们可以在效果被清除时将其切换到`false`，并使用该变量来确定我们是否仍然想要调用`setData`函数。

```
import React, { useState, useEffect } from "react";
import Axios from "axios";

export default function Fetcher({ url }) {
  const [data, setData] = useState(null);

  useEffect(
    () => {
      // Start it off by assuming the component is still mounted
      let mounted = true;

      const loadData = async () => {
        const response = await Axios.get(url);
        // We have a response, but let's first check if component is still mounted
        if (mounted) {
          setData(response.data);
        }
      };
      loadData();

      return () => {
        // When cleanup is called, toggle the mounted variable to false
        mounted = false;
      };
    },
    [url]
  );

  if (!data) {
    return <div>Loading data from {url}</div>;
  }

  return <div>{JSON.stringify(data)}</div>;
} 
```

## 组件卸载时取消 Axios 调用

有了上面的例子，你可能会问自己...如果我们知道一个事实，我们甚至不需要它，为什么还要费心去等待一个回应呢？原来 Axios 有一种[方式来取消请求](https://github.com/axios/axios#cancellation)。我们可以使用与上面相同的方法，使用一个局部变量和一个效果清理函数，但是这次局部变量将是一个 Axios 取消源/令牌，允许我们调用`source.cancel()`来停止 Axios。

请记住，这将引发一个我们应该捕捉的异常。Axios 为我们提供了一种方法，使用`Axios.isCancel(error)`来确定我们捕获的内容是因为我们自己的取消还是其他一些意外错误。

```
import React, { useState, useEffect } from "react";
import Axios from "axios";

export default function Fetcher({ url }) {
  const [data, setData] = useState(null);

  useEffect(
    () => {
      // Set up a cancellation source
      let source = Axios.CancelToken.source();

      const loadData = async () => {
        try {
          const response = await Axios.get(url, {
            // Assign the source.token to this request
            cancelToken: source.token
          });
          setData(response.data);
        } catch (error) {
          // Is this error because we cancelled it ourselves?
          if (Axios.isCancel(error)) {
            console.log(`call for ${url} was cancelled`);
          } else {
            throw error;
          }
        }
      };
      loadData();

      return () => {
        // Let's cancel the request on effect cleanup
        source.cancel();
      };
    },
    [url]
  );

  if (!data) {
    return <div>Loading data from {url}</div>;
  }

  return <div>{JSON.stringify(data)}</div>;
} 
```

## 结论

我希望这篇文章能够阐明一些利用效果的不同方法，是什么导致它们被执行，以及如何处理一个效果可能在组件已经被卸载后执行代码*的问题。有了`useEffect`,我们能够将设置和清理结合在一起，在基于类的组件中，你需要在`componentDidMount`和`componentDidUnmount`生命周期事件中分割功能。*