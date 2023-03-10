# 需要定制 aws cloudwatch 监控理念

> 原文：<https://dev.to/taragrg6/custom-aws-cloudwatch-monitoring-ideas-required-10cb>

执行:我试图监控 EC2 实例上运行的 docker 容器。对于一个简单的用例，这就是我所尝试的。使用下面给出的简单 bash 脚本获取机器中运行的容器数量。

我将在 cronjob 上运行一次，每次运行时，它都会计算容器的运行次数，并将数据发送到 cloudwatch。

```
result=$(docker ps --format "{{.Names}}" | wc -l)
 aws cloudwatch put-metric-data --metric-name "Container running $result/6" --unit Count --value "$result" --dimensions Total-Container=$result --namespace System/Docker 
```

[![](img/6fa319b56e41e551c6b784f448485a15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xHdRBRgv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x9076ripysfux2szdp8l.png)

这可能是预期的 cloudwatch 行为，但我如何在图中跟踪最新的行为。