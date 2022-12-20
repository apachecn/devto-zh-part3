# ★何时使用 Gate::after in Laravel

> 原文：<https://dev.to/freekmurze/when-to-use-gate-after-in-laravel-22d3>

在 Laravel 应用[中，策略](https://laravel.com/docs/master/authorization)是组织围绕模型的授权逻辑的好方法。

很长时间以来，我一直使用`Gate::before`来允许超级管理员做他们想做的任何事情。在开发一个新的应用程序时，它终于明白了 [`Gate::after`](https://laravel.com/docs/master/authorization#gates) 也很有用。我想在这篇博文中分享这些知识。

本文假设您了解 Laravel 的授权特性，并有这方面的经验。如果没有，[请先阅读相关文档](https://laravel.com/docs/master/authorization)。

## 解释门::前

我们团队正在开发的新应用[在介绍中提到过，叫做](https://spatie.be)[出席。完成后，它将允许活动组织者注册他们的活动、位置和发言人。与会者可以在活动及其发言人的位置上留下反馈。](https://github.com/spatie/attended.io)

让我们看一下策略中处理是否允许用户管理事件的部分。

```
class EventPolicy
{
   use HandlesAuthorization;

   public function administer(User $user, Event $event)
   {
      return $user->organizes($event);
   }
} 
```

使用这个策略，您可以像这样进行授权检查:

```
$user->can('administer', $event); // returns a boolean 
```

这将为组织事件的用户返回`true`，为所有其他用户返回`false`。

在我们的系统中，我们也有超级管理员，他们可以做任何事情。他们不组织活动，但仍应被允许管理活动。这可以通过增加一个`Gate::before` :
来解决

```
// somewhere in a service provider

Gate::before(function ($user, $ability) {
    if ($user->isSuperAdmin()) {
        return true;
    }
}); 
```

传递给`Gate::before`的闭包将在`EventPolicy`(和所有其他策略)被调用之前使用。如果您是超级管理员，即使您没有组织该活动，您现在也可以管理该活动。生活真好！

注意，如果你不是超级管理员，我们不返回`false`，而是`null`(不返回任何内容)。这是故意的。如果我们在这里返回`false`，策略类将不会被检查。只有超级管理员能够管理事件。

## 解释门::后

在我们的 attended.io 应用程序中，登录的用户可以查看某个时间段。每个用户只能查看一个时间段。在时段开始之前，用户不能查看时段。

下面是策略类:

```
class SlotPolicy
{
    use HandlesAuthorization;

    public function review(User $user, Slot $slot)
    {
        if ($user->hasReviewed($slot)) {
            return false;
        }

        if ($slot->starts_at->isFuture()) {
            return false;
        }

        return true;
    }
} 
```

对于非超级管理员来说，这很好。我们所有的规则都受到尊重。但是前面提到的`Gate::before`赋予了我们超级管理员太多的权力。超级管理员现在可以多次查看插槽，并且可以在插槽开始之前查看插槽。让我们解决这个问题！

不要用`Gate::before`，我们用`Gate::after`吧。

```
// somewhere in a service provider

Gate::after(function ($user, $ability) {
   return $user->isSuperAdmin();
}); 
```

有了这个`Gate::after`而不是`Gate::before`，策略将首先被调用，即使是超级管理员。包括超级管理员在内的所有用户现在都无法查看尚未开始的插槽。太好了！

如果你有敏锐的眼光，你可能会注意到，对于对`SlotPolicy`的`review`检查，`Gate::after`没有被调用，因为`SlotPolicy`已经返回了`true`。这是真的，因为没有`Gate::after`这个特殊的检查也会起作用。

但是让我们再看一下上一节的`EventPolicy`。

```
class EventPolicy
{
   use HandlesAuthorization;

   public function administer(User $user, Event $event)
   {
      return $user->organizes($event);
   }
} 
```

目前，超级管理员不允许管理事件，因为`administer`返回一个布尔值。让我们解决这个问题。

```
class EventPolicy
{
  use HandlesAuthorization;

  public function administer(User $user, Event $event)
  {
     if ($user->organizes($event)) {
        return true;
     }
  }
} 
```

现在，我们不会为不组织活动的普通用户返回任何内容。在这种情况下，我们的`Gate::after`回调将被调用，它将为超级管理员返回`true`，为所有其他用户返回`false`。