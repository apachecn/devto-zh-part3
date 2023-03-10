# 带标记的模板文字:探索样式化组件对该语法的使用

> 原文：<https://dev.to/leighhalliday/tagged-template-literals-exploring-styled-components-use-of-this-syntax-2nj5>

我在许多流行的库中不断看到[标记的模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_templates)，但是我不明白它们是如何工作的。本文探索了它们是什么，如何在“野外”使用它们，然后我们将构建一个小版本的`css`函数，该函数根据传递给它的属性动态生成一些 CSS。

[https://www.youtube.com/embed/quTKcx_INfc](https://www.youtube.com/embed/quTKcx_INfc)

## 普通旧模板文字

我们以前见过模板文字，你有一个使用反勾号的字符串，允许你在字符串内部嵌入值。这些函数会立即生成一个新的字符串，将任何嵌入的值转换成它们的字符串表示。标记的模板文字与普通的模板文字有很大的不同，我们将在后面看到。

```
const color = "blue";
const css = `body { color: ${color}; }`;
// Produces: body { color: blue; } 
```

上面的代码基本上是硬编码的`color`变量，这不允许我们在讨论 React 组件时容易地修改基于可能收到的不同属性生成的字符串。当然你可以把它包装在一个函数中:

```
function css(props) {
  return `body { color: ${props.color}; }`;
}
css({ color: "blue" });
// Produces: body { color: blue; } 
```

但是我们将看到带标签的模板文字是如何允许我们将函数的动态特性与模板文字的易用性结合起来的。

## 在野外

在野外，我们看到标签化的模板文字被[样式化的组件](https://www.styled-components.com/)使用，注意这里我们不仅仅是嵌入值，我们还嵌入了一个函数，该函数稍后将接收一些属性，并从这些属性产生不同的 CSS。

```
const Button = styled.a`
  display: inline-block;
  border-radius: 3px;
  padding: 0.5rem 0;
  margin: 0.5rem 1rem;
  width: 11rem;
  background: transparent;
  color: white;
  border: 2px solid white; ${props =>
    props.primary &&
    css`
      background: white;
      color: palevioletred;
    `} `; 
```

[lit-html](https://lit-html.polymer-project.org/) 在生成 html 模板时也使用带标签的模板文字。

```
let sayHello = name =>
  html`
    <h1>Hello ${name}</h1>
  `;

render(sayHello("World"), document.body); 
```

最后，我们看到 Apollo 库中的 [graphql-tag](https://github.com/apollographql/graphql-tag) 在创建 graphql 标签时使用了带标签的模板文字。

```
const query = gql`
  {
    user(id: 5) {
      firstName
      lastName
    }
  }
`; 
```

## 嵌入值

与模板文字不同，带标记的模板文字将值传递给它们被“标记”的函数。这使您能够解析和操作嵌入到字符串中的值。

```
function tagged(strings, color) {
  console.log(strings);
  console.log(color);
}

const color = "blue";
tagged`body { color: ${color}; }`; 
```

如果我们查看`strings`数组的内容，我们会看到 2 个元素:

```
["body { color: ",  "; }"] 
```

它将我们的字符串分成嵌入的`color`变量之前的所有内容和之后的所有内容。它将嵌入的`color`变量作为第二个参数传递给`tagged`函数。如果我们想要产生想要的结果，我们可以使用普通的模板文字来重建字符串:

```
function tagged(strings, color) {
  return `${strings[0]}${color}${strings[1]}`;
}

const color = "blue";
tagged`body { color: ${color}; }`; 
```

但是如果我们只打算这样做，最好使用普通的模板文字，不是吗？当您接受嵌入的函数，而不仅仅是嵌入的值(它会立即产生一个嵌入到字符串中的结果)时，这种能力就发挥出来了。

## 嵌入功能

使用带标签的模板文字，我们可以嵌入函数。这使得它们可以在以后执行，并在那时产生一个值，而不是在它们被定义的时候。在这个例子中，颜色是

```
function colorMode(strings, color) {
  return `${strings[0]}${color()}${strings[1]}`;
}

const color = () => localStorage.getItem("color") || "black";

tagged`body { color: ${color}; }`;
// body { color: black; }

// Now update local storage
localStorage.setItem("color", "white");

tagged`body { color: ${color}; }`;
// body { color: white; } 
```

上面的代码失败了，因为它只被设置为接收一个嵌入的`color`函数...如果我想要两个或三个呢？

## 用道具制作 CSS

现在我们已经看到了基础知识，让我们生成一个可以接受多个嵌入函数的`css`函数，当被调用时，它将接收 props，允许它们动态生成 CSS。

我们知道`strings`包含一个字符串部分的数组，如果我们使用 spread 操作符，我们可以将所有嵌入的项放入一个名为`args` :
的数组中

```
function css(strings, ...args) {
  // ...
} 
```

我们接下来需要做的是将它们组合起来，并按照正确的顺序排列:

*   字符串 0
*   参数 0
*   字符串 1
*   参数 1
*   字符串 2

这个过程可以称为`interleaving`:

> 在书页之间插入书页，通常是空白页。

```
const interleaved = args.reduce(
  (acc, arg, index) => {
    return [...acc, arg, strings[index + 1]];
  },
  [strings[0]]
); 
```

我们最终得到的是一个“交错的”`string, arg, string, arg, string`数组。与其将它们组合成一个字符串并立即执行函数，不如实际返回一个函数，让这个过程在以后发生:

```
return props =>
  interleaved
    .map(part => (typeof part === "function" ? part(props) : part))
    .join(""); 
```

如果“部分”是一个函数，我们将调用它，传递道具给它，否则我们将只使用“部分”，最后我们可以将它们连接在一起产生一个字符串。这允许我们创建一个`div`函数，用不同的道具多次调用它，每次都产生唯一的 CSS:

```
const div = css`
  div {
    color: ${props => props.color};
    size: ${props => props.size};
  }
`;

console.log(div({ color: "black", size: 15 }));
console.log(div({ color: "white", size: 15 })); 
```

### 完整代码示例

下面是完整的代码示例，将字符串和参数交织在一起，返回一个稍后要调用的函数，该函数将接收 props 并将数组的元素拼凑在一起，最终再次连接成一个字符串。

```
function css(strings, ...args) {
  const interleaved = args.reduce(
    (acc, arg, index) => {
      return [...acc, arg, strings[index + 1]];
    },
    [strings[0]]
  );

  return props =>
    interleaved
      .map(part => (typeof part === "function" ? part(props) : part))
      .join("");
}

const div = css`
  div {
    color: ${props => props.color};
    size: ${props => props.size};
  }
`;

console.log(div({ color: "black", size: 15 }));
console.log(div({ color: "white", size: 15 })); 
```

## 结论

我还没有在自己的代码中找到标记模板文字的可靠用法，但是考虑到它是我在上面提到的包中一直使用的东西，深入了解一下这个 ES6 JavaScript 特性实际上是如何工作的是很棒的。你能想到在什么情况下使用带标签的模板文字有用吗？