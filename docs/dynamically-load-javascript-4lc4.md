# 动态加载 javascript

> 原文：<https://dev.to/johns/dynamically-load-javascript-4lc4>

只要把 ele.src 改成你想要的任何东西

```
(function () {
    var ele = document.createElement('script');
    ele.src = "https://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js";
    ele.id = "JQuery";
    ele.onload = function () {
        //code to be executed when the document has been loaded
    };
    document.getElementsByTagName('head')[0].appendChild(ele);
})(); 
```

来源[https://stack overflow . com/questions/10994335/JavaScript-head-body-or-jquery](https://stackoverflow.com/questions/10994335/javascript-head-body-or-jquery)