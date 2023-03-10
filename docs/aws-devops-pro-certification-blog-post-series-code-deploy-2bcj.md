# AWS DevOps Pro 认证博客文章系列:代码部署

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-code-deploy-2bcj>

凯瑟琳·麦科马克在 Unsplash 上的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我尝试完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以一定要定期重温博客帖子。

## 什么？

代码构建是一项托管部署服务。

## 为什么？

部署服务通常是构建场的一部分，因此也会增加基础设施支出。

## 什么时候？

SDLC 自动化:`CodeCommit -> CodeBuild -> [CodeDeploy]`

## 如何？

这基于部署到 EC2 自动扩展组的教程。关键的区别在于，我将它精简为只包含在使用 Amazon v2 AMI 的基于 Linux/MacOS 的机器上使用的指令。

### 创建 CodeDeploy 服务角色和 EC2 IAM 实例概要文件

这是[入门](https://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-codedeploy.html)指南的一部分。

注意:我们已选择使用托管策略来部署到 EC2/内部计算平台。

复制下面的 JSON 对象并粘贴到一个名为`CodeDeployDemo-Trust.json`
的新文件中

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "",  "Effect":  "Allow",  "Principal":  {  "Service":  [  "codedeploy.amazonaws.com"  ]  },  "Action":  "sts:AssumeRole"  }  ]  } 
```

```
aws iam create-role \
  --role-name CodeDeployServiceRole \
  --assume-role-policy-document file://CodeDeployDemo-Trust.json

aws iam attach-role-policy \
  --role-name CodeDeployServiceRole \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSCodeDeployRole 
```

复制下面的 JSON 对象并粘贴到一个名为`CodeDeployDemo-EC2-Trust.json`
的文件中

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "",  "Effect":  "Allow",  "Principal":  {  "Service":  "ec2.amazonaws.com"  },  "Action":  "sts:AssumeRole"  }  ]  } 
```

复制下面的 JSON 对象并粘贴到一个名为`CodeDeployDemo-EC2-Permissions.json`
的文件中

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Action":  [  "s3:Get*",  "s3:List*"  ],  "Effect":  "Allow",  "Resource":  "*"  }  ]  } 
```

```
aws iam create-role \
  --role-name CodeDeployDemo-EC2-Instance-Profile \
  --assume-role-policy-document file://CodeDeployDemo-EC2-Trust.json

aws iam put-role-policy \
  --role-name CodeDeployDemo-EC2-Instance-Profile 
  --policy-name CodeDeployDemo-EC2-Permissions 
  --policy-document file://CodeDeployDemo-EC2-Permissions.json

aws iam create-instance-profile \
  --instance-profile-name CodeDeployDemo-EC2-Instance-Profile

aws iam add-role-to-instance-profile \
  --instance-profile-name CodeDeployDemo-EC2-Instance-Profile \
  --role-name CodeDeployDemo-EC2-Instance-Profile 
```

### 创建自动缩放组(ASG)

复制下面的脚本并粘贴到一个名为`instance-setup.sh`的新文件中。这将安装 CodeDeploy 代理，并将应用程序部署到由部署组关联的实例(通过 ASG)。

```
#!/bin/bash
yum -y update
yum install -y ruby
cd /home/ec2-user
curl -O https://aws-codedeploy-????.s3.amazonaws.com/latest/install
chmod +x ./install
./install auto 
```

编辑`????`以反映您所在的地区，即如果您所在的地区是巴黎，则值为`eu-west-3`。

我们要定义的环境变量是:

*   AMI_ID =您所在地区的 Amazon v2 AMI
*   KEY_NAME =您在该地区的密钥对
*   AZ =我们地区的可用区域

这里有几个 aws cli 命令，可以帮助您为自己的环境获取正确的值。

```
# Find the Amazon v2 AMI for your region
aws ec2 describe-images --owners amazon \
  --filters \
    'Name=name,Values=amzn2-ami-hvm-2.0.????????-x86_64-gp2' \
    'Name=state,Values=available' \
  --output json | \
jq -r '.Images | sort_by(.CreationDate) | last(.[]).ImageId')

# List key pairs in your region
aws ec2 describe-key-pairs | jq -r '.KeyPairs[].KeyName | sort_by(.CreationDate)'

# List availability zones for your region
aws ec2 describe-availability-zones 
```

创建启动配置:

```
AMI_ID=__FILL_ME_IN__
KEY_NAME=__FILE_ME_IN__
aws autoscaling create-launch-configuration \
  --launch-configuration-name CodeDeployDemo-AS-Configuration \
  --image-id $AMI_ID \
  --key-name $KEY_NAME \
  --iam-instance-profile CodeDeployDemo-EC2-Instance-Profile \
  --instance-type t2.micro \
  --user-data file://instance-setup.sh 
```

创建自动缩放组:

```
set AZ eu-west-3a
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name CodeDeployDemo-AS-Group \
  --launch-configuration-name CodeDeployDemo-AS-Configuration \
  --min-size 1 \
  --max-size 1 \
  --desired-capacity 1 \
  --availability-zones $AZ 
```

运行以下命令检查 ASG 的状态。当状态为“健康运行”时，进入下一步:

```
aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names CodeDeployDemo-AS-Group \
  --query "AutoScalingGroups[0].Instances[*].[HealthStatus, LifecycleState]" \
  --output text 
```

## 将应用程序部署到 ASG

我们要定义的环境变量是:

*   服务角色 ARN =我们在实验开始时创建的服务角色
*   地区=我们的地区

```
SERVICE_ROLE_ARN=$(aws iam get-role --role-name CodeDeployServiceRole --query "Role.Arn" --output text)
REGION eu-west-3
BUCKET_NAME aws-codedeploy-$REGION

aws deploy create-application --application-name SimpleDemoApp

aws deploy create-deployment-group \
  --application-name SimpleDemoApp \
  --auto-scaling-groups CodeDeployDemo-AS-Group \
  --deployment-group-name SimpleDemoDG \
  --deployment-config-name CodeDeployDefault.OneAtATime \
  --service-role-arn $SERVICE_ROLE_ARN

DEPLOYMENT_ID=$(aws deploy create-deployment \
  --application-name SimpleDemoApp \
  --deployment-config-name CodeDeployDefault.OneAtATime \
  --deployment-group-name SimpleDemoDG \
  --s3-location bucket=$BUCKET_NAME,bundleType=zip,key=samples/latest/SampleApp_Linux.zip | jq -r .deploymentId) 
```

### AppSpec 文件

让我们下载一份示例应用程序，并检查其内容:

```
 curl -LO https://s3.$REGION.amazonaws.com/$BUCKET_NAME/samples/latest/SampleApp_Linux.zip
 unzip SampleApp_Linux.zip
 tree # to see the structure of the app
 cat appspec.yml 
```

存档的内容是:

*   我们将通过 CodeDeploy 部署的网页
*   安装/停止/启动 web 服务器的脚本
*   [AppSpec](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html) 文件，CodeDeploy 代理将使用该文件将网页部署到部署组中的每个实例。

让我们回到我们的部署。

继续检查部署，直到以下命令输出“成功”。

```
aws deploy get-deployment --deployment-id $DEPLOYMENT_ID \
  --query "deploymentInfo.status" \
  --output text 
```

提示:如果在下载阶段收到拒绝访问错误，EC2 IAM 实例配置文件可能配置不正确，或者没有附加允许访问 S3 的策略。

让我们通过获取实例的公共地址来验证我们的部署是否有效。

```
INSTANCE_ID=$(aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names CodeDeployDemo-AS-Group \
  --query "AutoScalingGroups[0].Instances[*].InstanceId" --output text)

curl $(aws ec2 describe-instances \
  --instance-id $INSTANCE_ID \
  --query "Reservations[0].Instances[0].PublicDnsName" \
  --output text) 
```

### 增加 ASG 中的实例数量

我们将通过 ASG 增加实例计数，使其总数达到 2。

```
aws autoscaling update-auto-scaling-group \
  --auto-scaling-group-name CodeDeployDemo-AS-Group \
  --min-size 2 \
  --max-size 2 \
  --desired-capacity 2 
```

继续检查 ASG 中的实例，只有当它们都“正常运行”时，才继续下一步。

```
aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names CodeDeployDemo-AS-Group \
  --query "AutoScalingGroups[0].Instances[*].[HealthStatus, LifecycleState]" \
  --output text 
```

现在检查部署的状态，它应该是“Succeeded”。

```
DEPLOYMENT_ID=$(aws deploy list-deployments \
  --application-name SimpleDemoApp \
  --deployment-group-name SimpleDemoDG \
  --query "deployments" | jq -r 'last(.[])')

aws deploy get-deployment \
  --deployment-id $DEPLOYMENT_ID \
  --query "deploymentInfo.[status, creator]" \
  --output text 
```

接下来，让我们卷曲所有的实例来验证我们得到了一个工作站点。

```
INSTANCE_IDS=$(aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names CodeDeployDemo-AS-Group \
  --query "AutoScalingGroups[0].Instances[*].InstanceId" \
  --output text)

  --instance-ids $INSTANCE_IDS \
  --query "Reservations[0].Instances[0].PublicDnsName" \
  --output text

aws ec2 describe-instances \
  --instance-ids $INSTANCE_IDS \
  --query "Reservations[*].Instances[*].PublicDnsName" \
  | jq -r .[][] | xargs curl {} 
```

## 清理干净

```
aws autoscaling delete-auto-scaling-group \
  --auto-scaling-group-name CodeDeployDemo-AS-Group \
  --force-delete

aws autoscaling delete-auto-scaling-group \
  --auto-scaling-group-name CodeDeployDemo-AS-Group \
  --force-delete

aws deploy delete-application \
  --application-name SimpleDemoApp 
```

## API 和 CLI 特性和动词

### 特性

*   部署
    *   部署组
    *   应用程序修订
*   应用程序
*   部署配置
*   内部实例

### 动词(CRUD)

*   创造
*   批处理-获取/获取/列出/描述
*   更新/上传
*   删除

### 离群值

*   现场注册实例
*   批量获取内部部署实例
*   将标记添加到本地实例
*   从本地实例中删除标记
*   取消注册-内部-实例
*   继续部署
*   停止部署
*   删除-git-hub-account-令牌
*   注销
*   安装
*   推
*   注册
*   注册-申请-修订
*   放置-生命周期-事件-挂钩-执行-状态

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*