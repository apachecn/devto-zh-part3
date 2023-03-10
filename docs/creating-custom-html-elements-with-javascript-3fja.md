# 用 Javascript 创建自定义 HTML 元素

> 原文：<https://dev.to/wangonya/creating-custom-html-elements-with-javascript-3fja>

我今天才知道，用 Javascript 创建自定义 HTML 元素并扩展现有元素是可能的。这里有一个例子:

```
const InitiallyHiddenElement = document.registerElement(
  "initially-hidden",
  class extends HTMLElement {
    createdCallback() {
      this.revealTimeoutId = null;
    }
    attachedCallback() {
      const seconds = Number(this.getAttribute("for"));
      this.style.display = "none";
      this.revealTimeoutId = setTimeout(() => {
        this.style.display = "block";
      }, seconds * 1000);
    }
    detachedCallback() {
      if (this.revealTimeoutId) {
        clearTimeout(this.revealTimeoutId);
        this.revealTimeoutId = null;
      }
    }
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

在网页上运行`<initially-hidden for="2">Hello</initially-hidden>` & `<initially-hidden for="5">World</initially-hidden>`会分别延迟元素出现 2 秒和 5 秒。

这个想法看起来很酷，但我只是在想:所有这些仍然可以在 JS 和 CSS 中完成，而不必创建自定义元素。

有哪些创建定制元素是更好的方法的好用例？