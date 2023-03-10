# 用 breakOn 在浏览器中调试 JS

> 原文：<https://dev.to/unchar1/debugging-js-in-the-browser-with-breakon-4c8c>

TLDR:参见[突破](https://github.com/paulirish/break-on-access)

### 问题

上周，我遇到了一个奇怪的 bug，其中一个对象的属性在网络调用之间随机变化。这个特定的对象在多个地方被引用，我很难弄清楚到底哪里发生了变化。在用`debugger`语句和 chrome 的 devTools 调试了一段时间后，我意识到我基本上需要的是查看对象修改的日志。

### 选项 1:对象。观察

JS 有一个有用的实用函数`Object.observe(obj, handler)`方法，它跟踪对象上的所有动作。所以在这种情况下，我可以只做一个，

```
 Object.observe(obj, ...console.log); 
```

它会打印出`obj`道具的所有信息

不幸的是，这种方法已经被弃用，而且在 chrome 或我测试过的其他现代浏览器中也不能(轻易)启用。

### 选项 2: ES6 代理

ES6 中的新`Proxy`被推荐作为旧`Object.observe`的替代品，尽管它们在功能上并不等同。使用代理类我们可以创建一个这样的观察者，

```
const observe = objToObserve => {
  const handler = {
    get: (obj, prop) => {
      console.log(`GET ${prop} = ${obj[prop]}`);
      return obj[prop];
    },
    set: (obj, prop, value) => {
      console.log(`SET ${prop} = ${value}`);
      obj[prop] = value;
      return true;
    }
  };
  return new Proxy(objToObserve, handler);
}; 
```

现在我们可以做

```
let me = { name: "John" };
me = observe(me);

me.name = "Jake";           // Console output: SET name = Jake
me.age = 12;                // Console output: SET age = 12

if (me.age > 10) {          // Console output: GET age = 12
  console.log("You're old enough to ride a bike!");
} 
```

这里的问题是,`observe`创建了一个新对象，所以我们需要修改原始对象引用来跟踪变化。这不是什么大问题，但是必须这样做意味着我们需要知道引用在哪里被初始化，并适当地修改它。如果你使用`const`来初始化你的变量，这也会导致问题。因此，尽管它可以工作，我仍然不满意这个解决方案。

### 选项 3: breakOn

进入[中断](https://github.com/paulirish/break-on-access)。
我通过 stackoverflow 看到了 breakOn，这正是我所需要的。breakOn 是一个单文件库，只有一个方法`breakOn`。`breakOn`可以在属性改变时设置断点(或者被访问！).
这是你使用它的方法，

```
const me = {name: 'John'}; // We can use const

breakOn(me, 'name');

person.me = "Jake"; // Breakpoint is triggered 
```

现在你可以使用[在 chrome](https://developers.google.com/web/tools/chrome-devtools/snippets) 上添加这个代码片段，你可以在任何网站上从 chrome 的 devTool 控制台使用它！

不幸的是，我需要的是日志，而不是真正的断点，但好消息是，该函数可以很容易地修改为控制台日志(或任何其他操作)，而不是中断，我最终这样做了。

breakOn 正是我一直在寻找的，虽然代码现在有点旧(2013 年左右)，而且如果现在编写它，有很多事情不会以它的方式完成，它很好地服务于我的用例，希望它对你也有用！