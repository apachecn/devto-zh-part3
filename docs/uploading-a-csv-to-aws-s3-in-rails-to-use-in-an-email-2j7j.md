# 在 Rails 中将 CSV 上传到 AWS S3 以便在电子邮件中使用

> 原文：<https://dev.to/lukewduncan/uploading-a-csv-to-aws-s3-in-rails-to-use-in-an-email-2j7j>

最近，我们为一个客户(我们称他们为客户 X)处理了一个问题，这个客户已经跟了我们好几年了。他们在我们的系统中有成千上万的记录，我们开始处理规模问题。

对于不是客户 X 的典型客户，他们只需进入我们创建的控制面板，点击一个按钮，就可以直接从浏览器下载包含数百条记录的 CSV 报告。对于客户 X 来说，这根本行不通，因为后端的计算对于浏览器来说耗时太长，并且经常超时。这是一个用后台工作解决的简单问题，这正是我们几年前所做的。

我们实现了一个后台作业来运行查询，当准备就绪时，将允许用户从我们发送给他们的电子邮件中下载报告。对于客户 X 来说，这是一个完美的解决方案，直到另一个问题出现。下面是在后台工作中执行的电子邮件方法:

```
def email_sales_report(email)
  @report = Reports::Sales.new(report_params)
  attachments['sales_report.csv'] = @report.csv
  mail to: email, subject: "Email Sales Report #{Time.now}"
end 
```

这看起来很好，对不对？

我们认为它也是，直到有一天我们开始收到关于这封邮件报告无法发送的错误。经过几个小时的挖掘和不相关的错误消息，我们发现电子邮件服务器无法处理我们试图使用的附件大小。

为了让这个报告再次工作，我们必须想出一个解决方案，我们可以托管 CSV 并从电子邮件链接到它。AWS S3，你们大多数人应该知道是“简单的存储服务”,允许你存储文档，文件和更多是完美的解决方案。

下面是我们在 Rails 应用程序中设置它的基本步骤:

## 将 AWS SDK 添加到 gemfile 并捆绑安装

对于本教程，如果您的 Rails repo 中还没有 AWS-SDK，请继续添加它，然后进行捆绑安装。

```
gem 'aws-sdk' 
```

## 设置初始化器

首先我们设置一个初始化器让 S3 工作。我们在自己创建的文件中这样设置— `/config/initializers/amazon_s3.rb`

我们将它设置为一个全局变量，可以在 rails 应用程序的任何地方通过亚马逊 S3 客户端
访问它

```
AMAZON_S3_CLIENT = Aws::S3::Resource.new(region: 'us-west-1',
  access_key_id: 'XXXXXXXXXXXXXXX',
  secret_access_key: 'XXXXXXXXXXXXXXXXXXXXXXXXX'
) 
```

## 创建类命令来实例化 S3 上传

我们在整个应用程序中使用命令，这些命令被放入后台作业中，就像我们在报告中讨论的那样。我们创建了另一个将 CSV 上传到 S3 的命令。这是我们如何在下面一个名为`commands/reports/upload_to_s3.rb` :
的文件中设置它

```
class Report::UploadToS3 < Command::Base
  attribute :csv

  action do
    self.set_aws_bucket
    obj = AMAZON_S3_CLIENT.bucket(@bucket_path).object("#{Time.now}/report.csv")
    obj.put(body: csv, acl: 'public-read', content_disposition: 'attachment')
    return obj.public_url
  end

  def set_aws_bucket
    case Rails.env
    when 'production' then
      @bucket_path = 'csv'
    when 'staging' then
      @bucket_path = 'staging.csv'
    else
      @bucket_path = 'dev.csv'
    end
  end
end 
```

我想指出几件事。请注意，我们根据我们工作的环境使用不同的存储桶路径，我们不想混合和匹配 csv 上传。

Next —您将看到 Time.now 包含在用于在 S3 保存文件名的字符串中。这将创建一个名为“01/01/2019 18:00:00”的文件夹，并在其中保存实际文件。这只是为了说明您可以创建子文件夹。

在我们实际的应用程序中，我们做了一些复杂的事情来为个人用户创建文件夹，这样每个用户都有自己的文件夹，我们可以保存他们下载的所有报告。您应该能够自己找到实现这一点的方法，但是强烈建议您以安全的方式这样做，不要使用任何常见的标识符，如 user_id 和类似的东西。

最后，您会注意到 content _ disposition:“attachment”非常重要，因为它允许下载 CSV，ACL:“public-read”也很重要，因为它使您的客户可以查看您的 CSV。此文件的 URL 使用 public_url 实例方法返回，如下所示:

[https://docs . Aws . Amazon . com/SDK-for-ruby/v3/API/Aws/S3/object . html # public _ URL-instance _ method](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/Object.html#public_url-instance_method)

## 调用命令并检索 URL

这就是我们的 email_sales_report 方法的样子:)

```
def email_sales_report(email)
  @report = Reports::Sales.new(report_params)
  @s3_public_url = Report::UploadToS3.new(csv: @report.csv).execute

  mail to: email, subject: "Email Sales Report #{Time.now}"
end 
```

@s3_public_url 可以在邮件模板中这样使用此方法:

```
<p>Your all attendee ticket list is attached. Please click on the link below:</p>
<a href="#{@s3_public_url}" download>Download Report</a>
Now you can send a file as you want over any email server. This has helped us immensely not only send out large reports to our customers, but it has also helped us to see which customers are downloading reports and how frequently they are downloading them. 
```

希望这对你有所帮助，并且很容易理解。如果你有任何问题或需要任何帮助来上传文件到 S3，并在你的 rails 应用中使用这些文件——给我发电子邮件到[luke.will.duncan@gmail.com](mailto:luke.will.duncan@gmail.com)