# 配置和管理 Azure 密钥库

> 原文：<https://dev.to/cheahengsoon/configure-and-manage-azure-key-vault-3foj>

**管理对密钥库的秘密、证书和密钥的访问和权限**

*   您可以通过两个界面控制对密钥库的访问:管理平面和数据平面
*   管理平面:
    *   操作:创建和删除保管库，更新访问策略，检索保管库属性
    *   身份验证:Azure AD
    *   授权:RBAC
*   数据平面:
    *   操作:查看和管理证书、密钥和机密
    *   身份验证:Azure AD
    *   授权:密钥保管库访问策略

**管理证书**
-支持保险库级访问控制
-审计和通知
-支持证书联系人
-自动更新
[![Alt text of image](img/eee1fc6e7d2de7a04f5053a69ab350cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WhXZsbP7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gd90h9ejvjun65px785h.png)

**配置 HSM 密钥生成解决方案**

*   Azure 硬件安全模块的主要特征:
    *   符合联邦信息处理标准(FIPS)140-2 2 级安全标准
    *   由 Azure Key Vault 管理的主机加密材料
    *   支持 HSM 边界内的加密操作
    *   支持在自带密钥(BYOK)情况下安全传输现有密钥
*   传输受 HSM 保护的密钥:
    *   客户在其内部环境中生成密钥
    *   客户使用目标密钥库来生成不可移植的密钥交换密钥
    *   客户使用密钥交换密钥对密钥进行加密，并通过使用特定于 HSM 的工具集将其绑定到保险库
    *   客户将密钥转移到密钥库中

**上传秘密**
[![Alt text of image](img/5934e4aac18b7c8f4062f8f477aa9c19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_d18SG_F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3egv3f9stj7ddlnxf7ct.png)

**配置密钥旋转**

*   密钥库允许您在不影响应用程序行为的情况下更新密钥和机密
*   您可以通过多种方式轮换机密:
    *   作为手动过程的一部分
    *   通过使用 REST API 以编程方式
    *   通过 Azure 自动化脚本