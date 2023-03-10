# 使用 Rumale 使用 Ruby 实现简单的机器学习

> 原文：<https://dev.to/kojix2/easy-machine-learning-with-ruby-using-svmkit-4n86>

## 什么是鲁玛勒

用纯 Ruby 写的强大的机器学习库！
Rumale 是由 [@yoshoku](https://dev.to/yoshoku) 创造的。https://github.com/yoshoku/rumale

> Rumale (Ruby 机器学习)是 Ruby 中的一个机器学习库。Rumale 提供了机器学习算法，接口类似于 Python 中的 Scikit-Learn。Rumale 支持线性/核支持向量机、逻辑回归、线性回归、Ridge、Lasso、因式分解机、朴素贝叶斯、决策树、AdaBoost、梯度树提升、随机森林、Extra-Trees、K-最近邻分类器、K-Means、高斯混合模型、DBSCAN、幂迭代聚类、多维标度、t-SNE、主成分分析和非负矩阵因式分解。

## 安装

```
gem install rumale 
```

Enter fullscreen mode Exit fullscreen mode

## 准备一个数据集

```
require 'rumale'
require 'daru'
require 'rdatasets'

# load datasets
iris = RDatasets.load(:datasets, :iris)
# Daru::DataFrame

# labels # Numo::Int32#shape=[150]
iris_labels = iris['Species'].to_a
encoder = Rumale::Preprocessing::LabelEncoder.new
labels = encoder.fit_transform(iris_labels) 

# samples Numo::DFloat#shape=[150,4]
# (Daru -> NArray )
samples = Numo::DFloat[*iris[0..3].to_matrix.to_a] 
```

Enter fullscreen mode Exit fullscreen mode

## 分类车型

```
# Support vector machine
model = Rumale::LinearModel::SVC.new(
  reg_param: 0.0001,
  fit_bias: true,
  max_iter: 3000,
  random_seed: 1
) 
```

Enter fullscreen mode Exit fullscreen mode

#### 各种量词

*   见官方参考。[https://www.rubydoc.info/gems/rumale/](https://www.rubydoc.info/gems/rumale/)

```
model = Rumale::Tree::DecisionTreeClassifier.new(random_seed: 1)
model = Rumale::Ensemble::RandomForestClassifier.new(random_seed: 1)
model = Rumale::NearestNeighbors::KNeighborsClassifier.new(n_neighbors: 5)
model = Rumale::NaiveBayes::GaussianNB.new
# etc... 
```

Enter fullscreen mode Exit fullscreen mode

## 交叉验证

```
# KFold
kf = Rumale::ModelSelection::StratifiedKFold.new(
  n_splits: 5,
  random_seed: 1
)

cv = Rumale::ModelSelection::CrossValidation.new(
  estimator: model,
  splitter: kf
)
report = cv.perform(samples, labels) 
```

Enter fullscreen mode Exit fullscreen mode

## 结果

```
scores = report[:test_score]
puts scores.sum / scores.size
# 0.9466666666666667 
```

Enter fullscreen mode Exit fullscreen mode

## 学习和预测

```
# Learning
model.fit(samples, labels)

# Predicting
# accept 2D NArray  (Numo::DFloat#shape=[150,4])
p model.predict(samples).to_a 
```

Enter fullscreen mode Exit fullscreen mode

## 保存并加载模型

```
# Save a model
File.binwrite("model.dat", Marshal.dump(model))

# Load a model
model = Marshal.load(File.binread("model.dat")) 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！