# 脚本标签

> 原文：<https://dev.to/5anthosh/script-tag--1h4f>

这篇文章最初发表在我的博客上，在这里找到原文

核心 JavaScript 是独立于平台的，但是 JavaScript 的能力依赖于它的环境。

对于服务器端环境，您可以使用类似于
的命令来执行脚本

```
santhosh@ubuntu-18-04:~$  node script.js 
```

> 您需要首先安装 nodejs 来运行服务器端 JavaScript

在浏览器中，我们必须给网页附加一个脚本，JavaScript 程序可以借助`script`标签插入到 HTML 文档的任何部分。

```
<!DOCTYPE html>
<html lang="en">
 <head>
 </head>
<body>
    <script>
        alert("Hello World");
    </script>
</body>
</html> 
```

`alert`是浏览器特有的功能。标签包含当浏览器加载此网页时将自动执行的脚本。

`script`标签包含很少的属性。

## **式**

在 **HTML5** 中不需要指定类型，通常是`type="text/javascript"`。

> 在旧的标准 HTML4 中，需要放置脚本的类型

## 外部脚本

只有最简单的脚本被放入 HTML。更复杂的驻留在单独的文件中。
脚本文件附加到带有`src`属性的 HTML。

```
<script src="/path/to/script.js"></script> 
```

我们也可以附上第三方脚本。

```
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script> 
```

我们可以根据需要附加许多脚本。

```
<script src="/path/to/script1.js"></script>
<script src="/path/to/script2.js"></script>
<script src="/path/to/script3.js"></script>
<script src="/path/to/script4.js"></script> 
```

> 如果 script 元素指定了 src 属性，则它不应该在其标记中嵌入脚本。

警报语句将不会运行。

```
<script src="/path/to/script4.js">
    alert("this will not run");
</script> 
```