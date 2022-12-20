# 在 scikit-learn 中使用 SageMaker XGBoost 模型

> 原文：<https://dev.to/aws/using-a-sagemaker-xgboost-model-in-scikit-learn-1nbk>

这是一个回答我经常遇到的问题的快速帖子:“*我如何在 scikit 中使用-学习我在 SageMaker 上训练的 XGBoost 模型？*”。

开始了。一旦你在 SageMaker 中训练了你的 [XGBoost](https://docs.aws.amazon.com/sagemaker/latest/dg/xgboost.html) 模型(例子[在这里](https://github.com/awslabs/amazon-sagemaker-examples/tree/master/introduction_to_amazon_algorithms)，获取训练任务名称和模型工件的位置。

> 我在这里使用 CLI，但是您当然可以使用任何 AWS 语言 SDK。

```
$ export TRAINING\_JOB\_NAME='xgboost-190511-0830-010-14f41137'

$ export MODEL\_ARTIFACT=`aws sagemaker describe-training-job \
--training-job-name $TRAINING\_JOB\_NAME \
--query ModelArtifacts.S3ModelArtifacts \
--output text

$ echo $MODEL\_ARTIFACT
s3://sagemaker-eu-west-1-613904931467/sagemaker/DEMO-hpo-xgboost-dm/output/xgboost-190511-0830-010-14f41137/output/model.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

然后，下载工件，提取模型。

```
$ aws s3 cp $MODEL\_ARTIFACT .

$ tar xvfz model.tar.gz
x xgboost-model 
```

Enter fullscreen mode Exit fullscreen mode

该模型是一个腌制的 Python 对象，所以现在让我们切换到 Python 并加载该模型。

```
$ python3
>>> import sklearn, pickle
>>> model = pickle.load(open("xgboost-model", "rb"))
>>> type(model)
<class 'xgboost.core.Booster'> 
```

Enter fullscreen mode Exit fullscreen mode

你完了。从现在开始，你可以像在本地训练一样使用这个模型。比如你可以把它转储出来，可视化。

```
>>> model.dump\_model('model.txt')
>>> exit()

$ head model.txt
booster[0]:
0:[f2<512] yes=1,no=2,missing=1
 1:[f1<3.5] yes=3,no=4,missing=3
  3:[f2<1.5] yes=7,no=8,missing=7
   7:[f42<0.5] yes=15,no=16,missing=15
    15:leaf=0.508301735
    16:leaf=1.51004589
   8:leaf=1.72906268
  4:[f52<0.5] yes=9,no=10,missing=9
   9:leaf=1.39554036 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？那超级简单:)

感谢阅读。很高兴在这里或在 [Twitter](https://twitter.com/julsimon) 上回答问题。