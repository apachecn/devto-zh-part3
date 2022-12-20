# 使用 sed 查找和替换

> 原文：<https://dev.to/samrocksc/using-sed-to-find-and-replace-2ba2>

## 使用 sed 查找和替换

当您需要查找和替换大量的模块时(想象一下您必须将一个需求重构到您公司的私有范围的包中)。

幸运的是，我们使用的是基于 T4 的系统(除非是 windows)...然后随便，享受 windows)。我们可以使用`sed`，它基本上是针对一个流运行命令。`find`基本上是将一个流直接通过管道传输到`sed`，sed 将运行一个简单的查找/替换方案(就像我们使用 vim 一样)。

**修复 foo 并将其移动到私有回购:**

```
find ./ -type f -exec sed -i "s/'foo/'@my-org\/foo/g" {} \; 
```