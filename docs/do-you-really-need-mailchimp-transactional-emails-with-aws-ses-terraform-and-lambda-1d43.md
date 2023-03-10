# 你真的需要 MailChimp 吗？(使用 AWS SES、Terraform 和 Lambda 的交易电子邮件)

> 原文：<https://dev.to/mailslurp/do-you-really-need-mailchimp-transactional-emails-with-aws-ses-terraform-and-lambda-1d43>

# 你真的需要 MailChimp 吗？(使用 AWS SES、Terraform 和 Lambda 的交易电子邮件)

## 什么是事务性邮件？

许多应用程序需要发送电子邮件。通常，这些电子邮件是在代码中触发的，以响应给定的事件，例如购买产品。因此，它们通常被称为事务性电子邮件。但是一个应用程序或后端服务不能只是发送一封电子邮件，它需要有一个 SMTP 服务器设置。设置电子邮件服务器可能很棘手，因此存在许多发送带有 API 请求的电子邮件的服务。主要玩家有:MailChimp (Mandrill)、Sendgrid、MailGun、SendinBlue。

然而，最近越来越多的开发者开始使用亚马逊的“简单电子邮件服务”或 SES。它不像其他的那样出名，但是它以开发人员为中心，非常适合任何现有的 AWS 基础设施。此外，很多时候 SES 更容易使用，也更便宜。这里有一个设置 SES 域、发送电子邮件和接收电子邮件的简单指南。稍后，我们将使用真实的电子邮件地址来测试您的电子邮件操作。

如果你想使用构建在 SES 上的强大的替代品，试试 mailsurp。

## 在 AWS 中构建基础设施

### 注册域名

首先，你需要一个发送和接收电子邮件的域名。如果你已经有了一个很好的，如果没有，那就去买一个吧。您还需要一个 AWS 帐户。

### 添加 Route53 域记录

接下来，我们需要将这个域的 MX 记录添加到 Route53。我将使用 Terraform 来建立我的 AWS 基础设施，因为从长远来看，它更易于维护。

首先让我们在 Terraform 中创建一些变量:

```
variable "zone_id" {
  default = "your-route-53-domain-zone-id"
}

variable "domain" {
  default = "your-domain-here"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们创建一个 SES 域标识和与之关联的 Route53 记录。这将让我们从域发送电子邮件。

```
# ses domain
resource "aws_ses_domain_identity" "ms" {
  domain = "${var.domain}"
}

resource "aws_route53_record" "ms-domain-identity-records" {
  zone_id = "${var.zone_id}"
  name = "_amazonses.mailslurp.com"
  type = "TXT"
  ttl = "600"

  records = [
    "${aws_ses_domain_identity.ms.verification_token}",
  ]
}

# ses dkim
resource "aws_ses_domain_dkim" "ms" {
  domain = "${aws_ses_domain_identity.ms.domain}"
}

resource "aws_route53_record" "ms-dkim-records" {
  count = 3
  zone_id = "${var.zone_id}"
  name = "${element(aws_ses_domain_dkim.ms.dkim_tokens, count.index)}._domainkey.mailslurp.com"
  type = "CNAME"
  ttl = "600"

  records = [
    "${element(aws_ses_domain_dkim.ms.dkim_tokens, count.index)}.dkim.amazonses.com",
  ]
}

# ses mail to records
resource "aws_route53_record" "ms-mx-records" {
  zone_id = "${var.zone_id}"
  name = "${var.domain}"
  type = "MX"
  ttl = "600"

  records = [
    "10 inbound-smtp.us-west-2.amazonses.com",
    "10 inbound-smtp.us-west-2.amazonaws.com",
  ]
}

resource "aws_route53_record" "ms-spf-records" {
  zone_id = "${var.zone_id}"
  name = "${var.domain}"
  type = "TXT"
  ttl = "600"

  records = [
    "v=spf1 include:amazonses.com -all",
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 设置 SES 收货规则

太好了，现在我们可以从我们注册的域名发送和接收了。但是我们仍然需要告诉 SES 使用这个域和一组电子邮件地址。这些配置称为收款规则集。让我们创建一个规则，捕捉我们域中任何地址的所有入站电子邮件，并将它们保存到 S3，并通知一个 SNS 主题。

```
# ses rule set
resource "aws_ses_receipt_rule_set" "ms" {
  rule_set_name = "ms_receive_all"
}

resource "aws_ses_active_receipt_rule_set" "ms" {
  rule_set_name = "${aws_ses_receipt_rule_set.ms.rule_set_name}"

  depends_on = [
    "aws_ses_receipt_rule.ms",
  ]
}

# lambda catch all
resource "aws_ses_receipt_rule" "ms" {
  name = "ms"
  rule_set_name = "${aws_ses_receipt_rule_set.ms.rule_set_name}"

  recipients = [
    "${var.domain}",
  ]

  enabled = true
  scan_enabled = true

  s3_action {
    bucket_name = "${aws_s3_bucket.ms.bucket}"
    topic_arn = "${aws_sns_topic.ms2.arn}"
    position = 1
  }

  stop_action {
    scope = "RuleSet"
    position = 2
  }

  depends_on = ["aws_s3_bucket.ms", "aws_s3_bucket_policy.ms_ses", "aws_lambda_permission.with_ses"]
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可能还想捕捉电子邮件错误。让我们为此添加一条规则，并将它们发送到 cloudwatch 日志中。

```
resource "aws_ses_configuration_set" "ms" {
  name = "ms-ses-configuration-set"
}

resource "aws_ses_event_destination" "ses_errors" {
  name = "ses-error-sns-destination"
  configuration_set_name = "${aws_ses_configuration_set.ms.name}"
  enabled = true

  matching_types = [
    "reject",
    "reject",
    "send",
  ]

  sns_destination {
    topic_arn = "${aws_sns_topic.ms2_ses_error.arn}"
  }
}

resource "aws_ses_event_destination" "ses_cloudwatch" {
  name = "event-destination-cloudwatch"
  configuration_set_name = "${aws_ses_configuration_set.ms.name}"
  enabled = true

  matching_types = [
    "reject",
    "reject",
    "send",
  ]

  cloudwatch_destination = {
    default_value = "default"
    dimension_name = "dimension"
    value_source = "emailHeader"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### SNS 如何融入

因此，在上述规则集中，我们为所有入站电子邮件定义了一个“全部捕获”。它的行动是保存电子邮件给 S3，然后通知一个 SNS 主题。SNS 是一个简单的通知服务，我们可以在我们的应用程序中订阅，或者使用 lambda 来处理入站电子邮件。我们在这些处理程序中接收的事件将包含一个链接，该链接指向包含电子邮件的 S3 存储桶项目。下面是我们如何用 lambda 在 Terraform 中建立一个 SNS 主题来处理事件。

```
resource "aws_sns_topic" "ms2" {
  name = "ms2-receipt-sns"
}

resource "aws_sns_topic_subscription" "ms2_receive" {
  topic_arn = "${aws_sns_topic.ms2.arn}"
  protocol = "lambda"
  endpoint = "${aws_lambda_function.ms_receive_mail.arn}"
}

resource "aws_sns_topic" "ms2_ses_error" {
  name = "ms2-ses-error"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 创建一个 Lambda 来处理你的邮件

在这一点上，你可以对 SNS 主题做任何你想做的事情来处理电子邮件。你可以在你的应用程序中订阅它，或者你可以把它挂在一个 Lambda 上。我将向您展示如何为这种事件设置一个 Lambda，并对入站电子邮件采取行动。

下面是 Lambda 可能的样子:

```
import os
from botocore.vendored import requests

# handle the event here
# (the object will contain a url to the S3 item containing the full email)
def handler(event, context):
    print(event) 
```

Enter fullscreen mode Exit fullscreen mode

这里是我们如何使用 Terraform 部署 Lambda。

```
# this points to your lambda python script and zips it during terrafom apply
data "archive_file" "ms_receive_mail" {
  type = "zip"
  source_file = "${path.module}/${var.receive_dist}"
  output_path = "${path.module}/dist/receive.zip"
}

# receive mail lambda definition uses the data archive file
resource "aws_lambda_function" "ms_receive_mail" {
  role = "${aws_iam_role.lambda.arn}"
  handler = "lambda.handler"
  runtime = "python3.6"
  filename = "${data.archive_file.ms_receive_mail.output_path}"
  function_name = "ms_receive_mail"
  source_code_hash = "${base64sha256(file(data.archive_file.ms_receive_mail.output_path))}"
  timeout = "${var.receive_lambda_timeout}"
}

# allow sns to invoke the lambda
resource "aws_lambda_permission" "sns_notify_ms_receive" {
  statement_id = "AllowExecutionFromSNS"
  action = "lambda:InvokeFunction"
  function_name = "${aws_lambda_function.ms_receive_mail.function_name}"
  principal = "sns.amazonaws.com"
  source_arn = "${aws_sns_topic.ms2.arn}"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 SES

### 收到邮件

我们现在有 Route53、SES、SNS 和 Lambda 来处理入站电子邮件。我们现在可以从某个电子邮件客户端向“[test@mydomain.com](//mailto:test@mydomain.com)”发送电子邮件，并观察我们的 Lambda 被调用。从这一点上来说，这取决于你想如何处理入站电子邮件事件。

### 发送事务性邮件

现在来看文章的关键:发送事务性电子邮件。有了上面创建的设置，我们现在可以通过简单的 API 调用 AWS 从我们注册的域名上的任何地址发送电子邮件。为此，最好的方法是使用给定语言的 AWS SDK。我在 MailSlurp 中使用了很多 Kotlin，所以我将使用 AWS SES SDK for Java 向您展示我的方法。

```
// gradle dependencies
dependencies {
        compile "com.amazonaws:aws-java-sdk-ses:${awsVersion}"
}

// lets create a client for sending and receiving emails with SES
@Service
class SESClient {

    @Autowired
    lateinit var appConfig: AppConfig

    lateinit var client: AmazonSimpleEmailService

    @PostConstruct
    fun setup() {
        client = AmazonSimpleEmailServiceClientBuilder.standard().withRegion(appConfig.region).build()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是你如何给客户发送电子邮件。

```
@Service
class SESService : MailService {

    @Autowired
    private lateinit var appConfig: AppConfig

    @Autowired
    private lateinit var sesClient: SESClient

    override fun sendEmail(emailOptions: SendEmailOptions) {
        // validate options
        if (emailOptions.to.isEmpty()) {
            throw Error400("No `to address` found for send")
        }
        // build message
        val content = Content().withCharset(emailOptions.charset).withData(emailOptions.body)
        val body = if (emailOptions.isHTML) {
            Body().withHtml(content)
        }
        else {
            Body().withText(content)
        }
        val message = Message()
                .withBody(body)
                .withSubject(Content().withCharset(emailOptions.charset).withData(emailOptions.subject))
        val request = SendEmailRequest()
                .withDestination(Destination()
                        .withToAddresses(emailOptions.to)
                        .withBccAddresses(emailOptions.bcc)
                        .withCcAddresses(emailOptions.cc))
                .withMessage(message)
                .withReplyToAddresses(emailOptions.replyTo.orElse(appConfig.defaultReplyTo))
                .withSource(emailOptions.from.orElseThrow { Error400("Missing `from address` for email send") })

        // send
        sesClient.client.sendEmail(request)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## AWS SES 的利弊

SES 很棒，但也有一些缺点:

*   没有供非编码人员发送和接收电子邮件的 GUI
*   没有事务性电子邮件联系人管理
*   缺少许多使其他供应商变得优秀的功能

积极因素包括:

*   比其他供应商更便宜
*   更多控制
*   适合现有基础设施

## 测试您的交易邮件

所以，如果我们能确定它的可靠性，所有这些基础设施都没有多大用处。这就是端到端测试电子邮件功能如此重要的原因。我们可以使用 [MailSlurp](https://www.mailslurp.com) 测试我们的 infra 是否接收和处理真实的电子邮件！

### 邮件测试是如何进行的？

基本上，MailSlurp 是一个 API，它让你创建新的电子邮件地址用于测试。你可以创建任意多的邮件，并通过 API 或 SDK 发送和接收邮件。

### 编写入站邮件测试

首先你需要注册 MailSlurp 并获得一个免费的 API 密匙。然后让我们使用其中一个 SDK 来测试我们的域。

我们可以使用`npm install mailslurp-client`安装 javascript 的 SDK。那么在 Jest 或 Mocha 这样的测试框架中，我们可以写:

```
// javascript jest example. other SDKs and REST APIs available
import * as MailSlurp from "mailslurp-client";
const api = new MailSlurp({ apiKey: "your-api-key" });

test("my app can receive and handle emails", async () => {
  // create a new email address for this test
  const inbox = await api.createInbox();

  // send an email from the new random address to your application
  // the email will by sent from something like '123abc@mailslurp.com`
  await api.sendEmail(inbox.id, { to: "contact@mydomain.com" });

  // assert that app has handled the email in the way that we chose
  // this function would be written by you and validate some action your lambda took
  expect(myAppReceivedEmail()).resolves.toBe(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行这个测试并通过，这意味着我们所有的基础设施都在工作，我们的 Lambda 正在正确地处理我们的事件。

> 我们刚刚用一个真实的电子邮件地址和真实的电子邮件测试了我们的入站电子邮件处理代码。

### 测试交易类邮件(是否实际发出？)

最后，让我们测试一下我们的应用程序是否能真正发送电子邮件，以及它们是否能被正确接收。我们可以通过每次向我们自己的个人帐户发送电子邮件并手动确认它的存在来做到这一点，但这并不适合自动 CD 测试套件。使用 MailSlurp，我们可以在自动化测试过程中使用真实地址测试接收的或真实的交易电子邮件。这确保了我们应用程序的关键部分(电子邮件)实际上正在工作。

它看起来像什么？

```
// can your app send emails properly
import * as MailSlurp from "mailslurp-client"
const api = new MailSlurp({ apiKey: "your-api-key" })

test('my app can send emails', async () => {
    // create a new email address for this test
    const inbox = await api.createInbox()

    // trigger an app action that sends an email
    // to the new email address. this might be a new sign-up
    // welcome email, or a new product payment for example
    await signUpForMyApp(inbox.emailAddress)

    // fetch welcome email from the inbox we created
    const emails = await api.getEmails(inbox.id, { minCount: 1 })

    // assert that the correct email was sent
    expect(emails[0].length).toBe(1)
    expect(emails[0].content).toBe(expectedContent)

    // profit!
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果测试通过，我们可以放心，我们的应用程序将在正确的时间发送事务性电子邮件，并且这些电子邮件实际上已经发送了！这太棒了！

## 把一切都包起来

总之，事务性电子邮件是许多应用程序的重要组成部分。使用 AWS SES 这样做是获得对 MailChimp 和 MailGun 等其他服务更多控制的一个好方法。然而，无论您选择哪种解决方案，请记住使用真实的电子邮件地址来测试您的事务性电子邮件收发。MailSlurp 使这变得简单和自动化，因此你可以确定你的应用程序正在发送和接收它应该发送和接收的内容。

我希望这有所帮助！

谢谢，

MailSlurp 的 Jack