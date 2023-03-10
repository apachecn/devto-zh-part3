# 快速提示:将胶子模型转换成符号格式

> 原文：<https://dev.to/juliensimon/quick-tip-converting-gluon-models-to-symbolic-format-4m5g>

如前所述，Gluon 是一个命令式 API，位于由 T2 Apache MXNet 实现的符号 API 之上。

胶子的一个很酷的特性是在[胶子 CV](https://github.com/dmlc/gluon-cv) 中提供的大量[预训练计算机视觉模型集合](https://gluon-cv.mxnet.io/api/model_zoo.html)。将这些模型与 Gluon API 一起使用非常容易，但有时我们宁愿使用符号 API。

一个原因可能是语言支持:Gluon 仅支持 Python，而 MXNet 支持包括 C++在内的所有语言，您可能需要使用 c++来获得最佳的预测性能。可惜 MXNet 模型动物园和胶子模型动物园不同步，不能随便抢同一个模型。

这个问题的一个简单解决方案是使用 Gluon API 下载模型，将它们导出为符号格式，然后使用 MXNet API 加载它们。

事情是这样的。

```
from gluoncv import model\_zoo
import mxnet as mx
import numpy as np

# Download the model from the Gluon model zoo
# You'll find it in ~/.mxnet/models
net = model\_zoo.get\_model('resnet50\_v1', pretrained=True)

# Convert the model to symbolic format
net.hybridize()

# Build a fake image to run a single prediction
# This is required to initialize the model properly
x = np.zeros([1,3,224,244])
x = mx.nd.array(x)

# Predict the fake image
net.forward(x)

# Export the model
net.export('resnet50\_v1') 
```

这将导出模型权重和包含模型符号定义的 JSON 文件。

```
$ ls -1 resnet50\*
resnet50\_v1-0000.params
resnet50\_v1-symbol.json 
```

现在您可以像往常一样使用[*model . load _ check point()*](https://mxnet.incubator.apache.org/api/python/module/module.html)API 加载这个模型。只要确保你使用的是最新版本的 MXNet，因为胶子模型可能与旧版本不兼容。YMMV。

```
import mxnet as mx

sym, arg, aux = mx.model.load\_checkpoint("resnet50\_v1", 0)
... 
```

就是这样。现在您可以使用符号 API 享受所有这些模型了:)