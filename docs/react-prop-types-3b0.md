# 反应道具类型

> 原文：<https://dev.to/vish448/react-prop-types-3b0>

## PropTypes 同 React

如果我们创建一个接受 props 作为数组的组件，当用户错误地传递一个字符串而不是一个数组时会发生什么？是的，你说得对。它会坏掉的。因此，在这一点上，react 道具类型开始发挥作用。

```
class Users extends React.Component {
  render() {
    return (
      <ul>
        {this.props.list.map(function (friend) {
          return <li>{friend}</li>
        })}
      </ul>
    )
  }
} 
```

想象一下，如果我把 string 作为道具传递给一个组件

T2`<Users list="Jason, Minky, Orio" />`

由于字符串没有映射函数，所以一切都会中断。所以为了避免这个问题，我们可以使用 Proptypes。

PropTypes 允许您声明传递给组件的每个属性的“类型”(字符串、数字、函数等)。然后，如果传入的道具不是声明的类型，您将在控制台得到一个警告。

T2`Users.propTypes = {
list: PropTypes.array.isRequired
}`

为了使用 PropTypes，您需要安装它们。注意:它们以前包含在 React 中，但是从 React 15.5 开始，它们变成了自己的包，可以作为 prop-type 从 npm 下载。

PropTypes 对于查找组件中的 bug 非常有用，但是我最喜欢它们的地方是它们向组件添加文档的能力。当我看到一个写得很好的组件时，我可以通过 render 方法来弄清楚它将会是什么样子，我可以通过它的 propTypes 来弄清楚它需要接受什么才能正确地呈现。

要查看更深入的 API [请点击此处](https://reactjs.org/docs/typechecking-with-proptypes.html)