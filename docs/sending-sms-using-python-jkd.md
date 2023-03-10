# 使用 Python 发送短信

> 原文：<https://dev.to/mraza007/sending-sms-using-python-jkd>

每当我们谈论使用任何编程语言向我们的手机发送短信时，我们首先想到的是 Twilio SMS 库。但是在这篇文章中，我们将避免使用 twilio，而使用 python 内置模块`SMTPLIB`我知道这听起来很疯狂，因为`SMTPLIB`是用来发送电子邮件的，但是我们也可以使用该模块发送文本消息。为了发送短信到你的手机，你只需要知道短信网关。所以每个运营商都有自己的短信网关，你可以在这里了解更多[短信网关](https://en.wikipedia.org/wiki/SMS_gateway)。

### 每个运营商的短信网关

*   [number]@txt.att.net 美国电话电报公司
*   sprint:[number]@messaging.sprintpcs.com 或[数字] [@pm](https://dev.to/pm) .sprint.com
*   t-Mobile:[number]@tmomail.net
*   [number]@vtext.com 威瑞森
*   助推移动:[number]@myboostmobile.com
*   板球:[number]@sms.mycricket.com
*   地铁个人电脑:[number]@mymetropcs.com
*   [number]@mmst5.tracfone.com
*   美国移动电话公司:[number]@email.uscc.net
*   维珍移动:[number]@vmobl.com

## 因此，让我们开始编写脚本

*   首先，我们需要导入 smtplib 和 MIME 模块，这将有助于我们构建我们的消息，你可以在这里阅读更多关于 MIME 的内容[多用途互联网邮件扩展(MIME)](https://en.wikipedia.org/wiki/MIME)

```
import smtplib 
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

email = "Your Email"
pas = "Your Pass"

sms_gateway = 'number@tmomail.net'
# The server we use to send emails in our case it will be gmail but every email provider has a different smtp 
# and port is also provided by the email provider. smtp = "smtp.gmail.com" 
port = 587
# This will start our email server server = smtplib.SMTP(smtp,port)
# Starting the server server.starttls()
# Now we need to login server.login(email,pas)

# Now we use the MIME module to structure our message. msg = MIMEMultipart()
msg['From'] = email
msg['To'] = sms_gateway
# Make sure you add a new line in the subject msg['Subject'] = "You can insert anything\n"
# Make sure you also add new lines to your body body = "You can insert message here\n"
# and then attach that body furthermore you can also send html content. msg.attach(MIMEText(body, 'plain'))

sms = msg.as_string()

server.sendmail(email,sms_gateway,sms)

# lastly quit the server server.quit() 
```

这是我们简单的 python 脚本，可以让你发送短信到你的手机上。我希望你喜欢这篇文章，如果你认为我错过了什么，请随时在推特上给我留言

此外，这是我写的 python 模块，它允许你不用 twilio 就能发送文本消息。
[termtext](https://github.com/mraza007/terminal-text)
[结账我的网站](http://muhammadraza.me)