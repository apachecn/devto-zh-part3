# 我如何遇见你的盖茨比-第一天和第二天

> 原文：<https://dev.to/sandeepbalachandran/how-i-met-your-gatsby-day-1-day-2-5e6c>

这次我试着设计了组件的样式。我复制粘贴

1) [使用 CSS 模块设计组件样式](https://www.gatsbyjs.org/tutorial/part-two/#-build-a-new-page-using-css-modules)
2) [使用 CSS 模块构建新页面](https://www.gatsbyjs.org/tutorial/part-two/#-style-a-component-using-css-modules)

似乎很有趣。道具和导入变量的工作很酷。

```
 import React from "react"
import styles from "./about-css-modules.module.css"
import Container from "../components/container"

console.log(styles)

const User = props => (
  <div className={styles.user}>
    <img src={props.avatar} className={styles.avatar} alt="" />
    <div className={styles.description}>
      <h2 className={styles.username}>{props.username}</h2>
      <p className={styles.excerpt}>{props.excerpt}</p>
    </div>
  </div>
)

export default () => (
  <Container>
    <h1>About CSS Modules</h1>
    <p>CSS Modules are cool</p>
    <User
      username="Jane Doe"
      avatar="https://s3.amazonaws.com/uifaces/faces/twitter/adellecharles/128.jpg"
      excerpt="I'm Jane Doe. Lorem ipsum dolor sit amet, consectetur adipisicing elit."
    />
    <User
      username="Bob Smith"
      avatar="https://s3.amazonaws.com/uifaces/faces/twitter/vladarbatov/128.jpg"
      excerpt="I'm Bob Smith, a vertically aligned type of guy. Lorem ipsum dolor sit amet, consectetur adipisicing elit."
    />
  </Container>
) 
```

Enter fullscreen mode Exit fullscreen mode

我设法找到了一些随机的新的 npm 包和其他用于不同目的的工具，尽管我只涉及了盖茨比的一小部分。

*   汹涌
*   部署总部
*   Netlify

1) [浪涌](https://surge.sh/)——面向前端开发者的静态网页发布

```
npm install --global surge 
```

Enter fullscreen mode Exit fullscreen mode

[![Alt text of image](img/d9ff35a01d65bd942be2fcc452678e13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uN7IVTbb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.gatsbyjs.org/static/2718feec8167496f9e7a5087a5f539e7/73d01/surge-deployment.png)

2)[Deployhq](https://www.deployhq.com/)——构建、部署和管理
现代 web 项目

3) [Netlify](https://www.netlify.com/) -构建、部署和管理
现代网络项目(额外的东西)

## 即将到来-创建嵌套布局组件