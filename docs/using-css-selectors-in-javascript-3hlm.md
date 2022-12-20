# 在 Javascript 中使用 CSS 选择器

> 原文：<https://dev.to/neutrino2211/using-css-selectors-in-javascript-3hlm>

在 CSS 中，选择器是用来选择你想要样式化的元素的模式，但是从上面的标题中你可以看出，选择器在 javascript 中也是有用的，下面是一些如何使用它们的例子。

### 基础知识

#### 在 javascript 中使用选择器

*   使用`.querySelector`方法

```
const div = document.querySelector("div") // => First occurence of a div element in the document

const p = div.querySelector("p") // => First occurence of a p element in the div 
```

Enter fullscreen mode Exit fullscreen mode

*   要获得所有匹配的元素，使用`document.querySelectorAll`方法

```
document.querySelectorAll("div") // NodeList of all div elements 
```

Enter fullscreen mode Exit fullscreen mode

#### 通过 id

要通过 ID 获取元素，在元素 ID
前使用一个`#`

```
document.querySelector("#id") // => document.getElementById('id') 
```

Enter fullscreen mode Exit fullscreen mode

#### 按类划分

要按类获取元素，在类名
前使用一个`.`

```
document.querySelectorAll(".myElementClass") 
```

Enter fullscreen mode Exit fullscreen mode

#### 按标签名

要通过标签名获取元素，只需输入标签名

```
document.querySelectorAll("body") // => document.getElementsByTagName('body') 
```

Enter fullscreen mode Exit fullscreen mode

#### 复制`.getElementById`和`getElementsByTagName`

*   复制`.getElementById`

```
document.querySelector("#myDivId") // => document.getElementById('myDivId') 
```

Enter fullscreen mode Exit fullscreen mode

*   复制`.getElementsByTagName`

```
document.querySelectorAll("a") // => document.getElementsByTagName('a') 
```

Enter fullscreen mode Exit fullscreen mode

#### 所有元素

要获取所有元素，请使用`*`

```
document.querySelectorAll("*") // => NodeList[...] 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用多个选择器

为了使用多个选择器，我们用一个`,`来分隔它们。

```
<html>
    <body>
        <p>Hello i am a paragraph</p>
        <a href="https://awesomeplace.com">Hey I am a link</a>
        <a href="https://anotherplace.com">I am another link</a>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelectorAll("p, a") // => NodeList[p,a,a] 
```

Enter fullscreen mode Exit fullscreen mode

### 多带元素

在上面的例子中，我们进行了基本的查询，但是也可以做其他的事情，比如按顺序或父元素获取元素。

#### 获取元素子元素

这有两种变体，一种是获取元素的子元素，不管它在树中有多深，另一种是获取元素的直接子元素。

```
<html>
    <body>
        <p>Hello i am a paragraph</p>
        <div>
            <a href="https://awesomeplace.com">Hey I am a link</a>
            <p>
                Hello i am a paragraph and here's
                <a href="https://anotherplace.com">a link</a>
            </p>
        </div>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

以上面的 HTML 为例，我们来看看这两种变体。

*   直系子女

```
document.querySelector("div > a") // => <a href="https://awesomeplace.com">Hey I am a link</a> 
```

Enter fullscreen mode Exit fullscreen mode

*   所有儿童

```
document.querySelectorAll("div a") // => NodeList[a,a] 
```

Enter fullscreen mode Exit fullscreen mode

#### 按顺序获取元素

有两种方法可以做到这一点。考虑下面的 HTML。

```
<html>
    <body>
        <div>
            <a href="https://awesomeplace.com">Hey I am a link</a>
            <pre>I should intervene but i wont</pre>
            <p>Hello i am another paragraph</p>
        </div>
        <p>Hello i am a paragraph</p>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

*   放置在之后

```
document.querySelector("div + p") // => <p>Hello i am a paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

*   之前是

使用`~`，紧跟在匹配后面的元素无关紧要。

```
document.querySelector("a ~ p") // => <p>Hello i am another paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到,`pre`元素并没有影响结果，因为一开始`pre`是否存在并不重要。但是下面的选择器会失败，因为它检查紧接在它上面的元素。

```
document.querySelector("a + p") // => null 
```

Enter fullscreen mode Exit fullscreen mode

### 按属性获取元素

属性选择器以`[`开始，以`]`结束，并被用作

```
<html>
    <body>
        <p style="color:blue;">Hello i am styled</p>
        <p>Hello i have no style</p>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector("p[style]") // => <p style="color:blue;">Hello i am styled</p> 
```

Enter fullscreen mode Exit fullscreen mode

#### 检查属性值

为了检查一个属性值，我们使用了`=`符号。

```
<html>
    <body>
        <a href="https://awesomeplace.com">Hey I am a link</a>
        <a href="https://anotherplace.com">I am another link</a>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector('a[href="https://awesomeplace.com"]') // => <a href="https://awesomeplace.com">Hey I am a link</a> 
```

Enter fullscreen mode Exit fullscreen mode

#### 多带属性值

*   检查属性是否以开头...

```
document.querySelector('a[href^="https://awesome"]') // => <a href="https://awesomeplace.com">Hey I am a link</a> 
```

Enter fullscreen mode Exit fullscreen mode

*   检查属性是否以结尾...

```
document.querySelectorAll('a[href$=".com"]') // => NodeList[a,a] 
```

Enter fullscreen mode Exit fullscreen mode

*   检查属性是否包含子字符串

```
document.querySelectorAll('a[href*="place"]') // => NodeList[a,a] 
```

Enter fullscreen mode Exit fullscreen mode

### 高级选择器

*   :聚焦

这将选择当前具有焦点的元素

*   :已访问

这与`a`标签一起使用，选择已经访问过的链接

*   :链接

这也与`a`标签一起使用，但是在这种情况下，选择没有被访问过的链接

*   :已启用

这将选择已启用(*未禁用* )
的元素

```
<html>
    <body>
        <p>I am a paragraph</p>
        <p>I am another paragraph</p>
        <a href="https://awesomeplace.com">Hey I am a link</a>
        <a href="https://anotherplace.com">I am another link</a>
        <button disabled="true"> I have been disabled </button>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelectorAll(":enabled") // => NodeList[p,p,a,a] 
```

Enter fullscreen mode Exit fullscreen mode

*   :已禁用

这将选择已被禁用的元素

```
<html>
    <body>
        <p>I am a paragraph</p>
        <p>I am another paragraph</p>
        <a href="https://awesomeplace.com">Hey I am a link</a>
        <a href="https://anotherplace.com">I am another link</a>
        <button disabled="true"> I have been disabled </button>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector(":disabled") // => <button disabled="true"> I have been disabled </button> 
```

Enter fullscreen mode Exit fullscreen mode

*   :第一个孩子

这将选择其父元素
的第一个子元素

```
<html>
    <body>
        <p>I am a paragraph</p>
        <p>I am another paragraph</p>
        <a href="https://awesomeplace.com">Hey I am a link</a>
        <a href="https://anotherplace.com">I am another link</a>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector("p:first-child") // => <p>I am a paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

*   :最后一个孩子

这将选择其父元素
的最后一个子元素

```
<html>
    <body>
        <p>I am a paragraph</p>
        <p>I am another paragraph</p>
        <a href="https://awesomeplace.com">Hey I am a link</a>
        <a href="https://anotherplace.com">I am another link</a>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector("p:last-child") // => <a href="anotherplace.com">I am another link</a> 
```

Enter fullscreen mode Exit fullscreen mode

*   *el* :首款

这将选择作为其父元素的第一个子元素并且与 *el*
类型相同的元素

```
<html>
    <body>
        <p>I am a paragraph</p>
        <a href="https://awesomeplace.com">Hey I am a link</a>
        <p>I am another paragraph</p>
        <a href="https://anotherplace.com">I am another link</a>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector("a:first-of-type") // => <a href="https://awesomeplace.com">Hey I am a link</a> 
```

Enter fullscreen mode Exit fullscreen mode

*   el :最后一种类型

这将选择其父元素的最后一个子元素，并且与 *el*
的类型相同

```
<html>
    <body>
        <p>I am a paragraph</p>
        <a href="https://awesomeplace.com">Hey I am a link</a>
        <p>I am another paragraph</p>
        <a href="https://anotherplace.com">I am another link</a>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector("p:last-of-type") // => <p>I am another paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

*   :非(*选择器*

这将选择与选择器
不匹配的元素

```
<html>
    <body>
        <p>I am a paragraph</p>
        <a href="https://awesomeplace.com">Hey I am a link</a>
        <a href="https://anotherplace.com">I am another link</a>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector(":not(a)") // => <p>I am a paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

*   :第 n 个子代( *n* )

这将选择其父元素的第 *n* 个子元素。

```
<html>
    <body>
        <div>
            <p>I am a paragraph</p>
            <a href="https://awesomeplace.com">Hey I am a link</a>
            <a href="https://anotherplace.com">I am another link</a>
        </div>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector("div:nth-child(2)") // => <a href="https://awesomeplace.com">Hey I am a link</a> 
```

Enter fullscreen mode Exit fullscreen mode

*   :第 n 个最后一个孩子( *n* )

这将选择其父元素的第 *n* 个到最后一个子元素。

```
<html>
    <body>
        <div>
            <p>I am a paragraph</p>
            <a href="https://awesomeplace.com">Hey I am a link</a>
            <a href="https://anotherplace.com">I am another link</a>
        </div>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector("div:nth-last-child(1)") // => <a href="https://anotherplace.com">I am another link</a> 
```

Enter fullscreen mode Exit fullscreen mode

### 混搭

这些选择器可以混合在一起执行一些复杂的检查。例如

*   “btn”类的禁用按钮

```
<html>
    <body>
        <div>
            <p>I am a paragraph</p>
            <a href="https://awesomeplace.com">Hey I am a link</a>
            <a href="https://anotherplace.com">I am another link</a>
        </div>
        <button disabled="true">I am disabled</button>
        <button disabled="true" class="btn">I am also disabled</button>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector('button.btn:disabled') // => <button disabled="true" class="btn">I am also disabled</button> 
```

Enter fullscreen mode Exit fullscreen mode

*   `form`中所有禁用的按钮

```
<html>
    <body>
        <form method="POST">
            <input type="text" name="firstname" placeholder="firstname"/>
            <input type="text" name="lastname" placeholder="lastname"/>
            <button disabled="true">Clear inputs</button>
            <button type="submit">Submit</button>
        </form>
        <button disabled="true">I am disabled</button>
        <button disabled="true" class="btn">I am also disabled</button>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector('form > button:disabled') // => <button disabled="true">Clear inputs</button> 
```

Enter fullscreen mode Exit fullscreen mode

*   一个`form`中所有被禁用的按钮及其外部的所有按钮

```
<html>
    <body>
        <form method="POST">
            <input type="text" name="firstname" placeholder="firstname"/>
            <input type="text" name="lastname" placeholder="lastname"/>
            <button disabled="true">Clear inputs</button>
            <button type="submit">Submit</button>
        </form>
        <button>I am not disabled</button>
        <button class="btn">I am also not disabled</button>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelectorAll('form > button:disabled, form ~ button') // => NodeList[button, button, button] 
```

Enter fullscreen mode Exit fullscreen mode

*   指向外部页面的所有链接

```
<html>
    <body>
        <div>
            <p>I am a paragraph</p>
            <a href="https://awesomeplace.com">Hey I am a link</a>
            <a href="https://anotherplace.com">I am another link</a>
        </div>
        <a href="/otherpage.html">I will to the other pages</a>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelectorAll('a[href^="https://"]') // => NodeList[a,a] 
```

Enter fullscreen mode Exit fullscreen mode

要获得非外部页面的链接，使用

```
document.querySelector('a:not([href^="https://"])') // => <a href="/otherpage.html">I will to the other pages</a> 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这些只是你在 javascript 中使用选择器的一些方法，如果你想知道更多，这里有一个链接到 w3c 上的 CSS 选择器参考。

### 感谢阅读！！！