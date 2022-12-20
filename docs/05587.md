# 通过 Next.js 使用客户端 Javascript 库

> 原文：<https://dev.to/sinacodes/using-client-side-javascript-librarys-with-next-js-22gh>

因此，前几天我试图将 [wow.js](https://wowjs.uk/) 集成到一个 [next.js](https://nextjs.org/) 应用程序中，以获得流畅的滚动动画。事实证明:这并不像我想象的那么简单，因为 wow.js 是在客户端执行的(因为它需要 window 属性), Next.js 以服务器端呈现(SSR) React 代码而闻名。或者准确的说，如果你知道它是如何工作的，就没那么难了。

也就是说，不是通常的顶层导入，而是需要 componentDidMount 函数内部的库，该函数只在客户端执行。

```
componentDidMount() {

  if(typeof window !== 'undefined') {

    window.WOW = require('wowjs');

  }

  new WOW.WOW().init();

} 
```

以这种方式导入后，您可以通过类名以通常的方式使用 wowJS:

```
<div className="wow fadeInUp" data-wow-offset="20">

  <h1>My awesome animated Component</h1>

</div> 
```

注意:wow.js 在每个视图中只需要渲染一次。例如，如果您有几个想要制作动画的组件，您可以在包装器组件中需要 wow.js。这样的话，它只装载一次。

考虑一下也很好:如果有适合您的用例，请考虑使用 react-alternative，而不是这种变通方法。