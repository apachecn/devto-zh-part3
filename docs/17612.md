# 在 locopy 上寻找反馈/帮助

> 原文：<https://dev.to/fdosani/looking-for-feedbackhelp-on-locopy-5c1h>

大家好，

这里是第一个帖子。我是如何偶然发现 dev.to 的，这似乎是一个学习和合作的好地方！想分享我正在做的一个项目。这不是什么花哨的东西，但我会喜欢任何反馈/帮助，因为这是我第一次真正尝试维护一个包。

`locopy`帮助抽象一些从 Amazon Redshift 获取数据的繁琐工作。它帮助获取进出 S3 的数据，并构建要执行的`COPY` / `UNLOAD`。它让用户选择他们的 DBAPI 包(`psycopg2`、`pg8000`等)。)vs 强加一个在他们身上。

我目前正在做一些重构，也希望添加更多我可以支持的数据库(雪花，Postgres 等)。)

[https://github . com/capital one/Data-Load-and-Copy-using-Python](https://github.com/capitalone/Data-Load-and-Copy-using-Python)

感谢任何想法，或帮助！