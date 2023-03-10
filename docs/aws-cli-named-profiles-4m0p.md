# AWS: CLI 命名配置文件

> 原文：<https://dev.to/setevoy/aws-cli-named-profiles-4m0p>

[![aws-logo-square-02](img/9cf5c5b689deb4884e18a510f67d47cf.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s---dkzETw5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2014/11/aws-logo-square-02-e1417012834176-1.png) AWS CLI 支持所谓的 *[命名概要文件](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)* 存储在配置文件`.aws/config`和`~/.aws/credentials`文件中，文件中的关键字默认包含……嗯——*默认*概要文件。

可使用带有`--profile`选项的`aws configure`命令添加一个额外的轮廓。

下面是一个已经存在两个概要文件的例子:

```
$ cat ~/.aws/credentials
[default]
aws_access_key_id = AKI***6GA
aws_secret_access_key = cbd***M6W
[TAG]
aws_access_key_id = AKI***FJQ
aws_secret_access_key = 6T***4sG 
```

添加一个新的–执行:

```
$ aws configure --profile example
AWS Access Key ID [None]: 123456
AWS Secret Access Key [None]: 0123456789
Default region name [None]: eu-west-1
Default output format [None]: json 
```

现在检查它们:

```
$ cat ~/.aws/credentials
[default]
aws_access_key_id = AKI***6GA
aws_secret_access_key = cbd***M6W
[TAG]
aws_access_key_id = AKI***FJQ
aws_secret_access_key = 6T/***4sG
[example]
aws_access_key_id = 123456
aws_secret_access_key = 0123456789 
```

和配置文件:

```
$ cat ~/.aws/config
[default]
aws_access_key_id = AKI***TCQ
region = eu-west-1
output = json
aws_secret_access_key = nSD***dj+
[preview]
cloudfront = true
[profile example]
output = json
region = eu-west-1 
```

要使用其中一个配置文件运行命令，使用`--profile`选项。

例如，使用*默认*一:

```
$ aws s3 ls
2016-05-10 12:35:03 elasticbeanstalk-eu-west-1
2016-08-29 17:45:34 rtfmbackups
2016-09-15 18:01:53 static-site-example 
```

类似地，但是使用*标签*配置文件:

```
$ aws s3 ls --profile TAG
2016-04-06 18:16:08 elasticbeanstalk-eu-west-1
2016-03-15 18:42:46 elasticbeanstalk-us-west-2
2016-05-09 19:22:16 profile-dev.connected.com
2016-05-09 21:09:40 profile-staging.connected.com
2016-08-17 16:02:28 profileapp.connected.com
2015-07-23 01:05:41 tag-cms-media-dev
2015-08-13 00:22:57 tag-cms-media-prod
2015-08-13 00:23:12 tag-cms-media-staging
2015-07-14 21:31:41 tag-ecs-config
... 
```

此外，您可以使用`$AWS_DEFAULT_PROFILE`环境变量:
设置您的默认配置文件

```
$ export AWS_DEFAULT_PROFILE=TAG
$ aws s3 ls
2016-04-06 18:16:08 elasticbeanstalk-eu-west-1
2016-03-15 18:42:46 elasticbeanstalk-us-west-2
2016-05-09 19:22:16 profile-dev.connected.com
2016-05-09 21:09:40 profile-staging.connected.com
2016-08-17 16:02:28 profileapp.connected.com
... 
```

更多详情请见[文档> > >](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-multiple-profiles) 。

### 类似的帖子

*   <small>10/17/2016</small> [AWS:您试图访问的桶必须使用指定的端点进行寻址。](https://rtfm.co.ua/aws-the-bucket-you-are-attempting-to-access-must-be-addressed-using-the-specified-endpoint/) <small>(0)</small>
*   <small>2017 年 9 月 13 日</small>[【AWS:云锋】【SSL′k′k′k′k〖T3〗【0】](https://rtfm.co.ua/aws-cloudfront-update-ssl-sertifikat/)
*   <small>09/30/2017</small>[AWS:миграцияrtfm 2.1-cloud formationдляec2c Jenkins](https://rtfm.co.ua/aws-cloudformation-dlya-ec2-c-jenkins/)<small>(0)</small>
*   <small>03/17/2019</small>[Terraform:AWS](https://dev.to/setevoy/terraform-main-commands-state-files-backend-storages-and-modules-in-examples-on-aws-5ehb)<small>(0)</small>上示例中的主命令、状态文件、后端存储和模块