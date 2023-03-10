# terraform:AWS 示例中的主要命令、状态文件、后端存储和模块

> 原文：<https://dev.to/setevoy/terraform-main-commands-state-files-backend-storages-and-modules-in-examples-on-aws-5ehb>

[![](img/351bd6e55d6ae2f35407bf88a61407b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cU4la9P2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2015/10/terraform_logo-e1446187655503.png) 如何使用 Terraform 的例子，用它的后端和模块工作。

这里将在 AWS 中设置一个简单的 EC2 实例，并将 Terraform 的状态文件存储在 AWS S3 桶中。

简而言之，但是有真实的例子和文档链接。

在 Arch Linux 上的安装:

```
$ sudo pacman -S terraform 
```

授权将使用现有的 [AWS 概要文件](https://rtfm.co.ua/en/aws-cli-named-profiles/) *setevoy-root* 。

### `main.tf`

创建一个`main.tf`文件，并配置一个 [AWS 提供者](https://www.terraform.io/docs/providers/aws/index.html)，要使用的 AWS 区域，以及 AWS 概要文件名称:

```
provider "aws" {
  region = "${var.aws-region}"
  profile = "setevoy-root"
} 
```

这里的`aws`提供者的`region`参数定义在一个变量中，该变量设置在`variables.tf`文件中:

```
variable "aws-region" {
  default = "eu-west-1"
  description = "Default Amazon region"
} 
```

接下来，在`main.tf`中添加一个[地形资源](https://www.terraform.io/docs/configuration/resources.html)–[AWS EC2](https://www.terraform.io/docs/providers/aws/r/instance.html):

```
provider "aws" {
  region = "${var.aws-region}"
  profile = "setevoy-root"
}
resource "aws_instance" "tf-example-ec2" {
  ami = "ami-34414d4d"
  instance_type = "t2.nano"
  key_name = "${var.aws-key-name}"
  associate_public_ip_address = "true"
  tags {
    "Name"      = "tf-example-ec2"
    "Env"       = "${var.aws-cluster-name}"
  }
} 
```

在`variables.tf`中——一个带有用于 SSH 访问的键名的新变量——本例中的*TF-example-ec2-key*:

```
variable "aws-region" {
  default = "eu-west-1"
  description = "Default Amazon region"
}
variable "aws-key-name" {
  default = "tf-example-ec2-key"
  description = "EC2 acces key pair"
} 
```

Terraform 的变量文档在这里是[>>>](https://www.terraform.io/docs/configuration/variables.html)。

### 第一次运行

由于我们使用的是`aws`提供者，Terraform 需要先下载它的插件。

为此，使用 [`init`](https://www.terraform.io/docs/commands/init.html) :

```
$ terraform init
Initializing provider plugins...
- Checking for available provider plugins on https://releases.hashicorp.com...
- Downloading plugin for provider "aws" (1.24.0)...
The following providers do not have any version constraints in configuration,
so the latest version was installed.
To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.aws: version = "~> 1.24"

Terraform has been successfully initialized!
You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform, rerun this command to reinitialize your working directory. If you forget, other
 commands will detect it and remind you to do so if necessary. 
```

默认情况下，`aws`插件文件将被保存在当前目录下，Terraform 将在该目录下创建一个`.terraform`目录(可以使用`-plugin-dir=`设置路径):

```
$ ls -la .terraform/plugins/linux_amd64/
total 74232
drwxr-xr-x 2 setevoy setevoy     4096 Jun 25 17:19 .
drwxr-xr-x 3 setevoy setevoy     4096 Jun 25 17:19 ..
-rwxr-xr-x 1 setevoy setevoy       79 Jun 25 17:19 lock.json
-rwxr-xr-x 1 setevoy setevoy 75997088 Jun 25 17:19 terraform-provider-aws_v1.24.0_x4 
```

`init`还将执行后端存储配置，以存储 Terraform 的状态文件——稍后再谈。

#### `tarraform plan`

现在你可以执行 [`plan`](https://www.terraform.io/docs/commands/plan.html) 命令，看看 Terraform 到底会执行什么:

```
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.
------------------------------------------------------------------------
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
+ create
Terraform will perform the following actions:
+ aws_instance.tf-example-ec2
id:                           <computed>
ami:                          "ami-34414d4d"
associate_public_ip_address:  "true"
availability_zone:            <computed>
ebs_block_device.#:           <computed>
ephemeral_block_device.#:     <computed>
get_password_data:            "false"
instance_state:               <computed>
instance_type:                "t2.nano"
ipv6_address_count:           <computed>
ipv6_addresses.#:             <computed>
key_name:                     "tf-example-ec2-key"
network_interface.#:          <computed>
network_interface_id:         <computed>
password_data:                <computed>
placement_group:              <computed>
primary_network_interface_id: <computed>
private_dns:                  <computed>
private_ip:                   <computed>
public_dns:                   <computed>
public_ip:                    <computed>
root_block_device.#:          <computed>
security_groups.#:            <computed>
source_dest_check:            "true"
subnet_id:                    <computed>
tags.%:                       "1"
tags.Name:                    "tf-example-ec2"
tenancy:                      <computed>
volume_tags.%:                <computed>
vpc_security_group_ids.#:     <computed>
Plan: 1 to add, 0 to change, 0 to destroy.
------------------------------------------------------------------------
Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.

Here in its output, we can see that a new resource will be created (`+` symbol before `aws_instance.tf-example-ec2`). 
```

“`-`表示删除资源，“`~`表示修改现有资源。

#### `terraform apply`

如`main.tf`中所述，要准确运行创建/删除/修改资源的操作，必须使用 [`apply`](https://www.terraform.io/docs/commands/apply.html) 命令:

```
$ terraform apply
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
+ create
Terraform will perform the following actions:
+ aws_instance.tf-example-ec2
id:                           <computed>
ami:                          "ami-34414d4d"
associate_public_ip_address:  "true"
availability_zone:            <computed>
ebs_block_device.#:           <computed>
ephemeral_block_device.#:     <computed>
get_password_data:            "false"
instance_state:               <computed>
instance_type:                "t2.nano"
ipv6_address_count:           <computed>
ipv6_addresses.#:             <computed>
key_name:                     "tf-example-ec2-key"
network_interface.#:          <computed>
network_interface_id:         <computed>
password_data:                <computed>
placement_group:              <computed>
primary_network_interface_id: <computed>
private_dns:                  <computed>
private_ip:                   <computed>
public_dns:                   <computed>
public_ip:                    <computed>
root_block_device.#:          <computed>
security_groups.#:            <computed>
source_dest_check:            "true"
subnet_id:                    <computed>
tags.%:                       "1"
tags.Name:                    "tf-example-ec2"
tenancy:                      <computed>
volume_tags.%:                <computed>
vpc_security_group_ids.#:     <computed>
Plan: 1 to add, 0 to change, 0 to destroy.
Do you want to perform these actions?
Terraform will perform the actions described above.
Only 'yes' will be accepted to approve.
Enter a value: yes
aws_instance.tf-example-ec2: Creating...
ami:                          "" => "ami-34414d4d"
associate_public_ip_address:  "" => "true"
availability_zone:            "" => "<computed>"
ebs_block_device.#:           "" => "<computed>"
ephemeral_block_device.#:     "" => "<computed>"
get_password_data:            "" => "false"
instance_state:               "" => "<computed>"
instance_type:                "" => "t2.nano"
ipv6_address_count:           "" => "<computed>"
ipv6_addresses.#:             "" => "<computed>"
key_name:                     "" => "tf-example-ec2-key"
network_interface.#:          "" => "<computed>"
network_interface_id:         "" => "<computed>"
password_data:                "" => "<computed>"
placement_group:              "" => "<computed>"
primary_network_interface_id: "" => "<computed>"
private_dns:                  "" => "<computed>"
private_ip:                   "" => "<computed>"
public_dns:                   "" => "<computed>"
public_ip:                    "" => "<computed>"
root_block_device.#:          "" => "<computed>"
security_groups.#:            "" => "<computed>"
source_dest_check:            "" => "true"
subnet_id:                    "" => "<computed>"
tags.%:                       "" => "1"
tags.Name:                    "" => "tf-example-ec2"
tenancy:                      "" => "<computed>"
volume_tags.%:                "" => "<computed>"
vpc_security_group_ids.#:     "" => "<computed>"
aws_instance.tf-example-ec2: Still creating... (10s elapsed)
aws_instance.tf-example-ec2: Still creating... (20s elapsed)
aws_instance.tf-example-ec2: Still creating... (30s elapsed)
aws_instance.tf-example-ec2: Still creating... (40s elapsed)
aws_instance.tf-example-ec2: Creation complete after 47s (ID: i-062174155cee10e51)
Apply complete! Resources: 1 added, 0 changed, 0 destroyed. 
```

现在让我们检查一下在 Terraform 的输出中返回的 EC2 的 ID:

```
$ aws ec2 describe-instances --instance-ids i-062174155cee10e51
{
"Reservations": [
{
"Groups": [],
"Instances": [
{
"AmiLaunchIndex": 0,
"ImageId": "ami-34414d4d",
"InstanceId": "i-062174155cee10e51",
"InstanceType": "t2.nano",
"KeyName": "tf-example-ec2-key",
"LaunchTime": "2018-06-25T14:36:09.000Z",
"Monitoring": {
"State": "disabled"
},
... 
```

[![](img/3fcf861c3819be96b77198a27f12f80c.png "Terraform: пример работы - основные команды, state-файлы, бекенды, модули")](https://rtfm.co.ua/wp-content/uploads/2018/06/Screenshot_20180625_174128.png)

#### `terraform destroy`

要删除从我们的`main.tf`创建的资源–使用 [`destroy`](https://www.terraform.io/docs/commands/destroy.html) 命令:

```
$ terraform destroy
aws_instance.tf-example-ec2: Refreshing state... (ID: i-062174155cee10e51)
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
- destroy
Terraform will perform the following actions:
- aws_instance.tf-example-ec2
Plan: 0 to add, 0 to change, 1 to destroy.
Do you really want to destroy?
Terraform will destroy all your managed infrastructure, as shown above.
There is no undo. Only 'yes' will be accepted to confirm.
Enter a value: yes
aws_instance.tf-example-ec2: Destroying... (ID: i-062174155cee10e51)
aws_instance.tf-example-ec2: Still destroying... (ID: i-062174155cee10e51, 10s elapsed)
aws_instance.tf-example-ec2: Still destroying... (ID: i-062174155cee10e51, 20s elapsed)
aws_instance.tf-example-ec2: Still destroying... (ID: i-062174155cee10e51, 30s elapsed)
aws_instance.tf-example-ec2: Destruction complete after 32s
Destroy complete! Resources: 1 destroyed. 
```

### 状态-文件

Terraform 将其关于基础设施的信息保存在所谓的“状态文件”中。

默认情况下，这将是项目目录中的一个`terraform.tfstate`。

例如，如果您现在检查它，这里将是零资源，因为我们刚刚执行了`destroy` :

```
$ cat terraform.tfstate
{
"version": 3,
"terraform_version": "0.11.7",
"serial": 3,
"lineage": "a83f41ba-0a1e-ec21-cc4c-312940dfb53f",
"modules": [
{
"path": [
"root"
],
"outputs": {},
"resources": {},
"depends_on": []
}
]
} 
```

在运行`apply`之前，Terraform 将在`terraform.tfstate.backup`中创建一个现有状态文件的备份(如果存在的话)–现在你可以看到在`destroy` :
之前创建的资源

```
$ cat terraform.tfstate.backup

{
"version": 3,
"terraform_version": "0.11.7",
"serial": 3,
"lineage": "a83f41ba-0a1e-ec21-cc4c-312940dfb53f",
"modules": [
{
"path": [
"root"
],
"outputs": {},
"resources": {
"aws_instance.tf-example-ec2": {
"type": "aws_instance",
"depends_on": [],
"primary": {
"id": "i-062174155cee10e51",
... 
```

使用 [`-state`](https://www.terraform.io/docs/commands/apply.html#state-path) 和备份文件名—[`-backup`](https://www.terraform.io/docs/commands/apply.html#backup-path)可以设置保持基础设施状态的文件名。

让我们检查一下——再次创建一个ес2，让我们使用`-auto-approve`来避免每次都输入*yes*:

```
$ terraform apply -state /tmp/tf-example-ec2.tfstate -backup /tmp/tf-example-ec2.tfstate.backup -auto-approve 
```

检查您的状态文件:

```
$ cat /tmp/tf-example-ec2.tfstate
{
"version": 3,
"terraform_version": "0.11.7",
"serial": 4,
"lineage": "a83f41ba-0a1e-ec21-cc4c-312940dfb53f",
"modules": [
{
"path": [
"root"
],
"outputs": {},
"resources": {
"aws_instance.tf-example-ec2": {
"type": "aws_instance",
"depends_on": [],
"primary": {
"id": "i-01b63239677ef31e8",
... 
```

类似地，当使用`destroy`时，你必须指定相同的状态文件——`/tmp/tf-example-ec2.tfstate`:

```
$ terraform destroy -state /tmp/tf-example-ec2.tfstate -backup /tmp/tf-example-ec2.tfstate.backup -auto-approve
aws_instance.tf-example-ec2: Refreshing state... (ID: i-0c6779781dabd81d8)
aws_instance.tf-example-ec2: Destroying... (ID: i-0c6779781dabd81d8)
...
aws_instance.tf-example-ec2: Destruction complete after 31s

Destroy complete! Resources: 1 destroyed. 
```

#### `terraform state`

使用状态文件 Terraform 有 [`state`](https://www.terraform.io/docs/commands/state/index.html) 命令。

例如，要检查状态文件中已经存在的资源，可以使用它的 [`list`](https://www.terraform.io/docs/commands/state/list.html) 命令:

```
$ terraform state list -state=/tmp/tf-example-ec2.tfstate
aws_instance.tf-example-ec2 
```

#### 后端

默认情况下，Terraform 将在本地创建文件，但也可以使用远程存储。

最常用的存储(至少从我自己的实践来看)是 AWS S3 存储桶。

让我们更新我们的`main.tf`并描述一个存储状态文件的 S3 桶——添加`backend "s3"`配置:

```
provider "aws" {
  region = "${var.aws-region}"
  profile = "setevoy-root"
}
resource "aws_instance" "tf-example-ec2" {
  ami = "ami-34414d4d"
  instance_type = "t2.nano"
  key_name = "${var.aws-key-name}"
  associate_public_ip_address = "true"
  tags {
    "Name"      = "tf-example-ec2"
  }
}
terraform {
  backend "s3" {
    profile = "setevoy-root"
    bucket  = "tf-example-states"
    key     = "tf-example/terraform.tfstate"
    region  = "eu-west-1"
  }
} 
```

创建这个桶:

```
$ aws s3api create-bucket --bucket tf-example-states --region eu-west-1 --create-bucket-configuration LocationConstraint=eu-west-1
{
  "Location": "http://tf-example-states.s3.amazonaws.com/"
} 
```

当我们添加 s3 后端时，再次运行`init`:

```
$ terraform init
Initializing the backend...
Do you want to copy existing state to the new backend?
Pre-existing state was found while migrating the previous "local" backend to the
newly configured "s3" backend. No existing state was found in the newly
configured "s3" backend. Do you want to copy this state to the new "s3"
backend? Enter "yes" to copy and "no" to start with an empty state.
Enter a value: yes
Successfully configured the backend "s3"! Terraform will automatically
use this backend unless the backend configuration changes.
Initializing provider plugins...
The following providers do not have any version constraints in configuration,
so the latest version was installed.
To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.
* provider.aws: version = "~> 1.24"
Terraform has been successfully initialized!
You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.
If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary. 
```

Terraform 已经将状态文件复制到此桶:

```
$ aws s3 ls s3://tf-example-states/tf-example/
2018-06-25 18:40:11        317 terraform.tfstate 
```

现在，项目中的所有更改都将保存到这个 bucket 中的这个文件中，当您在某种自动化中使用 Terraform 时，这是一个很好的方法，例如，当您在 Jenkin 的作业中从临时 Docker 容器运行 Terraform 时(例如，参见[AWS:билдJava+Maven+Docker+Packer+Terraform](https://rtfm.co.ua/aws-bild-java-maven-docker-packer-terraform/)(*Rus*)post)。

### 地形模块

另外需要提到的是 [Terraform 模块](https://www.terraform.io/docs/modules/usage.html)，它允许单独保存资源，便于管理。

例如，我们的`main.tf`文件是我们项目的根模块，在这里我们描述我们的资源。

同样，您可以创建一个新目录，例如`ec2`，在其中描述您的 EC2 实例，并在这个模块中保存它自己的变量:

```
$ mkdir ec2 
```

现在在这里创建一个新文件`ec2.tf`和变量文件——`variables.tf`:

```
touch ec2/{ec2.tf,variables.tf} 
```

在`ec2/ec2.tf`文件中描述您的 EC2 资源:

```
resource "aws_instance" "tf-example-ec2" {
  ami = "${var.aws-ec2-ami-id}"
  instance_type = "${var.aws-ec2-type}"
  key_name = "${var.aws-key-name}"
  associate_public_ip_address = "true"
  tags {
    "Name"      = "tf-example-ec2"
  }
} 
```

和变量:

```
variable "aws-ec2-ami-id" {
      default = "ami-34414d4d"
}
variable "aws-ec2-type" {
  default = "t2.nano"
}
variable "aws-key-name" {
  default = "tf-example-ec2-key"
} 
```

接下来——添加这个要从`main.tf`调用的`ec2`模块:(注意，旧的`resource "aws_instance"`用`/* ... */`注释):

```
provider "aws" {
  region = "${var.aws-region}"
  profile = "setevoy-root"
}
module "ec2" {
  source            = "ec2"
}
/*
resource "aws_instance" "tf-example-ec2" {
  ami = "ami-34414d4d"
  instance_type = "t2.nano"
  key_name = "${var.aws-key-name}"
  associate_public_ip_address = "true"
  tags {
    "Name"      = "tf-example-ec2"
  }
}
*/
terraform {
  backend "s3" {
    profile = "setevoy-root"
    bucket  = "tf-example-states"
    key     = "tf-example/terraform.tfstate"
    region  = "eu-west-1"
  }
} 
```

当我们添加一个新模块时，再次调用`init` :

```
$ terraform init
Initializing modules...
- module.ec2
Getting source "ec2"
Initializing the backend...
Initializing provider plugins...
... 
```

现在调用`plan` :

```
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.
------------------------------------------------------------------------
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
+ create
Terraform will perform the following actions:
+ module.ec2.aws_instance.tf-example-ec2
id:                           <computed>
ami:                          "ami-34414d4d"
... 
```

现在运行`apply`:

```
$ terraform apply -auto-approve
module.ec2.aws_instance.tf-example-ec2: Creating...
ami:                          "" => "ami-34414d4d"
associate_public_ip_address:  "" => "true"
availability_zone:            "" => "<computed>"
ebs_block_device.#:           "" => "<computed>"
ephemeral_block_device.#:     "" => "<computed>"
get_password_data:            "" => "false"
instance_state:               "" => "<computed>"
instance_type:                "" => "t2.nano"
ipv6_address_count:           "" => "<computed>"
ipv6_addresses.#:             "" => "<computed>"
key_name:                     "" => "tf-example-ec2-key"
network_interface.#:          "" => "<computed>"
network_interface_id:         "" => "<computed>"
password_data:                "" => "<computed>"
placement_group:              "" => "<computed>"
primary_network_interface_id: "" => "<computed>"
private_dns:                  "" => "<computed>"
private_ip:                   "" => "<computed>"
public_dns:                   "" => "<computed>"
public_ip:                    "" => "<computed>"
root_block_device.#:          "" => "<computed>"
security_groups.#:            "" => "<computed>"
source_dest_check:            "" => "true"
subnet_id:                    "" => "<computed>"
tags.%:                       "" => "1"
tags.Name:                    "" => "tf-example-ec2"
tenancy:                      "" => "<computed>"
volume_tags.%:                "" => "<computed>"
vpc_security_group_ids.#:     "" => "<computed>"
module.ec2.aws_instance.tf-example-ec2: Still creating... (10s elapsed)
module.ec2.aws_instance.tf-example-ec2: Creation complete after 15s (ID: i-08d703ddd4f252382)
Apply complete! Resources: 1 added, 0 changed, 0 destroyed. 
```

一般来说，这就是开始使用 Terraform 所需要知道的一切。

### 类似的帖子

*   t010/31/2015 t1t2terra form:创建项目并运行 AWS ec2t3t 4
*   <small>05/03/2017</small> [AWS【中国】:начало](https://rtfm.co.ua/aws-china-nachalo/) <small>(0)</small>
*   <small>2/20/2017</small>[AWS:Java 装置+maven+dock+packer+terra reform](https://rtfm.co.ua/aws-bild-java-maven-docker-packer-terraform/)<small></small>
<small>*   <small>06/28/2018</small> [地形:созданиероектасEC2、VPC и AWS 跨区 VPC 对等](https://rtfm.co.ua/terraform-sozdanie-proekta-s-ec2-vpc-i-aws-cross-region-vpc-peering/) <small>(0)</small></small>

<small></small>