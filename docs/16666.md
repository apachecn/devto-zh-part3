# React 没有官方的风格指南

> 原文：<https://dev.to/metamn/there-is-no-official-style-guide-for-react-c5f>

我们显然很怀念它。

## 样式指南

与编程语言相关的风格指南提高了代码质量和代码重用。

当代码遵循一个风格指南时，它很容易被其他遵循相同风格指南的人理解和扩展。

通常，风格指南包含行业最佳实践。毫无疑问，它为用户提供了真正的解脱，因此被许多人采用。

不幸的是，React 没有脸书提供的官方风格指南。这个角色是 AirBnb 自愿接的，值得质疑。

只是一些片段:

*   用`.jsx`代替`.js`作为文件扩展名——一个带有*疑问*的建议，违背了直觉，至少对我个人来说是这样的。
*   将参数作为列表而不是数组传递，这违背了[松耦合](https://alistapart.com/article/coding-with-clarity#section3)的通用编码原则。

```
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
} 
```

总之，我现在不使用任何样式指南。我宁愿等待官方的风格指南出来。

并不是说 AirBnb 的 styleguide 有错。事实是连 React 本身都不坚持。

## 更新

*   Dan Abramov 解释了为什么 React 没有官方的风格指南。

## 资源

*   [AirBnb 风格指南与最佳实践建议相矛盾](https://github.com/airbnb/javascript#destructuring)
*   [清晰编码](https://alistapart.com/article/coding-with-clarity#section3)