# 建立一个数字助手来帮助繁忙的临床工作人员

> 原文：<https://dev.to/nhsdeveloper/building-a-digital-wingman-to-assist-busy-clinical-staff-4mj7>

# 打造数字化僚机，协助忙碌的临床工作人员

我一直致力于创造一个“数字助手”来帮助繁忙的临床工作人员，通过检测特定药物是否在出院通知中遗漏了。

[https://www.youtube.com/embed/9KJzKkSG5jo](https://www.youtube.com/embed/9KJzKkSG5jo)

抱歉，该视频没有任何画外音，所以这是您看到的内容:

*   一封非结构化的出院信被发送到 AWS 的医学理解人工智能
*   在文本中检测到药物、剂量和路线信息的列表，以及四十个感兴趣的项目。这些包括医疗程序和诊断。
*   然后产生并显示医学术语及其相互关系的视觉模型。
*   然后我们去掉治疗高血压的药物，重新开始这个项目。当在临床叙述中提到高血压时，系统检查所有可能被开出用于治疗高血压的药物。
*   如果找不到，它就会向护理人员发出警报进行调查。

## AWS 医学领悟

medical intensive API 是 AWS 最近推出的产品，它接受非结构化的医学文本，并返回一个 JSON 对象，概述文本中的所有医学、程序和诊断术语。对于药物，如果在文本中找到，它还会返回剂量、途径和频率。对于找到的每个医学术语，将返回一个置信度得分，以帮助您决定是否应该信任该值。还返回一个偏移位置，指示找到的医学术语的起点。

## 使用 Go 的例子

第一步是为 Go 安装 AWS 软件开发工具包(SDK)。这可以通过在终端或命令提示符下使用下面的 Go get 命令来完成。

```
go get github.com/aws/aws-sdk-go/... 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了 AWS SDK，您就需要将相关部分导入到您的程序中，以便能够与 medical intensive API 进行交互。

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
    "github.com/aws/aws-sdk-go/service/comprehendmedical"
) 
```

Enter fullscreen mode Exit fullscreen mode

您将需要创建一个 AWS 会话，选择一个支持 medical intensive API 的地区(并非所有地区都支持)。在下面的例子中，新创建的会话被分配给`s`变量。会话对象是通过传入区域标识符和您的 AWS 凭证(id 和密钥)来创建的。您的 id 和密钥可以从您的 AWS 帐户获得。

```
func main() {

    // create an AWS session which can be
    // reused if we're uploading many files
    s, err := session.NewSession(&aws.Config{
        Region: aws.String("us-east-1"),
        Credentials: credentials.NewStaticCredentials(
            "XXX",
            "YYY",
            ""),
    })

    if err != nil {
        log.Fatal(err)
    }

    client := comprehendmedical.New(s)
    input  := comprehendmedical.DetectEntitiesInput{}

    input.SetText(`
        Pt is 40yo mother, highschool teacher
        HPI   : Sleeping trouble on present dosage of Clonidine.
                Severe Rash  on face and leg, slightly itchy
        Meds  : Vyvanse 50 mgs po at breakfast daily,
                Clonidine 0.2 mgs -- 1 and 1 / 2 tabs po qhs
        HEENT : Boggy inferior turbinates, No oropharyngeal lesion
        Lungs : clear
        Heart : Regular rhythm
        Skin  : Mild erythematous eruption to hairline
    `)

    result, err := client.DetectEntities(&input)

    if err != nil {
        log.Fatal(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，使用传入 AWS 会话变量`s`的新函数定义一个 medical intensive 客户端。然后定义一个输入对象，并使用`SetText`函数来定义您想要处理的医学文本。当调用`DetectEntities`函数并将结果存储在结果变量中时，医学文本的处理就完成了。

要获得返回的 JSON 字符串，可以使用如下所示的`GoString`函数。如果您希望将 JSON 存储在 Postgres JSONB 数据库字段中，以便在许多患者中执行搜索查询，这将非常有用。

```
fmt.Println(result.GoString()) 
```

Enter fullscreen mode Exit fullscreen mode

您可以循环检查检测到的术语，并使用以下模式仅打印出找到的药物:

```
for _, entity := range result.Entities {
    if *entity.Category == "MEDICATION" {
        fmt.Println(*entity.Text)
        fmt.Println(*entity.Type)
        fmt.Println(*entity.Score)
        fmt.Println("-----------")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将输出以下结果:

```
Clonidine
GENERIC_NAME
0.9948381781578064
-----------
Vyvanse
BRAND_NAME
0.9995348453521729
-----------
Clonidine
GENERIC_NAME
0.997945249080658
----------- 
```

Enter fullscreen mode Exit fullscreen mode

## 类别

其他有用的类别名称包括:-

*   DX 名称
*   诊断
*   症状
*   系统 _ 器官 _ 部位
*   剂量和方向。