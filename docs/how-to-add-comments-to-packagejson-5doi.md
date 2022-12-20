# 如何给 package.json 添加注释？

> 原文：<https://dev.to/napolux/how-to-add-comments-to-packagejson-5doi>

当你无聊的时候，你通常会有你最好的想法。今天早上在推特上浪费时间的时候，我遇到了一个来自 [@caludio](https://twitter.com/caludio) 的合法问题(你真的应该跟着他):

> 有计划让 package.json 包含注释吗？我可以理解其中的含义，但是我想写下为什么我必须将一个包锁定到一个特定的版本(hello，TypeScript)

我有点困惑...这是一个 JSON，我可以用它做任何我想做的事情！所以我整理了一些代码来运行一个`npm install`...

```
{
  "name": "napolux-frontend",
  "version": "1.0.0",
  "description": "it's a test",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "MIT",
  "devDependencies": {
    "@babel/core": "^7.2.2",
    "@babel/preset-env": "^7.3.1",
    "babel-polyfill": "^6.26.0",
    "gulp": "^4.0.0",
    "gulp-babel": "^8.0.0",
    "gulp-rename": "^1.4.0",
    "gulp-uglify": "^3.0.1"
  },
  "dependencies": {
    "jquery": "^3.3.1"
  },
  "comments": {
    "dependencies": "we use jQuery because of reasons",
    "repository": "our beloved repo",
    "license": "we love MIT, so why not",
    "devDependencies": {
      "@babel/core": "it's @ version 7.2.2 because of...",
      "gulp-rename": "why not"
    }
  }
} 
```

有用！这不是最好的解决方案，当然没有内联注释，但是如果你保持你的`comments`部分的结构非常接近于`package.json`部分的结构，它肯定是可读的！

这篇文章最初出现在[https://coding.napolux.com](https://coding.napolux.com)上，但是为了你的阅读乐趣被带到了 dev.to.