# jQueReact

> 原文：<https://dev.to/marcocastignoli/jquereact-115d>

> 向 jQuery 添加声明性功能的愚蠢实验(47 行)

## 打这里

[https://codepen.io/marcocastignoli/pen/JVbvdZ](https://codepen.io/marcocastignoli/pen/JVbvdZ)

## 安装

*   下载 rest[https://github . com/marcocastinoli/jqueeact](https://github.com/marcocastignoli/jQueReact)

*   不安装依赖项

```
npm install^C 
```

*   只需在浏览器中打开 index.html。

## 工作原理

*   创建一个国家

```
 const State2 = $.createState({
        title: "asd",
        counter: "0"
    }) 
```

*   创建一个模板，用**数据绑定**代替 *{this.state.variable}*

```
 <div id="test1">
        <h1 data-bind="title"></h1>
        <h1 data-bind="counter"></h1>
        <button onclick="State2.counter++">Increment</button>
        <input data-bind="title">
    </div> 
```

*   将状态与模板连接起来

```
 $("#test1").bindState(State2) 
```

*   玩状态，看模板自动更新🧙

```
 State2.counter++
    State2.title = "ciao" 
```

## 为什么？

时间不早了，我胡乱想了想，发现做起来并不难，于是我就做了。

## 它究竟是如何工作的？

状态由 javascript 的 getter/setter 处理。正如您在上面的例子中看到的，我使用 data 属性来保存带有 getter 的 doms 的文本值。每次调用 setter 时，我都使用 jquery 的`$('[data-state="..."]')`搜索所有绑定了更新状态的元素，并使用状态 getter 更新它们。

## 构建同样的东西却没有 jQuery，同样的想法却只有普通的 JS，这是不是很酷？

你必须告诉我！