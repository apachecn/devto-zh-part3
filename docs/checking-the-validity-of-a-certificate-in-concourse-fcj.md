# 在 Concourse 中检查证书的有效性

> 原文：<https://dev.to/sirech/checking-the-validity-of-a-certificate-in-concourse-fcj>

我们最近遇到了一个情况，我们使用 [LetsEncrypt](https://letsencrypt.org/) 创建的一个证书即将过期，由于一封提醒邮件，我们几乎没有及时注意到。出于各种原因，我们现在不能自动更新，但这让我觉得依赖电子邮件按时阅读只是自找麻烦。

我所做的是让这个检查成为我们在[广场](https://concourse-ci.org/)的构建管道的一部分，它应该在证书到期前 30 天被触发，这样我们就有足够的时间来处理它。

## 在中央大厅重复检查

*Concourse* 使用[时间](https://github.com/concourse/time-resource)资源来模拟周期性触发。您可以像这样定义每日检查:

```
- name: 24h
  type: time
  source:
    interval: 24h
    start: 11:00 AM
    stop: 11:15 AM
    location: Europe/Berlin 
```

然后，您可以通过获取以下资源来定义每 24 小时触发一次的作业:

```
- name: certificate
  serial: true
  plan:
  - aggregate:
    - get: git
      passed: [prepare]
      trigger: true
    - get: 24h
      trigger: true
    - get: dev-container
      passed: [prepare]
  - task: check-certificate
    image: dev-container
    params:
      CERT: certs/fullchain.pem
    file: git/pipeline/tasks/check-certificate/task.yml 
```

与你在像 *Jenkins* 这样的东西中所做的相比，使用计时器作为资源有点古怪，但是一旦你习惯了 *Concourse* 如何做事，使用资源来模拟来自外部的每个“输入”是有意义的。

## 检查证书

我们可以利用`openssl`来完成这项检查，并通过将它安装在容器上(如果您使用 alpine，则通过`apk`)将其集成到我们的管道中。检查证书的脚本如下所示:

```
#!/bin/sh

set -e

export THIRTY_DAYS=2592000

if openssl x509 -checkend "${THIRTY_DAYS}" -noout -in "${CERT}"
then echo "Certificate is good for another day!"
  exit 0
else echo "Certificate has expired or will do so within 30 days!"
  exit 1
fi 
```

您可以看到，我们正在检查证书文件，它是服务于该域的代理的一部分，而不是实时 URL。这依赖于您的管道足够频繁地运行，以便您拥有的与部署的相匹配。

## 就这样？

差不多吧！有了这个检查，你的管道将在证书到期前一个月变成红色，给你足够的时间去做一些事情。下一步不仅可以自动发出警报，还可以自动更新证书。