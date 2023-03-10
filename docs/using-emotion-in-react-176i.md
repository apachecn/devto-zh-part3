# 在反应中运用情感

> 原文：<https://dev.to/paulryan7/using-emotion-in-react-176i>

## 简介

CSS-in-JS 是一个激烈争论的话题，就个人而言，我认为你应该专注于运输和使用你想要的任何东西！然而，情绪是一个很好的工具，我将很快向你展示如何利用它。

我已经创建了下面的组件，但问题是所有的样式都存在于`style.css`文件中，我们将把它转换成使用情感。

首发沙盒在这里:
[https://codesandbox.io/embed/927ny28zx4](https://codesandbox.io/embed/927ny28zx4)

## 安装情感

你需要两个依赖关系才能使用情感
`@emotion/core`
`@emotion/styled`

因此，如果您在本地工作，请将这些添加到您的项目或`npm install`中。

## 给组件添加样式

看看下面的代码，我们在 JS 文件中设计组件的样式。

```
import React from "react";
import styled from "@emotion/styled";

const Component = styled("card")`
  .card {
    background-image: url("https://i.pinimg.com/originals/1c/97/df/1c97df47df64fe9357290bd7f51d0a1a.jpg");
    background-repeat: no-repeat;
    background-size: 200px 350px;
    height: 350px;
    width: 200px;
    position: relative;
    display: flex;
    justify-content: center;
  }

... more styles
`;

const Card = ({ handleClick }) => {
  return (
    <Component>
      <div className="card">
        <div className="text">
          <div className="action">
            <span>VISIT</span>
          </div>
          <div className="country">
            <span>Iceland</span>
          </div>
        </div>
        <button onClick={handleClick}>DISCOVER</button>
      </div>
    </Component>
  );
};

export default Card; 
```

正如你从上面看到的，我们创建了一个名为`Component`的新组件，并将其分配给`styled(Card)`，然后我们使用一个模板文本来插入我们的样式。

这是一个很好的方法来保持你的 css 的作用域，并且保持 css 和组件在同一个文件中。如果将来我们需要删除这个组件，我们就不必担心寻找与之相关的所有样式，因为它们都在同一个文件中。太棒了。

## 导入颜色

因为我们使用的是 JS，所以我们可以从一个对象导入颜色，并将其添加到组件中。

创建一个名为`colors.js`的文件，并添加以下内容:

```
export default {
  dark: "#333"
}; 
```

现在在组件中我们可以用
`import color from '../../colors'`导入颜色

我们可以这样使用深色:

```
button {
    background-color: ${color.dark};
    ....
} 
```

那都是乡亲们！

大概就是这样，这只是最基本的，但是足够让你行动起来了！

最后一个例子:
[https://codesandbox.io/embed/kw6yj8mx37](https://codesandbox.io/embed/kw6yj8mx37)