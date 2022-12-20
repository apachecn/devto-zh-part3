# 它可以做，也可以扩展

> 原文：<https://dev.to/molly/it-will-do-vs-it-will-scale-p38>

很多人想知道如何进行规模化设计。老实说，学习如何为规模编码需要大量的实践。在我看来，一旦你经历了这些痛苦，就很容易做到。当你看到慢代码的缺点后，将来就更容易把它找出来。

在这篇文章中，我想分享一个例子来说明“它会做”代码和“它会扩展”代码之间的区别。

## 查找违规代码

首先也是最重要的，我是如何找到这段未优化的代码的？我们有些工作需要很长时间才能完成，我决定试着找出原因。我获取了一些样本数据，并用`Rails.logger.level = 0`在控制台中运行了这个任务。这是我看到的一个片段。

```
Role Load (0.6ms)  SELECT  `roles`.* FROM `roles` WHERE `roles`.`description` = 'read-only'  ORDER BY description ASC LIMIT 1
Role Load (0.6ms)  SELECT  `roles`.* FROM `roles` WHERE `roles`.`description` = 'basic'  ORDER BY description ASC LIMIT 1
Role Load (0.6ms)  SELECT  `roles`.* FROM `roles` WHERE `roles`.`description` = 'admin'  ORDER BY description ASC LIMIT 1
...
Role Load (0.6ms)  SELECT  `roles`.* FROM `roles` WHERE `roles`.`description` = 'read-only'  ORDER BY description ASC LIMIT 1
Role Load (0.6ms)  SELECT  `roles`.* FROM `roles` WHERE `roles`.`description` = 'basic'  ORDER BY description ASC LIMIT 1
Role Load (0.6ms)  SELECT  `roles`.* FROM `roles` WHERE `roles`.`description` = 'admin'  ORDER BY description ASC LIMIT 1
...
Role Load (0.6ms)  SELECT  `roles`.* FROM `roles` WHERE `roles`.`description` = 'read-only'  ORDER BY description ASC LIMIT 1
Role Load (0.6ms)  SELECT  `roles`.* FROM `roles` WHERE `roles`.`description` = 'basic'  ORDER BY description ASC LIMIT 1
Role Load (0.6ms)  SELECT  `roles`.* FROM `roles` WHERE `roles`.`description` = 'admin'  ORDER BY description ASC LIMIT 1 
```

Enter fullscreen mode Exit fullscreen mode

我们一遍又一遍地寻找这三个相同的角色。我知道要处理数据，我们需要检查用户的角色来确定权限，但是我不知道为什么我们要发出 3 个数据库请求来完成这项工作。我最终追踪 MySQL 请求到这行代码:`Role.system_roles.include?(user.role)`。从那里，我走向角色模型，看看这个方法在做什么。

```
def self.system_roles
  [readonly_role, default_role, admin_role]
end 
```

Enter fullscreen mode Exit fullscreen mode

这三个角色中的每一个都有自己的定义方法。

```
def self.admin_role
  find_by(:description => ADMIN_ROLE)
end

def self.default_role
  find_by(:description => DEFAULT_ROLE)
end

def self.readonly_role
  find_by(:description => READONLY_ROLE)
end 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/bf41a1ac2579119996c9e9f83bcd6fe6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8AsrayQY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6rtz45s2dw0x6rpyfxsq.gif)

现在这一切都完全说得通了。立刻，我对我找到的代码嗤之以鼻。但后来我阻止了自己。与其喋喋不休地抱怨这段代码有多低效，我还不如花些时间去理解为什么一开始要这样写。

## 写代码就行了

原来，第一组单独的角色方法是先编写的。代码中使用了单独的 find 方法来对单个角色进行简单的一次性权限检查。

稍后，当我们第一次通过基于角色的访问控制(RBAC)时，我们实现了`system_roles`方法。我们使用这种方法来检查用户在控制器操作中的访问权限。因为我们只使用它在控制器动作中进行一次查找，所以我们不担心 3 个`0.6ms` MySQL 查询。我们编写了“可以做到”的代码。

最终，我们扩展了 RBAC，以包括控制对来自我们的 Elasticsearch 集群的数据的访问。每次我们向 Elasticsearch 发出请求时，我们都会检查发出请求的用户的角色权限。当实现这个的时候，我(是的，这个在我身上！)在角色模型上找到了`system_roles`方法并使用了它。当时，我没有想到如果我们开始调用这个方法几百万次会发生什么。这是一个我们已经有的简单方法，所以我想我会用它。这一年多来效果很好。

## 为标尺编写代码

今天，当我们从 Elasticsearch 获取数据时，我们仍然使用这个代码。除了现在，我们在并行后台工作器中运行这个代码成百上千次。这 3 个对 MySQL 的单独调用加在一起，为我们的工作增加了不少时间。为了解决这个问题，我更新了`system_roles`方法，使用了一个单独的`where`子句。

```
def self.system_roles
  where(:description => [ADMIN_ROLE, DEFAULT_ROLE, READONLY_ROLE])
end 
```

Enter fullscreen mode Exit fullscreen mode

我们现在只需要发出一个`0.3ms` MySQL 请求，而不是发出 3 个`0.6ms` MySQL 请求。这不仅节省了我们的时间，还节省了我们数据库的额外负载。

## 第一课学到——试着从一开始就编写可扩展的代码。

当你写代码的时候，很容易只考虑当前的用例，然后写“可以”的代码。当你编写代码时，试着超越当前的用例。这个代码在将来会有不同的用法吗？如果是的话，它是表演性的吗？立即尝试编写尽可能高性能的代码，你未来的自己或同事会因此而感谢你。

## 第二课学到了——不要盲目重用代码。

当你添加一个新的特性时，很容易获得一个已经存在的方法或类并使用它。对此感到厌倦吧！该类或方法可能是为完全不同于您计划使用它的目的而编写的。原始代码的作者可能并没有打算按照您将要使用的方式来使用它。确保它在逻辑和性能方面满足您的所有需求。

同样的概念也适用于宝石。有许多优秀的宝石并不是按比例设计的。意识到这一点。如果您打算经常使用 gem，那么就稍微浏览一下源代码。确保它针对您的使用案例进行了优化。当我试图为后台工作人员实现插件时，我不止一次遇到过代码运行缓慢的问题。

当你开始写下一行代码时，问问你自己，这段代码是“可以”的还是“可以伸缩”的？