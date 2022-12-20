# 使用 Hashicorp Vault 动态保护数据库

> 原文：<https://dev.to/xfrarod/dynamically-securing-databases-using-hashicorp-vault-2kf>

如今，很难深入讨论 it 行业的安全性，因为它必须在这么多不同的层面上考虑:从保护代码块、保护容器，到保护复杂的基础架构，以及在整个企业中定义强大的授权和身份验证策略。

Vault 是一个工具，可以让您存储机密，您的公司需要保护的任何类型的机密，还可以让您动态生成它们。

## 一般语境

我将用一个简单的例子来解释这一点:让我们假设您必须保护一个数据集市，它包含一些由不同微服务访问的数据库，每个微服务都可以访问自己的数据库，并且还可以根据需求动态地扩展和缩小。每当实例数量增加时，都会部署新的微服务，并且每个实例都需要提供适当的配置。

[![](img/8586676b0aa36a39ea2be71dceceaf04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1IhZ8LeH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6l4b9lfzr5r6yrffxl8a.png)

配置的一个重要部分是数据库凭证，它通常是微服务所有实例的静态值。因此，通常用于在部署时向每个微服务提供凭证和秘密的典型方法并不科学:

*   可以作为明文或加密的环境变量提供。
*   可以在每个实例的配置文件中提供
*   假设您在 Kubernetes 环境中处理您的编排，可以作为配置图或秘密提供。

我可以举一些其他的例子，但是处理你的数据库秘密总是一种手动的过程；想一想，如果由于某种原因，您不得不更改数据库的用户和密码，因为它们被破坏了，该怎么办？例如，满足每 90 天轮换一次密码的合规性要求如何？更改凭据的含义是什么？

仅举一个例子，可能的情况是，微服务的所有实例都必须使用新的凭证重新部署，或者至少重新启动，以便采用新的值。或者您可能需要更改 k8s 集群上的配置映射或密码，等等。我不想深入讨论这个问题，而是考虑一个更广泛的场景，在这个场景中，不仅仅是一个数据库，而是所有的数据库都受到了威胁；解决这个问题的努力可能是巨大的。

这个保护数据库的简单场景可以扩展到其他一些领域，比如 ssh 密钥、证书、令牌、身份、凭证、加密即服务等。

如果您在一个集中的存储生态系统中管理所有这些，您可以在不中断操作的情况下迅速采取行动。你可以让你的所有系统在没有人工干预的情况下进行自我认证，这是 DevSecOps 良好实现的目标。

回到我们的数据库示例，您可以让所有微服务向 vault 请求凭据，vault 会在每次部署微服务时为每个微服务实例动态创建凭据，并根据明确定义的安全策略轮换/撤销凭据。下图显示了 vault 如何实现这一点:

[![](img/9938d85b10fcbc670b2c361aa804e05e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BtFsdRyb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fhc1lep7i2xl1aqjmjqf.png)

## 微服务的认证/授权

在开始向数据库请求动态凭证之前，每个微服务都应该通过内部或外部系统的认证。身份验证是对每个微服务进行验证并为其分配身份的过程。支持多种身份验证方法。

一旦微服务通过验证，它必须考虑与这种身份相关的访问控制和权限，这称为授权。Vault 通过定义 Vault 中的策略来处理授权，这些策略控制用户或微服务可以访问的内容。例如:

```
path "database/*" {
  capabilities = ["create"]
}
path "database/creds/my-role" {
  capabilities = ["read"]
} 
```

Enter fullscreen mode Exit fullscreen mode

## 设置流程

为了在运行环境中进行设置，您必须遵循以下简单步骤:

1.  从 vault 端:管理员用户必须通过 Vault 的认证，然后他应该启用数据库密码并配置 postgres 插件，这需要创建一个创建动态凭证的角色。
2.  从应用程序端来看:应该为应用程序提供一个有效的令牌以便进行身份验证，然后，通过 API，它可以动态地请求 postgres 凭据，如下图所示:

[![](img/8819dc39ab83e67b849f0fd73ca4c465.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oV0dcJdt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p071ugszn0j1rtdojck9.png)

### 为启用数据库机密运行以下命令:

```
$ vault secrets enable database
Success! Enabled the database secrets engine at: database/ 
```

Enter fullscreen mode Exit fullscreen mode

### 用于配置 postgres 插件

```
$ vault write database/config/myapp plugin_name="postgresql-database-plugin"  connection_url="postgresql://postgres:postgres@postgres:5432/myapp?sslmode=disable" allowed_roles="my-role, readonly" 
```

Enter fullscreen mode Exit fullscreen mode

### 创建一个角色，该角色将 vault 中的名称映射到要执行的 SQL 语句，以创建数据库凭据

```
$ vault write database/roles/my-role \
    db_name=myapp \
    creation_statements="CREATE ROLE \"{{name}}\" WITH LOGIN PASSWORD '{{password}}' \
    VALID UNTIL '{{expiration}}'; 
    GRANT SELECT ON ALL TABLES IN SCHEMA public TO \"{{name}}\";" \
    default_ttl="5m" \
    max_ttl="1h"

Success! Data written to: database/roles/my-role 
```

Enter fullscreen mode Exit fullscreen mode

### 生成 postgres 秘密

```
$ vault read /database/creds/my-role
Key                Value
--------                -----
lease_id           database/creds/my-role/6E252RJKZOid7rpUxHEeaabT
lease_duration     1m
lease_renewable    true
password           A1a-2mfSG9ClFPjt42Hi
username           v-root-my-role-2PvoHLCRL3mK1kmE3w1J-1544215033 
```

Enter fullscreen mode Exit fullscreen mode

### 您也可以通过 API 生成密码

```
$ curl --header "X-Vault-Token: ${ROOT_TOKEN}" http://localhost:8200/v1/database/creds/my-role |  jq -r

{
  "request_id": "cc08db9d-a665-0cb6-188d-20cf570d78b1",
  "lease_id": "database/creds/my-role/5OidV08wNZLXkWgYTxR84n3u",
  "renewable": true,
  "lease_duration": 300,
  "data": {
    "password": "A1a-5xXk6hSwUfK3fzYW",
    "username": "v-root-my-role-5njEgQlMloc4vqOZtUA6-1544217564"
  },
  "wrap_info": null,
  "warnings": null,
  "auth": null
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个基本的使用案例，说明如何使用 Vault 来动态保护数据库以及其他许多东西，以便为您的微服务和数据库提供更安全的部署。