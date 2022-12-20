# 原因警告:使用了未实现的原语

> 原文：<https://dev.to/hossman333/reasonml-warning-unimplemented-primitive-used-1nhi>

如果你遇到了问题`Warning: Unimplemented primitive used:put`或其他什么`put`对你来说，这可能是因为你忘了在包装前包括`[@bs.module "<library>"]`或任何其他 FFI。

例如:

```
external putJson: (string, Js.Json.t) => request = "put"; 
```

Enter fullscreen mode Exit fullscreen mode

这给了我高于^.的误差修复？

```
[@bs.module "superagent"] external putJson: (string, Js.Json.t) => request = "put"; 
```

Enter fullscreen mode Exit fullscreen mode

至少对我来说是这样。(: