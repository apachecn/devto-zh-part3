# 在使用框架多年后重新审视普通 JavaScript

> 原文：<https://dev.to/zachdixon/revisiting-vanilla-javascript-after-years-of-using-frameworks-13hm>

# 香草 JavaScript...如此清新

我最近被分配了一个小项目，为我们的 web 应用程序构建定价表。起初，我对此并不以为意，但当我的同事发给我构建代码时，我震惊了；为此，我必须使用普通的 JavaScript/HTML/CSS。

作为一名 7 年的前端开发人员，我承认这有点尴尬。我每天都写 JavaScript，但是大部分都是框架特定的代码，比如 React。是的，React 是 JavaScript，但是这个项目包括 DOM 操作，在 React 中你不会做很多。不过没问题，让我们开始吧。

## 项目要求

就像我提到的，这个项目是为我们的应用程序创建定价页面。我得到了一些代码，这是一个良好的开端，我只需要添加一些简单的东西:

*   更新款式/设计
*   改变价格的滑块输入，包括最高价格的“让我们聊天”

## 重新认识

给我的代码是这样的:

[https://codesandbox.io/embed/8z0kg](https://codesandbox.io/embed/8z0kg)

## 造型

不错的开始！HTML/CSS 不在这篇文章的讨论范围内，所以我只总结一下我修改的地方。

*   添加了主题颜色和边距/填充间距的 CSS 变量
*   重新设计以符合我们的风格指南
*   通过自动前缀匹配器运行 CSS 查找供应商前缀
*   移动了一些东西
*   样式化滑块输入

## 到 JavaScript！

现在我只需要让滑块在价格变化时更新价格。这方面的 JavaScript 相当简单；观察滑块`oninput`事件，抓取相应的 HTML 元素并更改`innerHTML`。

```
let input = document.getElementById("followers"),
  priceLite = document.getElementById("ep_lite"),
  priceMod = document.getElementById("ep_mod"),
  followerCount = document.getElementById("follows");

input.oninput = function() {
  let value = this.value;
  priceLite.innerHTML = values[value].price.lite;
  priceMod.innerHTML = values[value].price.mod;
  followerCount.innerHTML = values[value].label;
};
input.oninput(); 
```

接下来，我继续定义`values`数组。起初，我只是自己更新号码，这样做很好，但后来我到了需要显示“让我们聊天”的地方，这与 HTML 的`$`和`/mo`部分没有很好地融合。所以我创建了另一个元素，我会在必要的时候隐藏/显示它，看起来像这样:

```
<div class="price">...</div>
<div class="lets-chat hidden">Let's Chat!</div> 
```

```
.hidden {
  display: none;
} 
```

```
let input = document.getElementById("followers"),
...
   priceEls = document.getElementsByClassName("price"),
   chatEls = document.getElementsByClassName("lets-chat");

function checkPrice = (value) => {
  if (value === parseInt(input.max)) {
  // if value is max of input, hide prices and show let's chat
    Array.prototype.forEach.call(priceEls, (el) => {
      el.classList.add("hidden");
    });
    Array.prototype.forEach.call(chatEls, (el) => {
      el.classList.remove("hidden");
    });
  } else {
  // else hide let's chat and show prices
    Array.prototype.forEach.call(priceEls, (el) => {
      el.classList.remove("hidden");
    });
    Array.prototype.forEach.call(chatEls, (el) => {
      el.classList.add("hidden");
    });
  }
} 
```

这做了工作，但对我来说有点乱。它或许可以写得更有效率，但是...

💡

“我可以像在 React 中一样创建可重用的组件”，我想。这是使用模板文字(模板字符串)的最佳时机。

所以我把 HTML 移到了 JS 中，创建了两个函数:

```
const Price = (price) => {
  return `<p class="price">
    $<span>${price}</span><span class="price_time"> /mo</span>
  </p>`;
};

const LetsChat = () => {
  return `<p class="lets-chat">Let's chat!</p>`;
}; 
```

如果你真的了解 React 或任何其他框架，这可能看起来很熟悉。它们基本上是功能组件，只是返回一个字符串而不是 JSX。

现在我们可以使用这些函数创建`values`数组。

```
let values = [
  { label: "< 100k", price: { lite: Price(10), mod: Price(18) } },
  { label: "< 500k", price: { lite: Price(18), mod: Price(29) } },
  { label: "< 1M", price: { lite: Price(25), mod: Price(59) } },
  { label: "1M+", price: { lite: LetsChat(), mod: LetsChat() } }
]; 
```

这是最后的结果:

[https://codesandbox.io/embed/4nrv8](https://codesandbox.io/embed/4nrv8)

## 最后的想法

尽管很小很简单，但我真的很喜欢做这个项目。我认为作为前端开发人员，重温普通 JavaScript 极其重要。人们很容易被框架的魔力所吸引，而忘记了它的本质。