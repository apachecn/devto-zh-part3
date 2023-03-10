# BentoML:从 Jupyter 笔记本中的模型到部署只需 5 分钟

> 原文：<https://dev.to/yubozhao/bentoml-from-model-in-jupyter-notebook-to-deployment-in-5-mins-3030>

大家好，我想分享一个我们为数据科学家做的[开源项目](//www.github.com/bentoml/bentoml)。它是一个用于打包和部署模型的机器学习工具包。

BentoML 是一个用于打包和部署机器学习模型的 python 库。它在不改变模型训练工作流程的情况下做了两件事:

*   标准化如何为生产打包您的 ML 模型，包括它的预处理/特征提取代码、依赖性和配置。

*   轻松分发你的 ML 模型作为 PyPI 包，API 服务器(在 Docker 镜像中)，命令行工具或者 Spark/Flink UDF。

我们构建 BentoML 是因为我们认为应该有一种更简单的方式让机器学习团队将模型用于生产。他们不应该等待工程团队为生产环境重新实现他们的模型，或者为实验模型构建复杂的特性管道。

我们的愿景是让机器学习科学家能够像软件工程师一样，端到端地构建和运送他们自己的模型作为生产服务。BentoML 本质上是机器学习项目中缺失的“构建工具”。

考虑到这一点，以下是 BentoML 的最高设计目标:

*   多框架支持——BentoML 应该支持各种现成的 ML 框架，包括 Tensorflow、PyTorch、Scikit-Learn、xgboost，并且可以很容易地扩展到新的或定制的框架。

*   最佳实践内置- BentoML 用户可以轻松地为他们的模型定制遥测和日志记录，并使其易于与生产系统集成。

*   简化部署工作流程——BentoML 支持通过 Docker、Kubernetes、AWS EC2、ECS、Google Cloud Platform、AWS SageMaker 和 Azure ML 将模型部署到 REST API 端点。

*   定制模型运行时——在实时模型服务中，轻松将您的 python 代码与高性能模型运行时后端(如 tf-serving、tensorrt-inference-server)集成。

这里有一个简单的例子:

我们有一个来自 Scikit-learn 的非常简单的模型:

```
from sklearn import svm
from sklearn import datasets

clf = svm.SVC(gamma='scale')
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y) 
```

要用 BentoML 打包这个模型，您需要通过子类化它来创建一个新的 BentoService，并为它提供工件和 env 定义:

```
%%writefile iris_classifier.py
from bentoml import BentoService, api, env, artifacts
from bentoml.artifact import PickleArtifact
from bentoml.handlers import DataframeHandler

@artifacts([PickleArtifact('model')])
@env(conda_dependencies=["scikit-learn"])
class IrisClassifier(BentoService):

    @api(DataframeHandler)
    def predict(self, df):
        return self.artifacts.model.predict(df) 
```

现在，要保存您的训练模型以供生产使用，只需导入您的 BentoService 类，并将其与所需的工件打包:

```
from iris_classifier import IrisClassifier

svc = IrisClassifier.pack(model=clf)

svc.save('./saved_bento', version='v0.0.1') # Saving archive to ./saved_bento/IrisClassifier/v0.0.1/ 
```

就是这样。现在您已经创建了您的第一个 BentoArchive。它是一个目录，包含在生产中运行该模型所需的所有源代码、数据和配置文件。

#### 如何使用打包存档

##### 在 Python 中加载 BentoArchive

```
import bentoml

bento_svc = bentoml.load('./saved_bento/IrisClassifier/v0.0.1/')
bento_svc.predict(X[0]) 
```

##### 将 BentoArchive 安装为 PyPI 包

```
pip install ./saved_bento/IrisClassifier/v0.0.1/ 
```

导入它并将其用作 python 模块:

```
from IrisClassifier import IrisClassifier

installed_svc = IrisClassifier()
installed_svc.predict(X[0]) 
```

喜欢听你们的反馈和想法！

干杯