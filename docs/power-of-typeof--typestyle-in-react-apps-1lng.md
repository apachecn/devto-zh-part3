# react 应用程序中 typeof 和 typestyle 的强大功能

> 原文：<https://dev.to/aexol/power-of-typeof--typestyle-in-react-apps-1lng>

你听说过 [typestyle](https://github.com/typestyle/typestyle) 吗？没有吗？这是一个伟大的无网络包造型解决方案。在我的生活中，我尝试了几乎所有可能的方法:

*   纯 CSS
*   SCSS
*   厚颜无耻
*   CSS 模块
*   CSS 下一步
*   内嵌反应样式
*   字体

大多数解决方案都与 webpack 捆绑在一起，这意味着如果你想编写包含可扩展可替换样式的库，你不能使用 webpack！因此，您需要自动取消这些解决方案的资格:

*   CSS 下一步
*   CSS 模块
*   SCSS
*   厚颜无耻
*   纯 CSS

所以让我们更仔细地看看这些:

*   内嵌反应样式
*   字体

内联的 react 样式很好，但是会产生意大利面条式的代码

```
import * as React from 'react'
export const MyInlineComponent = ({ children, style }) => (
  <div
    style={{
      fontSize: 15,
      ...style,
    }}
  >
    {children}
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

此外，您不能包括媒体和悬停指令。但是使用 onmouseover 是很难看的，你需要状态来控制它。

让我们试试字体，然后

```
import * as React from 'react'
import { style, classes, media } from 'typestyle'

export const MyComponentStyle = style(
  {
    fontSize: 15,
    $nest: {
      '&:hover': {
        fontSize: 18,
      },
    },
  },
  media({ maxWidth: 480 }, { fontSize: 10 })
)

// Extending the style

const MyNewStyle = classes(
  MyComponentStyle,
  style({
    color: '#0ae',
  })
)

export const MyStyledComponent = ({ children, style = '' }) => (
  <div className={classes(MyNewStyle, style)}>{children}</div>
) 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错是吗？现在是高级程序员风格解决方案的时候了。想象一个有更多样式的组件。我将为这个解决方案在单独的文件中编写样式

styles.tsx

```
import { style } from 'typestyle'

export const Title = style({
  fontSize: 18,
})
export const SubTitle = style({
  fontSize: 18,
})
export const ImageSize = style({
  width: 20,
  height: 20,
}) 
```

Enter fullscreen mode Exit fullscreen mode

component.tsx

```
import * as React from 'react'
import * as styles from './styles'

export const MyStyledComponent = ({
  overrideStyles = {},
}: {
  overrideStyles: typeof styles
}) => {
  const componentStyles = {
    ...styles,
    ...overrideStyles,
  }
  return (
    <div>
      <div className={componentStyles.Title}>Hello</div>
      <div className={componentStyles.SubTitle}>World</div>
      <img
        className={componentStyles.ImageSize}
        src="https://source.unsplash.com/random"
      />
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

usingComponent.tsx

```
import * as React from "react";
import { style } from "typestyle";
import { MyStyledComponent } from "./component";

const Page = () => (
  <div>
    <MyStyledComponent
      overrideStyles={{
        Title: style({
          color: "#00F"
        })
      }}
    />
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

瞧，你可以使用这个组件，用完全智能覆盖它的样式！！只需在模块上使用 typeof。