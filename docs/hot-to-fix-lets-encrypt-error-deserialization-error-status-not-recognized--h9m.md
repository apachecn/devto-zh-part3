# Hot to fix Let's encrypt error 反序列化错误:状态未识别。

> 原文：<https://dev.to/digitalsquad/hot-to-fix-lets-encrypt-error-deserialization-error-status-not-recognized--h9m>

测试续订认证时出现错误。

```
$sudo certbot renew --dry-run

Saving debug log to /var/log/letsencrypt/letsencrypt.log
-------------------------------------------------------------------------------
Processing /etc/letsencrypt/renewal/mydomain.com.conf
-------------------------------------------------------------------------------
Cert is due for renewal, auto-renewing...
Plugins selected: Authenticator nginx, Installer nginx
Starting new HTTPS connection (1): acme-staging-v02.api.letsencrypt.org
Renewing an existing certificate
Attempting to renew cert (mydomain.com) from /etc/letsencrypt/renewal/mydomain.com.conf produced an unexpected error: Deserialization error: Could not decode 'status' (u'ready'): Deserialization error: Status not recognized. Skipping.
All renewal attempts failed. The following certs could not be renewed:
  /etc/letsencrypt/live/mydomain.com/fullchain.pem (failure)
-------------------------------------------------------------------------------
** DRY RUN: simulating 'certbot renew' close to cert expiry
**          (The test certificates below have not been saved.)
All renewal attempts failed. The following certs could not be renewed:
  /etc/letsencrypt/live/mydomain.com/fullchain.pem (failure)
** DRY RUN: simulating 'certbot renew' close to cert expiry
**          (The test certificates above have not been saved.)
------------------------------------------------------------------------------- 
```

第一次，renew 命令成功完成，但第二次返回错误。
这个错误似乎出现在低于 0.25 版本的 certbot 上。
要修复此错误，请更新 certbot 包。

```
sudo yum update certbot -y 
```