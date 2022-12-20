# 无视你的埃斯林规则

> 原文：<https://dev.to/mlennox/riding-rough-shod-over-your-eslint-rules-1c3p>

在 HMH，我们对代码复杂性采用了相当严格的 eslint 规则。在之前，我已经写过关于 eslint 复杂性度量的文章，毫无疑问，我还会再写一次！这些规则对我们的代码来说很好，但是当我们写测试的时候，它们会成为我们的障碍。

## 测试也需要代码标准！

我并不是说你应该放弃所有单元测试代码的代码标准，但是从本质上来说，测试通常比它们所针对的代码更大、更详细、更复杂。我们已经采用了一些方法来尝试保持测试的灵活性和可维护性，通过将它们分成多个文件，添加少量的[坚实的原则](https://en.wikipedia.org/wiki/SOLID)并且通常使用我们的常识。

尽管如此，我们的测试文件仍然是一堆弯弯曲曲的绿色线条，告诉我们没有编写 cromulent 代码。如果我们能放宽测试代码的规则就好了！

## 超驰救驾

幸运的是，我们确实可以让 eslint [对不同的文件](https://eslint.org/docs/user-guide/configuring#disabling-rules-only-for-a-group-of-files)应用不同的规则。

对我们来说，我们知道测试文件被一致地命名为`somemodule.test.js`，所以相关的 eslint 配置将是:

```
{
  "rules": {
    /* rules here will apply to all files */
    "complexity": [
            "error",
            6
        ],
        "max-nested-callbacks": [
            "error",
            2
        ],
        "max-lines": [
            "warn",
            80
        ],
  },
  "overrides": [
    {
      "files": ["*.test.js"],
      "rules": {
        // we've changed all the errors to warnings
        // and relaxed the rules
        "complexity": [
            "warn",
            12
        ],
        "max-nested-callbacks": [
            "warn",
            5
        ],
        "max-lines": [
            "warn",
            200
        ],
      }
    },
    {
      "files": ["*.any.other.files.js"],
      "rules": {
        /* add suitable rule configs here */
      }
    }
  ]
} 
```

正如您所看到的，您可以为不同的文件类型设置多个覆盖，尽管我们只覆盖单元测试规则。