# 向动态导入添加文件扩展名以减小大小

> 原文：<https://dev.to/lgraziani2712/add-file-extensions-to-your-dynamic-imports-to-reduce-size-4b86>

更新:这里有一个这种情况的 code sandbox:[https://code sandbox . io/s/web pack-dynamic-import-duplication-array-gv0gq](https://codesandbox.io/s/webpack-dynamic-import-duplication-array-gv0gq)

## TL；速度三角形定位法(dead reckoning)

而不是像这样写动态导入

```
import(`./my-path/${fileName}`) 
```

这样写它们

```
import(`./my-path/${fileName}.ext`); 
```

它将减少包的大小！

## 龙解释

昨天我在读 webpack 生成的代码，发现了一些有趣的东西:

```
var map = {
  "./en/_extras": [
    3,
    0
  ],
  "./en/_extras.js": [
    3,
    0
  ],
  "./en/color": [
    8,
    5
  ],
  "./en/color.js": [
    8,
    5
  ],
  // The rest of the files
}; 
```

那个`map`对象代表了特定动态导入的每一个可能的块，它的大小是我预期的两倍。

然后我突然想到:我没有在我的动态导入中编写文件扩展名。当我这样做的时候，每个对象都缩小了它的大小以匹配文件的数量！

这并不会大幅度减少你的包的大小，但这是你可以免费得到的东西！