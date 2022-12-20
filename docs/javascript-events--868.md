# Javascript 事件

> 原文：<https://dev.to/richardj/javascript-events--868>

在一个消息系统上工作，我想捕捉消息元素或其子元素上发生的所有事件。这是做这件事的最佳方式吗？

我还可以通过 CSS 在所有子元素上添加一个`pointer-events: none`。但是这看起来并不是一个好的可扩展的方法。

```
document.addEventListener('click', function(event) {
    for (var i = 0; i < event.path.length; i++) {
        if (event.path[i].classList && event.path[i].classList.contains('message'))         
        {
            doThings();
            break;
        }
   }
}); 
```

Enter fullscreen mode Exit fullscreen mode