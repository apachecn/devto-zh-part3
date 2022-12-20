# 列出来自 CloudTrail 事件的 IP

> 原文：<https://dev.to/abdallah/list-ips-from-cloudtrail-events-p4l>

列出来自 AWS CloudTrail 事件的 IP 的快速命令。

```
#!/bin/bash ACCESS\_KEY\_ID=AKIASMOETHINGHERE MAX\_ITEMS=100 aws cloudtrail lookup-events --lookup-attributes AttributeKey=AccessKeyId,AttributeValue=${ACCESS\_KEY\_ID} --max-items ${MAX\_ITEMS} \ | jq -r '.Events[].CloudTrailEvent' \ | jq '.sourceIPAddress' \ | sort | uniq 
```

这当然可以扩展到包括更多的信息，例如:

```
#!/bin/bash ACCESS\_KEY\_ID=AKIASMOETHINGHERE MAX\_ITEMS=100 aws cloudtrail lookup-events --lookup-attributes AttributeKey=AccessKeyId,AttributeValue=${ACCESS\_KEY\_ID} --max-items ${MAX\_ITEMS} \ | jq -r '.Events[].CloudTrailEvent' \ | jq '{ User: .userIdentity.userName, IP: .sourceIPAddress, Event: .eventName }' 
```