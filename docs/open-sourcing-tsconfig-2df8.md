# 开放采购 tsconfig

> 原文：<https://dev.to/krzysztofzuraw/open-sourcing-tsconfig-2df8>

# 再次嗨🖖

今天我想写一写我们在工作中做的事情。我目前在[英格丽](https://www.ingrid.com/)工作。你所有的代码都是用 TypeScript 编写的，它使用`tsconfig`作为一种方式
来告诉编译器该做什么。大多数 tsconfig 对你所有的项目
都是一样的，所以我们决定开源它🎉。

## 为什么

你可能会问为什么要做这种事？第一个论点是，我们可以对我们所有的代码库实施良好的实践。如果有新的设置-我们可以在一个地方调整它，所有的项目都会有它。

这也是制作我们内部 ts 指南的第一步。我们已经有了一个共同的更漂亮的配置。下一步将添加 Ingrid tslint 规则。

## 怎么用呢

因为它是开源的，你可以通过安装它在你自己的项目中使用它:

```
npm install --save-dev @ingridab/tsconfig 
```

Enter fullscreen mode Exit fullscreen mode

并扩展自己的配置:

```
{  "extends":  "@ingridab/tsconfig",  "compilerOptions":  {  "baseUrl":  "./",  "paths":  {  "~src/*":  ["src/*"]  },  "plugins":  [  {  "name":  "typescript-tslint-plugin"  }  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 总结&TL；速度三角形定位法(dead reckoning)

我工作的公司开源了他们的 tsconfig。
你也可以在自己的项目中使用它！贵公司开源的其他
工具或配置有哪些？请写在评论里！

页（page 的缩写）S

如果你想比 dev.to 上的帖子早一周得到这些帖子，你可以在[https://krzysztofzuraw.com/newsletter](https://krzysztofzuraw.com/newsletter)上加入我的时事通讯。