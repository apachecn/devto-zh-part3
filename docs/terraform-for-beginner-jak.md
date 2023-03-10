# 面向 AWS 初学者的 Terraform

> 原文：<https://dev.to/anthonylaw/terraform-for-beginner-jak>

# 学习之旅#RoadToDevOps

上周，我选择了 Terraform 作为我的研究和学习主题。

对于 DevOps 开发来说，Terraform 是非常有用和强大的工具，它允许你编写计划(基础设施作为代码)和管理你的云服务。

这意味着你可以在编码中编写一个云基础设施，比如定义多少实例服务，你应该在防火墙中激活什么端口号。用几行代码处理如此多的服务器是很有趣的。

## 简介

通过使用 Terrafrom 在 AWS EC2 中部署虚拟机(ubuntu OS)。

## 目标

在本文中，您应该能够:

*   设置 AWS 帐户
*   使用基本的 Terraform-CLI 命令
*   编写地形语言(HCL)
*   在 AWS 云中部署虚拟机(EC2)。

## AWS 设置

如果您是 AWS 新手，请在此注册，享受 [AWS 免费等级](https://aws.amazon.com/free/) 

我们需要 AWS 用户的`Access Key ID`和`Secret Key ID`，并通过`AmazonEC2FullAccess`向`User`做出承诺。

如果你不知道如何创建用户-> [看这个](https://www.youtube.com/watch?v=XMi5fXL2Hes)

## 地形

确保您的机器[安装 Terraform](https://learn.hashicorp.com/terraform/getting-started/install.html) 。

我们知道如果你用`javascript`编程，你会把文件扩展名命名为`*.js`。在 Terraform 中，会将扩展名定义为`*.tf`，并使用 JSON 格式或 HCL(哈希公司配置语言)。

你可能会觉得很奇怪什么是 HCL (HashiCorp 配置语言)。放心，不复杂，易学易懂。[盐酸文件](https://www.terraform.io/docs/configuration/index.html)

一旦你完成了你的`*.tf`脚本，你必须使用 [Terrform-CLI](https://www.terraform.io/docs/commands/index.html) 来执行`tf`文件。

### 动手

我们将使用`HCL`中的关键词。

| 关键词 | 描述 | 例子 |
| --- | --- | --- |
| 供应者 | 定义云服务提供商 | aws，谷歌 |
| 资源 | 定义我们将使用的资源 | aws_instance，google_compute_instance |
| 可变的 | 定义变量 | - |
| 输出 | 执行后打印输出 | - |

我们将使用`Terraform CLI`中的关键词。

| 关键词 | 描述 |
| --- | --- |
| 初始化 | 初始化文件夹中的 terraform 项目 |
| 计划 | 从`*.tf`开始评估您当前的基础设施 |
| 应用 | 添加所有已定义资源。 |
| 显示 | 列出资源信息和当前状态 |
| 破坏 | 移除所有已定义的资源。 |

### 开始

1.  在您的机器上创建一个文件夹。

2.  创建一个文件`my-first-vm.tf`。

3.  在编辑器中打开文件并启动代码。

```
# my-first-vm.tf

# We are pointing "aws" as our provider.

provider "aws" {
    region = "ap-southeast-1"
    access_key = "<access-key-from-aws-user>"
    secret_key = "<secret-key-from-aws-user>"
} 
```

Enter fullscreen mode Exit fullscreen mode

命令:

```
$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

* provider.aws: version = "~> 2.12"

Terraform has been successfully initialized! 
```

Enter fullscreen mode Exit fullscreen mode

在`my-first-vm.tf`
中追加资源“aws_instance”

```
provider "aws" {...}

resource "aws_instance" "ec2" { # I called name "ec2", you can change it
  ami = "ami-0dad20bd1b9c8c004" # Image: Ubuntu Server 18.04 LTS (HVM), SSD Volume Type
  instance_type = "t2.micro" # VM Spec
} 
```

Enter fullscreen mode Exit fullscreen mode

命令:

```
$ terraform plan

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + aws_instance.ec2
      id:                           <computed>
      ami:                          "0dad20bd1b9c8c004"
      arn:                          <computed>
      associate_public_ip_address:  <computed>
      availability_zone:            <computed>
      cpu_core_count:               <computed>
      cpu_threads_per_core:         <computed>
      ebs_block_device.#:           <computed>
      ephemeral_block_device.#:     <computed>
      get_password_data:            "false"
      host_id:                      <computed>
      instance_state:               <computed>
      instance_type:                "t2.micro"
      ipv6_address_count:           <computed>
      ipv6_addresses.#:             <computed>
      key_name:                     <computed>
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
      tenancy:                      <computed>
      volume_tags.%:                <computed>
      vpc_security_group_ids.#:     <computed>

Plan: 1 to add, 0 to change, 0 to destroy. 
```

Enter fullscreen mode Exit fullscreen mode

它显示基础设施将要执行，您将看到`computed`这是因为如果您没有定义，terraform 将帮助您分配值。

另外，你可以看到`Plan: 1 to add, 0 to change, 0 to destroy.`意味着 1 个资源将被执行。

在这个阶段，实际上您已经完成了实例的设置。

命令:

```
$ terraform apply
Terraform will perform the following actions:

  + aws_instance.ec2

...

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

...

aws_instance.ec2: Still creating... (10s elapsed)
aws_instance.ec2: Still creating... (20s elapsed)
aws_instance.ec2: Creation complete after 22s (ID: i-083d6c6c7b01d1640)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed. 
```

Enter fullscreen mode Exit fullscreen mode

祝贺您，您已经成功地在 AWS 中设置了 EC2 实例，您可以登录 AWS dashboard 并查看它。

尝试使用此命令获取资源信息。

```
$ terraform show 

aws_instance.ec2:
  id = i-083d6c6c7b01d1640
  ami = ami-0dad20bd1b9c8c004
  arn = arn:aws:ec2:ap-southeast-1:907443295242:instance/i-083d6c6c7b01d1640
  associate_public_ip_address = true
...
... 
```

Enter fullscreen mode Exit fullscreen mode

！！！！

但是等等，你怎么能在没有设置防火墙的情况下进入呢？

让在`my-first-vm.tf`
中追加资源“aws_security_group”

```
provider "aws" {...}

resource "aws_instance" "ec2" {...}

# Setup aws_security_group with SSH access

resource "aws_security_group" "allow_ssh" {
  name        = "allow ssh"
  description = "only ssh"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "TCP"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port       = 0
    to_port         = 0
    protocol        = "-1"
    cidr_blocks     = ["0.0.0.0/0"]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经让“ec2”实例应用我们定义的“aws_security_group ”,并为该实例分配了密钥对。

所以我们可以使用`keypair`来访问带有端口`ssh 22`的实例。

如果你不知道如何在 AWS 中创建密钥对，[看这个](https://www.youtube.com/watch?v=DpyLAdMD09w)

```
provider "aws" {...}

resource "aws_instance" "ec2" { # I called name "ec2", you can change it
  ami = "ami-0dad20bd1b9c8c004" # Image: Ubuntu Server 18.04 LTS (HVM), SSD Volume Type
  instance_type = "t2.micro" # VM Spec
  security_groups = ["${aws_security_group.allow_ssh.name}"]
  key_name = "aws-anthony"
}

# Setup aws_security_group with SSH access

resource "aws_security_group" "allow_ssh" {...} 
```

Enter fullscreen mode Exit fullscreen mode

命令:

```
$ terraform apply
aws_instance.ec2: Refreshing state... (ID: i-083d6c6c7b01d1640)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
-/+ destroy and then create replacement

Terraform will perform the following actions:

-/+ aws_instance.ec2 (new resource required)
...
...

+ aws_security_group.allow_ssh
...
...

Plan: 2 to add, 0 to change, 1 to destroy.
...
...
...
aws_instance.ec2: Still creating... (10s elapsed)
aws_instance.ec2: Still creating... (20s elapsed)
aws_instance.ec2: Creation complete after 32s (ID: i-079e9b8c1d98e4af5)

Apply complete! Resources: 2 added, 0 changed, 1 destroyed. 
```

Enter fullscreen mode Exit fullscreen mode

您可能会看到它与刚才不同，因为 terraform 将跟踪`*.tfstate`中的所有资源状态。
所以它表明，它将`destroy`先前的实例并再次重新创建。

太好了，您现在可以在 AWS 仪表板中查看它了。

最后，您成功地在 Ubuntu OS 中创建了一个实例。登录 AWS 仪表板并检查实例公共 ip 地址是什么很麻烦。

您可以使用`output`关键字来显示公共 IP 地址。

```
provider "aws" {...}

resource "aws_instance" "ec2" {...}

resource "aws_security_group" "allow_ssh" {...}

output "showPublicIP" {
  value = "${aws_instance.ec2.*.public_dns}"
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ terraform apply 

aws_security_group.allow_ssh: Refreshing state... (ID: sg-0ca7e9133a701f216)
aws_instance.ec2: Refreshing state... (ID: i-079e9b8c1d98e4af5)

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

showPublicIP = [
    ec2-3-0-184-126.ap-southeast-1.compute.amazonaws.com
] 
```

Enter fullscreen mode Exit fullscreen mode

```
ssh -i aws-anthony.pem ubuntu@ec2-3-0-184-126.ap-southeast-1.compute.amazonaws.com 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

Terraform 总是执行文件夹中的`*tf`，每个基础设施都应该创建一个文件夹，比如`aws-instance`或者`google-instance`。

如果你觉得把所有东西都放在一个`.tf`文件里会很乱，你可以把它们分开放在不同的`.tf`文件里，比如 aws_provider.tf，aws_security.tf

[示例代码](https://gist.github.com/AnthonyLaw/0738a40d9f7e46111f6d670aa7684fdc)

[商店分离示例](https://github.com/AnthonyLaw/terraform-starter-pack)