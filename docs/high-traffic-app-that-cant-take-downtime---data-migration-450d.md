# 不能停机的高流量应用程序-数据迁移

> 原文：<https://dev.to/mscccc/high-traffic-app-that-cant-take-downtime---data-migration-450d>

当您不能停机时，数据迁移的“诀窍”是:**考虑小的变化。**

1.  新表格
2.  更新写入以转到旧的和新的
3.  将旧数据迁移到新数据的后台作业
4.  将读取移动到新表
5.  停止写入旧表

这些步骤中的每一步都是部署。