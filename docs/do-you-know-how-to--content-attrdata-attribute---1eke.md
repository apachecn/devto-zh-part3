# 你知道如何{ content:attr({ data-attribute })；} ?

> 原文：<https://dev.to/rinstarskykujat/do-you-know-how-to--content-attrdata-attribute---1eke>

我们可以将 deta 传递给 el::before，after { content:' '；}

```
 <div class="button">
        <span data-attribute="HOME">HOME</span>
    </div> 
```

```
 .button{
        span::before {
            content: attr(data-attribute);
            }
        }
    } 
```

这非常简单！！！

我用这个机制制作了一个简单的动画。

谢谢！！！！

参见 [CodePen](https://codepen.io) 上 Rin_T_T ( [@Rin_T_T](https://codepen.io/Rin_T_T) )的笔[内容数据-属性](https://codepen.io/Rin_T_T/pen/WYJVpd/)。