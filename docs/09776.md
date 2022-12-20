# 在您的 Vue 应用中轻松控制访问

> 原文：<https://dev.to/umarov/easy-access-control-in-your-vue-apps-hhp>

随着应用程序的增长和用户的增多，您可能会希望在应用程序的某些部分添加一些访问控制。在构建复杂的应用程序时，这是一个很常见的问题

假设您正在为一家零售店构建一个应用程序，您将拥有不同级别的访问权限。

*   售货员
*   经理

销售人员可以结账，但不能给需要折扣的顾客提供折扣。经理可以进来，验证他们自己，并能够看到用户界面，允许他们给予折扣。

这种类型的用例可能发生在我们为其构建用户界面的任何环境中。因此，提出一个可靠的解决方案将有助于您交付更有效的应用程序。

* * *

## 我们如何在 Vue 中做这样的事情？

### `v-if`

假设我们有一个应用折扣的组件:

```
<customer-discount :order="order" @discount-applied="onDiscountApplied" /> 
```

该组件可以包括一个带有`<button>`的`<input>`字段，以应用折扣。

如果不允许用户提供折扣，一个基本的解决方案可能是添加一个`v-if`检查来隐藏这个组件。

```
<customer-discount
  v-if="user.canApplyDiscount"
  :order="order" 
  @discount-applied="onDiscountApplied" /> 
```

这对于大多数用例来说都很好。如果你已经在遵循这个模式，继续做下去。没毛病。

在任何需要访问控制的地方使用`v-if`可能会变得很麻烦。你最终会在多个地方重复这个逻辑。一旦你想在不同的 Vue 应用之间分享行为，这将变得更糟。为了模块化和可重用性，我们需要做不同的事情。

那么什么是更好的解决方案呢？

### `<slot />`

在 Vue 中，你可以创建一些非常棒的组件。它们允许您使用模板合成要素并将其添加到应用程序中。

在这个例子中，我们将创建一个名为`<access-control />`的组件。该组件可以接受一个角色，用户必须拥有该角色才能看到组件内部的内容。组件内部的内容将基于`<access-control />`内部的逻辑进行切换。

让我们看一下实现:

```
<template>
  <div v-if="hasRole">
    <slot />
  </div>
</template>

<script>
export default {
  props: {
    roles: { type: Array, default: () => [] },
    role: { type: String, default: '' }
  },
  computed: {
    hasRole () {
      return this.roles.length === 0 || this.roles.includes(this.role)
    }
  }
}
</script> 
```

所有的神奇都发生在`hasRole` computed 属性中。一切都由传递给这个组件的`props`驱动。您可以将该组件放在应用程序中的任何位置，并在其他角色中重用。

```
<access-control :roles="['salesperson']" role="manager">
  <customer-discount :order="order" @discount-applied="onDiscountApplied" />
</access-control> 
```

比方说，您现在想要显示一个基于多个角色的组件。您可以重构 props 来接受一组角色作为需求。你可以写`<access-control />`，而不是那样做。

下面是一个允许用户订购库存的组件示例:

```
<access-control :roles="userRoles" role="manager">
  <access-control :roles="userRoles" role="team-lead">
    <order-inventory />
  </access-control>
</access-control> 
```

看起来很酷。一个缺点是重复`:roles="userRoles"`。如果您正在使用 Vuex，您可以使用一个`mapGetter`来获取用户角色。这将清理你的模板，让你通过必要的`role`。

现在，我已经可以听到功能组件爱好者在下面输入关于我如何可以在这里使用功能组件。他们是对的！

### 输入`functional: true`

我用一个功能组件创建了相同的示例。它也变短了。由于是`functional`，Vue 不会为`functional`组件创建新的组件对象。这也使得它更适合为多个角色作曲。

```
<script>
export default {
  functional: true,
  props: {
    roles: { type: Array, default: () => [] },
    role: { type: String, default: '' }
  },
  render (_, { props: { roles, role }, children }) {
    if (roles.length === 0 || roles.includes(role)) return children
  }
}
</script> 
```

信不信由你，这就是全部的成分。您可以导入它，并像使用非功能版本一样使用它。这一个将会是超级轻重量和强大的。

* * *

下面是显示两个版本的 Codesandbox:

[https://codesandbox.io/embed/rmjvxwn56o](https://codesandbox.io/embed/rmjvxwn56o)

* * *

所以给你。一个简单的组件，您可以在任何需要一些访问控制的 Vue 应用程序中使用。它易于使用且重量轻。

我希望你喜欢阅读这篇文章。让我知道你是否做过这样的事情！这是一个相当普遍的问题。我最近偶然发现了它，并在工作中为它创建了一个组件。