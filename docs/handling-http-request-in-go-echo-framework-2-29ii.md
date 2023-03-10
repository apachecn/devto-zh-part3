# 在 Go Echo 框架中处理 HTTP 请求(2)

> 原文：<https://dev.to/ykyuen/handling-http-request-in-go-echo-framework-2-29ii>

*最初发布在[水手长博客](https://blog.boatswain.io/post/handling-http-request-in-go-echo-framework-2/)上。*

* * *

这是关于在 [Echo](https://echo.labstack.com/) 框架中处理 HTTP 请求的第二篇文章，我们将继续研究之前的例子。

*   [在 Go Echo 框架中处理 HTTP 请求(1)](https://blog.boatswain.io/post/handling-http-request-in-go-echo-framework-1/)

这次我们将创建一个简单的 HTML 表单，并将其作为 POST 请求提交。

## 创建一个 POST 请求端点

签出这个 [repo](https://gitlab.com/ykyuen/handling-http-request-in-go-echo-example-1) 并创建 *api/post_full_name.go* 来处理 post 请求。这个新文件几乎和 *api/get_full_name.go* 一样，除了 JSON 在返回客户端之前被修饰了一下。

`api/post_full_name.go`

```
package api

import (
  "bytes"
  "encoding/json"
  "fmt"
  "net/http"

  "github.com/labstack/echo"

  "gitlab.com/ykyuen/handling-http-request-in-go-echo-example-1/model"
)

func PostFullName(c echo.Context) error {
  // Bind the input data to ExampleRequest
  exampleRequest := new(model.ExampleRequest)
  if err := c.Bind(exampleRequest); err != nil {
    return err
  }

  // Manipulate the input data
  greeting := exampleRequest.FirstName + " " + exampleRequest.LastName

  // Pretty print the json []byte
  var resp bytes.Buffer
  var b = []byte(
    fmt.Sprintf(`{
      "first_name": %q,
      "last_name": %q,
      "msg": "Hello %s"
    }`, exampleRequest.FirstName, exampleRequest.LastName, greeting),
  )
  err := json.Indent(&resp, b, "", "  ")
  if err != nil {
    return err
  }

  // Return the json to the client
  return c.JSONBlob(
    http.StatusOK,
    []byte(
      fmt.Sprintf("%s", resp.Bytes()),
    ),
  )
} 
```

在 *main.go* 中添加新路线。

```
...
func main() {
  // Echo instance
  e := echo.New()

  // Instantiate a template registry with an array of template set
  // Ref: https://gist.github.com/rand99/808e6e9702c00ce64803d94abff65678
  templates := make(map[string]*template.Template)
  templates["home.html"] = template.Must(template.ParseFiles("view/home.html", "view/base.html"))
  templates["about.html"] = template.Must(template.ParseFiles("view/about.html", "view/base.html"))
  e.Renderer = &TemplateRegistry{
    templates: templates,
  }

  // Route => handler
  e.GET("/", handler.HomeHandler)
  e.GET("/about", handler.AboutHandler)

  // Route => api
  e.GET("/api/get-full-name", api.GetFullName)
  e.POST("/api/post-full-name", api.PostFullName)

  // Start the Echo server
  e.Logger.Fatal(e.Start(":1323"))
} 
```

### 使用 curl 测试新的端点

运行 [Echo](https://echo.labstack.com/) 服务器，使用 **curl** 发送 POST 请求。

```
curl http://localhost:1323/api/post-full-name -H "Content-Type: application/json" -d '{ "first_name": "Kit", "last_name": "Yuen"  }' 
```

以下是示例结果。

[![A JSON is return based on our POST request payload.](img/d43fec0121c7876c9950c54a8caf9f1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k99P92hb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j1xx9is5w6hmlswwxo66.jpg)

## 添加 HTML 表单

POST 请求端点已经准备好了，现在我们可以添加一个 HTML 表单并完成整个设置。让我们将表单添加到“关于”页面中。

`view/about.html`

```
{{define "title"}}
  Boatswain Blog | {{index . "name"}}
{{end}}

{{define "body"}}
  <h1>{{index . "msg"}}</h1>
  <h2>This is the about page.</h2>

  <!-- HTML form -->
  <form id="post-full-name-form">
    <input type="text" id="first-name" placeholder="First Name">
    <input type="text" id="last-name" placeholder="Last Name">
    <button type="button" id="submit-btn">Submit</button>
  </form>

  <!-- Print the response after the jQuery ajax request -->
  <pre id="form-result"></pre>

  <script type="text/javascript">
    // Send a ajax request when the submit button is clicked
    $("#submit-btn").on("click", function(){
      var firstName = $("#first-name").val();
      var lastName = $("#last-name").val();

      $.ajax({
        type: "POST",
        url: "/api/post-full-name",
        data: JSON.stringify({
          first_name: firstName,
          last_name: lastName
        }),
        processData: false,
        contentType: "application/json",
        dataType: "json"
      }).done(
        function(data){
          $("#form-result").text(JSON.stringify(data, null, 2));
        }
      ).fail(
        function(data){
          $("#form-result").text("POST request failed!");
        }
      )
    });
  </script>
{{end}} 
```

由于表单提交需要 **jQuery** ，我们将其包含在*视图/base.html* 中。

`view/base.html`

```
{{define "base.html"}}
  <!DOCTYPE html>
  <html>
    <head>
      {{template "title" .}}
      <!-- jQuery -->
      <script src="https://code.jquery.com/jquery-3.3.1.min.js"
        integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8="
        crossorigin="anonymous"></script>
    </head>
    <body>
      {{template "body" .}}
    </body>
  </html>
{{end}} 
```

### 测试关于页面中的 HTML 表单

启动 [Echo](https://echo.labstack.com/) 服务器，浏览 about 页面。填写名字和姓氏，然后单击提交按钮。它应该显示如下响应 JSON。

[![Click the submit button and get the response.](img/e5cfa02b72d84556f510a78bdf8ac0b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_weRmS1T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8xxkjzs37c6t6hak7ijp.jpg)

### 项目结构

这是我们新的项目结构。*(项目被重命名为**handling-http-request-in-go-echo-example-2**，如果要更改项目名称，请确保在 [Golang](https://golang.org/) 源文件中正确设置了 **import** 语句。)*

```
handling-http-request-in-go-echo-example-2/
  ├── api/                     # folder of api endpoints
  │   ├── get_full_name.go     # api for get full name
  │   ├── post_full_name.go    # api for post full name
  ├── handler/                 # folder of request handlers
  │   ├── home_handler.go      # handler for home page
  │   └── about_handler.go     # handler for about page
  ├── model/                   # folder of custom struct types
  │   ├── example_request.go   # struct type of get_full_name request
  │   └── example_response.go  # hstruct type of get_full_name response
  ├── vendor/                  # dependencies managed by dep
  │   ├── github.com/*
  │   └── golang.org/*
  ├── view/                    # folder of html templates
  │   ├── base.html            # base layout template
  │   ├── home.html            # home page template
  │   └── about.html           # about page template
  ├── Gopkg.lock               # dep config file
  ├── Gopkg.toml               # dep config file
  └── main.go                  # programme entrypoint 
```

## 总结

这是第二集[在 Go Echo 框架(1)](https://blog.boatswain.io/post/handling-http-request-in-go-echo-framework-1/) 中处理 HTTP 请求，我们添加了以下内容:

*   在 [Echo](https://echo.labstack.com/) 框架中设置一个 POST 请求端点。
*   美化 JSON 字节数组
*   添加一个 HTML 表单，并使用 jQuery/javascript 中的 AJAX 将其绑定到新的 POST 请求。

完整的例子可以在[gitlab.com](https://gitlab.com/ykyuen/handling-http-request-in-go-echo-example-2)找到。