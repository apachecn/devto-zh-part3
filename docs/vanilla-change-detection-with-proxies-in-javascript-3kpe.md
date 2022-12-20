# 用 Javascript 中的代理检测普通变化

> 原文：<https://dev.to/mandrewdarts/vanilla-change-detection-with-proxies-in-javascript-3kpe>

在一个只有少量交互的小项目中，你可能想保持一点状态。如果你使用过 Redux、MobX、NGRX 或任何其他状态管理库，你的第一反应可能是使用你熟悉的库。让我们便宜一点，看看这个平台，看看我们可以用什么工具在没有库的情况下完成这项工作。

## 代理人

代理在 Javascript 中是一个新概念。代理只是提供了一种增强对象的方法，例如监听器、动态属性和验证。快速举例。

```
 const person = { firstName: "Luke", lastName: "Skywalker" };

const personHandler = {
  set: function(obj, prop, value) {
   console.log(`${prop} changed from ${obj[prop]} to ${value}`);
   obj[prop] = value;
   return true;
  }
};

const personProxy = new Proxy(person, personHandler);

personProxy.firstName = "Not Luke";
// console.logs "firstName changed from Luke to Not Luke"; 
```

Enter fullscreen mode Exit fullscreen mode

只用了 10 行代码，我们就实现了一个简单的代理，它记录了对象的变化。

## 一小步

让我们更进一步。假设我们想要跟上笔记列表。这些笔记将需要在页面上呈现。我们需要一种简单的方法来更新 notes 数组，并且不加思索地将这些更改反映到页面上。如果我们想一想，数据是真相的来源，渲染是副作用。也就是说，让我们创建一个代理工厂，它将接收一个要观察的对象和一组在对象改变时运行的函数。

```
export const observer = (obj, ...listeners) => {
  return new Proxy(obj, {
    set: function(obj, prop, value) {
      listeners.forEach(fn => fn({...obj, [prop]: value}, obj));
      obj[prop] = value;
      return true;
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这是什么☝️？这是一个工厂函数，当它改变时，它将创建代理并运行一些函数。我如何使用这个？

```
const notesObserver = observer(
 { notes: [{ text: "Do something." }] }, 
 renderNotes,
 logChanges
);

function renderNotes(notes) {
  // render notes here...
}

function logChanges(notes, oldNotes) {
  console.log(notes, oldNotes);
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个工作示例。随着网络平台的发展，事情只会变得更加令人兴奋！想想那些可以用代理做的很酷的事情。很想听听想法和主意！

下面是一个 notes 应用程序使用代理的例子。

[https://stackblitz.com/edit/render-proxy?view=preview](https://stackblitz.com/edit/render-proxy?view=preview)