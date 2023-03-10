# 使用 Fargate、RDS 和 Terraform 在 AWS 上部署 Hasura

> 原文：<https://dev.to/lineup-ninja/deploying-hasura-on-aws-with-fargate-rds-and-terraform-4gk7>

Hasura 是 Postgres 的一个很棒的 GraphQL 网关。你可以很简单地在 Heroku 上开始，但是如果你想在 AWS 上进行全自动的部署，这篇文章将通过一种可能的方法来指导你。

在 AWS 中部署时，强烈建议跨多个可用性区域(AZ)进行部署，这样可以确保如果一个 AZ 出现故障，您的服务只会受到短暂的中断，而不会宕机，直到 AZ 恢复。

此部署中使用的组件有:

*   “多 AZ”中部署的 Postgres RDS 数据库
*   哈苏拉部署在法盖特，跨越多个 AZ
*   Hasura 任务之间的 ALB 负载平衡
*   ACM 颁发的证书，用于保护到 ALB 的流量。
*   将 RDS、ECS 和 ALB 记录到 Cloudwatch 日志中。

这是我们将要构建的架构:

[![ECS Fargate Hasura RDS Diagram](img/54e03c8bed44c30e4b031300e7793554.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vyVnTNe_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aweemd5t6l5qqj6wzwql.png)

你可以使用 Cloudformation 来构建这个，但是我选择 Terraform 有各种原因，不仅仅是因为它有能力做`terraform plan`。

顺便说一句，如果你刚刚开始使用 Fargate，那么就开始在 web 管理控制台中进行试验，它会处理下面的许多复杂问题，例如创建服务角色、IAM 权限、日志组等。当你想自动化的事情回来，并深入到下面的细节。

在 AWS 帐户中配置 ECS 资源之前，必须在该帐户中创建`AWSServiceRoleForECS` IAM 角色。如果您已经在 web 控制台中手动创建了集群，那么这将是为您创建的。如果您想使用 Terraform 管理它，可以将其导入到 Terraform 配置中。

需要注意的是，`AWSServiceRoleForECS`对于每个帐户只能存在一次(它不支持服务角色后缀)，因此如果您在一个 AWS 帐户中部署多个 Hasura 堆栈，那么服务角色的 Terraform 将需要独立于主堆栈而存在。

创建这样的角色

```
# Service role allowing AWS to manage resources required for ECS
resource "aws_iam_service_linked_role" "ecs_service" {
  aws_service_name = "ecs.amazonaws.com"
} 
```

在深入基础设施组件之前，需要一些变量

```
# Which region to deploy to
variable "region" { }
# Which domain to use. Service will be deployed at hasura.domain
variable "domain" { }
# The access key to secure hasura with. For admin access
variable "hasura_access_key" { }
# The secret shared HMAC key for JWT authentication
variable "hasura_jwt_hmac_key" { }
# User name for RDS
variable "rds_username" { }
# Password for RDS
variable "rds_password" { }
# The DB name in the RDS instance. Note that this cannot contain -'s
variable "rds_db_name" { }
# The size of RDS instance, eg db.t2.micro
variable "rds_instance" { }
# How many AZ's to create in the VPC
variable "az_count" { default = 2 }
# Whether to deploy RDS and ECS in multi AZ mode or not
variable "multi_az" { default = true } 
```

接下来，我们将为 ALB 创建一个证书。如果您打算定期删除和重新创建您的堆栈，比方说对于一个开发环境，那么在一个单独的 Terraform 堆栈中创建证书是一个好主意，这样它就不会每次都被销毁和重新创建。新的 AWS 帐户每年有 20 个证书的默认限制，所以很容易意外用完。可以根据要求增加，但根据我的经验，需要一两天才能完成。

如果您使用 Route 53，您可以自动验证您的 ACM 证书，这是实现全自动工作流程的最简单方法。或者，如果 Terraform 支持您的 DNS 提供商，您可以让它在那里添加 DNS 记录。

创建证书:

```
resource "aws_acm_certificate" "hasura" {
  domain_name       = "hasura.${var.domain}"
  validation_method = "DNS"

  lifecycle {
    create_before_destroy = true
  }
} 
```

验证证书

```
data "aws_route53_zone" "hasura" {
  name         = "${var.domain}."
}

resource "aws_route53_record" "hasura_validation" {
    depends_on = ["aws_acm_certificate.hasura"]
    name = "${lookup(aws_acm_certificate.hasura.domain_validation_options[0], "resource_record_name")}"
    type = "${lookup(aws_acm_certificate.hasura.domain_validation_options[0], "resource_record_type")}"
    zone_id = "${data.aws_route53_zone.hasura.zone_id}"
    records = ["${lookup(aws_acm_certificate.hasura.domain_validation_options[0], "resource_record_value")}"]
    ttl = 300
}

resource "aws_acm_certificate_validation" "hasura" {
    certificate_arn = "${aws_acm_certificate.hasura.arn}"
    validation_record_fqdns = ["${aws_route53_record.hasura_validation.*.fqdn}" ]
} 
```

好了，现在我们可以开始处理基础设施的主体了。

首先，我们需要一个 VPC 来放置这些基础设施。我们将为 RDS 创建一个专用子网，为 ECS 创建一个公共子网。ECS 任务被放在一个公共子网中，因此它们可以从 docker hub 获取 Hasura 映像。如果您将它们放在私有子网中，您将需要添加一个 NAT 网关，使它们能够提取它们的映像。

```
 ### VPC

# Fetch AZs in the current region
data "aws_availability_zones" "available" {}

resource "aws_vpc" "hasura" {
  cidr_block = "172.17.0.0/16"
}

# Create var.az_count private subnets for RDS, each in a different AZ
resource "aws_subnet" "hasura_rds" {
  count             = "${var.az_count}"
  cidr_block        = "${cidrsubnet(aws_vpc.hasura.cidr_block, 8, count.index)}"
  availability_zone = "${data.aws_availability_zones.available.names[count.index]}"
  vpc_id            = "${aws_vpc.hasura.id}"
}

# Create var.az_count public subnets for Hasura, each in a different AZ
resource "aws_subnet" "hasura_ecs" {
  count                   = "${var.az_count}"
  cidr_block              = "${cidrsubnet(aws_vpc.hasura.cidr_block, 8, var.az_count + count.index)}"
  availability_zone       = "${data.aws_availability_zones.available.names[count.index]}"
  vpc_id                  = "${aws_vpc.hasura.id}"
  map_public_ip_on_launch = true
}

# IGW for the public subnet
resource "aws_internet_gateway" "hasura" {
  vpc_id = "${aws_vpc.hasura.id}"
}

# Route the public subnet traffic through the IGW
resource "aws_route" "internet_access" {
  route_table_id         = "${aws_vpc.hasura.main_route_table_id}"
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = "${aws_internet_gateway.hasura.id}"
} 
```

现在创建一些安全组，以便 ALB 可以与 ECS 对话，ECS 任务可以与 RDS 对话:

```
# Security Groups

# Internet to ALB
resource "aws_security_group" "hasura_alb" {
  name        = "hasura-alb"
  description = "Allow access on port 443 only to ALB"
  vpc_id      = "${aws_vpc.hasura.id}"

  ingress {
    protocol    = "tcp"
    from_port   = 443
    to_port     = 443
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port = 0
    to_port   = 0
    protocol  = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
# ALB TO ECS

resource "aws_security_group" "hasura_ecs" {
  name        = "hasura-tasks"
  description = "allow inbound access from the ALB only"
  vpc_id      = "${aws_vpc.hasura.id}"

  ingress {
    protocol        = "tcp"
    from_port       = "8080"
    to_port         = "8080"
    security_groups = ["${aws_security_group.hasura_alb.id}"]
  }

  egress {
    protocol    = "-1"
    from_port   = 0
    to_port     = 0
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# ECS to RDS
resource "aws_security_group" "hasura_rds" {
  name        = "hasura-rds"
  description = "allow inbound access from the hasura tasks only"
  vpc_id      = "${aws_vpc.hasura.id}"

  ingress {
    protocol        = "tcp"
    from_port       = "5432"
    to_port         = "5432"
    security_groups = ["${aws_security_group.hasura_ecs.id}"]
  }

  egress {
    protocol    = "-1"
    from_port   = 0
    to_port     = 0
    cidr_blocks = ["0.0.0.0/0"]
  }
} 
```

现在我们可以创建 RDS 实例了。它需要一个“子网组”来放置实例，我们将使用上面创建的`hasura_rds`子网。

```
resource "aws_db_subnet_group" "hasura" {
  name       = "hasura"
  subnet_ids = ["${aws_subnet.hasura_rds.*.id}"]
} 
```

然后创建 RDS 实例本身

```
resource "aws_db_instance" "hasura" {
    name                        = "${var.rds_db_name}"
    identifier                  = "hasura"
    username                    = "${var.rds_username}"
    password                    = "${var.rds_password}"
    port                        = "5432"
    engine                      = "postgres"
    engine_version              = "10.5"
    instance_class              = "${var.rds_instance}"
    allocated_storage           = "10"
    storage_encrypted           = false
    vpc_security_group_ids      = ["${aws_security_group.hasura_rds.id}"]
    db_subnet_group_name        = "${aws_db_subnet_group.hasura.name}"
    parameter_group_name        = "default.postgres10"
    multi_az                    = "${var.multi_az}"
    storage_type                = "gp2"
    publicly_accessible         = false
    # snapshot_identifier       = "hasura"
    allow_major_version_upgrade = false
    auto_minor_version_upgrade  = false
    apply_immediately           = true
    maintenance_window          = "sun:02:00-sun:04:00"
    skip_final_snapshot         = false
    copy_tags_to_snapshot       = true
    backup_retention_period     = 7
    backup_window               = "04:00-06:00"
    final_snapshot_identifier   = "hasura"
} 
```

在上面的配置中，将构建一个名为`hasura`的新 RDS 实例。可以让 terraform 从现有快照恢复 RDS 实例。您可以通过取消对`# snapshot_identifier`行的注释来做到这一点。然而，我建议在从快照创建实例之前阅读[本期](https://github.com/terraform-providers/terraform-provider-aws/issues/4126)。简而言之，如果您从快照创建实例，您必须在模板的未来运行中包含`snapshot_identifier`,否则它将删除并重新创建新的实例。

向前到 ECS / Fargate...

创建 ECS 集群

```
resource "aws_ecs_cluster" "hasura" {
  name = "hasura-cluster"
} 
```

在我们创建 Hasura 服务之前，让我们创建一个地方让它记录到

```
resource "aws_cloudwatch_log_group" "hasura" {
  name = "/ecs/hasura"
} 
```

创建日志组很简单，像大多数事情一样，允许 ECS 任务记录日志要稍微复杂一点！

```
data "aws_iam_policy_document" "hasura_log_publishing" {
  statement {
    actions = [
      "logs:CreateLogStream",
      "logs:PutLogEvents",
      "logs:PutLogEventsBatch",
    ]
    resources = ["arn:aws:logs:${var.region}:*:log-group:/ecs/hasura:*"]
  }
}

resource "aws_iam_policy" "hasura_log_publishing" {
  name        = "hasura-log-pub"
  path        = "/"
  description = "Allow publishing to cloudwach"

  policy = "${data.aws_iam_policy_document.hasura_log_publishing.json}"
}

data "aws_iam_policy_document" "hasura_assume_role_policy" {
  statement {
    actions = ["sts:AssumeRole"]

    principals {
      type        = "Service"
      identifiers = ["ecs-tasks.amazonaws.com"]
    }
  }
}

resource "aws_iam_role" "hasura_role" {
  name               = "hasura-role"
  path               = "/system/"
  assume_role_policy = "${data.aws_iam_policy_document.hasura_assume_role_policy.json}"
}

resource "aws_iam_role_policy_attachment" "hasura_role_log_publishing" {
  role = "${aws_iam_role.hasura_role.name}"
  policy_arn = "${aws_iam_policy.hasura_log_publishing.arn}"
} 
```

然后创建一个任务定义。这是调整实例大小的地方，也是配置传递给 docker 容器的环境属性的地方。这里，我们正在为 JWT 身份验证配置实例。

将`image`定义更新为您想要运行的版本。您需要更新您的应用程序名称的`CORS`设置，或者完全删除它。

```
 resource "aws_ecs_task_definition" "hasura" {
  family                   = "hasura"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  cpu                      = "256"
  memory                   = "512"
  execution_role_arn       = "${aws_iam_role.hasura_role.arn}"

  container_definitions = <<DEFINITION
    [
      {
        "image": "hasura/graphql-engine:v1.0.0-alpha34",
        "name": "hasura",
        "networkMode": "awsvpc",
        "portMappings": [
          {
            "containerPort": 8080,
            "hostPort": 8080
          }
        ],
        "logConfiguration": {
          "logDriver": "awslogs",
          "options": {
            "awslogs-group": "/ecs/hasura",
            "awslogs-region": "${var.region}",
            "awslogs-stream-prefix": "ecs"
          }
        },
        "environment": [
          {
            "name": "HASURA_GRAPHQL_ACCESS_KEY",
            "value": "${var.hasura_access_key}"
          },
          {
            "name": "HASURA_GRAPHQL_DATABASE_URL",
            "value": "postgres://${var.rds_username}:${var.rds_password}@${aws_db_instance.hasura.endpoint}/${var.rds_db_name}"
          },
          {
            "name": "HASURA_GRAPHQL_ENABLE_CONSOLE",
            "value": "true"
          },
          {
            "name": "HASURA_GRAPHQL_CORS_DOMAIN",
            "value": "https://app.${var.domain}:443"
          },

          {
            "name": "HASURA_GRAPHQL_PG_CONNECTIONS",
            "value": "100"
          },
          {
            "name": "HASURA_GRAPHQL_JWT_SECRET",
            "value": "{\"type\":\"HS256\", \"key\": \"${var.hasura_jwt_hmac_key}\"}"
          }
        ]
      }
    ]
DEFINITION

} 
```

现在创建 ECS 服务。如果您已经将`multi_az`属性设置为 true，它将启动两个任务。它会自动将任务平均分配到服务中配置的子网，即两个 AZ。

```
resource "aws_ecs_service" "hasura" {
  depends_on      = ["aws_ecs_task_definition.hasura", "aws_cloudwatch_log_group.hasura"]
  name            = "hasura-service"
  cluster         = "${aws_ecs_cluster.hasura.id}"
  task_definition = "${aws_ecs_task_definition.hasura.arn}"
  desired_count   = "${var.multi_az == true ? "2" : "1"}"
  launch_type     = "FARGATE"

  network_configuration {
    assign_public_ip  = true
    security_groups   = ["${aws_security_group.hasura_ecs.id}"]
    subnets           = ["${aws_subnet.hasura_ecs.*.id}"]
  }

  load_balancer {
    target_group_arn = "${aws_alb_target_group.hasura.id}"
    container_name   = "hasura"
    container_port   = "8080"
  }

  depends_on = [
    "aws_alb_listener.hasura",
  ]
} 
```

现在我们有了 ECS 服务和 RDS 数据库，我们只需要一些对它的公共访问，这将由 ALB 提供。

首先为 ALB 创建一个记录日志的地方(如果你想记录日志的话)。从一个 S3 桶开始。您可以添加任何您想要的生命周期策略，并且记住存储段名称是全局唯一的

```
resource "aws_s3_bucket" "hasura" {
  bucket = "hasura-${var.region}"
  acl    = "private"
} 
```

添加 IAM 策略以允许 ALB 记录到该策略。记得更新存储桶名称。

```
data "aws_elb_service_account" "main" {}

resource "aws_s3_bucket_policy" "hasura" {
  bucket = "${aws_s3_bucket.hasura.id}"

  policy = <<POLICY
{
  "Id": "hasuraALBWrite",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "hasuraALBWrite",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::hasura-${var.region}/alb/*",
      "Principal": {
        "AWS": [
          "${data.aws_elb_service_account.main.arn}"
        ]
      }
    }
  ]
}
POLICY
} 
```

如果您已经将 ACM 证书放在单独的 Terraform 堆栈中，那么您将需要导入它。

```
data "aws_acm_certificate" "hasura" {
  domain   = "hasura.${var.domain}"
  types       = ["AMAZON_ISSUED"] 
  most_recent = true
  statuses = ["ISSUED"]
} 
```

创建 ALB 本身。

```
resource "aws_alb" "hasura" {
  name            = "hasura-alb"
  subnets         = ["${aws_subnet.hasura_ecs.*.id}"]
  security_groups = ["${aws_security_group.hasura_alb.id}"]

  access_logs {
    bucket = "${aws_s3_bucket.hasura.id}"
    prefix = "alb"
    enabled = true
  }
} 
```

然后创建目标组。当 ECS 停止/开始
时，它们将向该目标组注册任务

```
resource "aws_alb_target_group" "hasura" {
  name        = "hasura-alb"
  port        = 8080
  protocol    = "HTTP"
  vpc_id      = "${aws_vpc.hasura.id}"
  target_type = "ip"
  health_check {
    path = "/"
    matcher = "302"
  }
} 
```

然后创建侦听器。如果已经导入，将`certificate_arn`设置为`"${data.aws_acm_certificate.hasura.arn}"`。

```
resource "aws_alb_listener" "hasura" {
  load_balancer_arn = "${aws_alb.hasura.id}"
  port              = "443"
  protocol          = "HTTPS"
  certificate_arn   = "${aws_acm_certificate.hasura.arn}"

  default_action {
    target_group_arn = "${aws_alb_target_group.hasura.id}"
    type             = "forward"
  }
} 
```

最后创建一个 Route 53 记录，指向您的 ALB

```
resource "aws_route53_record" "hasura" {
  zone_id = "${data.aws_route53_zone.hasura.zone_id}"
  name    = "hasura.${var.domain}"
  type    = "A"

  alias {
    name                   = "${aws_alb.hasura.dns_name}"
    zone_id                = "${aws_alb.hasura.zone_id}"
    evaluate_target_health = true
  }
} 
```

这就完成了 terraform 配置！你应该试一试！

堆栈应该用一个空模式和一个在`https://hasura.domain`监听的 Hasura 实例启动

祝你好运，如果你有任何意见，请随时打电话给我，或者你可以在哈苏拉不和谐中通过 [@elgordino](https://dev.to/elgordino) 找到我。

* * *

[@rayraegah](https://dev.to/rayraegah) 拿了这个帖子，把它变成了一个合适的 Terraform 模块。如果你想部署它，你应该看看这里:【https://github.com/Rayraegah/terraform-aws-hasura T2】