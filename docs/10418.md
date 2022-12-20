# console.fun()或者如何搞乱 DOM

> 原文：<https://dev.to/janpauldahlke/consolefun-or-how-to-mess-up-the-dom-2kkb>

我在 [vue-land](https://vue-land.js.org/) #random 频道偶然发现了这个小宝石，它让我微笑。由 *xan#2554* 发布。

我想也许它也能让你微笑，并决定与你分享。玩得开心。

### 指令

> 1.  Copy script
> 2.  Visit your favorite website-... Most likely, [will develop into](//dev.to)
> 3.  Open the console.
> 4.  Paste the script and press `enter`

```
(function funnyFunction() {
  var screenwidth = window.innerWidth;
  var screenheight = window.innerHeight;
  document.querySelectorAll('body *').forEach(function(node) {
    randT = Math.floor(Math.random() * screenheight);
    randL = Math.floor(Math.random() * screenwidth);
    node.setAttribute('style', 'transition-duration:500ms; transition-property: left, top; position: fixed; top:'+randT+'px; left:'+randL+'px');
  });
  setInterval(funnyFunction, 500);
})(); 
```

Enter fullscreen mode Exit fullscreen mode

对自己诚实，你知道会发生什么吗？

干杯