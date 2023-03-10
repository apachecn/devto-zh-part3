# Go，谷歌功能和 Gitlab-ci 完美结合

> 原文：<https://dev.to/renatosuero/go-google-functions-and-gitlab-ci-a-perfect-combination-4lao>

上周，我在 GCP T1 用 Go 测试了谷歌的功能，之后，我决定添加另一个我喜欢的服务 T2 git lab T3。我非常喜欢它，所以我决定写这篇文章。

如果您知道如何添加帐户以在 GCP 进行部署，或者您有一个帐户，则可以忽略此部分。

## 创建在 Gitlab 使用的账户。

我会加很多截图说清楚。
您需要访问 IAM &管理和服务账户。
[![Service Account](img/1866f6e179e19607f36d74253f0309bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yq1RnYxN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/li425pbjjwp44ifb9cbi.png)

点击
[![Service Account](img/3593a5e90b4d3a93add50cf05b8db6fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8xvnTX27--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e3avlb1trnb5c5oph89n.png) 按钮“+创建服务账户”

键入一个名称来标识账户
[![Service Account](img/f8b27452ee8a191548093877600690f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LM6b6xEO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jesqynonz1celsilrzmx.png)

您需要添加 3 个角色
[![Service Account](img/1dae643f996a9486b072fa44b0a5452f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--munq8Tw4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zhd3b5sfs5zdr5g4nfib.png)

创建一个键
[![Service Account](img/db80d300bdfc1939b96260357af6da9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---m8QQPXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0r5d9rzau63iqpq1q1pp.png)

检查 JSON 是否被选中，并点击创建按钮
[![Service Account](img/65334f504494e89a3472a21593a163c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qbmCLMLp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/PIC%206)

* * *

我们在 GCP 完成了配置，现在我们将在存储库中添加环境变量。PROJECT_ID 是项目的 ID，SERVICE_ACCOUNT 是创建密钥
[![Service Account](img/04b28dd55a805c908466162839f92e83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Zapsl6G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gds21jd33zpsesieyqpb.png) 时下载的内容

好了，现在我们将创建代码来测试我们的部署。我正在使用来自谷歌
的[示例，我们将创建 3 个文件，hello_world.go，这是函数](https://github.com/GoogleCloudPlatform/golang-samples/tree/master/functions/helloworld) 

```
// Package helloworld provides a set of Cloud Function samples.
package helloworld

import (
    "fmt"
    "net/http"
)

// HelloGet is an HTTP Cloud Function.
func HelloGet(w http.ResponseWriter, r *http.Request) {
    fmt.Fprint(w, "Hello, World!")
} 
```

Enter fullscreen mode Exit fullscreen mode

hello_world_test.go 这是函数
的测试

```
 package helloworld

import (
    "io/ioutil"
    "net/http/httptest"
    "strings"
    "testing"
)

func TestHelloGet(t *testing.T) {
    payload := strings.NewReader("")
    req := httptest.NewRequest("GET", "/", payload)

    rr := httptest.NewRecorder()
    HelloGet(rr, req)

    out, err := ioutil.ReadAll(rr.Result().Body)
    if err != nil {
        t.Fatalf("ReadAll: %v", err)
    }
    want := "Hello, World!"
    if got := string(out); got != want {
        t.Errorf("HelloWorld = %q, want %q", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

还有。gitlab-ci.yml 是运行持续集成的配置

```
image: google/cloud-sdk:alpine

test_production:
  image: golang:alpine
  stage: test only:
  - production
  script:
  - CGO_ENABLED=0 go test ./...

deploy_production:
  stage: deploy
  environment: Production
  only:
  - production
  script:
  - echo $SERVICE_ACCOUNT > /tmp/$CI_PIPELINE_ID.json
  - gcloud auth activate-service-account --key-file /tmp/$CI_PIPELINE_ID.json
  - gcloud --quiet --project $PROJECT_ID functions deploy HelloGet --runtime go111 --trigger-http
  after_script:
  - rm /tmp/$CI_PIPELINE_ID.json 
```

Enter fullscreen mode Exit fullscreen mode

工作流程是，当我们合并生产分支中的变更时，操作将运行。

就这样，正如您在下一个截图中看到的，部署已经完成，从 Google 返回的结果显示 URL 已创建

[![Service Account](img/8bf1584f5986d154ccc3eb68a4ac7dab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cNKXmUCU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ptb9e4zladmqjhfz7397.png)

乡亲们就这些，希望能对你们有用=)。您可以在[库](https://gitlab.com/renatosuero/gcp-functions-example)中看到代码