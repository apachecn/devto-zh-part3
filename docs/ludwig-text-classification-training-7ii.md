# 路德维希文本分类训练

> 原文：<https://dev.to/0xkoji/ludwig-text-classification-training-7ii>

优步/路德维希
T1】https://github.com/uber/ludwig

Ludwig 是一个建立在 TensorFlow 之上的工具箱，它允许训练和测试深度学习模型，而无需…
github.com

### 安装

```
$ pip install ludwig
$ python -m spacy download en 
```

Enter fullscreen mode Exit fullscreen mode

### 被测文本-分类

```
$ mkdir text-classification
$ cd text-classification
$ wget http://boston.lti.cs.cmu.edu/classes/95-865-K/HW/HW2/reuters-allcats-6.zip
$ unzip reuters-allcats-6.zip
$ vim model_definition.yaml 
```

Enter fullscreen mode Exit fullscreen mode

`model_definition.yam`

```
input_features:
    -
        name: text
        type: text
        encoder: parallel_cnn
        level: word
output_features:
    -
        name: class
        type: category 
```

Enter fullscreen mode Exit fullscreen mode

`train.sh`

```
#!/bin/sh

ludwig experiment \
  --data_csv reuters-allcats.csv \
  --model_definition_file model_definition.yaml 
```

Enter fullscreen mode Exit fullscreen mode

### 可视化

```
$ ludwig visualize --visualization learning_curves --training_statistics ./results/experiment_run_0/training_statistics.json
_         _        _      
| |_  _ __| |_ __ _(_)__ _ 
| | || / _` \ V  V / / _` |
|_|\_,_\__,_|\_/\_/|_\__, |
                     |___/ 
ludwig v0.1.0 - Experiment 
```

Enter fullscreen mode Exit fullscreen mode