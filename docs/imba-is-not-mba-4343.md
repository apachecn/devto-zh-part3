# IMBA 不是 MBA

> 原文：<https://dev.to/chenge/imba-is-not-mba-4343>

IMBA 是一种新的网络语言。正如网站上的介绍，Scrimba 是与 imba 开发的。

下面是 Todo 示例的代码，很容易理解。

```
tag Todo < li
    def ontap
        data:completed = !data:completed

    def render
        <self .done=data:completed> data:title

tag App
    def addItem
        if @input.value
            data.push(title: @input.value)
            @input.value = ""

    def render
        <self.vbox>
            <form.header :submit.prevent.addItem>
                <input@input type='text'>
                <button> 'add'
            <ul> for item in data
                <Todo[item]>

var items = []
Imba.mount <App[items]> 
```

其站点示例代码:

```
Imba.mount <nav#hero ->
    <a href='/guides'> "Get started"
    <a.github href=linkToGitHub> "GitHub" 
```

我不知道这里的 MBA 是什么。去看看 IMBA。