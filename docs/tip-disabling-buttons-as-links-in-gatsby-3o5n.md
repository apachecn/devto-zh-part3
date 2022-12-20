# 提示:在 Gatsby 中禁用按钮链接

> 原文：<https://dev.to/bbarbour/tip-disabling-buttons-as-links-in-gatsby-3o5n>

我今天正在做我的盖茨比博客项目，遇到了一个小问题。我想要导航按钮，让用户进入博客中的下一篇和上一篇文章。我希望这些按钮是盖茨比链接。很简单，对吧？然而，当用户到达第一个帖子或最后一个帖子时，我希望该按钮链接被禁用。

例如:

```
 <Link
          disabled={!nextPost}
          className="button"
          to={nextPost ? nextPost.node.fields.slug : "/"}
        >Next Post</Link> 
```

Enter fullscreen mode Exit fullscreen mode

看，如果 nextPost 是 falsy，链接应该被禁用。除此之外，在实践中我发现，尽管它看起来被禁用，但它仍然可以被点击和发射。它会把它们甩到网站的索引页上。

在谷歌上做了一些调查后，我发现了这个巧妙的技巧。它要求我将这个类添加到我的 CSS 中。

```
.disabled-link {
  pointer-events: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

在那之后，我只需要一个三元语句，把它放在组件的返回 JSX 之前。

```
 const disabledNext = nextPost ? "" : "disabled-link" 
```

Enter fullscreen mode Exit fullscreen mode

最后，我将我的链接按钮变形为:

```
 <Link
          disabled={!nextPost}
          className={`button ${disabledNext}`}
          to={nextPost ? nextPost.node.fields.slug : "/"}
        >Next Post</Link> 
```

Enter fullscreen mode Exit fullscreen mode

的。disabled-link CSS 类使它根本不能被点击，返回我想要的禁用功能。砰，砰，砰！