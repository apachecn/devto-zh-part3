# 更好的可重用媒体情感查询

> 原文：<https://dev.to/bidah/better-reusable-media-queries-on-emotion---4dkk>

当你想使用可重复使用的媒体对情感进行查询时，你会在官方文档中得到下面的例子:

```
/** @jsx jsx */
import { jsx, css } from '@emotion/core'

const breakpoints = [576, 768, 992, 1200]

const mq = breakpoints.map(
  bp => `@media (min-width: ${bp}px)`
)

render(
  <div>
    <div
      css={{
        color: 'green',
        [mq[0]]: {
          color: 'gray'
        },
        [mq[1]]: {
          color: 'hotpink'
        }
      }}
    >
      Some text!
    </div>
    <p
      css={css`
        color: green; ${mq[0]} {
          color: gray;
        } ${mq[1]} {
          color: hotpink;
        }
      `}
    >
      Some other text!
    </p>
  </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

首先，在数组中设置断点。

```
const breakpoints = [576, 768, 992, 1200] 
```

Enter fullscreen mode Exit fullscreen mode

然后将这些值映射到一个字符串，该字符串将包含每个媒体查询的签名。这是可重用媒体查询为您简化的一段代码。

```
const mq = breakpoints.map(
  bp => `@media (min-width: ${bp}px)`
) 
```

Enter fullscreen mode Exit fullscreen mode

最后，只需使用`mq`变量并获取想要使用的索引，就可以轻松地使用媒体查询。

```
${mq[0]} {
 color: gray;
} 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但我们可以做得更好。

通过这种方法，我发现在设置我的媒体查询时，他们并没有告诉我正在使用的断点。`mq[0]`是移动，但是`mq[3]`呢。是 Ipad，更大的手机，还是台式机？

我需要一种更能说明问题的方式。更清楚地知道我正在使用或需要实现什么断点。

长话短说，我留给你们的是以前的可重用媒体查询的例子，但是有了新的实现。

```
/** @jsx jsx */
import { jsx, css } from '@emotion/core'

const bp = {
  small: 500,
  large: 1200
};

const mq = n => {
  const bpArray = Object.keys(bp).map(key => [key, bp[key]]);

  const [result] = bpArray.reduce((acc, [name, size]) => {
    if (n === name) return [...acc, `@media (min-width: ${size}px)`];
    return acc;
  }, []);

  return result;
};

render(
  <div>
    <div
      css={{
        color: 'green',
        [mq('small')]: {
          color: 'gray'
        },
        [mq('large')]: {
          color: 'hotpink'
        }
      }}
    >
      Some text!
    </div>
    <p
      css={css`
        color: green; ${mq('small')} {
          color: gray;
        } ${mq('large')} {
          color: hotpink;
        }
      `}
    >
      Some other text!
    </p>
  </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在用一个对象定义断点。我们可以用一个键来命名断点，然后设置值。这是一个普通的旧对象，它比以前的数组有更好的用途。

```
const bp = {
  small: 500,
  large: 1200
}; 
```

Enter fullscreen mode Exit fullscreen mode

另请注意，这一次我们也可以对订单更加灵活。与以前的版本相比没有关系，在以前的版本中，改变数组中的顺序会弄乱断点，你认为你在使用断点，但你没有。

```
const bp = {
  small: 500,
  smaller: 300,
  large: 1200,
  iphone4: 320
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们创建一个方法，将`bp`对象转换成一个数组，数组中包含断点名称和宽度对的值。然后我们将它简化成一个只有一个字符串的数组，这个字符串包含您作为`n`参数传递的媒体查询的签名，就像 Emotion docs 前面的例子一样。
我们最终将数组字符串值析构为一个我们将返回的`result`变量。

```
const mq = n => {
  const bpArray = Object.keys(bp).map(key => [key, bp[key]]);

  const [result] = bpArray.reduce((acc, [name, size]) => {
    if (n === name) return [...acc, `@media (min-width: ${size}px)`];
    return acc;
  }, []);

  return result;
}; 
```

Enter fullscreen mode Exit fullscreen mode

因此，有了这个配置，我们现在可以用一种简单明了的方式通过名字来定义和调用断点，而且还能清楚地知道我们在改变什么。

```
 ${mq('small')} {
          color: gray;
        }
        ${mq('large')} {
          color: hotpink;
        } 
```

Enter fullscreen mode Exit fullscreen mode