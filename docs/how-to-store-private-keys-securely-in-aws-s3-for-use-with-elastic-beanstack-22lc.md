# 如何在 AWS S3 中安全地存储私钥，以便与 Elastic Beanstalk 一起使用

> 原文：<https://dev.to/eesnaola/how-to-store-private-keys-securely-in-aws-s3-for-use-with-elastic-beanstack-22lc>

您在项目中使用的私钥不应该与源代码相冲突。最好的选择是配置 Elastic Beanstalk 在应用程序部署期间从 AWS S3 下载文件。

下面的例子展示了一个 Elastic Beanstalk 的配置文件[从一个 S3 桶中获取一个私钥文件。](https://docs.aws.amazon.com/en_en/elasticbeanstalk/latest/dg/ebextensions.html) 

```
# .ebextensions/serverkey.config
Resources:
  AWSEBAutoScalingGroup:
    Metadata:
      AWS::CloudFormation::Authentication:
        S3Auth:
          type: "s3"
          buckets: ["elasticbeanstalk-region-account-id"]
          roleName: 
            "Fn::GetOptionSetting": 
              Namespace: "aws:autoscaling:launchconfiguration"
              OptionName: "IamInstanceProfile"
              DefaultValue: "aws-elasticbeanstalk-ec2-role"
files:
  # Private key
  "/etc/pki/tls/certs/server.key":
    mode: "000400"
    owner: webapp
    group: webapp
    authentication: "S3Auth"
    source: https://s3.amazonaws.com/elasticbeanstalk-region-account-id/server.key 
```

Enter fullscreen mode Exit fullscreen mode

实例概要文件“aws-elasticbeanstalk-ec2-role”必须具有从指定存储桶读取密钥对象的权限。看[这里](https://docs.aws.amazon.com/en_en/elasticbeanstalk/latest/dg/iam-instanceprofile.html#iam-instanceprofile-addperms)看看怎么做。

您使用这样的环境变量设置了 URL:

```
source: { "Fn::Join" : ["", ["https://s3.amazonaws.com/elasticbeanstalk-region-account-id/", {"Fn::GetOptionSetting": {"Namespace": "aws:elasticbeanstalk:application:environment", "OptionName": "APP_ENV"}}, ".key"]]} 
```

Enter fullscreen mode Exit fullscreen mode

希望你有美好的一天！