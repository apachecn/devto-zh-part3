# 可怕的片段-检查字符串

> 原文：<https://dev.to/samrocksc/horrible-snippets-check-the-string-33lp>

**用例**
假设你要实现一个 darklaunch 特性，你有一些选项，你可以传递一堆 env 变量(RUN_EVENT_1=true，等等。)或者您可以传递一个带有一些逗号分隔变量的`DARK_LAUNCH`标志。

```
const stringydingy = 'foo,bar,baz';

stringydingy.split(',').includes('foo');

if(stringydingy.split(',').includes('foo')) {
  runDarkLaunchDotCalm();
} 
```

使用它，在您的项目中尝试它！请给我反馈，告诉我如何改进！