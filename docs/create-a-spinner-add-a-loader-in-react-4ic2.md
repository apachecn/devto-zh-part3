# 创建一个微调器并在 React 中添加一个加载器

> 原文：<https://dev.to/stephencweiss/create-a-spinner-add-a-loader-in-react-4ic2>

使用两个库，`styled-components`和`react-image`，我学习了一种新的简单方法来创建一个`Loader`组件(即一个旋转器),当一个图像被加载到`react`应用程序中时使用。

# 创建您的微调器

微调器只是一个带有动画的组件。然而，在这样做的过程中，我在`styled-components`中了解到了对`keyframes`的标记模板文字支持。 <sup>1</sup>

如果你正在构建一个 UI 组件库(像我一样)，这种类型的抽象会非常好。

这里有一个`Loader`组件的例子。

```
import styled, { keyframes } from 'styled-components'

const spin = keyframes`
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(360deg);
    }
`

export const Loader = styled.div`
  border: 0.2em solid rgba(0, 0, 0, 0.1);
  border-top: 0.2em solid #767676;
  border-radius: 50%;
  width: 2.28571429rem;
  height: 2.28571429rem;
  animation: ${spin} 0.6s linear infinite;
`

export default Loader 
```

# 用加载器创建图像

现在我有了一个加载器，我可以把它拉进我的 ImageComponent，并在加载图像时使用它，这要感谢简单明了的 API`react-image`。<sup>2</sup>T3】

```
import React from 'react'
import Img from 'react-image'

import { housePlaceholder } from 'assets'
import { Loader } from './Loader'

function ImageComponent(props) {
  const { alt, image } = props
  return (
    <Img alt={alt} src={image ? image : housePlaceholder} loader={<Loader />} />
  )
}

export default ImageComponent 
```

## 脚注

*   <sup>1</sup> [API 参考|风格化组件](https://www.styled-components.com/docs/api)
*   <sup>2</sup>T3】react . js`<img>`标签渲染带多重回退&加载器支持| react-image