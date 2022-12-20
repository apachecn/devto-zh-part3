# TIL:使用逗号链接“let”和“const”语句

> 原文：<https://dev.to/samelawrence/til-chaining-statements-using-commas-1fm>

对我来说，现在很明显，像这样的功能是可能的，但这是你在第一次看到它之前不会想到的事情之一。作为 Pluralsight 教程的一部分，我正在开发一个 21 点游戏，我刚刚看到讲师用逗号将多个`let`和`const`语句连接在一起。

```
const suits = ['Hearts', 'Clubs', 'Diamonds', 'Spades'],
  values = ['Ace', 'King', 'Queen', 'Jack', 'Ten', 'Nine', 'Eight', 'Seven', 'Six', 'Five', 'Four', 'Three', 'Two'];

let textArea = document.getElementById('text-area'),
  newGameButton = document.getElementById('new-game-button'),
  hitButton = document.getElementById('hit-button'),
  stayButton = document.getElementById('stay-button'); 
```

Enter fullscreen mode Exit fullscreen mode

对我的编码实践来说，这是一个很小的改变，但是我喜欢它在页面上组织代码的方式。如果可能的话，我想我会更多地使用它，哪怕只是为了美观。