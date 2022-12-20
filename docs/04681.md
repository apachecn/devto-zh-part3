# 出于企业目的，如何扩展 python 服务器？

> 原文：<https://dev.to/perigk/how-to-scale-python-server-for-enterprise-purposes-1nfp>

我正在研究如何扩展 flask-python API 的解决方案。

我见过的(适合企业级的)解决方案有:

*   Gunicorn 和几个工人
*   芹菜或类似的技术来完成不简单的任务
*   使用负载平衡器的多重处理
*   以上各项的组合

您是否介意建议您可能见过的任何其他方法，或者对上述方法进行评论？

为了简单起见，我们假设没有绑定到特定的云供应商。