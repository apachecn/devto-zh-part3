# 普通 JS (POC)中的单向数据绑定

> 原文：<https://dev.to/phoinixi/one-way-data-binding-in-vanilla-js-poc-4dj7>

**代码和演示**
[https://stackblitz.com/edit/one-way-data-binding?](https://stackblitz.com/edit/one-way-data-binding?)

### 让我们把它分解成小块

我假设你已经熟悉了数据绑定。

大多数现代前端框架都提供现成的数据绑定，随时可以使用，但是它的内部有什么呢？

我试着实现了一个单向数据绑定的基本版本，非常有趣:)

那么，我们需要什么来进行单向数据绑定呢？

*   一个**视图**，在我们的例子 HTML 中。
*   一个**状态**，用 JavaScript 保存在内存中。

关键特征是:

> 每次状态改变时，视图都需要更新

所以让我们假设我们有一个 HTML **视图** :

```
 <div data-binding="quote"></div> 
```

Enter fullscreen mode Exit fullscreen mode

和一个**状态** :

```
 const state = {
      quote: 'Either you run the day, or the day runs you.'
    }; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以很容易地在第一时间设置视图:

```
 document.querySelector('[data-binding="quote"]').innerHTML = state.quote 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一些魔法，这样当我们更新状态时:

```
 state.quote = 'We become what we think about' 
```

Enter fullscreen mode Exit fullscreen mode

景色会神奇地改变。为了实现这一点，我们可以修改`state`对象的`set`属性的默认行为，这样除了更新模型，它还会更新我们的**视图**。

在 JavaScript 中做到这一点的一种方法是使用[代理对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) :

```
 const createState = (state) => {
      return new Proxy(state, {
        set(target, property, value) {
          target[property] = value; // default set behaviour
          render(); // updates the view everytime the state changes
          return true;
    }
      });
    };

    const state = createState({
      quote: 'Either you run the day, or the day runs you.' // creates initial state
    }); 
```

Enter fullscreen mode Exit fullscreen mode

有了代理的力量，每次我们更新我们的`state`，函数`render`就会被调用。
`render`的一个可能实现可以是:

```
 const render = () => {
     document.querySelector('[data-binding="quote"]').innerHTML = state.quote
    }; 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们用
来修改状态

```
 state.quote = 'We become what we think about.' 
```

Enter fullscreen mode Exit fullscreen mode

将调用`render`函数，视图将更新！*魔法*！👨‍💻