# 使用 setValue 和 patchValue

> 原文：<https://dev.to/nishugoel/using-setvalue-and-patchvalue-4kp1>

在这篇博文中，我们将看到在构建反应式表单时，如果我们需要从我们的组件类更新表单上的输入元素，我们使用 setValue 和 patchValue。

如果您不熟悉反应式表单，我建议您阅读这篇文章:反应式(模型驱动)表单

我们使用 setvalue 来设置表单上每个控件的值。例如，在我们的表单模型中有三个表单控件。现在，因为我们想要更新其中每一个的值，我们使用 setValue。

[https://gist . github . com/NishuGoel/1a 9560 e 14952 e 58 AE 56 f 0446 ad 0b 3 de 1](https://gist.github.com/NishuGoel/1a9560e14952e58ae56f0446ad0b3de1)

但是，如果我们只想设置值的子集，那么我们使用 patchValue。在本例中，我们只设置了三个值中的一个。

[https://gist . github . com/NishuGoel/2f 2 BC 061482279 B1 ea 22 de 793 D8 e 84 f 8](https://gist.github.com/NishuGoel/2f2bc061482279b1ea22de793d8e84f8)

让我们现在就动手试试吧！

在组件模板中，让我们像这样用一个按钮来检查数据:

[https://gist . github . com/NishuGoel/2f 4 e 81 a 846 BC 2 FD E5 b 7 c 9 b 62 F3 c 96360](https://gist.github.com/NishuGoel/2f4e81a846bc2fde5b7c9b62f3c96360)

现在让我们在 component.ts 文件中使用这个方法。

[https://gist . github . com/NishuGoel/932 a0c 47 a2 c 6121415063 BD 04606 c8e 2](https://gist.github.com/NishuGoel/932a0c47a2c6121415063bd04606c8e2)

现在，当我们在浏览器中选中此项时，它会将浏览器中的数据填充为:

Value {"name": "Wilson "，" city": "Bangalore "，" email ":"[wilsondcousta@gmail.com](mailto:wilsondcousta@gmail.com)" }
在这种情况下，在 checkData()方法中，我们不设置 name 和 email 的值，只为城市设置，如下所示:

this . my form . setvalue({
city:' gur gaon '
})
在这种情况下，控制台会抛出如下错误:

[https://thepractical dev . S3 . Amazon AWS . com/I/znyf 66 jnw 2 io 6 ag 7 lff 1。PNG](https://thepracticaldev.s3.amazonaws.com/i/znyf66jnw2io6ag7lff1.PNG)

要解决这个，简单的解决方法！我们使用 patchValue，因为我们只对表单元素的一个子集进行修改，而不是对表单的所有元素进行修改。

我们将编写以下代码来代替上面的代码:

this . my form . patch value({
city:' gur gaon '
})
然后，很好用。