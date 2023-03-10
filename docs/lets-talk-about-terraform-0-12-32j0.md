# 让我们来谈谈 Terraform 0.12

> 原文：<https://dev.to/simplytunde/lets-talk-about-terraform-0-12-32j0>

Terraform 0.12 最近发布了，你可以在 [terraform 博客](https://www.hashicorp.com/blog/announcing-terraform-0-12)查看更多详细信息。这篇博文的重点将是发布的新特性以及如何使用它们。你可以找到这个 [Github repo](https://github.com/simplytunde/tutorial/tree/master/terraform) 中使用的代码。下面是 ec2 main.tf.
的内容

```
module "test_ec2" {
      source = "./ec2_module"
      //I am passing the subnet object instead of the id
      subnet = aws_subnet.private.0
      instance_tags = [
        {
          Key = "Name"
          Value = "Test"
        },
        {
          Key = "Environment"
          Value = "prd"
        }
    ]
} 
```

这里是 ec2 模块的内容；

```
data "aws_ami" "ubuntu" {
  most_recent = true

  filter {
    name   = "name"
    values = ["ubunimg/hvm-ssd/ubuntu-trusty-14.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }

  owners = ["099720109477"] # Canonical
}
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  subnet_id  = var.subnet.id
  user_data  = templatefile("${path.module}/userdata.tpl", { instance_tags = var.instance_tags })
  tags = {
     for tag in var.instance_tags:
       tag.Key => tag.Value
  }
}

variable "instance_type" {
     default = "t2.micro"
}

variable "instance_tags" {
     type = list
}

variable "subnet" {
    type = object({
        id = string
    })
} 
```

### 为表情

在我看来，这是 0.12 最好的特性之一，它允许你迭代一个列表或地图，你可以对每个条目做任何你想做的事情。从上面可以看到，我们从用户传入的映射列表中生成了实例标签属性。

```
 tags = {
     for tag in var.instance_tags:
       tag.Key => tag.Value
  } 
```

### 第一类表情

从上面可以看出，我们不需要对变量或对象值进行插值，而是将它们直接用作一级变量。这将节省一些墨水，并允许使用 terraform 进行更复杂的操作。

```
...
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  subnet_id  = var.subnet.id
... 
```

### 富值类型

到目前为止，我对这个特性的印象是，我们可以将用户定义的类型和现有的类型作为一级类型来支持，而不需要引用。我们定义了一个类型，它期望 map 带有 id 属性，如果传递一个不匹配的对象，它将被拒绝，这很酷。

```
variable "instance_tags" {
     type = list
}

variable "subnet" {
    type = object({
        id = string
    })
} 
```

### 新型插补

新的 terraform 现在可以让你循环输入你的用户数据。对于我们的例子，这里是用户数据；

```
#!/bin/bash

%{ for tag in instance_tags~}
cat ${tag.Key}=${tag.Value}
%{ endfor ~} 
```

我们能够使用 forloop 来检查标签，并在实例用户数据中使用它，这很可爱，无需通过黑客攻击。

这里没有讨论的另一个特性是*动态块*，你可以在 terraform [博客页面](https://www.hashicorp.com/blog/hashicorp-terraform-0-12-preview-for-and-for-each)上查看。