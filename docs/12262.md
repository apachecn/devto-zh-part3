# 有组织的，干净的方法处理 SCSS 变量

> 原文：<https://dev.to/andrei_says/organized-clean-approach-to-scss-variables-3ccp>

我是一个懒惰的人，有很多事情要做。我喜欢清晰高效，不喜欢死记硬背。

我在所有的项目中都使用一种处理 SCSS 变量的方法——这种方法符合我的思维习惯，省去了我打字的时间。

该技术的一个应用示例:

```
p {
  color: c(body-text);
} 
```

数据结构和 SCSS 函数使这成为可能:

```
// the data map
$colors: (
  body-text: rgba(#000, 0.9),
  body-link: #33c,
  // etc.
)

// the function
@function c($the-color) {
  @return map-get($colors, $the-color);
} 
```

我用颜色`c(color)`、断点`bp(phone-landscape)`、【神奇数字】- `n(w-desktop-max-width)`等来使用它。

干燥项目，保持名称空间干净，并使查找东西变得容易。

快乐编码，让我知道你是否使用类似的小省时器！