# 在一个下午写一个静态站点生成器

> 原文：<https://dev.to/xoryouyou/writing-a-static-site-generator-in-an-afternoon-5dgf>

### [T1】简介](#intro)

我一直对成熟的博客系统有疑问，不管它们需要很多资源还是需要很长时间来实现简单的功能。所以我决定在一天之内创建一个简单的静态页面生成器，我有几个目标:

*   使用`pug.js`进行模板化
*   能够添加元数据
*   使用`browser-sync`
*   可选语法突出显示

### 用`pug.js`编译 HTML

首先，让我们创建一个名为`posts/`的文件夹，并在其中创建一个名为`first-post/`的新文件夹

在这个文件夹中放一个简单的`post.pug`文件，包含以下几行:

```
// - post.pug
html
  body
    h1 Hello World 
```

现在我们可以编写一个名为`build.js`的小脚本，它循环遍历`posts/`中的所有文件夹
，并编译其中的`post.pug`。

```
 const fs = require("fs");
const path = require("path");
const pug = require("pug");

let postDir = "./posts"
let distDir = "./dist"
let imgDir = "./img";
let assetDir = "./assets";

// get all posts
let posts = fs.readdirSync(postDir);
// for each post
for(let p of posts)
{
// compile the pug file
    let c = pug.compileFile(path.join(postDir,p,"/post.pug"),{pretty:true});
    let html = c();
    fs.writeFileSync(path.join(distDir,p+".html"),html);
}

// copy stuff over
fsExtra.copySync(imgDir, path.join(distDir,"img/"));
fsExtra.copySync(assetDir, path.join(distDir,"assets/")); 
```

这创建了`./dist/first-post.html`，它应该只在你的浏览器中显示一个大的“Hello World”。

### 附加数据

要添加一些额外的数据，如标题等，让我们在`post.pug`文件旁边放一个`data.json`，并在其中放一些内容。

```
{  "title":"Getting started",  "description":  "Some Description",  "keywords":"just, a, few, keywords"  } 
```

多亏了 pug，我们可以简单地将一些元数据传递给渲染函数，并将其作为模板中的变量使用。

```
...
let {title, description, keywords} = require("../site/posts/"+p+"/data.json");
let html = c({title, description, keywords});
... 
```

现在我们可以在 pug 文件中使用`h1 #{title}`,并在这里显示我们的 json 数据。

### 使用`browser-sync`

因为我并不总是想要手动运行构建并在浏览器中刷新页面，所以我想利用`browser-sync`。

为此，我们首先需要将构建脚本封装到一个模块中，只需将其导出为如下函数:

```
 module.exports = function()
{
  // build.js code here
} 
```

现在，我们可以创建一个`watch.js`文件，它可以监视所有的 pug 和 json 文件，当某些东西发生变化时，它会调用构建脚本并刷新浏览器。

```
const bs = require("browser-sync").create();
const build = require("./build");

function fn(event, file) 
{

    build();
    bs.reload();
}

bs.init({
    https: true,
    server: "./dist",
    files: [
        {
            match:"./site/**/*.pug",
            fn: fn
        },
        {
            match:"./site/**/*.json",
            fn: fn
        }
    ]
}); 
```

现在，我们只需在这些文件上点击 CTRL-S，整个编译/刷新过程就会自动运行。

### 附加:`prism.js`的自定义过滤器

因为我希望页面是一个完整的静态 html 文件包，前端没有 javascript。

那么我们如何在你询问的页面上从`prism.js`获得语法高亮呢？

通过简单地在`pug.js`中编写一个自定义过滤器，它使用`prism.js`来呈现 html，然后在前端我们只需要包含一些 css 来样式化它。

所以我们创建了一个名为`highlight.js`的新文件，其中包含以下代码:

```
var Prism = require("prismjs");
var loadLanguages = require("prismjs/components/");
loadLanguages(["javascript"]);

function highlight(text, options)
{
    let html = Prism.highlight(text, Prism.languages.javascript, "javascript");
    return html;
}

module.exports = highlight; 
```

现在我们需要告诉 pug 使用我们的自定义过滤器，将它添加到选项 json 中。

```
 const highlight = require("./highlight");

// in our for loop add the filter to the compile step
let c = pug.compileFile(path.join(postDir,f,"/post.pug"),
{
  pretty:true,
  filters:
  {
    highlight: highlight
  }
}); 
```

现在，对于 pug 中的最后一步，我们可以简单地通过一个过滤器来包含文件:

```
html
  head
    link(rel="stylesheet", href="assets/prism.css")
  body
    h1 Hello syntax highlighting
    include:highlight some-file.js 
```

### 结论

所以现在我们有了一个基本的设置，但总的来说，这是一个下午项目的良好开端。

一些人认为接下来的日子会是:

*   创建索引页
*   使用一些 pug 模板来创建来自`data.json`的字段
*   s3 或 github 的设置部署-页面