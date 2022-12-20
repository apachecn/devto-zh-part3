# 利用模型思想减少 jquery 应用程序中的意大利面条式代码

> 原文：<https://dev.to/luciunknown/reducing-spaghetti-code-on-jquery-application-using-model-idea--9ba>

使用 jquery 有时会很棘手，通常是在代码没有遵循任何关注点分离的时候。这些情况通常与对 javascript 承诺和回调理解不佳有关。经常会看到脚本文件中有大量的 ajax 调用传递相同的参数，只改变 url。

作为一个可能的解决方法，我建议使用 MVC 框架、js 模型中的 M 来创建保存 ajax 调用的独立类。

所以让我们从封装 ajax 调用开始。我最终得到了这个脚本，但是你可以改变它来为你工作。

```
class Api {
   static request(url, promise, formData, method) {
    if (promise)
      return $.ajax({
        url: url,
        dataType: 'json',
        method: method,
        data: formData
      })
    else
      $.ajax({
        url: url,
        dataType: 'json',
        method: 'POST',
        data: formData
      })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们做一些模板来渲染我们的项目。

```
var PostTemplates = {
    listItem:'<li><span>%(id)i</span><pre>%(title)s</pre></li>'
} 
```

Enter fullscreen mode Exit fullscreen mode

使用模型思想，让我们创建一个类，集中与该对象相关的 ajax 调用

```
class Post {
    constructor(postInfo) {
        this.id = postInfo.id;
        this.title = postInfo.title;
    }

    static getList(){
        const url = 'https://jsonplaceholder.typicode.com/posts';
        let request = Api.request(url, true, {}, 'GET');
        return $.when(request); 
    }

    insert(){
         //...
    }

    remove(){
       //...
    }

    render(item){
        return sprintf(PostTemplates.listItem, this)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们通过调用 getList 函数
来使用这个模型

```
Post.getList().then(function(posts){
    let rootElement = $("#banner-message");
    rootElement.empty();
    $.each(posts, function(index, postInfo){
         rootElement.append(new Post(postInfo).render());
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode