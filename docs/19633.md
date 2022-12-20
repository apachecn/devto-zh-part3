# apache solr -隐藏/编辑敏感属性

> 原文：<https://dev.to/risdenk/apache-solr---hideredact-senstive-properties-56ca>

### 概述

Apache Solr 是一个基于 [Apache Lucene](https://lucene.apache.org/solr/) 的全文搜索引擎。在 [solr-user](http://lucene.apache.org/solr/community.html#mailing-lists-irc) 邮件列表上的一个常见问题(即:这里[这里](http://lucene.472066.n3.nabble.com/Disabling-jvm-properties-from-ui-td4413066.html)和这里[这里](http://lucene.472066.n3.nabble.com/jira-Commented-SOLR-11369-Zookeeper-credentials-are-showed-up-on-the-Solr-Admin-GUI-td4405383.html))是如何对 [Solr UI](https://lucene.apache.org/solr/guide/7_5/overview-of-the-solr-admin-ui.html) 隐藏敏感值。有一个鲜为人知的设置可以隐藏这些敏感值。

### Apache Solr 并隐藏敏感属性

Apache Solr 有几个地方可以在 Solr UI 上看到敏感值。密钥库和信任库密码是作为 [SOLR-10076](https://issues.apache.org/jira/browse/SOLR-10076) 的一部分出现的两个例子。从 Solr 6.6 和 7.0 开始，当系统属性`solr.redaction.system.enabled`设置为 true 时，Solr 将隐藏`/admin/info/system` API 中包含单词`password`的任何属性。`/admin/info/system` API 用于支持 Solr UI。这在大多数情况下都能很好地工作，但是实现更加通用，使它能够隐藏任何自定义属性。

属性`solr.redaction.system.pattern`是一个采用正则表达式的系统属性。如果正则表达式匹配属性名，那么系统属性值将被删除。这可以隐藏定制库或其他用例的敏感值。

下表列出了 Solr 6.6 或更高版本中可以配置的两个属性。

| 财产 | 缺省值 | 目的 |
| --- | --- | --- |
| `solr.redaction.system.enabled` | `false`在 Solr 6.6 中；`true`在 Solr 7.0 中 | 启用或禁用密文 |
| `solr.redaction.system.pattern` | `.*password.*` | 要密文的属性的正则表达式 |

### Apache Solr 和隐藏指标属性

[Solr Metrics API](https://lucene.apache.org/solr/guide/7_5/metrics-reporting.html) 也可能泄露敏感信息。有一个 [`hiddenSysProps`配置](https://lucene.apache.org/solr/guide/7_5/metrics-reporting.html#the-metrics-hiddensysprops-element)可以防止某些属性通过度量 API 暴露。如果需要隐藏其他属性，则需要在`hiddenSysPropes`部分进行配置。

### 结论

目前，关于隐藏敏感值的可用选项的文档非常有限。不得不在两个地方配置隐藏敏感值是令人沮丧的，但仍有改进的希望。本月早些时候创建了 SOLR-12976 ，试图解决重复和文档化问题。