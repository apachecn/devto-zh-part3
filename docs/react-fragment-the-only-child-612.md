# 做出反应。片段，独生子女

> 原文：<https://dev.to/nicolasamabile/react-fragment-the-only-child-612>

这是一篇关于我在 ReactJS 中构建向导组件时遇到的一些问题的短文。

*   使用`React.cloneElement`时不能引用“falsy”子级。
*   `React.Fragment`返回单个子代。

开始时，我的向导实例看起来像这样:

```
<Wizard>
  <Step1 />
  <Step2 />
  <Step3 />
  <Step4 />
  <Step5 />
</Wizard> 
```

在幕后，组件将只呈现当前步骤。

```
render () {
  const { children } = this.props
  const { activeStep } = this.state
  const extraProps = {...} // Some extra info I need on each step.
  return (
    …
    {React.cloneElement(children[activeStep], extraProps)}
    …
  )
} 
```

基于一些业务规则，我想隐藏/显示一些步骤，所以我的向导实例看起来像这样:

```
renderStep2 () {
  if (conditionForStep2) {
    return <Step2 />
  }
}
render () {
  return ( 
    <Wizard>
      <Step1 />
      {this.renderStep2()}
      <Step3 />
      {conditionForStep4 && <Step4 />}
      <Step5 />
    </Wizard>
  )
} 
```

这些表达式对于`Step2`评估为未定义，对于`Step4`评估为假，并且当执行`React.cloneElement(children[activeStep], extraProps)`时，这些值中的任何一个都可以用作有效的子代，其中`activeStep`是`Step2`或`Step4`的索引，React 将会抱怨😩我的指数也会是错的。
[![React error. Element type is invalid](img/19461245ab01b2a4401b0dd2fe59744e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VgD-pdR1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rolxf46uxm08o11snwpp.png)

我没有直接使用孩子，而是创建了一个只返回“真实”步骤的函数:

```
const getChildren = children => children.filter(child => !!child)
And change my Wizard render function to something like this:
render () {
 const { children } = this.props
 const { activeStep } = this.state
 const filteredChildren = getChildren(children)
 return (
   …
   {React.cloneElement(filteredChildren[activeStep], extraProps)}
   …
 )
} 
```

第一个问题解决了🎉

为了简化我的逻辑，我想将一些步骤分组。举个例子，我需要使用相同的条件来渲染`Step3`、`Step4`和`Step5`，所以我把它们组合成一个`React.Fragment`。

```
renderMoreSteps () {
  if (condition) {
    return (
      <Fragment>
        <Step3 />
        <Step4 />
        <Step5 />
      </Fragment>
    )
  }
} 
```

我的向导实例:

```
<Wizard>
  <Step1 />
  <Step2 />
  {this.renderMoreSteps()}
</Wizard> 
```

问题是:尽管 Fragment 没有被表示为 DOM 元素，但它返回的是单个子组件，而不是单个子组件。
解决方法:让孩子变平。

```
import { isFragment } from 'react-is'
const flattenChildren = children => {
  const result = []
  children.map(child => {
    if (isFragment(child)) {
      result.push(…flattenChildren(child.props.children))
    } else {
      result.push(child)
    }
  })
  return result
} 
```

更新了 getChildren 函数:

```
const getChildren = children => flattenChildren(children).filter(child => !!child && !isEmpty(child)) 
```

为了简单起见，我使用了 [react-is](https://www.npmjs.com/package/react-is) ，但是实现非常简单:

```
function isFragment (object) {
  return typeOf(object) === REACT_FRAGMENT_TYPE
}
const REACT_FRAGMENT_TYPE = hasSymbol
  ? Symbol.for('react.fragment')
  : 0xeacb;
const hasSymbol = typeof Symbol === 'function' && Symbol.for; 
```

希望这有帮助！欢迎所有的评论。