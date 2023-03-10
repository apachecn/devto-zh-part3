# 用 Python、MkDocs 和 S3 创建一个静态站点

> 原文：<https://dev.to/yloganathan/how-to-create-a-static-site-with-mk-docs-and-s3-38m8>

我们需要一个轻量级的解决方案来为我们的测试版客户提供文档、即将推出的特性和常见问题。我们想用 markdown 来让开发者开心，并且想要简单易用的东西。我们决定使用 Python MkDocs 来生成一个静态站点，该站点将在 AWS S3 托管。

我们决定将客户文档作为代码添加到同一个 git repo 中，并开始寻找从 markdown 创建静态站点的解决方案。我选择了 MkDocs，因为它非常轻便并且易于上手。

## 从降价中建立一个静态网站

1.  安装 MkDocs。`pip install mkdocs`
2.  创建 MkDocs 需要的文件夹结构。下面是我们的示例站点:

    ```
     documents
         -- mkdocs.yml
         -- docs
           -- index.md
           -- faq.md
           -- images/
           -- stylesheets/ 
    ```

3.  为 mkdocs 安装材料主题`pip install mkdocs-material`

4.  通过更新 mkdocs.yml
    设置 mkdocs

    ```
    site_name: TrainingPeaks Beta
    site_description: How to documents for Beta users.
    theme:
        name: 'material'
        favicon: 'images/favicon.ico'
        extra_css:
           - 'stylesheets/extra.css' 
    ```

    我们对材料主题做了一些小的修改:

    *   设置收藏夹图标。
    *   提供一个额外的样式表，它有一些小的覆盖。
5.  本地服务站点`mkdocs serve`

## 将 MkDocs 站点部署到 S3

### 设置 S3

我建议使用 cloudformation 模板创建一个公共存储桶来存放文档。S3 站点模板上的 [AWS 文档](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/quickref-s3.html)。

1.  创建一个 yaml 文件

    ```
    AWSTemplateFormatVersion: 2010-09-09
    Description: Resources to host documentation.

    Parameters:
    SiteName:
        Description: Site name
        Type: String
        Default: tp-beta-learning

    Resources:
    S3Bucket:
        Type: AWS::S3::Bucket
        Properties:
            BucketName: !Ref SiteName
            AccessControl: PublicRead
            WebsiteConfiguration:
                IndexDocument: index.html
                ErrorDocument: error.html

    BucketPolicy:
        Type: AWS::S3::BucketPolicy
        Properties:
        PolicyDocument:
            Id: MyPolicy
            Version: 2012-10-17
            Statement:
              - Sid: PublicReadForGetBucketObjects
                Effect: Allow
                Principal: '*'
                Action: 's3:GetObject'
                Resource: !Join
                - ''
                - - 'arn:aws:s3:::'
                    - !Ref S3Bucket
                    - /*
        Bucket: !Ref S3Bucket

    Outputs:
    WebsiteURL:
        Value: !GetAtt
            - S3Bucket
            - WebsiteURL
        Description: URL for website hosted on S3 
    ```

2.  使用 aws cli 部署云结构。

    `aws cloudformation create-stack --template-body file://help-site.yaml --stack-name Help-Site`

3.  验证堆栈创建是否成功。我们只需要做一次这一步。

### 将站点部署到 S3

1.  在本地目录中，运行`mkdocs build`。该命令将创建一个包含 html 文件的`site`文件夹。
2.  将站点部署到 s3 `aws s3 sync ./site s3://tp-beta-learning --recursive`

该网站是建立和运行在网址`http://<bucket-name>.s3-website-<region>.amazonaws.com.`

下一步是为网站使用自定义域。