# 我无法理解“工厂”

> 原文：<https://dev.to/webjayant/i-am-not-able-to-understand-factory-39a6>

前几天我偶然发现了下面的代码

```
(function (factory) {
    var jQuery;
    if (typeof define === 'function' && define.amd) {
        // AMD (Register as an anonymous module)
        define(['jquery'], factory);
    } else if (typeof exports === 'object') {
        // Node/CommonJS
        try {
            jQuery = require('jquery');
        } catch(e) {}
        module.exports = factory(jQuery);
    } else {
        // Browser globals
        var _OldCookies = window.Cookies;
        var api = window.Cookies = factory(window.jQuery);
        api.noConflict = function() {
            window.Cookies = _OldCookies;
            return api;
        };
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有人能给我解释一下什么是“工厂”以及它是如何在上面的代码中使用的吗？

我知道工厂函数是用来创建对象的，但我不明白它在这里是如何使用的。

*(这是我在这里的第一篇帖子，因此对任何错误表示歉意)*

还有，如何在 dev.to 上使用表情符号