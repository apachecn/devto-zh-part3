# 我为什么喜欢 useReducer

> 原文：<https://dev.to/hswolff/why-i-love-usereducer-4mnk>

直到最近我才意识到我有多爱这个 [React Hook useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer) 。这是一种高级挂钩，虽然我阅读了关于它的文档，并且已经有了大量使用 [Redux](https://redux.js.org/) 的经验，但是我花了一点时间才完全理解`useReducer`可以让你的组件变得多么强大。

# 我为什么爱 useReducer？

简单的答案是，它可以让你将*什么*和*如何*分开。进一步说，可能是*用户想要做的是`login`。*

用`useState`当一个用户想要`login`的时候，我创建一个函数来处理大量的*如何*。*当用户想要`login`时，我的组件应该如何表现:*

*   将`loading`设置为真
*   清除旧的`error`状态
*   禁用按钮。

有了`useReducer`，我的组件所要做的就是考虑*用户想要什么*。那就是:

*   `dispatch('login')`

之后所有的*是如何在`loginReducer`函数中被处理的。*

 *此外，任何未来*如何*的问题成为完全集中在一个`loginReducer`功能。我的组件可以一直想着*什么*。

这是一个微妙的区别，但非常重要。

为了进一步说明这一点[，你可以在这里查看完整的源代码](https://github.com/hswolff/youtube/tree/master/videos/why-i-love-usereducer)或者查看这些内嵌的例子。

我将忽略显示用户界面，如果你想看到你可以检查回购。现在，我只想关注我们存储和更新的数据。

# 使用使用状态

这里我调用了 5 次 useState 来管理所有不同的状态转换。

在我的`onSubmit`调用中，我必须小心地编排所有我想要的状态变化。

它们与 onSubmit 处理程序紧密耦合，很难提取。

```
function LoginUseState() {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [isLoading, showLoader] = useState(false);
  const [error, setError] = useState('');
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  const onSubmit = async e => {
    e.preventDefault();

    setError('');
    showLoader(true);

    try {
      await login({ username, password });
      setIsLoggedIn(true);
    } catch (error) {
      setError('Incorrect username or password!');
      showLoader(false);
      setUsername('');
      setPassword('');
    }
  };

  return; // remaining UI code here
} 
```

# 使用 useReducer

虽然总体上可能会更长，但我认为阅读和跟踪正在发生的事情要容易得多。

如果你直接跳到`onSubmit`功能，我现在可以清楚地显示用户的意图。只有 3 种行为可能发生，“登录”，“成功”和“错误”。这意味着什么与我的组件无关，都在`loginReducer`中处理。

更好的是，对我来说，对状态更改进行大范围的更改变得更加容易，因为所有的状态更改都位于中央。

更令人兴奋的是，默认情况下，所有状态更改都变得易于共享。

如果我想在组件的其他地方显示我的错误状态，我可以很容易地重用同一个`dispatch({ type: 'error' })`,这样就可以了。

```
function LoginUseReducer() {
  const [state, dispatch] = useReducer(loginReducer, initialState);
  const { username, password, isLoading, error, isLoggedIn } = state;

  const onSubmit = async e => {
    e.preventDefault();

    dispatch({ type: 'login' });

    try {
      await login({ username, password });
      dispatch({ type: 'success' });
    } catch (error) {
      dispatch({ type: 'error' });
    }
  };

  return; // UI here
}

function loginReducer(state, action) {
  switch (action.type) {
    case 'field': {
      return {
        ...state,
        [action.fieldName]: action.payload,
      };
    }
    case 'login': {
      return {
        ...state,
        error: '',
        isLoading: true,
      };
    }
    case 'success': {
      return {
        ...state,
        isLoggedIn: true,
        isLoading: false,
      };
    }
    case 'error': {
      return {
        ...state,
        error: 'Incorrect username or password!',
        isLoggedIn: false,
        isLoading: false,
        username: '',
        password: '',
      };
    }
    case 'logOut': {
      return {
        ...state,
        isLoggedIn: false,
      };
    }
    default:
      return state;
  }
}

const initialState = {
  username: '',
  password: '',
  isLoading: false,
  error: '',
  isLoggedIn: false,
}; 
```

# 像用户一样思考

让你以用户与组件交互的方式编写代码。

鼓励您在*中思考什么*并将所有*如何*的问题集中在减速器内部。

我太激动了`useReducer`现在是内置反应。这是我喜欢它的另一个原因。

* * *

如果你喜欢这篇文章，你可以在我的博客上找到更多类似的文章[！](https://hswolff.com/)

如果你喜欢看我的演讲，你可以去我的 YouTube 频道查看教程视频！*