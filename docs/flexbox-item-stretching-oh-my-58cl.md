# Flexbox 项目拉伸，天啊！

> 原文：<https://dev.to/engelmav/flexbox-item-stretching-oh-my-58cl>

要阻止项目在 flexbox 容器中垂直拉伸，请将其应用到容器:`align-self: flex-start;`。

在 tailwindcss 中，这将是`class="items-center"`(或者任何其他的`items-{position}`类)。这里有一个顺风的例子:

```
 <div class="flex items-center">
          <button>Edit</button>
          <button>Delete</button>
        </div> 
```

在 StackOverflow 驱动的开发中没有什么丢人的。

[https://stackoverflow.com/a/33887124/1258020](https://stackoverflow.com/a/33887124/1258020)
T3】https://tailwindcss.com/docs/flexbox-align-items