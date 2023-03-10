# 不喜欢 JIRA 的邮件处理程序？用 SES 和 Lambda 扩展。

> 原文：<https://dev.to/tportela1/don-t-like-jira-s-mail-handler-extend-it-with-ses-and-lambda-2f5c>

在我目前的工作环境中，吉拉管理是由一个单独的团队处理的。我已经创建了服务帐户供吉拉邮件处理程序使用，但是后来意识到邮件处理程序缺少很多功能！什么叫不能动态设置 component、issueType、assignee？

嗯。那是不行的。

我决定利用 JIRA API 和亚马逊的简单电子邮件服务，以及 Lambda。
[![alt text](img/f230a5e460f9592caf41f158cdf2ca2e.png "Architecture Diagram Flow of email --> SES --> Lambda --> Atlassian API")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--muYyQbPN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nh7v0itl16elxcm60hor.png)

让我们一步一步地分解它。首先是配置亚马逊的简单电子邮件服务来接收电子邮件，然后把它写给 S3。注意:利用 SES 提取电子邮件正文的唯一方法(目前)是将其写入简单通知服务(SNS)或简单存储服务(S3)。如果你需要帮助，我有另一篇文章可以告诉你如何创建它。

在 Lambda 中，下面是我的代码。我利用了一个惊人的 [Atlassian Python 库](https://atlassian-python-api.readthedocs.io/en/latest/index.html)。

```
import json, requests, email, boto3, urllib, time, os, base64, re
from JiraPy import *

def handler(event, context):

    s3 = boto3.client('s3')
    s3r = boto3.resource('s3')
    outputBucket = "jira"

    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.unquote_plus(event['Records'][0]['s3']['object']['key']).decode('utf8')

    try:
        if not outputBucket:
            global outputBucket
            outputBucket = bucket

        # Use waiter to ensure the file is persisted
        waiter = s3.get_waiter('object_exists')
        waiter.wait(Bucket=bucket, Key=key)
        response = s3r.Bucket(bucket).Object(key)

        # Read the raw text file into a Email Object
        msg = email.message_from_string(response.get()["Body"].read())
        messageTo = msg['To']
        messageFrom = msg['From']
        messageSubject = msg['Subject']
        messageBody = ''
        if msg.is_multipart():
            for part in msg.walk():
                ctype = part.get_content_type()
                cdispo = str(part.get('Content-Disposition'))
                # skip any text/plain (txt) attachments
                if ctype == 'text/plain' and 'attachment' not in cdispo:
                    messageBody = part.get_payload(decode=True)  
                    break
    else:
            messageBody = msg.get_payload(decode=True)

        if len(msg.get_payload()) == 2:
            attachment = msg.get_payload()[1]

        else:
            data = messageSubject.split('|')
            projectKey = data[0]
            issueType = data[1]
            component = data[2]
            summary = data[3]
            print(projectKey, issueType, messageFrom, component, summary)
            createIssue(projectKey, issueType, messageFrom, component, summary, messageBody)

    except Exception as e:
        print(e)
        raise e 
```

脚本的吉拉部分看起来像:

```
def createIssue(projectKey, issueType, assignee, component, summary, messageBody):
    if issueType == "Epic":
        jira.issue_create(fields={
        'project': {'key': projectKey},
        'issuetype': {
            "name": issueType},
        'customfield_10605': summary,
        'assignee': {'name': assignee},
        'components': [{'name': component}],
        'summary': summary,
        'description': messageBody,
        })
    else:
        jira.issue_create(fields={
        'project': {'key': projectKey},
        'issuetype': {
            "name": issueType},
        'assignee': {'name': assignee},
        'components': [{'name': component}],
        'summary': summary,
        'description': messageBody,
        })

def updateIssue(issueKey, messageBody):
    jira.issue_add_comment(issueKey, messageBody) 
```

相当简单！

这是一个对最终用户来说是什么样子的可视化表示。

[![alt text](img/d86f55e1a725b4be66a0a7a7297abaea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WSvRULvw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/542xazfoczubp4kxh833.png) “最终用户的电子邮件流”

很想听听你的想法，希望这能对处于同样困境的其他人有所帮助。