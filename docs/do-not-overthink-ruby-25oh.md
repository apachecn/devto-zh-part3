# 不要过度思考 Ruby

> 原文：<https://dev.to/scottw/do-not-overthink-ruby-25oh>

我通读了 Ruby regex 文档，试图找出如何迭代一个字符串并替换特定模式的所有匹配。

棘手的部分是匹配不是一对一的(不仅仅是查找和替换)。我尝试了一堆不同的东西，然后开始希望`gsub`能有所突破。原来它已经这样做了。

示例:

```
"1234".gsub(/\d/) {|i| i.to_i * 2} # 2468 
```

更好的是，它处理所有的替换

```
"Square this number: 5".gsub(/\d/) {|i| i.to_i * i_to_i}
# Square this number: 25 
```