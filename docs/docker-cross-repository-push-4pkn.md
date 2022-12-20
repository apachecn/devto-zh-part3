# Docker 跨存储库推送

> 原文：<https://dev.to/tathagata/docker-cross-repository-push-4pkn>

[![header](img/f9c04f448c04af2d6e79e0e17e234dcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A10mdbQh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aes0ixj8nlw39n6p9ibv.jpg) 
刚发现 dev.to 就立马爱上了复古🤣看啊！我没有花太多心思，而是开始绞尽脑汁——主要是想那些一开始就行不通的事情。没有任何努力是有益的-打字错误，错误，误传可能会提前。欢迎来到互联网！

### 问题:需要将 docker 映像从一个 AWS 帐户推送到另一个帐户

ECR 是由 AWS 提供的 docker 存储库服务。为了更好地分离您的环境，您通常会有一个 prod 帐户和一个或多个非 prod 帐户。您的 CI 管道将在您的非 prod 帐户中运行，允许您快速迭代和构建新的映像。一旦您的测试周期完成，您可能希望将该映像提交给生产客户。下面是将您的发布候选从非生产提升到生产所需的一组命令。

#### 登录您的 docker 注册源账户

```
aws ecr --profile dev --region us-east-1 get-login --no-include-email | bash 
```

相当直接。进行一个 cli 调用，返回通过管道在 bash 中执行的`docker login`命令。`dev`是包含 docker 图像的帐户的 aws 配置文件。

#### CI 制作的 docker 图像

```
docker pull 123456789012.dkr.ecr.us-east-1.amazonaws.com/img:aa567e02ae7477a6b022da30fa50137a252dd143 
```

在 123456789012.dkr.ecr.us-east-1.amazonaws.com，`123456789012`代表我们 docker 镜像所在的 aws 账号。
假设您已经完成了测试，现在您满意地将其推广到生产。

#### 切换储存库并试推

登录到产品

```
aws ecr --profile prod --region eu-west-1 get-login --no-include-email | bash 
```

试推

```
docker push 234567890121.dkr.ecr.us-east-1.amazonaws.com/img:ab77ad7b362d7552bc2c7a69f2650a5b50bcdcc1 
```

和...

```
The push refers to repository [234567890121.dkr.ecr.us-east-1.amazonaws.com/img]
tag does not exist: 234567890121.dkr.ecr.us-east-1.amazonaws.com/img:aa567e02ae7477a6b022da30fa50137a252dd143 
```

#### 呃哦忘记标记了

这对我来说不是很明显。但是 docker 虽然很棒，却给出了上面的好的错误消息，告诉你哪里出错了。要推送至 prod 注册表，您需要相应地标记图像。

```
docker tag 123456789012.dkr.ecr.us-east-1.amazonaws.com/img:aa567e02ae7477a6b022da30fa50137a252dd143 234567890121.dkr.ecr.us-east-1.amazonaws.com/img:aa567e02ae7477a6b022da30fa50137a252dd143 
```

#### 推开

最后推送图片就大功告成了。

```
docker push 234567890121.dkr.ecr.us-east-1.amazonaws.com/img:ab77ad7b362d7552bc2c7a69f2650a5b50bcdcc1 
```

恭喜你，你提升了形象！

#### 等待。上面的图片和这个帖子有什么关系？

一件都没有。这是我在锡耶纳拍的照片。❤️Italy，等不及要回去了！