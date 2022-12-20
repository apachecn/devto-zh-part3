# AWS DevOps Pro 认证博文系列:AWS ECS

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-aws-ecs-38kp>

chuttersnap 在 [Unsplash](https://unsplash.com/photos/9cCeS9Sg6nU) 上拍摄的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我试图完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以请确保定期重温博客帖子。

## 什么？

AWS ECS...

*   是亚马逊的 Docker 托管服务。自然地，您也获得了一个容器注册服务，其形式为[弹性容器注册](https://aws.amazon.com/ecr/) (ECR)。
*   有两种类型(发射类型):EC2 或 Fargate。
    *   顾名思义，基于 EC2 的集群使用 EC2 实例作为容器的 Docker 主机。
    *   您需要为增加的资源付费，例如 EC2 实例和/或 EBS 卷。
    *   您还负责维护 EC2 实例(打补丁、监控和确保安全)。
    *   Fargate 抽象出 EC2 实例(很像无服务器函数)。
    *   您为容器使用的 vCPU(虚拟 CPU)和内存资源付费。这是按每秒的速率收费的(最低收费为一分钟)。
    *   Fargate 仅在少数[地区](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html)可用(撰写本文时为 13 个)
    *   不需要维护 EC2 实例，只需要维护您的任务。
*   不是 Kubernetes 托管服务，这是一个单独的服务，它有一个吸引人的名字[弹性容器服务，用于 Kubernetes 服务](https://aws.amazon.com/eks/) (EKS)

其他资源:

*   [AWS ECS 用户指南](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)
*   [AWS ECS 常见问题解答](https://aws.amazon.com/ecs/faqs/)
*   [AWS ECS API](https://docs.aws.amazon.com/AmazonECS/latest/APIReference/Welcome.html)
*   [AWS ECS CLI](https://docs.aws.amazon.com/cli/latest/reference/ecs/index.html)

## 为什么？

*   与所有托管服务一样，您希望关注服务的功能而不是维护。
*   随着 Docker 成为云的通用语言，您可以利用第三方映像并以构建块的方式构建解决方案。诚然，在 Docker 成为主流之前，Amazon 已经使用这个模块化概念很多年了(OpsWorks 和 Elastic Beanstalk)。

## 什么时候？

*   微服务和批处理作业是集群的理想工作负载。
*   您希望从通过内部基础架构管理的 Docker 或不受 ECS 管理的 EC2 实例中迁移出来。

## 如何？

在本节中，我们将使用 Fargate，而不是基于 EC2 的 ECS 集群。

这主要是为了降低供应将加入 ECS 集群的 EC2 实例的额外复杂性。本指南大致基于开发者指南中的教程。我刚刚删除了关于设置服务的部分，并使用我们可以访问的公共 IP 运行了一个任务来进行测试。

也就是说，对于日常工作，您可能会发现自己仍然在使用基于 EC2 的集群，直到 Fargate 在所有地区都可用。从节约成本的角度来看，您可能还会发现自己在使用基于 EC2 的集群。如果任何人有他们创造的计算器，当使用 Fargate launch-type 比 EC2 更有意义时，请在评论中分享或在 Twitter 上@ me！

### 先决条件

*   至少有一个子网可以入站和出站访问互联网的 VPC。
*   根据上一点选择合适的安全组

```
export ECS_CLUSTER_NAME=your-cluster-name
export ECS_SECURITY_GROUP=your-security-group
export ECS_SUBNETS=yoursubnet

# create a cluster
aws ecs create-cluster --cluster-name $ECS_CLUSTER_NAME

# register a task

cat <<EOF > fargate-task.json
{
  "family": "sample-fargate",
  "networkMode": "awsvpc",
  "containerDefinitions": [
    {
      "name": "fargate-app",
      "image": "httpd:2.4",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp"
        }
      ],
      "essential": true,
      "entryPoint": ["sh", "-c"],
      "command": [
        "/bin/sh -c \"echo '<html> <head> Hello dev.to-ers <style>body {margin-top: 40px; background-color: #333;} </style> </head><body> <div style=color:white;text-align:center> <h1>Hello, world!</h1> </div></body></html>' >  /usr/local/apache2/htdocs/index.html && httpd-foreground\""
      ]
    }
  ],
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512"
} EOF aws ecs register-task-definition \
  --cli-input-json file://fargate-task.json

# create a task, assign it to our network and enable the public IP

aws ecs run-task \
  --cluster $ECS_CLUSTER_NAME \
  --task-definition sample-fargate:2 \
  --count 1 \
  --launch-type "FARGATE" \
  --network-configuration "awsvpcConfiguration={subnets=[$ECS_SUBNETS],securityGroups=[$ECS_SECURITY_GROUP],assignPublicIp="ENABLED"}"

# list tasks

aws ecs list-tasks --cluster $ECS_CLUSTER_NAME

# describe the task

export ECS_TASK_ID=$(aws ecs list-tasks --cluster $ECS_CLUSTER_NAME --query "taskArns" --output text)
aws ecs describe-tasks --cluster $ECS_CLUSTER_NAME --tasks $ECS_TASK_ID

# get the public IP of the task (well, the one bound to the ENI)

export ECS_TASK_NETWORK_ID=$(aws ecs describe-tasks --cluster $ECS_CLUSTER_NAME --tasks $ECS_TASK_ID --query 'tasks[*].attachments[*].details[?name==`networkInterfaceId`].value' --output text)

export ECS_TASK_PUBLIC_IP=$(aws ec2 describe-network-interfaces --network-interface-ids $ECS_TASK_NETWORK_ID --query "NetworkInterfaces[*].PrivateIpAddresses[*].Association.PublicIp" --output text)

# test

curl $ECS_TASK_PUBLIC_IP

# tear down

aws ecs stop-task --task $ECS_TASK_ID --cluster $ECS_CLUSTER_NAME
aws ecs delete-cluster --cluster $ECS_CLUSTER_NAME 
```

## API 和 CLI 特性和动词

**特性**

*   簇
*   服务
*   任务集

**动词(CRUD)**

*   创造
*   描述/列出(集群/服务)
*   更新(服务/任务集)
*   删除

**离群值**

*   删除帐户设置
*   删除-属性
*   部署
*   注销-容器-实例
*   注销-任务-定义
*   描述-容器-实例
*   描述-任务-定义
*   描述-任务
*   发现-轮询-端点
*   列表-帐户-设置
*   列表-属性
*   列表-容器-实例
*   资源的列表标记
*   列表-任务-定义-系列
*   列表-任务-定义
*   列表-任务
*   记账设置
*   put-帐户-设置-默认
*   put 属性
*   注册-容器-实例
*   注册任务定义
*   运行任务
*   开始-任务
*   停止任务
*   提交-容器-状态-更改
*   提交-任务-状态-更改
*   标签资源
*   UNTAG-资源
*   更新-容器-代理
*   更新-容器-实例-状态
*   更新服务主要任务集
*   等待

Unsplash 路径(我使用什么术语来获得封面图像):容器

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*