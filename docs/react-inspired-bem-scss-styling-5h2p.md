# 受 React 启发的贝姆/ SCSS 风格

> 原文：<https://dev.to/metamn/react-inspired-bem-scss-styling-5h2p>

老问题的新技术。

* * *

在 React 上工作了一段时间后，我不得不切换回一个经典的项目:WordPress 和带有一些 BEM 的普通 SCSS。任务是将古老的代码库带入现代组件时代。

我想知道如何保持 React 提供的令人愉快的工作流:组件对它们自己的依赖和状态负责。它们是独立的、自包含的。

我正在寻找实现类似于:

```
// Component.js

// Imports all dependencies
import React from 'react'
import PropTypes from 'prop-types'
import styled, { css } from 'styled-components'

...

// Manages state
class Component extends React.Component {
    constructor(props) {
        super(props)

        this.state = {
            ...
        }
    }

    changeState(i) {

        ...

        this.setState({
            ...
        })
    }

    render() {
        return (
            <Container handleClick={i => this.changeState(i)}>
                ...
            </Container>
        )
    }
} 
```

事实证明，用 SCSS /边界元法实现类似的东西是非常容易的

```
// button.scss

// Imports all dependencies
@import '../colors/colors.scss';
@import '../link/link.scss';
@import '../border/border.scss';
@import '--primary/button--primary.scss';
@import '--secondary/button--secondary.scss';

@mixin button($variant: primary) {
  @include colors(inverted);
  @include link(not-underlined);
  @include border(rounded);

  // Manages state / variations / deviations
  @if ($variant == primary) {
    @include button--primary;
  }

  @if ($variant == secondary) {
    @include button--secondary;
  }

  ...;
} 
```

在以这种方式编写了数百个组件之后，就可以安心了。

每个组件都是独立的、自包含的。

导入其内部工作所需的所有必要内容，并包含默认情况下的所有偏差和变化。