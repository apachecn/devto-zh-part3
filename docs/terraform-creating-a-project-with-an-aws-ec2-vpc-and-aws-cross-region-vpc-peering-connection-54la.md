# Terraform:创建具有 AWS EC2、VPC 和 AWS 跨区域 VPC 对等连接的项目

> 原文：<https://dev.to/setevoy/terraform-creating-a-project-with-an-aws-ec2-vpc-and-aws-cross-region-vpc-peering-connection-54la>

[![](img/351bd6e55d6ae2f35407bf88a61407b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cU4la9P2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2015/10/terraform_logo-e1446187655503.png) 在我的 AWS CloudFormation 体验中，令人失望的一个惊喜是，它无法自动创建跨区域的 VPC 对等连接。

**注** : *这个帖子[原本是 2018 年 6 月 28 日用俄语](https://rtfm.co.ua/terraform-sozdanie-proekta-s-ec2-vpc-i-aws-cross-region-vpc-peering/)写的但是现在 CloudFormation 可以做了，查看 [`AWS::EC2::VPCPeeringConnection`](https://docs.aws.amazon.com/en_us/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpcpeeringconnection.html#w2ab1c21c10d111d202c10) 资源的`PeerRegion`参数。*

结果，它试图在同一区域建立连接，显然失败了。

在 AWS 论坛上发现了一个来自用户的[主题](https://forums.aws.amazon.com/thread.jspa?threadID=275496)——但是还没有 AWS 开发者的评论。

为了解决这个问题，我不得不为我们的 Jenkins 堆栈使用 Terraform(它位于欧洲，而我们的其他资源托管在美国 AWS 地区)。

下面是创建 AWS EC2、VPC 和 VPC 跨区域对等连接的示例。

在这个堆栈( *eu-west-1* )中，将运行一个 Jenkins 实例，该实例将使用 AWS VPC 对等连接到我们在 *us-east-2* 地区的 Prometheus 监控堆栈。

### 项目的结构

文件和目录如下所示:

```
$ tree terraform/
terraform/
├── ec2
│   ├── ec2_icmp_and_default_sg.tf
│   ├── ec2.tf
│   ├── jenkins_security_group.tf
│   └── variables.tf
├── main.tf
├── terraform_exec.sh
├── variables.tf
└── vpc
    ├── variables.tf
    └── vpc.tf 
```

这里的`ec2`和`vpc`目录是 [Terraform 的模块](https://rtfm.co.ua/en/terraform-main-commands-state-files-backend-storages-and-modules-in-examples-on-aws/#Terraform_modules)，`terraform_exec.sh`脚本用于运行 Terraform 的`plan` / `apply` / `destroy`命令，带有必要的选项和参数。

### 脚本

该脚本只是 Terraform 如何在 Jenkins 作业中执行的一种草案，只是为了方便起见，以避免每次都重复相同的选项集。

在我们的项目中，一个 AWS S3 桶将被用作其[状态文件](https://rtfm.co.ua/en/terraform-main-commands-state-files-backend-storages-and-modules-in-examples-on-aws/#State-files)的[后端存储](https://rtfm.co.ua/en/terraform-main-commands-state-files-backend-storages-and-modules-in-examples-on-aws/#Backends)，并在`terraform_config()`函数中被初始化。

然后，在堆栈创建期间，将调用函数`terraform_plan()`，该函数将要求确认，然后是`terraform_apply()`。同样——在`destroy`期间。

剧本本身:

```
#/usr/bin/env bash
HELP="\n\t-a: apply
\n\t-D: delete
\n\t-e: environement to be used, default to \"dev\"\n\t"
# set default action to apply
apply=1
destroy=
while getopts "aDe:h" opt; do
    case $opt in
        a)
            apply=1
            ;;
        D)
            apply=
            destroy=1
            ;;
        e)
            ENV=$OPTARG
            ;;
        h)
            echo -e $HELP
            exit 0
            ;;
        ?)
            echo -e $HELP && exit 1
            ;;
 esac
done
# global vars
[[ -z $ENV ]] && ENV="dev"
AWS_PROFILE="jenkins-ci-provisioning"
AWS_REGION="eu-west-1"
CLUSTER_NAME="jenkins-ci-$ENV"
# monitoring peering data
MON_PROD_VPC_ID="vpc-51e8b639"
MON_PROD_REGION="us-east-2"
MON_PROD_VPC_CIDR="10.0.1.0/24"
# terraform backend vars
TF_BE_S3_BUCKET="terraform-$CLUSTER_NAME"
TF_BE_S3_STATE_KEY="$TF_BE_S3_BUCKET.tfstate"
echo -e "\nENV=$ENV
AWS CLI profile: $AWS_PROFILE
AWS region: $AWS_REGION
Application cluster name: $CLUSTER_NAME
Terraform backend S3 bucket name: $TF_BE_S3_BUCKET
Terraform backend key filename: $TF_BE_S3_STATE_KEY
"
read -p "Are you sure to proceed? [y/n] " -r
if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    exit 1
fi
# load modules
terraform get
# setup backend
terraform_config () {
    terraform init \
        -backend-config="bucket=$TF_BE_S3_BUCKET" \
        -backend-config="key=$TF_BE_S3_STATE_KEY" \
        -backend-config="region=$AWS_REGION" \
        -backend-config="profile=$AWS_PROFILE"
}
terraform_plan () {
    terraform plan \
        -var env=$ENV \
        -var aws-region=$AWS_REGION \
        -var aws-profile=$AWS_PROFILE \
        -var cluster-name=$CLUSTER_NAME \
        -var monitoring-prod-vpc-id=$MON_PROD_VPC_ID \
        -var monitoring-prod-region=$MON_PROD_REGION \
        -var monitoring-prod-vpc-cidr=$MON_PROD_VPC_CIDR
}
terraform_apply () {
    terraform apply \
        -var env=$ENV \
        -var aws-region=$AWS_REGION \
        -var aws-profile=$AWS_PROFILE \
        -var cluster-name=$CLUSTER_NAME \
        -var monitoring-prod-vpc-id=$MON_PROD_VPC_ID \
        -var monitoring-prod-region=$MON_PROD_REGION \
        -var monitoring-prod-vpc-cidr=$MON_PROD_VPC_CIDR
}
terraform_destroy () {
    terraform plan -destroy \
        -var env=$ENV \
        -var aws-region=$AWS_REGION \
        -var aws-profile=$AWS_PROFILE \
        -var cluster-name=$CLUSTER_NAME \
        -var monitoring-prod-vpc-id=$MON_PROD_VPC_ID \
        -var monitoring-prod-region=$MON_PROD_REGION \
        -var monitoring-prod-vpc-cidr=$MON_PROD_VPC_CIDR
    echo
    read -p "Plan complete. Are you sure to proceed? [y/n] " -r
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        exit 1
    fi
    terraform destroy \
        -var env=$ENV \
        -var aws-region=$AWS_REGION \
        -var aws-profile=$AWS_PROFILE \
        -var cluster-name=$CLUSTER_NAME \
        -var monitoring-prod-vpc-id=$MON_PROD_VPC_ID \
        -var monitoring-prod-region=$MON_PROD_REGION \
        -var monitoring-prod-vpc-cidr=$MON_PROD_VPC_CIDR
}
apply () {
    terraform_config || exit 1
    terraform_plan || exit 1
    read -p "Plan complete. Are you sure to proceed? [y/n] " -r
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        exit 1
    fi
    echo
    terraform_apply || exit 1
}
if [[ $apply == 1 ]]; then
    echo -e "\nRunning Apply action..."
    apply
elif [[ $destroy == 1 ]]; then
    echo -e "\nRunning Destroy action..."
    terraform_destroy
else
    echo -e "\nERROR: action does not set, exiting."
    exit 1
fi 
```

### 地形

#### `main.tf`

在`main.tf`中，配置了一个后端，将创建一个提供者并获取可用区域列表。

然后将调用两个模块——`ec2`和`vpc`,它们带有必要的变量，其中一些在 gloval 变量中定义，一些来自`main.tf`本身( *eu-west-1a* 值将从`data.aws_availability_zones.available.names[0]`中获得)。

文件的内容:

```
terraform {
  backend "s3" {
  }
}
provider "aws" {
    region = "${var.aws-region}"
    profile = "${var.aws-profile}"
}
data "aws_availability_zones" "available" {
    state = "available"
}
module "vpc" {
    source = "vpc"
    env = "${var.env}"
    aws-availability-zone = "${data.aws_availability_zones.available.names[0]}"
    monitoring-prod-vpc-id = "${var.monitoring-prod-vpc-id}"
    monitoring-prod-region = "${var.monitoring-prod-region}"
    monitoring-prod-vpc-cidr = "${var.monitoring-prod-vpc-cidr}"
    aws-profile = "${var.aws-profile}"
}
module "ec2" {
    source = "ec2"
    env = "${var.env}"
    aws-availability-zone = "${data.aws_availability_zones.available.names[0]}"
    jenkins-public-subnet-id = "${module.vpc.jenkins-public-subnet-id}"
    jenkins-vpc-id = "${module.vpc.jenkins-vpc-id}"
} 
```

#### `ec2`模块

这里描述了一个 EC2 实例，一个 EBS 卷将与所有 Jenkin 的数据相关联，并且将附加一个弹性 IP。

模块内容:

```
$ ls -l ec2/
total 16
-rw-r--r-- 1 setevoy setevoy  440 Jun 27 16:22 ec2_icmp_and_default_sg.tf
-rw-r--r-- 1 setevoy setevoy 1176 Jun 27 15:20 ec2.tf
-rw-r--r-- 1 setevoy setevoy 1032 Jun 28 10:25 jenkins_security_group.tf
-rw-r--r-- 1 setevoy setevoy 1611 Jun 27 14:27 variables.tf 
```

模块的主文件-`ec2.tf`:

```
resource "aws_volume_attachment" "jenkins-data-ebs-attach" {
    device_name = "/dev/xvdb"
    volume_id = "${lookup(var.ec2-data-ebs-id, var.env)}"
    instance_id = "${aws_instance.jenkins-ec2.id}"
}
resource "aws_instance" "jenkins-ec2" {
    ami = "${var.aws-ec2-ami-id}"
    instance_type = "${lookup(var.aws-ec2-type, var.env)}"
    key_name = "${lookup(var.aws-key-name, var.env)}"
    associate_public_ip_address = "true"
    availability_zone = "${var.aws-availability-zone}"
    vpc_security_group_ids = ["${aws_security_group.jenkins-web-ssh-sg.id}", "${aws_security_group.jenkins-default-sg.id}"]
    subnet_id = "${var.jenkins-public-subnet-id}"
    tags {
        "Name" = "jenkins-ec2-${var.env}"
    }
}
resource "aws_eip" "jenkins-eip" {
    instance = "${aws_instance.jenkins-ec2.id}"
    vpc = true
    tags {
        "Name" = "jenkins-ec2-${var.env}-eip"
    }
} 
```

VPC 子网将从`vpc`模块的[输出](https://www.terraform.io/docs/modules/usage.html#outputs)中设置。

##### 地形开发/生产

要在 Terraform 中重新定义*开发*和*生产*环境的变量值，您至少可以使用三种方法。

###### 变量映射

本例中使用的第一种方法是使用变量[映射](https://www.terraform.io/docs/configuration/variables.html#maps)。

例如，`ec2`模块的`instance_type`参数和它的`aws_instance`资源由下一种方式创建:

```
...
instance_type = "${lookup(var.aws-ec2-type, var.env)}"
... 
```

然后在`variables.tf`中创建一个[映射](https://www.terraform.io/docs/configuration/variables.html#maps)，它包含两个值和两个实例类型:

```
...
variable "aws-ec2-type" {
    description = "EC2 instance type for Dev and prod"
    type = "map"
    default = {
        "dev"           = "t2.nano"
        "production"    = "t2.medium"
    }
}
... 
```

这里，根据上面 bash-script 中设置的`env`变量值，将选择其中一个值——`t2.nano`代表*开发*，或者`t2.medium`代表*生产*。

###### 结束/生产目录

另一种更灵活且允许以更正确的方式使用模块概念的方法是使用创建不同的目录——例如，*开发*和*生产*，每个目录都有自己的`main.tf`和`variables.tf`文件。

然后从这样一个目录中的`main.tf`文件中，可以调用`ec2`，并从它的`variables.tf`中调用适当值的必要变量。

###### Terraform 工作区

第三个是通过使用 Terraform 的[工作空间](https://www.terraform.io/docs/state/workspaces.html)概念。但是它的目的有点不同(在不改变当前基础设施的状态文件的情况下测试项目)。

无论如何，根据文档——也可以使用:

```
...
resource "aws_instance" "example" {
  count = "${terraform.workspace == "default" ? 5 : 1}"
  # ... other arguments
}
... 
```

#### `vpc`模块

最后一个是`vpc`模块示例:

```
$ ls -l vpc/
total 8
-rw-r--r-- 1 setevoy setevoy 1073 Jun 28 10:37 variables.tf
-rw-r--r-- 1 setevoy setevoy 2382 Jun 28 10:39 vpc.tf 
```

Iits 主文件-`vpc.tf`:

```
provider "aws" {
    alias = "peer"
    region = "${var.monitoring-prod-region}"
    profile = "${var.aws-profile}"
}
resource "aws_vpc" "jenkins-vpc" {    
    cidr_block                        = "${lookup(var.jenkins-vpc-cidr, var.env)}"
    assign_generated_ipv6_cidr_block  = true
    enable_dns_hostnames              = true

    tags {
        "Name" = "jenkins-${var.env}-vpc"
    }
}
resource "aws_subnet" "jenkins-public-subnet" {
    vpc_id            = "${aws_vpc.jenkins-vpc.id}"
    cidr_block        = "${lookup(var.jenkins-pub-subnet-cidr, var.env)}"
    availability_zone = "${var.aws-availability-zone}"

    tags {
        "Name" = "jenkins-${var.env}-pub-net"
    }
}
resource "aws_internet_gateway" "jenkins-igw" {
    vpc_id = "${aws_vpc.jenkins-vpc.id}"
}
resource "aws_route_table" "jenkins-route-tbl" {
    vpc_id = "${aws_vpc.jenkins-vpc.id}"
    route {
        cidr_block = "0.0.0.0/0"
        gateway_id = "${aws_internet_gateway.jenkins-igw.id}"
    }
    route {
        cidr_block = "${var.monitoring-prod-vpc-cidr}"
        gateway_id = "${aws_vpc_peering_connection.monitoring-prod-vpc-peer.id}"
    }
    tags {
        Name = "jenkins-${var.env}-route-table"
    }
}
resource "aws_route_table_association" "public-assoc" {
    subnet_id = "${aws_subnet.jenkins-public-subnet.id}"
    route_table_id = "${aws_route_table.jenkins-route-tbl.id}"
}
resource "aws_vpc_peering_connection" "monitoring-prod-vpc-peer" {
    peer_vpc_id = "${var.monitoring-prod-vpc-id}"
    vpc_id = "${aws_vpc.jenkins-vpc.id}"
    peer_region ="${var.monitoring-prod-region}"

    tags {
        Name = "VPC Peering Jenkins and Monitoring Prod"
    }
}
resource "aws_vpc_peering_connection_accepter" "monitoring-peer-accepter" {
    provider                  = "aws.peer"
    vpc_peering_connection_id = "${aws_vpc_peering_connection.monitoring-prod-vpc-peer.id}"
    auto_accept               = true
}
output "jenkins-public-subnet-id" {
  value = "${aws_subnet.jenkins-public-subnet.id}"
}
output "jenkins-vpc-id" {
  value = "${aws_vpc.jenkins-vpc.id}"
} 
```

##### 跨地区 VPC 直联

将使用 [`aws_vpc_peering_connection`](https://www.terraform.io/docs/providers/aws/r/vpc_peering.html) 资源:
创建 VPC 对等

```
...
resource "aws_vpc_peering_connection" "monitoring-prod-vpc-peer" {
    peer_vpc_id = "${var.monitoring-prod-vpc-id}"
    vpc_id = "${aws_vpc.jenkins-vpc.id}"
    peer_region ="${var.monitoring-prod-region}"

    tags {
        Name = "VPC Peering Jenkins and Monitoring Prod"
    }
}
... 
```

这将在`peer_region`参数中获得一个监视堆栈的区域(es-east-2 ),该参数将通过`terraform_exec.sh`脚本的全局变量`$MON_PROD_REGION`传递。

要激活对等连接——[`aws_vpc_peering_connection_accepter`](https://www.terraform.io/docs/providers/aws/r/vpc_peering_accepter.html)将与一个附加的 [aws 提供商](https://www.terraform.io/docs/providers/aws/index.html)一起使用，该提供商具有一个地区和一个`alias` :

```
...
provider "aws" {
    alias = "peer"
    region = "${var.monitoring-prod-region}"
    profile = "${var.aws-profile}"
}
... 
```

`monitoring-prod-region`变量也在`$MON_PROD_REGION`变量中的`terraform_exec.sh`中定义。

类似于`ec2`模块——这里是用于分离 *dev* / *prod* 值的映射，例如，VPC CIDR-s:

```
...
variable "jenkins-vpc-cidr" {
    type = "map"
    default = {
        "dev" = "10.0.4.0/24"
        "production" = "10.0.5.0/24"
    }
}
... 
```

#### 创建一个堆栈

> *“现在有了这些东西，我们将试着飞起来”(с)*

让我们运行我们的堆栈创建(只是 CloudFormation 使用“堆栈”名称的一个习惯):

```
$ ./terraform_exec.sh -a
ENV=dev
AWS CLI profile: jenkins-ci-provisioning
AWS region: eu-west-1
Application cluster name: jenkins-ci-dev
Terraform backend S3 bucket name: terraform-jenkins-ci-dev
Terraform backend key filename: terraform-jenkins-ci-dev.tfstate

Are you sure to proceed? [y/n] y

- module.vpc
- module.ec2

Running Apply action...

Initializing modules...

- module.vpc
- module.ec2

Initializing the backend...
Initializing provider plugins...

...

Resource actions are indicated with the following symbols:

+ create

Terraform will perform the following actions:

+ module.ec2.aws_eip.jenkins-eip
id:                                          <computed>
allocation_id:                               <computed>
association_id:                              <computed>
domain:                                      <computed>
instance:                                    "${aws_instance.jenkins-ec2.id}"
network_interface:                           <computed>
private_ip:                                  <computed>
public_ip:                                   <computed>
tags.%:                                      "1"
tags.Name:                                   "jenkins-ec2-dev-eip"
vpc:                                         "true"
+ module.ec2.aws_instance.jenkins-ec2
id:                                          <computed>
ami:                                         "ami-34414d4d"
associate_public_ip_address:                 "true"
availability_zone:                           "eu-west-1a"

...

Plan: 12 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.

Plan complete. Are you sure to proceed? [y/n] y

data.aws_availability_zones.available: Refreshing state...

An execution plan has been generated and is shown below.

Resource actions are indicated with the following symbols:
+ create
Terraform will perform the following actions:
+ module.ec2.aws_eip.jenkins-eip
id:                                          <computed>

...

module.ec2.aws_eip.jenkins-eip: Creation complete after 2s (ID: eipalloc-ec98d0d1)
module.ec2.aws_volume_attachment.jenkins-data-ebs-attach: Still creating... (10s elapsed)
module.ec2.aws_volume_attachment.jenkins-data-ebs-attach: Still creating... (20s elapsed)
module.ec2.aws_volume_attachment.jenkins-data-ebs-attach: Creation complete after 23s (ID: vai-1099139600)
Apply complete! Resources: 12 added, 0 changed, 0 destroyed. 
```

检查喷丸:

[![](img/1cb358ac6c274f6134b8adb03943a799.png "Terraform: создание проекта с EC2, VPC и AWS cross-region VPC peering")](https://rtfm.co.ua/wp-content/uploads/2018/06/Screenshot_20180628_115404-1.png)

检查从 Jenkins 主机到监控主机的 ping:

```
admin@ip-10-0-4-10:~$ ping 10.0.1.6 -c 1
PING 10.0.1.6 (10.0.1.6) 56(84) bytes of data.
64 bytes from 10.0.1.6: icmp_seq=1 ttl=64 time=85.2 ms
--- 10.0.1.6 ping statistics ---

1 packets transmitted, 1 received, 0% packet loss, time 0ms

rtt min/avg/max/mdev = 85.214/85.214/85.214/0.000 ms 
```

又回来了:

```
admin@monitonrig-production:~$ ping 10.0.4.10 -c 1
PING 10.0.4.10 (10.0.4.10) 56(84) bytes of data.
64 bytes from 10.0.4.10: icmp_seq=1 ttl=64 time=85.4 ms
--- 10.0.4.10 ping statistics ---

1 packets transmitted, 1 received, 0% packet loss, time 0ms

rtt min/avg/max/mdev = 85.440/85.440/85.440/0.000 ms 
```

完成了。

### 类似的帖子

*   <small>03/17/2019</small>[Terraform:AWS](https://dev.to/setevoy/terraform-main-commands-state-files-backend-storages-and-modules-in-examples-on-aws-5ehb)<small>(0)</small>上示例中的主命令、状态文件、后端存储和模块
*   <small>05/03/2017</small> [AWS【中国】:начало](https://rtfm.co.ua/aws-china-nachalo/) <small>(0)</small>
*   <small>02/21/2019</small>[OpenVPN:OpenVPN 接入服务器设置和 AWS VPC 对等配置](https://dev.to/setevoy/openvpn-openvpn-access-server-set-up-and-aws-vpc-peering-configuration-5fpg) <small>(0)</small>
*   t010/31/2015 t1t2terra form:创建项目并运行 AWS ec2t3t 4