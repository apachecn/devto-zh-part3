# slim.js 中的自定义指令

> 原文：<https://dev.to/eavichay/custom-directives-in-slim-js-kc6>

我将演示如何使用 slim.js 中的自定义指令来应用视觉效果(例如 typewriter)

对于那些不熟悉 slim.js 的人来说——它是一个 web 组件创作库，具有零依赖性、快速、轻量级(3K ),使开发人员能够用声明性方法创建自定义元素(类似于 Angular 处理模板的方式)。

效果演示[这里](https://codepen.io/eavichay/pen/MdWROq)

首先，让我们用一个基本模板定义一些组件:

```
Slim.tag('my-tag',
`<p effect:typewriter>This is a sample text</p>`
class extends Slim {
}); 
```

如果您已经注意到了`effect:typewriter`属性，它仍然没有被定义为自定义指令，并且对页面没有任何影响。
具有名称空间的属性是有效的 html 标记，可以利用它们来避免与本地未来属性的冲突。

现在让我们定义效果指令

```
Slim.customDirective(
    (attribute) => attribute.localName === 'effect:typewriter',
    (source, target, attribute, match) => {
        // capture initial text
        const text = target.innerText;
        // parse delay from attribute or default to 50ms
        let delay = parseInt(attribute.nodeValue || '50');
        let i = 0;
        const interval = setInterval(() => {
          target.innerText = text.slice(0, i++); // set inner text
          if (i > text.length) {
            clearInterval(interval);
          }
        }, delay);
    }
); 
```

在 HTML 标记中，我们现在可以添加

```
<my-tag></my-tag> 
```

还有维奥拉，打字机效果随处可见。

关于 slim.js 的更多信息，你可以在这里看到在线文档