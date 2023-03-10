# 谈机器学习(一):设置

> 原文：<https://dev.to/mandrewcito/talking-about-machine-learning-i-setup-3273>

本系列接下来的两篇文章将是关于机器学习的教程，这是人工智能最受欢迎的[分支之一。](http://jmc.stanford.edu/artificial-intelligence/what-is-ai/branches-of-ai.html)

### 环境

我将使用以下库( [NumPy](https://www.numpy.org/) 、 [SciPy](https://www.scipy.org/) 、 [scikit-learn](https://scikit-learn.org/stable/index.html) 、 [matplotlib](https://matplotlib.org/) )。我构建了一个微小的安装脚本。

```
mkdir -p talkingaboutml/talkingaboutml
python3 -m virtualenv talkingaboutml/venv
talkingaboutml/venv/bin/pip install numpy scipy scikit-learn matplotlib 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的 talkingaboutml 目录看起来像:

```
talkingaboutml/
├── talkingaboutml (here we store our examples)
└── venv 
```

Enter fullscreen mode Exit fullscreen mode

### 第一个例子

在我们的第一个例子中，我将使用 sckit 数据集(可在 sklearn.datasets 上获得)，有许多[示例数据集](https://scikit-learn.org/stable/datasets/index.html)。我选择虹膜。该数据集是一个多类分类数据集。

作为第一个示例，我将训练一个简单的分类并运行一个预测。

我们需要一些进口，数据集，准确性指标和线性 svc:

```
from sklearn import datasets
from sklearn.metrics import accuracy_score
from sklearn.svm.classes import SVC 
```

Enter fullscreen mode Exit fullscreen mode

加载数据集，该数据集已被分割(数据，目标)。

```
iris = datasets.load_iris()
X = iris.data # each register, a iris with features y = iris.target # classification for each register 
feature_number = X.shape[1] 
```

Enter fullscreen mode Exit fullscreen mode

创建分类、训练和预测。

```
 clf = SVC(kernel='linear', C=1.0, probability=True, random_state=0) # 'Linear SVC' 
clf.fit(X, y) # Train 
y_pred = clf.predict(X)
accuracy = accuracy_score(y, y_pred)
print(accuracy) 
```

Enter fullscreen mode Exit fullscreen mode

因此...让我们这样做，在这个例子中，我用不同的 C(惩罚)值训练一个单独的分类器。该参数告诉 [svm](https://en.wikipedia.org/wiki/Support-vector_machine) 你希望如何匹配，以避免对每个训练样本进行错误分类。一个很好的解释可以在[这里](http://svms.org/parameters/)或者[这里](https://stats.stackexchange.com/questions/31066/what-is-the-influence-of-c-in-svms-with-linear-kernel#159051)找到。

```
import matplotlib.pyplot as plt
import numpy as np
from sklearn import datasets
from sklearn.metrics import accuracy_score
from sklearn.svm.classes import SVC

iris = datasets.load_iris()

X = iris.data # each register, a iris with features y = iris.target # clasiffication ir each register 

feature_number = X.shape[1]

penalties = list(np.arange(0.5,10.0, 0.1))

accs = []

for C in penalties:
    clf = SVC(kernel='linear', C=C, probability=True, random_state=0) # 'Linear SVC' 
    clf.fit(X, y) # Train 
    y_pred = clf.predict(X)
    accuracy = accuracy_score(y, y_pred)
    accs.append(accuracy)

# plot the data fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
ax.plot(penalties, accs, 'r')
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

[![penalties result](img/97e858d538ccc2cbed89a7336add2466.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BOlm0XCF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iuj7g9qwhyn65a347mlq.png)

我们可以看到，惩罚因子。如果它太大，我们有太多的支持向量，这可能会导致过拟合。