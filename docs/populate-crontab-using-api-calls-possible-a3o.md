# 使用 api 调用填充 crontab 可能吗？

> 原文：<https://dev.to/iakashpatel/populate-crontab-using-api-calls-possible-a3o>

好的，我正在考虑使用 http api 调用或其他方式来管理 crontab 任务。

我用 body 调用 api /cron/add 时的示例

{
命令:“回声‘ABC’”，
时间:“* * * * *”
}

是否有可能实现这样的事情，如果是，那么你可以给我一些提示，我如何可以或使用哪些工具，我可以做到这一点？

我已经为 crontab 任务运行了 ec2 实例，但是我希望使用 api 调用自动添加/删除它们

谢了。