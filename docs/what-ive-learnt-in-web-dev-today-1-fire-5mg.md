# 今天我在 web dev 中学到了什么[1]:“表单提交”

> 原文：<https://dev.to/islam/what-ive-learnt-in-web-dev-today-1-fire-5mg>

#### **简介:**

每天我都会面对问题，遇到未知的事物，遇到错误，寻找解决方案，最后我会学到新的东西。我认为把我每天学到的东西写下来会很有用。它可能会帮助其他愿意知道这些事情的人，或者只是想要快速提示和简要信息的人。

* * *

***【2019-03-06】*T3】**

### **事件监听器用于表单提交。**

*   今天当我想听一个[表单](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form)的提交事件时，我遇到了一个问题。
*   我认为我应该将[事件监听器](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)添加到`<input type="submit" />`或`<button type="submit"></button>`中，我应该在那里执行操作(单击以提交表单)。*我错了！*
*   这个概念是，当我单击提交按钮时，我提交的是表单而不是按钮。因此，将事件侦听器添加到表单本身而不是提交按钮是有意义的。

*举例:*

```
<form id="form-example">
  <input type="text" id="input-text" />
  <input type="submit" id="input-submit" value="Submit" /> <!-- or <button type="submit">Submit</button> -->
</form> 
```

```
const form = document.getElementById('form-example');
const text = document.getElementById('input-text');
form.addEventListener('submit', event => {
    event.preventDefault();
    console.log(`Text submitted: ${text.value}`)
}); 
```

*   我还发现你可以用这种方法通过[事件目标](https://developer.mozilla.org/en-US/docs/Web/API/Event/target)得到`<input type="text" />`的值。

```
form.addEventListener('submit', event => {
    event.preventDefault();
    console.log(`Text submitted: ${event.target['0'].value}`)
}); 
```

*   这是因为事件目标`<form>`是对[对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects)的引用，该对象调度了[事件](https://developer.mozilla.org/en-US/docs/Web/API/Event)及其子事件作为属性。并且每个属性键是从“0”开始的数字。

* * *

#### **参考文献:**

*   [表单提交](https://javascript.info/forms-submit)。
*   [如何使用 javaScript 提交表单](http://javascript-coder.com/javascript-form/javascript-form-submit.phtml)。

* * *

*   今天到此为止。请给✒️写个评论，告诉我是否应该继续这个系列。当然，如果我做错了什么，请纠正我。