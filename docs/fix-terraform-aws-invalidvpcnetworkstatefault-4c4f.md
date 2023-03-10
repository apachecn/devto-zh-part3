# 修复:terra form+AWS:invalidpcnetworkstatefault

> 原文：<https://dev.to/david_j_eddy/fix-terraform-aws-invalidvpcnetworkstatefault-4c4f>

最近在使用 Terraform 和 AWS 时，我遇到了一个似乎没有太多相关信息的错误。经过大约一天的研究和故障排除，我能够解决它。

## 错误

```
Error: Error applying plan:

1 error(s) occurred:

* module.web_app.aws_db_instance.rds: 1 error(s) occurred:

* aws_db_instance.rds: Error creating DB Instance: InvalidVPCNetworkStateFault: Cannot create a db.t2.micro database instance because no subnets exist in availability zones with sufficient capacity for VPC and storage type : gp2 for db.t2.micro. Please first create at least one new subnet; choose from these availability zones: us-west-1c, us-west-1b.

    status code: 400, request id: ea5f04be-8510-4cfc-9bb2-606c0e00d007 
```

这里的关键要点是 RDS、子网和可用性。因此，我检查了 VPC AZ 的，子网分配给他们，CIDR 范围等。有一次，我甚至把 VPC 配置比作一个工作区。据我所知，没有什么不同。

## 原因

经过一番挖掘，我注意到默认的 VPC 子网已经被删除。这将导致 VPC 和关联的 AZ 子网在默认数据库安全组中无效。在区域中重新创建默认子网的唯一方法是通过 CLI，此操作没有 web 控制台功能。

## 狐狸

修复方法是进入 RDS 子网组配置([https://us-west-1.console.aws.amazon.com/rds/home?)region = us-west-1 # d B- subnet-groups](https://us-west-1.console.aws.amazon.com/rds/home?region=us-west-1#db-subnet-groups:))并将两个新的默认子网重新分配给 RDS 组。之后，Terraform 的“计划”和“应用”恢复正常工作。