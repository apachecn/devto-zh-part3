# Javascript DOM 操作以提高性能

> 原文：<https://dev.to/grandemayta/javascript-dom-manipulation-to-improve-performance-459a>

我写这篇文章是为了用例子向您展示，在用普通 Javascript 开发 Web 应用程序时，如何避免性能问题。

### 使用选择器代替嵌套元素

```
// BAD
let menu = document.querySelector('header > nav > ul.menu');

// GOOD
let menu = document.querySelector('.menu'); 
```

Enter fullscreen mode Exit fullscreen mode

### 避免循环内的 DOM 操作

```
// BAD
for (let i = 0; i < 10; i++) {
    document.querySelector('.numbers').innerText += i;
}

// GOOD
let numbers = '';
for (let i = 0; i < 10; i++) {
    numbers += i;
}
document.querySelector('.numbers').innerText = numbers; 
```

Enter fullscreen mode Exit fullscreen mode

### 不要在循环中使用 DOM 值

```
// BAD
let nodes = document.querySelectorAll('.menu-items');
for (let i = 0; i < node.length; i++) { ... }

// GOOD
let nodes = document.querySelectorAll('.menu-items');
const size = nodes.length;
for (let i = 0; i < size; i++) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 css 类代替内联样式

```
// BAD 
let card = document.querySelector('.card');
card.style.width = '400px';
card.style.color = '#f0f0f0';
card.style.marginTop = '10px';

// GOOD 
let card = document.querySelector('.card');
card.style.cssText = ''.concat(
    'width       : 400px;',
    'color       : #f0f0f0;',
    'margin-top  : 10px;'
);

// BETTER 
.card-custom {
    width: 400px;
    color: #f0f0f0;
    margin-top: 10px;
}

let card = document.querySelector('.card');
card.classList.add('card-custom'); 
```

Enter fullscreen mode Exit fullscreen mode

### 追加 DOM 是开销最大的操作，选择正确的方法

```
// BAD
let container = document.querySelector('.container');
for (let i = 0; i < 1000; i++) {
    let a = document.createElement('a');
    a.text = `Row N° ${i}`;
    container.appendChild(a);
}

// GOOD
let container = document.querySelector('.container');
let html = '';
for (let i = 0; i < 1000; i++) {
    html = html.concat(`<a>Row N° ${i}</a>`);
}
container.innerHTML = html;

// BETTER
let a = document.createElement('a');
let container = document.querySelector('.container');
for (let i = 0; i < 1000; i++) {
    let cloneA = a.cloneNode(true);
    cloneA.text = `Row N° ${i}`;
    container.appendChild(cloneA);
}

// BETTER^2
let a = document.createElement('a');
let container = document.querySelector('.container');
let documentFragment = document.createDocumentFragment();
for (let i = 0; i < 1000; i++) {
    let cloneA = a.cloneNode(true);
    cloneA.text = `Row N° ${i}`;
    documentFragment.appendChild(cloneA);
}
container.appendChild(documentFragment); 
```

Enter fullscreen mode Exit fullscreen mode

### 仅在第一次渲染时使用 innerHTML，然后使用 DOM 方法

```
// BAD
let container = document.querySelector('.container');
container.innerHTML = '<input type="text" name="example" value="Hi DEVS!">';
container.innerHTML = '<input type="text" name="example" value="Bye DEVS!">';

// GOOD
let container = document.querySelector('.container');
container.innerHTML = '<input type="text" name="example" value="Hi DEVS!">';
let input = container.querySelector('input');
input.value = 'Bye DEVS!'; 
```

Enter fullscreen mode Exit fullscreen mode

如果你有一些建议，请留下你的评论。
保持冷静，编码！