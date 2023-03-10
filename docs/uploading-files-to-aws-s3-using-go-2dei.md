# 使用 Go 将文件上传到 AWS S3

> 原文：<https://dev.to/nhsdeveloper/uploading-files-to-aws-s3-using-go-2dei>

# 使用 Go 将文件上传到 AWS S3

我目前的副业项目是 [Glacial](https://glacial.io) ，一个安全的基于云的文档存储&查看解决方案，用于医疗记录&文档的长期归档。临床文档在离开医院网络之前在本地加密，并使用亚马逊的 S3 服务器端 AES256 加密再次加密。

这个快速教程演示了如何使用 Go 编程语言将文件上传到 AWS S3 存储桶。

第一步是为 Go 安装 AWS 软件开发工具包(SDK)。这可以通过在终端或命令提示符下使用下面的`go get`命令来完成。

```
go get github.com/aws/aws-sdk-go/... 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了 AWS SDK，您就需要将相关部分导入到您的程序中，以便能够与 S3 进行交互。

```
package main

import (
    "bytes"
    "fmt"
    "log"
    "net/http"
    "os"

    "github.com/aws/aws-sdk-go/aws"
    "github.com/aws/aws-sdk-go/aws/credentials"
    "github.com/aws/aws-sdk-go/aws/session"
    "github.com/aws/aws-sdk-go/service/s3"
) 
```

Enter fullscreen mode Exit fullscreen mode

你需要指定几个变量。这些文件将包含 AWS 区域标识符和您希望上传文件的 S3 存储区名称。

```
const (
    S3_REGION = "eu-west-1"
    S3_BUCKET = "glacial-io"
) 
```

Enter fullscreen mode Exit fullscreen mode

在程序的主要部分，您需要使用`NewSession`函数创建一个 AWS 会话。在下面的例子中，新创建的会话被分配给`s`变量。会话对象是通过传入区域标识符和您的 AWS 凭证(id 和密钥)来创建的。您的 id 和密钥可以从您的 AWS 帐户获得。`NewSession`函数返回一个指向会话对象的指针，如果有错误，比如你指定了一个无效的区域，那么返回一个错误结构。

因此，您应该检查错误变量的状态，并根据您的用例进行适当的处理。在本例中，我们只是将错误作为致命消息记录到控制台。

```
func main() {

    // create an AWS session which can be
    // reused if we're uploading many files
    s, err := session.NewSession(&aws.Config{
        Region: aws.String(S3_REGION),
        Credentials: credentials.NewStaticCredentials(
            "XXX",
            "YYY",
            ""),
    })

    if err != nil {
        log.Fatal(err)
    }

    err = uploadFileToS3(s, "discharge-letter-787653.pdf")

    if err != nil {
        log.Fatal(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦获得了指向有效 AWS 会话的指针，就可以重用该会话来上传多个文件。在上面的例子中，我们只使用它一次，因为我们将它传递给 upload 函数来上传解雇信。

## 上传功能

upload 函数有两个参数，指向会话对象的指针和要上传的文件的名称。首先，我们尝试打开文件。如果因为文件名无效而失败，那么我们返回错误并退出函数。如果文件确实存在，那么我们继续计算文件的大小，并将文件的字节读入缓冲区。

我们使用 SDK 的`PutObject`函数来上传文件。在这里，我们可以指定各种 S3 对象参数。例如，我们指定了存储桶名称和访问控制列表(ACL ),在这种情况下，我们将文件标记为私有。我们还指定应使用哪种 S3 存储分类，在本例中，我们实施了服务器端加密。

```
func uploadFileToS3(s *session.Session, fileName string) error {

    // open the file for use
    file, err := os.Open(fileName)
    if err != nil {
        return err
    }
    defer file.Close()

    // get the file size and read
    // the file content into a buffer
    fileInfo, _ := file.Stat()
    var size = fileInfo.Size()
    buffer := make([]byte, size)
    file.Read(buffer)

    // config settings: this is where you choose the bucket,
    // filename, content-type and storage class of the file
    // you're uploading
    e, s3err := s3.New(s).PutObject(&s3.PutObjectInput{
        Bucket:               aws.String(S3_BUCKET),
        Key:                  aws.String(fileName),
        ACL:                  aws.String("private"),
        Body:                 bytes.NewReader(buffer),
        ContentLength:        aws.Int64(size),
        ContentType:          aws.String(http.DetectContentType(buffer)),
        ContentDisposition:   aws.String("attachment"),
        ServerSideEncryption: aws.String("AES256"),
        StorageClass:         aws.String("INTELLIGENT_TIERING"),
    })

    return s3err
} 
```

Enter fullscreen mode Exit fullscreen mode

## 存储分类

除了标准存储类别`STANDARD`和非频繁访问类别`STANDARD_IA`之外，现在还有一种新的(截至 2018 年 11 月)存储类别，称为智能分层。这对于文件访问模式未知并且不确定使用哪个类的用例来说非常理想。智能分层选项通过基于使用模式在标准访问和不频繁访问之间自动移动文件，尝试为您节省资金。

您现在还可以使用值`GLACIER`将文件直接上传到 Glacier 存储引擎。

如果成本是一个问题，并且您不需要其他存储类别提供的冗余级别，那么您可以使用`REDUCED_REDUNDANCY`来降低每月费用。

* * *

还贴了@[https://paulbradley.org/gos3/](https://paulbradley.org/gos3/)