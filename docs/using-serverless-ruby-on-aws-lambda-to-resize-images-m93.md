# 在 AWS Lambda 上使用无服务器 Ruby 调整图像大小

> 原文：<https://dev.to/maartenvanvliet/using-serverless-ruby-on-aws-lambda-to-resize-images-m93>

转载自([https://maartenvanvliet.nl/2019/01/04/ruby_aws_lambda/](https://maartenvanvliet.nl/2019/01/04/ruby_aws_lambda/))

在上一次 AWS reinvention 上，宣布 AWS Lambda 将支持 Ruby 作为运行时语言。我渴望尝试一下，Ruby 强大的语法和特性是一种乐趣，将它与 AWS Lambda 结合起来，我想它可以被用来轻松调整 Lambda 的图像大小。

我从[无服务器框架](https://serverless.com/blog/api-ruby-serverless-framework/)开始，因为这是提供 Lambda 功能的简单方法。目标是当一个图像被上传到一个 S3 桶时，一个 Lambda 被启动，它调整图像的大小，然后将它上传到另一个桶。

第一步是安装`serverless`

```
npm install -g serverless
serverless create -t aws-ruby -p image-resizer 
```

这创建了一个无服务器框架 ruby Lambda 函数的基本样板。它将创建两个文件，一个是`handler.rb`，这个文件包含 lambda 将调用的实际函数，另一个是`serverless.yml`，这个文件是无服务器框架用来配置和提供 Lambda 及其附属服务(如 AWS S3 和 AWS API 网关)的文件。

我们将首先更改`serverless.yml`配置。我们添加了一个函数`handle_resize`，每当在`your-images` S3 桶中创建一个对象时，它就调用`handler.rb`中的`ImageHandler.process`函数。此外，在 provider 键下，我们授予函数对 S3 的访问权，并使用`ruby2.5`运行时。

我们还添加了一个 S3 资源`resized-your-images`，这将是我们移动调整大小的图像的桶。

```
service: image-resizer 

provider:
  name: aws
  runtime: ruby2.5
  iamRoleStatements:
    - Effect: Allow
      Action:
        - s3:*
      Resource: "*"

functions:
   handle_resize:
     handler: handler.ImageHandler.process
     events:
       - s3:
           bucket: your-images
           event: s3:ObjectCreated:*

resources:
  Resources:
    ResizedImages:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: resized-your-images 
```

现在我们可以实现`ImageHandler.process`函数了。

## 书写红宝石λ

首先，我们将添加一个 Gemfile，因为我们需要使用两个依赖项。AWS S3 SDK 用于检索文件并上传到 S3，以及`mini_magick` gem，imagemagick 的包装器。

```
#Gemfile
source 'https://rubygems.org'

gem 'aws-sdk-s3', '~> 1.30.0'
gem "mini_magick", '~> 4.9.0' 
```

接下来，我们更新`handler.rb`文件。

```
#handler.rb
require 'uploaded_file'

class ImageHandler

  def self.process(event:, context:)
    event = event["Records"].first
    bucket_name = event["s3"]["bucket"]["name"]
    object_name = event["s3"]["object"]["key"]

    file = UploadedFile.from_s3(bucket_name, object_name)
    file.resize "100x100"
    file.upload_file("resized-your-images", "resized_" + event["s3"]["object"]["key"] )
  end
end 
```

`ImageHandler.process`是 lambda 调用的函数，有两个参数,`event`,在我们的例子中是 S3 发送的关于新创建的文件的事件，以及`context`,基本上是关于函数及其环境的元数据。

`process`函数从事件对象中检索新创建的对象的桶和键。然后它调用尚未实现的`UploadedFile`类。该类检索 s3 对象，调整其大小，然后将其上传到另一个 bucket。这个类独立于 lambda，一般来说，所有的业务逻辑都应该是独立的。这使得在其他环境中使用代码更加容易，也使得测试更加容易。例如，我可以测试`UploadedFile.from_s3(bucket_name, object_name)`，而不必模拟整个 S3λ事件。

现在我们将实现 UploadedFile 类。

```
# uploaded_file.rb
require "aws-sdk-s3"
require "mini_magick"

class UploadedFile
    def self.from_s3(bucket_name, object_name)
        s3 = Aws::S3::Resource.new()
        object = s3.bucket(bucket_name).object(object_name)

        tmp_file_name = "/tmp/#{object_name}"
        object.get(response_target: tmp_file_name)

        UploadedFile.new(tmp_file_name)
    end

    def initialize(tmp_file)
        @tmp_file = tmp_file
    end

    def resize(params)
        image = MiniMagick::Image.open(@tmp_file)
        image.resize params
        @resized_tmp_file = "/tmp/resized.jpg"
        image.write @resized_tmp_file
    end

    def upload_file(target_bucket, target_object)
        s3 = Aws::S3::Resource.new()
        object = s3.bucket(target_bucket).object(target_object).upload_file(@resized_tmp_file)
    end
end 
```

`uploaded_file.rb`是典型的红宝石。它检索 S3 文件，将其存储在一个 tmp 文件中。我们调用`mini_magick`来调整图像的大小，然后再次上传文件。

## 部署

部署时，我们需要确保 Lambda 函数可以访问依赖项。无服务器框架将在本地构建一个包含代码和所有依赖项的 zip 文件，然后将其部署到 AWS。因此，我们在本地运行与 AWS 上相同版本的 ruby 是很重要的，那就是 ruby 2.5。如果你使用`rbenv`，你可以使用`rbenv install 2.5.0`和`rbenv local 2.5.0`为这个项目设置 ruby 版本。

接下来，我们需要出售依赖项，以便将它们包含在包中。运行`bundle install --path vendor/bundle`，现在依赖项在`vendor`目录中。

在部署之前，我们仍然需要为 AWS 设置凭证。更多信息参见[无服务器框架文档](https://serverless.com/framework/docs/providers/aws/guide/credentials/)。

现在已经完成了，您可以调用`sls deploy`，lambda 函数将被部署，S3 桶将被创建。上传一张图片到`your-images`桶，片刻之后一张调整过大小的图片会出现在`resized-your-images`桶中。如果出现任何问题，您可以在 AWS Cloudwatch 的日志文件中进行跟踪。lambda 函数将有一个日志流。也可以调用`sls logs -f handle_resize`来跟踪日志。

在 [github](https://github.com/maartenvanvliet/serverless_ruby_image_resizer) 上找到代码

Paulius Dragunas 在 Unsplash 上拍摄的照片