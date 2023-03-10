# 转到文本模板处理

> 原文：<https://dev.to/kirklewis/go-text-template-processing-181d>

我最近参与了一个项目，在这个项目中，我使用 Go 的模板包来解析和内插模板。处理完模板后，我需要使用插值后的字符串结果来保存新的文件内容，并命名目录和文件。这篇文章是我写的帮助处理文本模板的函数的演练，然后演示每个函数。

## 进行项目设置

因为我是在我创建这些函数的项目之外演示这些函数，所以我为这篇文章创建了一个更简单的项目，如下:

```
go-template-processing/
├── main.go
├── src
│   └── demo
│       └── template
│           └── process.go
└── templates
    └── got.tmpl 
```

Enter fullscreen mode Exit fullscreen mode

### 创建模板文件

将以下文本添加到文件`templates/got.tmpl`。稍后将处理此文件模板。

```
{{.Name}} of house {{.House}}

{{if .Traits -}}
Traits:
    {{- range .Traits}}
    * {{. -}}
    {{end}}
{{end}} 
```

Enter fullscreen mode Exit fullscreen mode

### 解剖模板

在模板执行期间，像`{{.Name}}`和`{{.House}}`这样的注释将被替换为它们各自的值。其他注释如`{{if}}`和`{{range}}`被称为[动作](https://golang.org/pkg/text/template/#hdr-Actions)，也将被评估。位于`{{`和`}}`分隔符两侧的连字符`-`，分别用于修剪它们前后的空格。点`.`表示被迭代的特征范围中的当前元素。

### 设置 GOPATH

```
cd go-template-processing
export GOPATH=$PWD 
```

Enter fullscreen mode Exit fullscreen mode

## 插值解析后的模板

为了将首先解析然后插入模板文件或字符串的问题分开，我在文件`process.go`中创建了一个函数，它首先进行插入。

> Go 将这种插值称为“执行模板”，其中模板被应用于数据结构。

```
package template

import (
    "bytes"
    "text/template"
)

// process applies the data structure 'vars' onto an already
// parsed template 't', and returns the resulting string.
func process(t *template.Template, vars interface{}) string {
    var tmplBytes bytes.Buffer

    err := t.Execute(&tmplBytes, vars)
    if err != nil {
        panic(err)
    }
    return tmplBytes.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

鉴于其注释、注解和实现，`process`函数应该很容易推理。

参数`vars`被类型化为一个[空接口](https://tour.golang.org/methods/14)，因此可以使用任何类型，如`map`或`Struct`，其字段对应于模板的注释。任何没有相应字段的`{{.Annotation}}`都被替换为`<no value>`，或者在评估失败时出现混乱。

> 上面写的`process`函数是一个先发制人的[提取方法](https://refactoring.guru/extract-method)重构，这样做是为了避免[干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)发生在代码的后面。

## 处理一个模板字符串

添加到`process.go`文件中的下一个函数将通过创建一个新的[模板](https://golang.org/pkg/text/template/#Template.New)来处理一个模板字符串，然后解析给定的字符串`str`参数。分配给`tmpl`的新的`Template`然后使用之前创建的私有`process`函数进行插值。

```
func ProcessString(str string, vars interface{}) string {
    tmpl, err := template.New("tmpl").Parse(str)

    if err != nil {
        panic(err)
    }
    return process(tmpl, vars)
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数将在后面演示。

## 处理一个模板文件

这个函数与`ProcessString`非常相似，但是它处理由`fileName`参数指示的文件。

> [ParseFiles](https://golang.org/pkg/text/template/#Template.ParseFiles) 函数可以接受多个文件名参数。然而，我一次只需要解析一个文件，所以我使用了一个`fileName`参数。

```
func ProcessFile(fileName string, vars interface{}) string {
    tmpl, err := template.ParseFiles(fileName)

    if err != nil {
        panic(err)
    }
    return process(tmpl, vars)
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，这两个函数现在都可以被客户端代码使用了。

## 使用流程功能

下面的`./main.go`文件演示了如何使用这两个过程函数。

```
package main

import (
    "fmt"
    "demo/template"
)

func main() {
    // data structure the template will be applied to
    vars := make(map[string]interface{})
    vars["Name"] = "Brienne"
    vars["House"] = "Tarth"
    vars["Traits"] = []string{"Brave", "Loyal"}

    // process a template string
    resultA := template.ProcessString("{{.Name}} of house {{.House}}", vars)

    // process a template file
    resultB := template.ProcessFile("templates/got.tmpl", vars)

    fmt.Println(resultA, "\n")
    fmt.Println(resultB)
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`go run main.go`应输出以下内容:

```
Brienne of house Tarth

Brienne of house Tarth

Traits:
    * Brave
    * Loyal 
```

Enter fullscreen mode Exit fullscreen mode

模板字符串和文件都已成功处理。产生的字符串现在可以用于电子邮件正文、文件内容等。

### 通过结构提供变量

同样，因为`vars`被类型化为空接口，所以这里也可以使用 struct。

*我在`main.go`中的`fmt.Println(resultB)`语句下添加了以下代码片段。*

```
// using a Struct
type Westerosi struct {
    Name   string
    House  string
    Traits []string
}

jorah := Westerosi{"Ser Jorah", "Mormont", []string{"Brave", "Protective"}}
resultC := template.ProcessFile("templates/got.tmpl", jorah)

fmt.Println(resultC) 
```

Enter fullscreen mode Exit fullscreen mode

再次运行`go run main.go`现在应该会输出以下内容:

```
Brienne of house Tarth

Brienne of house Tarth

Traits:
    * Brave
    * Loyal

Ser Jorah of house Mormont

Traits:
    * Brave
    * Protective 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 结论

使用 Go 模板是非常直接的，可以很容易地构建自己的项目特定函数。虽然我在这篇文章中只讨论了一个特定的任务，但是你可以在[https://golang.org/pkg/text/template](https://golang.org/pkg/text/template/)阅读更多关于 Go 模板包的内容。

文章中使用的代码可以在 [Github](https://github.com/kirklewis/go-template-processing) 上获得。
*撰写时使用的围棋版本`go version go1.12.1`*

感谢您的阅读。