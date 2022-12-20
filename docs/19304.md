# 超级小编译器！

> 原文：<https://dev.to/chenge/the-super-tiny-compiler-2ck0>

[![](img/ee9b90cc68a0f42ed837b942dce1fac0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uSISN36y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.githubusercontent.com/assets/952783/21579290/5755288a-cf75-11e6-90e0-029529a44a38.png)

代码比文字更清晰。我喜欢短代码。

一个 100 行的文件，解释编译器。

从下面的代码中，我们可以看到编译是整个过程。解析器是一个步骤。我认为元编程正处于变革阶段。AST 是内核数据结构。

了解更多编译将有助于你理解各种语言。

[链接- >](https://github.com/jamiebuilds/the-super-tiny-compiler)

```
/**
 * FINALLY! We'll create our `compiler` function. Here we will link together
 * every part of the pipeline.
 *
 *   1\. input  => tokenizer   => tokens
 *   2\. tokens => parser      => ast
 *   3\. ast    => transformer => newAst
 *   4\. newAst => generator   => output
 */

function compiler(input) {
  let tokens = tokenizer(input);
  let ast    = parser(tokens);
  let newAst = transformer(ast);
  let output = codeGenerator(newAst);

  // and simply return the output!
  return output;
} 
```