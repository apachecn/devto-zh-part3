# Web 组件:了解属性和特性

> 原文：<https://dev.to/basal/web-components-understanding-attributes-and-properties-c6e>

使用组件库时，Javascript 属性和 HTML 属性混合在一起，很难理解它们到底是什么以及它们是如何工作的。这可能是以后的问题，所以这里有一个分解。

HTML 元素有属性。这些是可以传递到 HTML 标签中的字符串。

```
 <img src="https://picsum.photos/200/300" /> 
```

Enter fullscreen mode Exit fullscreen mode

这是因为在与 DOM 结合之前，HTML 文档只是一个字符串。

文档对象模型(DOM)代表相同的文档...DOM 是 web 页面的面向对象的表示，可以用 JavaScript 等脚本语言进行修改。- MDN

当你用 Javascript 请求一个标签时，它会从 DOM 返回一个`Element`对象；使用这个 Javascript 对象，您可以更改 HTML 元素的*属性*，但是您也可以设置对象本身的属性，这些属性在文档中没有可见的表示。

属性可以是复杂的数据，如数组、函数或其他对象；这在存储与节点相关的数据时特别有用。

在您的浏览器控制台中亲自尝试一下。

```
$0 //will return the selected element in chrome/firefox/safari
$0.coolBeans = ['baked', 'black', 'refried'];
$0.coolBeans; // return a real array, not a string ['baked', 'black', 'refried'] 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们对属性
做同样的事情

```
$0 //will return the selected element in chrome/firefox/safari
$0.setAttribute('coolBeans', ['baked', 'black', 'refried']);
$0.coolBeans; // return undefined
$0.getAttribute('coolBeans'); // return a string "baked,black,refried" 
```

Enter fullscreen mode Exit fullscreen mode

当您开始使用一个已经被定义为该标签属性的属性时，事情就变得有点复杂了。

```
const imgTag = document.createElement('img'); //non rendered img tag
imgTag.srcset = ['https://picsum.photos/320/320 320w',
                                 'https://picsum.photos/480/320 480w'
                                 'https://picsum.photos/640/320 640w']
// this is going to return a real array right?
// nope
imgTag.srcset; //"https://picsum.photos/320/320 320w,https://picsum.photos/480/320 480w,https://picsum.photos/640/320 640w" 
```

Enter fullscreen mode Exit fullscreen mode

这个图像元素的类有一个属性处理程序用于`srcset`它的 ***反映*** 它有属性并设置属性。

在很大程度上，这不是问题，但理解这种关系是有帮助的，因为它将帮助您构建更好的、更可重用的 Web 组件。

在本文中，我们将使用 Google 的 library Lit 元素。Lit 元素包括一个很好的基础结构，允许你使用模板文字为你的组件创建 HTML 结构。

下面是一个基本的例子。

```
class BeanList extends LitElement {
  constructor() {
    super();
  }
  render() {
    return html`
       <ul>
            <li>Baked<li>
            <li>Black</li>
          <li>Refried</li>
         </ul>
    `
  }
}
customElements.define('cool-beans', BeanList);
// You can now add <cool-beans></cool-beans> to your html 
```

Enter fullscreen mode Exit fullscreen mode

这将呈现一个漂亮的酷豆列表，但它是一个静态字符串，所以让我们将它们移动到一个数组中。

```
class BeanList extends LitElement {
  constructor() {
    super();
    this.list = [
            'Baked',
            'Black',
            'Refried'
        ]
  }
  render() {
    return html`
      <ul> ${this.list.map((beanName)=> { return html`<li>${beanName}</li>`})} </ul>
        `
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经设置了对象的`.list`，我们可以使用 Javascript 访问这些数据。

```
$0 //will return the selected element in chrome/firefox/safari
$0.list // its return a real array ['Baked','Black','Refried']
$0.list = ['Baked','Black','Refried','Pino'];
$0.list // ['Baked','Black','Refried','Pino']

const x = document.createElement('cool-beans');
x.list = ['Baked','Black','Refried','Pino'];
document.body.appendChild(x);
// This WILL render the <li>Pino</li> because the list was updated before
// it rendered the tag 
```

Enter fullscreen mode Exit fullscreen mode

但是你会注意到 HTML 没有更新。对象属性未连接到渲染周期，如果该属性被更改，它将不会重新渲染。所以让它听听我们的新列表吧。

```
class BeanList extends LitElement {
  constructor() {
    super();
    this.list = [
            'Baked',
            'Black',
            'Refried'
        ]
  }
    static get properties() {
    return {
      list
    };
  }
  render() {
    return html`
      <ul> ${this.list.map((beanName)=> { return html`<li>${beanName}</li>`})} </ul>
        `
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经添加了一个属性监听器，当这个属性被更新时，它将重新呈现。

```
$0 //will return the selected element in chrome/firefox/safari
$0.list // its return a real array ['Baked','Black','Refried']
$0.list = ['Baked','Black','Refried','Pino'];
$0.list // It will re-render and you still have
              // a real ['Baked','Black','Refried','Pino'] 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们之前的例子运行得很好。现在，拼图的最后一块是将属性连接到我们的属性。

```
class BeanList extends LitElement {
    constructor() {
    super();
    this.list = [
            'Baked',
            'Black',
            'Refried'
        ]
  }
  static get properties() {
   return {
      list: {
      reflect: true,
      type: {
        fromAttribute: x => x.split(','),
        toAttribute: x => x.join(),
      }
    },
   }
  }
  render() {
    return html`
      <ul> ${this.list.map((beanName)=> { return html`<li>${beanName}</li>`})} </ul>
        `
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了上面的例子，你现在可以两者兼得了！

```
$0 //will return the selected element in chrome/firefox/safari
$0.setAttribute('list', ['Baked','Black','Refried','Pino'];);
$0.list = ['Baked','Black','Refried','Pino']; 
```

Enter fullscreen mode Exit fullscreen mode

这两条语句都将更新组件的属性、特性和 HTML。

`fromAttribute`和`toAttribute`是非常强大的接口，但`lit-element`带有一些预制的转换器，如`Array`，完整列表可以在这里找到。

如果你想使用更复杂的结构，你不太可能把它们反映为属性，所以你不需要转换器。

```
class BeanList extends LitElement {
  static get properties() {
   return {
      list: { attribute: false },
   }
  }
  constructor() {
    super();
    this.list = [];
  }
  render() {
    return html`
      <ul> ${this.list.map((bean)=> { return html`<li><caption><img src="${bean.img}" />${bean.name}</caption></li>`})} </ul>
        `
  }
}
customElements.define('cool-beans', BeanList); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，没有在元素属性上创建属性，而是使用一系列框架(包括本地 HTML)来设置它们。有戏。

一个警告是，一些框架还没有将属性正确地传递给 Web 组件。兼容框架的列表可以在找到