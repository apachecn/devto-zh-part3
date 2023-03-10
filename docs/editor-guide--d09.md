# dev.to 编辑指南╻╻╻╻╻。

> 原文：<https://dev.to/gaerae/editor-guide--d09>

这是 dev.to 编辑指南文件的重组文章。
目前还缺乏韩语支持，不方便，但以后期待的服务。

## **需要知道的东西**

*   使用[JEKYLL 前缀](https://jekyllrb.com/docs/frontmatter/)。
    *   [编辑 v2 测试版](https://dev.to/settings/misc)中没有前缀标签。
    *   编辑器 v1 是默认设置。
*   可以用 HTML 语法编写。
*   支持 Liquid，正在添加有趣的自定义标签。
*   非公开帖子也提供预览地址供反馈/审阅。
*   准备公开时，请将前言中的“T0”值设置为“T1”。
*   用前缀的“T0”值自动生成 URL，但不支持韩文，因此显示为“T1”。
    *   如果想制作好看的 URL，请用英文公开后将“T0”更改为韩文。
*   编辑器不会自动保存。请定期保存。

## **前言**

第一行中 3 个破折号字符(`---`)之间是前缀区域，并为每个帖子设置用户定义的变量。以下是可用列表。

*   **title:**帖子标题。
*   **published:** 使用`true`公开帖子，非公开的话使用`false`。
*   **description:**在 Twitter cards 或 open graph cards 中用作帖子说明。
*   **tags:**标签用逗号(，)分隔，最多可达 4 个。
*   **canonical _ url:输入帖子**的标准 URL。
*   **cover _ image:**可以插入帖子封面图像 URL，建议大小为 1000x420。
*   **系列:**帖子系列名称。

## **【markdown】**

下面是一些常用的降价语法的例子。如果你想更深入地了解，看看这个备忘单。

### **强调字母**

*字倾斜* : `*asterisks*`或`_underscores_`
**字粗** : `**글자 굵기**`或`__글자 굵기__`

### **链接**

这里是链接。 : `[여기가 링크입니다.](링크 주소)`

### **图像**

[![샘플 이미지-sloan](img/04c75bdfd9670e0ab2242faf86a88de9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lP-to7Dw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--OsLaFSo9--/c_fill%2Cf_auto%2Cfl_progressive%2Ch_220%2Cq_auto%2Cw_220/https://thepracticaldev.s3.amazonaws.com/uploads/user/profile_image/31047/af153cd6-9994-4a68-83f4-8ddf3e13f0bf.jpg)T3】

```
!\[이미지 대체 텍스트\](이미지 주소) 
```

您可以使用 HTML`figcaption`标签加入注解！

### **题目**

使用以下语法为帖子添加标题:

```
# 1개의 '#'은 h1 태그와 같습니다.
## 2개의 '#'은 h2 태그와 같습니다.
...
###### 6개의 '#'은 h6 태그와 같습니다. 
```

# 一个“#”等于 h1 标签。

## 两个“#”等于 h2 标签。

....

###### 6 个“#”等于 h6 标签。

## **╸╸╸╸╸╸╸。Liquid 基本语法**

[Liquid](https://shopify.github.io/liquid/) 支持默认标签，但也支持用户定义的标签。以下是用户定义的标记。

### **dev.to 文/Post 퍼가기**

请输入完整的 URL。

```
{% link https://dev.to/kazz/boost-your-productivity-using-markdown-1be %} 
```

除了域外，还可以从 ID 开始输入。

```
{% link kazz/boost-your-productivity-using-markdown-1be %} 
```

### **开发给用户퍼가기**

请只输入用户名。

```
{% user gaerae %} 
```

### **dev.to 标签퍼가기**

请只输入标签名称。

```
{% tag git %} 
```

### **开发者评论퍼가기**

点击评论的右上角箭头，然后在“Permalink”中确认并输入 URL 的“T0”信息。

```
{% devcomment 2d1a %} 
```

### **dev.to 播客插曲퍼가기**

请输入完整的 URL。

```
{% podcast https://dev.to/basecspodcast/s2e2--queues-irl %} 
```

### **推特퍼가기**

请输入帖子 T0。

```
{% twitter 834439977220112384 %} 
```

### **小故障퍼가기**

请输入项目 ID。

```
{% glitch earthy-course %} 
```

有几个“T1”选择选项“T2”。请在 ID 后输入空格分隔的内容。
`app`

*   仅显示应用程序预览。
*   `{% glitch earthy-course app %}`

`code`

*   仅显示代码。
*   `{% glitch earthy-course code %}`

`preview-first`

*   在屏幕左侧显示应用程序预览。
*   `{% glitch earthy-course preview-first %}`

`no-attribution`

*   隐藏作者信息。
*   `{% glitch earthy-course no-attribution %}`

`no-files`

*   隐藏文件列表。
*   `{% glitch earthy-course no-files %}`

`file`

*   可以更改默认输出文件。默认值为“T0”。
*   `{% glitch earthy-course file=script.js %}`

### **GitHub 回购퍼가기**

请输入用户和存储库名称。

```
{% github thepracticaldev/dev.to %} 
```

### **GitHub 发布或评论퍼가기**

请输入完整的 URL。

```
{% github https://github.com/thepracticaldev/dev.to/issues/9 %} 
```

### **GitHub 要诀퍼가기**

请输入完整的 URL。

```
{% gist https://gist.github.com/QuincyLarson/4bb1682ce590dc42402b2edddbca7aaa %} 
```

### **视频퍼가기**

请只输入 ID 值。

*   **YouTube，Vimeo**

```
{% youtube dQw4w9WgXcQ %}

{% vimeo 193110695 %} 
```

### **퍼가기**

请只输入 T0 的值。

```
{% slideshare rdOzN9kr1yK5eE %} 
```

### **CodePen 퍼가기**

请输入完整的 URL。

```
{% codepen https://codepen.io/twhite96/pen/XKqrJX %} 
```

`default-tab`

*   支持基本选项卡选项。默认值为“T0”。
*   `{% codepen https://codepen.io/twhite96/pen/XKqrJX default-tab=js,result %}`

### [**符号学**](#runkit-%ED%8D%BC%EA%B0%80%EA%B8%B0)

请参考下面的例子输入。

```
{% runkit
// 숨겨진 설정 코드를 추가하는 영역입니다.
const hiddenVar = 42
%}
// visible, reader-editable 코드를 추가하는 영역입니다.
console.log(hiddenVar)
{% endrunkit %} 
```

### **斯塔克布里兹퍼가기**

请输入 Stackblitz ID 值。:

```
{% stackblitz ball-demo %} 
```

### **CodeSandbox 퍼가기**

请输入 Sandbox ID 值。

```
{% codesandbox ppxnl191zx %} 
```

### **퍼가기**

请在 URL 中输入除域以外的信息。

```
{% replit @WigWog/PositiveFineOpensource %} 
```

### **Instagram 퍼가기**

请输入帖子 ID 值。

```
{% instagram BXgGcAUjM39 %} 
```

### **演讲者德克퍼가기**

请在嵌入链接信息中确认“T0”，然后输入到用户定义的标签中。

```
# 제공하는 Embed link 정보입니다.
< script async class="speakerdeck-embed"
data-id="7e9f8c0fa0c949bd8025457181913fd0"
data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js">< /script > 
```

请输入上面确认的 T0 值。

```
{% speakerdeck 7e9f8c0fa0c949bd8025457181913fd0 %} 
```

### [**声云【】**](#soundcloud-%ED%8D%BC%EA%B0%80%EA%B8%B0)

请输入完整的 URL。

```
{% soundcloud https://soundcloud.com/user-261265215/dev-to-review-episode-1 %} 
```

### **分析数据**

这是一种简单的使用方法。

```
`{% mytag %}{{ site.SOMETHING }}{% endmytag %}` 
```

使用`raw`标签的方法。

```
`{% raw %}{{site.SOMETHING }} {% ``endraw`` %}` 
```

### **常见的缺陷**

如果需要在编号列表之间添加图像，则必须在图像标记之前添加空格或制表符，才能连接编号。否则，图像标记下一个列表编号将初始化，从 1 开始。

```
목록 예제
1\. 목록 1입니다.
    ![이미지 대체 텍스트](이미지 주소)
2\. 목록 2입니다. 
```

#### [如果想进一步了解 Markdown 语法，请点击。](https://github.com/adam-p/markdown-here/wiki/Markdown-Here-Cheatsheet)

愉快地写帖子吧！￥￥￥￥￥。