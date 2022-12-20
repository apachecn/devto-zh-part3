# AWS DevOps Pro 认证博文系列:弹性豆茎

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-elastic-beanstalk-2o1n>

迈克尔·沃尔特在 Unsplash 上的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我试图完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以请确保定期重温博客帖子。

## 什么？

弹性豆茎...

*   是一个平台即服务(就像 Heroku，Netlify)，你部署你的代码，它提供你的应用运行所需的服务器。
*   是由幕后的云形成提供动力的。
*   (Elastic Beanstalk)扩展相当于 EC2 实例的用户数据字段，因为您可以添加一些需要在服务器供应期间运行的任务，即在 windows 上启用自动更新。
*   附带一个 cli (eb)，这主要是针对开发人员给他们提供类似于 Heroku 等其他提供商的 PaaS 体验，即链接您的版本控制系统(Git 等), Elastic Beanstalk 将完成其余工作。与 aws cli 相比，eb cli 有很多简化，例如`eb create my-env`(创建环境)需要三个 aws cli 弹性 Beanstalk 命令:`check-dns-availability`、`create-application-version`和`create-environment`。

其他资源:

*   [Elastic Beanstalk][devto _ Elastic Beanstalk]的 dev.to 标签
*   [弹性豆茎常见问题解答](https://aws.amazon.com/elasticbeanstalk/faqs/)
*   [弹性豆茎用户指南](https://aws.amazon.com/elasticbeanstalk/)
*   [弹性豆茎 API](https://docs.aws.amazon.com/elasticbeanstalk/latest/api/Welcome.html)
*   [弹性豆茎 CLI](https://docs.aws.amazon.com/cli/latest/reference/elasticbeanstalk/index.html)

## 为什么？

在 AWS 提供的所有编排工具中，这可能是最容易使用的。就像 Heroku 开创了这种简化应用程序部署的方法一样，这是针对那些不想参与供应基础架构的操作方面的开发人员的。

回想一下云形成的复杂程度，这并不适合每个人。

## 什么时候？

*   时间有限或不愿意学习更复杂但最终高度可配置的编排工具，如 CloudFormation。
*   手边没有系统管理员
*   希望快速发布您的应用程序，而不担心基础架构的细节。

## 如何？

我们将通过 AWS 提供的[示例应用](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/tutorials.html)中的一个来工作，下载 Go 示例(`go-v1.zip`)。环境设置命令也是抄袭自[用户指南](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environments-create-awscli.html)。

重要提示:你需要一个 S3 桶来储存溶液。

```
export APP_NAME=hello-eb
export APP_BUCKET=your-bucket

# copy the zip file to your bucket
aws s3 cp path/to/downloaded/go-v1.zip s3://$APP_BUCKET/

# create the application
aws elasticbeanstalk create-application --application-name $APP_NAME
{
    "Application": {
        "ApplicationArn": "arn:aws:elasticbeanstalk:eu-west-3:xxx:application/hello-eb",
        "ApplicationName": "hello-eb",
        "DateCreated": "2019-05-29T16:03:18.885Z",
        "DateUpdated": "2019-05-29T16:03:18.885Z",
        "ConfigurationTemplates": [],
        "ResourceLifecycleConfig": {
            "VersionLifecycleConfig": {
                "MaxCountRule": {
                    "Enabled": false,
                    "MaxCount": 200,
                    "DeleteSourceFromS3": false
                },
                "MaxAgeRule": {
                    "Enabled": false,
                    "MaxAgeInDays": 180,
                    "DeleteSourceFromS3": false
                }
            }
        }
    }
}

# create the application version
aws elasticbeanstalk create-application-version \
  --application-name $APP_NAME \
  --version-label v1 \
  --source-bundle S3Bucket="$APP_BUCKET",S3Key="go-v1.zip"
{
    "ApplicationVersion": {
        "ApplicationVersionArn": "arn:aws:elasticbeanstalk:eu-west-3:x:applicationversion/hello-eb/v1",
        "ApplicationName": "hello-eb",
        "VersionLabel": "v1",
        "SourceBundle": {
            "S3Bucket": "your_bucket",
            "S3Key": "go-v1.zip"
        },
        "DateCreated": "2019-05-29T16:07:20.796Z",
        "DateUpdated": "2019-05-29T16:07:20.796Z",
        "Status": "UNPROCESSED"
    }
}

# create a configuration template, this tells Elastic Beanstalk which
# specialised server image to use
aws elasticbeanstalk create-configuration-template \
  --application-name $APP_NAME \
  --template-name v1 \
  --solution-stack-name "64bit Amazon Linux 2018.03 v2.11.1 running Go 1.12.4"  
{
    "SolutionStackName": "64bit Amazon Linux 2018.03 v2.11.1 running Go 1.12.4",
    "PlatformArn": "arn:aws:elasticbeanstalk:eu-west-3::platform/Go 1 running on 64bit Amazon Linux/2.11.1",
    "ApplicationName": "hello-eb",
    "TemplateName": "v1",
    "DateCreated": "2019-05-29T16:09:58Z",
    "DateUpdated": "2019-05-29T16:09:58Z"
}

# to get a list of the prebuilt platforms you can use
aws elasticbeanstalk list-available-solution-stacks | jq -r '.SolutionStacks[]'
64bit Amazon Linux 2018.03 v2.8.3 running Python 3.6
64bit Amazon Linux 2018.03 v2.8.3 running Python 3.4
64bit Amazon Linux 2018.03 v2.8.3 running Python
64bit Amazon Linux 2018.03 v2.8.3 running Python 2.7
*snip*

# create the environment, well need to tell Elastic Beanstalk what IAM role to use to create ec2 instances
cat options.txt
[
    {
        "Namespace": "aws:autoscaling:launchconfiguration",
        "OptionName": "IamInstanceProfile",
        "Value": "aws-elasticbeanstalk-ec2-role"
    }
]

# create the environment
aws elasticbeanstalk create-environment \
  --cname-prefix $APP_NAME \
  --application-name $APP_NAME \
  --template-name v1 --version-label v1 \
  --environment-name $APP_NAME-env \
  --option-settings file://options.txt
{
    "EnvironmentName": "hello-eb-env",
    "EnvironmentId": "e-g8pwbwxc5j",
    "ApplicationName": "hello-eb",
    "VersionLabel": "v1",
    "SolutionStackName": "64bit Amazon Linux 2018.03 v2.11.1 running Go 1.12.4",
    "PlatformArn": "arn:aws:elasticbeanstalk:eu-west-3::platform/Go 1 running on 64bit Amazon Linux/2.11.1",
    "CNAME": "hello-eb.eu-west-3.elasticbeanstalk.com",
    "DateCreated": "2019-05-29T16:11:32.355Z",
    "DateUpdated": "2019-05-29T16:11:32.355Z",
    "Status": "Launching",
    "Health": "Grey",
    "Tier": {
        "Name": "WebServer",
        "Type": "Standard",
        "Version": "1.0"
    },
    "EnvironmentArn": "arn:aws:elasticbeanstalk:eu-west-3:xxx:environment/hello-eb/hello-eb-env"
}

# examine the status of the new environment
aws elasticbeanstalk describe-environments --environment-names $APP_NAME-env
{
    "Environments": [
        {
            "EnvironmentName": "hello-eb-env",
            "EnvironmentId": "e-g8pwbwxc5j",
            "ApplicationName": "hello-eb",
            "VersionLabel": "v1",
            "SolutionStackName": "64bit Amazon Linux 2018.03 v2.11.1 running Go 1.12.4",
            "PlatformArn": "arn:aws:elasticbeanstalk:eu-west-3::platform/Go 1 running on 64bit Amazon Linux/2.11.1",
            "EndpointURL": "awseb-e-g-AWSEBLoa-1S8LUETQTXR9H-746312279.eu-west-3.elb.amazonaws.com",
            "CNAME": "hello-eb.eu-west-3.elasticbeanstalk.com",
            "DateCreated": "2019-05-29T16:11:32.324Z",
            "DateUpdated": "2019-05-29T16:14:11.172Z",
            "Status": "Ready",
            "AbortableOperationInProgress": false,
            "Health": "Green",
            "Tier": {
                "Name": "WebServer",
                "Type": "Standard",
                "Version": "1.0"
            },
            "EnvironmentLinks": [],
            "EnvironmentArn": "arn:aws:elasticbeanstalk:eu-west-3:xxx:environment/hello-eb/hello-eb-env"
        }
    ]
}

# Once the status is Ready and health is Green, you can open the fully qualified domain name in `CNAME`

curl -sv http://$APP_NAME.eu-west-3.elasticbeanstalk.com/ | head
*snip*
*< HTTP/1.1 200 OK
< Accept-Ranges: bytes
< Content-Type: text/html; charset=utf-8
< Date: Wed, 29 May 2019 16:15:33 GMT
< Last-Modified: Thu, 17 Sep 2015 17:53:06 GMT
< Server: nginx/1.14.1
< Content-Length: 3049
< Connection: keep-alive
<
{ [1208 bytes data]
* Connection #0 to host hello-eb.eu-west-3.elasticbeanstalk.com left intact
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
  <!--
    Copyright 2015 Amazon.com, Inc. or its affiliates. All Rights Reserved.

    Licensed under the Apache License, Version 2.0 (the "License"). You may not use this file except in compliance with the License. A copy of the License is located at

        http://aws.Amazon/apache2.0/

# Let's tear down

aws elasticbeanstalk terminate-environment \
    --environment-name $APP_NAME-env

aws elasticbeanstalk delete-configuration-template \
    --application-name $APP_NAME --template-name v1

aws elasticbeanstalk delete-application \
    --application-name $APP_NAME 
```

注意:我注意到在删除应用程序时有一些延迟。您可能需要在 AWS 控制台上进行检查，以确认所有资源都已被删除。

## API 和 CLI 特性和动词

**特性**

*   应用程序
    *   版本
    *   资源生命周期
*   环境
    *   配置
*   平台版本
*   储存；储备

* *动词(CRUD) **

*   应用程序

    *   创建(应用程序[版本])
    *   描述(应用程序)
    *   更新(应用程序[资源生命周期])
    *   删除(应用程序[版本])
*   环境

*   平台版本

*   储存；储备

**离群值**

*   大量

    *   CancelUpdateStack
    *   ContinueUpdateRollback
    *   描述 StackDriftDetectionStatus
    *   估计模板成本
    *   GetTemplateSummary
    *   更新终止保护
    *   验证模板
*   更改集合

    *   执行变更集
*   堆栈集

    *   StopStackSetOperation

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*