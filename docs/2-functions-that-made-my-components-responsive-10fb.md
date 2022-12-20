# 让 React 组件只使用两个函数进行响应

> 原文：<https://dev.to/jepser/2-functions-that-made-my-components-responsive-10fb>

## 一点点脉络

最近，我作为一名前端工程师加入了[巴蒂](https://badi.com)，在开发主要产品时，我发现它使用了 Bootstrap 布局组件，特别是`Row`、`Col`和`Container`，这些组件有一个 props 接口，允许您通过普通属性或对象样式属性为每个可用的媒体查询配置组件。

```
import { Col } from 'super-library'

// either like this
<Col md={5} offsetMd={1} /> 
// or this
<Col md={{ size: 5, offset: 1 }} /> 
```

前端团队刚刚开始开发组件库，其中有最常用组件的标记，在我以前的经验中，我发现用于间距、对齐和排列的“布局组件”非常有用，所以我创建了两个组件:

*   这意味着以 8px 为单位管理间距，以提高设计精度(受我之前公司设计系统的启发)
*   这是一个围绕 flex 布局的简单实现，还添加了一些调料

```
const Spacer = ({
  top, left, bottom, right, children,
}) => {
  return (
    <Root
      top={ top }
      left={ left }
      bottom={ bottom }
      right={ right }
    >
      {children}
    </Root>
  );
}; 
```

```
const Root = styled.div`
$({
  top, left, right, bottom,
}) => css`
  margin-top: ${top ? unitizedPx(top) : null};
  margin-right: ${right ? unitizedPx(right) : null};
  margin-bottom: ${bottom ? unitizedPx(bottom) : null};
  margin-left: ${left ? unitizedPx(left) : null};
 `
`; 
```

我们很高兴，此时我们正从 CSS 模块转移到样式组件，因此我们没有使用重复的 flex 和 spacing 样式属性，而是使用描述性组件，这使我们更快，细节开销更少，因为经过良好测试的组件负责这些。

## 挑战

到目前为止一切都很好，直到我们有了一个元素列表，这些元素根据视口有不同的布局和间距。最快的方法是使用我们的`MatchMedia`组件，它使用 render props 模式来显示 mount 上的一个或另一个组件。

```
<MatchMedia query={BREAKPOINTS.md}>
  { matches => matches ? <UseThis /> : <UseOther /> }
</MatchMedia> 
```

因为这个组件只是在调用 API 之后才呈现，所以错误组件的闪现不是问题。

但是我们可以做得更好。

## 溶液(ish)

还记得自举方法吗？团队已经熟悉了这个接口，我们也已经在使用我们的媒体查询 enum，那么为什么不把这个实现绑定到`Spacer`和`Flex`组件中呢？

因此，间隔符应该是这样的:

```
// for base usage
<Spacer bottom={2} left={2}>cool component here</Spacer> 
// for responsive usage
<Spacer md={{ bottom: 2, left: 2 }} left={1}>cool component here</Spacer> 
```

简单不？

```
const Spacer = ({
  top, left, bottom, right, children, sm, md, sm, lg, xl,
}) => {
  return (
    <Root
      top={ top }
      left={ left }
      bottom={ bottom }
      right={ right }
      sm={sm}
      md={md}
      lg={lg}
      xl={xl}
    >
      {children}
    </Root>
  );
}; 
```

```
const baseStyles = ({
  top, left, right, bottom,
}) => css`
  margin-top: ${top ? unitizedPx(top) : null};
  margin-right: ${right ? unitizedPx(right) : null};
  margin-bottom: ${bottom ? unitizedPx(bottom) : null};
  margin-left: ${left ? unitizedPx(left) : null};
`;

export const Root = styled.div` ${
    ({
      top, left, right, bottom, sm, md, lg, xl
    }) => ` ${baseStyles({ top, left, right, bottom })}  ${sm && baseStyles(sm)}  ${md && baseStyles(md)}  ${lg && baseStyles(lg)}  ${xl && baseStyles(xl)} `
  } `; 
```

如果传递的属性格式正确，这将会起作用。但是，我们可以做得更好。

## 这 2 个函数

因此，上面的实现过于冗长，暴露了我们的媒体查询的实现，如果我们添加另一个规则，并且我们有几个响应组件，这将无法很好地扩展。

我们知道:

*   将会有基础道具，在`Spacer`、【上、右、下、左】的情况下
*   将有相同形状的媒体查询，这将允许更细粒度的控制，在我们的情况下，我们有[sm，md，lg，xl]

我们已经使用了样式组件库中技巧和提示中的[媒体模板工具](https://github.com/styled-components/styled-components/blob/master/packages/styled-components/docs/tips-and-tricks.md#media-templates)。

所以我们有自己的规则:

```
const Root = styled.div`
 //... ${mediaqueries.md`
  //specific rules for this break point
 `
` 
```

### 道具验证

我们需要验证响应条件下道具的形状，这样我们可以确保我们有预期的行为，这不需要添加任何依赖，所以我想到了这个:

```
/**
 * Generate a valid structure for responsive configuration for a component
 * @param {object} props props received from the component
 * @param {array} baseProps list of props to be validated
 *
 * @returns a structured object with the props for each media query
 */
export const generateResponsiveProps = (props, baseProps) => {
  // from the breakpoints registered check which props exists
  const shapedPropsWithMq = Object.keys(BREAKPOINTS).reduce(
    (responsiveProps, mqName) => {
      const propsForMq = props[mqName];
      if (!propsForMq && typeof propsForMq !== 'object') return responsiveProps;

      // for the props that exists, prepare them with the correct shape
      const shapedProps = baseProps.reduce(
        (propList, prop) => ({
          ...propList,
          [prop]: propsForMq[prop],
        }),
        {}
      );

      return {
        ...responsiveProps,
        [mqName]: shapedProps,
      };
    },
    {}
  );

  return shapedPropsWithMq;
}; 
```

这将创建一个 responsive props 对象，样式化组件的值为空。比如`Spacer`组件需要上右下右道具:

```
const BASE_PROPS = ['top', 'right', 'bottom', 'left']

// with this component:
<Spacer sm={{ bottom: 1, top: 2 }} md={{ bottom: 2, top: 1 }} sl={{ top: 1 }} /> 
const responsiveProps = generateResponsiveProps(props, BASE_PROPS)

// will generate this and remove sl because that's not in my media queries

{
  sm: {
    bottom: 1,
    top: 2,
    left: null,
    right: null
  },
  md: {
    bottom: 2,
    top: 1,
    left: null,
    right: null
  }
} 
```

这对将要传递给样式化组件的规则很有用。

### 响应式造型

既然道具的形状已经正确，下一件事就是将它们应用到组件中，为此，我创建了一个 helper 函数，它接收 styles 函数并返回给定道具的样式，以及为每个断点定义的样式。

```
import { css } from 'styled-components'

// this is what you should change if you have different breakpoints
const sizes = {
  giant: 1170,
  desktop: 992,
  tablet: 768,
  phone: 376,
}

// iterate through the sizes and create a media template
export const mediaqueries = Object.keys(sizes).reduce((accumulator, label) => {
  // use em in breakpoints to work properly cross-browser and support users
  // changing their browsers font-size: https://zellwk.com/blog/media-query-units/
  const emSize = sizes[label] / 16
  accumulator[label] = (...args) => css`
    @media (max-width: ${emSize}em) { ${css(...args)};
    }
  `
  return accumulator
}, {}) 
```

```
import { mediaqueries } from '../my-path-to-mq'

/**
 * Call the styles factory for with the correct props for each media query
 * @param {function} stylesGenerator function that generates the styles
 *
 * @returns {array} array of styles to be applied for the registered media queries
 */
export const generateResponsiveStyles = stylesGenerator => props =>
  Object.keys(mediaqueries).reduce((rules, mq) => {
    if (!props[mq]) return rules;

    const styles = mediaqueries[mq]` ${stylesGenerator(props[mq])} `;
    return [...rules, styles];
  }, []); 
```

最后，样式化的组件看起来应该是这样的:

```
// define the styling function
const baseStyles = ({ top, left, right, bottom }) => css`
  margin-top: ${top ? unitizedPx(top) : null};
  margin-right: ${right ? unitizedPx(right) : null};
  margin-bottom: ${bottom ? unitizedPx(bottom) : null};
  margin-left: ${left ? unitizedPx(left) : null};
`;

// define the styling function for the responsive props
const responsiveStyles = generateResponsiveStyles(baseStyles);

export const Root = styled.div` ${baseStyles} // the base styles ${responsiveStyles} // the responsive styles
}
`; 
```

这允许我们将可能的断点从组件样式的实现中分离出来，使其可以灵活地实现到其他组件，并且易于维护，因为媒体查询声明与样式声明是分开管理的。

你可以在[this codes box](https://codesandbox.io/s/responsive-styles-7ju13)中找到代码示例。

快乐编码。☕️