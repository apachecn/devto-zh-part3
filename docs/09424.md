# 在 React 中用 MAP()遍历数组

> 原文：<https://dev.to/vish448/loop-through-arrays-with-map-in-react-44ee>

map()方法创建一个新数组，其结果是对调用数组中的每个元素调用一个提供的函数。

React 文档强烈鼓励使用地图功能。不仅仅是因为它的简单，还因为它从数据中创建了一个新的数组，而不是试图改变/覆盖现有的数据。使用 react 应用程序，您将有更多的机会以 JSON 的形式从 API 收集数据，并根据您的需求获取数据。Map()是遍历这些数据并获取所需内容的最佳方式。

# 我们来举个例子

```
 let cart = [
{id: 1, itemName: 'cap',price:10 },
{id: 2, itemName: 'socks',price:20 },
{id: 3, itemName: 'hoodie',price:30 }
] 
```

在我们的 App 类构造函数中，我们将创建 this.cartItems 并将其分配给 this.state.cart(这是我们存储添加到购物车中的商品的地方)。然后，我们使用 ES2015 map 函数遍历 this.state.cart 中的每个项目。

我们用它传递两个参数。

1.  item:single`item`inside out this . state . cart
2.  key: React 使用来帮助其渲染器跟踪每个单独的项目。

```
 this.cartItems = this.cart.map((item, key) => 
    *   {item.itemName}
) 
```

我们想在代码中显示 cartItems，我们简单地称它为 via

 `我们可以在渲染方法中调用它，而不是从构造函数中调用它

```
 render() {
    const cartItems = this.state.cart.map((item,key) => 
        *   {item.itemName}
    ) 
```

我们可以称之为

众所周知，在 render()方法中这样调用并不是一个好主意，但是对于相当简单的应用程序，这样做应该没问题。

[https://codesandbox.io/embed/v0m1rm2mk7](https://codesandbox.io/embed/v0m1rm2mk7)`