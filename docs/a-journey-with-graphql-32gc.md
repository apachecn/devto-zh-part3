# GraphQL 之旅

> 原文：<https://dev.to/doylecodes/a-journey-with-graphql-32gc>

## GraphQ 什么？

在我开始使用 GraphQL 之前，我在许多帖子和播客上听说过它，都在描述它有多棒。我从来没有真正明白这是怎么回事，但我知道人们对此感到兴奋。那么，我为什么要感兴趣呢？

在我们深入了解什么是 GraphQL 之前，看一下传统的 REST API 可能是有益的。我曾经为一个关于 freecodecamp.com 的小项目制作了一个锻炼 API。它有一个简单的前端，允许您创建一个用户，添加一个练习，并根据用户 id 查找用户和练习。下面是请求检索用户的代码:

```
app.get('/api/exercise/log/:user', async (req, res) => {
  const user = await User.findById(req.params.user).populate('exercises'); 

  const to  = req.query.to || moment().format(); // set to current day if no entry
  const from  = req.query.from || moment(0).format(); // set to 1970 if no from
  const totalExercise = user.exercises.length;
  let exercises;

  if (req.query.limit) {
    exercises = await Exercise.find({ userId: req.params.user }, '_id description duration date')
      .where('date')
      .gte(from)
      .lte(to)
      .limit(parseInt(req.query.limit))
  } else {
    exercises = await Exercise.find({ userId: req.params.user }, '_id description duration date')
      .where('date')
      .gte(from)
      .lte(to)
  }

  const response = {
    userId: user._id,
    username: user.username,
    totalExercises: totalExercise,
    exercises: exercises
  }
  res.json(response)
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在看着这个让我好痛苦！当我第一次写这个的时候，我基本上需要能够获得一个用户，但是也能够传递可选的查询参数，这些参数允许结果在数量上或日期上受到限制。我通过检查查询参数的请求，然后构建一个新的对象来返回。有用吗？当然可以。问题是这真的不是可伸缩性的最佳途径。(我的意思是，当我在 Strava 上查看我的个人资料时，我假设这不是正在发生的事情。)

查询的困难在于如何更容易地过滤和/或获取返回的定制信息。当然，你可以创建一个用户，然后总是返回所有东西，但是你也不希望这样。最理想的情况是，你只想返回客户真正需要的东西，这样他们的设备就不会做所有繁重的工作。这就是 GraphQL 让生活变得简单的地方。

### 伟大，那么是什么？！

GraphQL 基本上是一种新的查询语言。它允许您编写如下所示的查询:

```
{
  query GET_USERS {
    users(name: "Ryan") { 
      id
      runs
      hikes
      totalExercises
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面这个名为“GET_USERS”的查询正在运行一个名为“USERS()”的方法/解析器(我稍后会讲到)，它接受一个名称。如果我传入名字“Ryan ”,我将得到所有名为 Ryan 的用户，以及他们的用户 id、跑步、远足和总运动量。如果我想知道所有叫简的人的行踪呢？我能做的:

```
{
  query JANE_RUNS {
    users(name: "Jane") { 
      id
      runs
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将为我提供名为 Jane 的用户以及他们的 id 和所有的跑步记录。好像挺棒的！GraphQL 也有*突变*你可以用类似的方式编写和编辑对数据库的修改。那么，这些是如何工作的呢？[你可以通过阅读我在 Prisma 上的下一篇文章找到答案！](https://dev.to/ryanmdoyle/graphql-basics-part-1---prisma-the-why-45h0) ~~(哪个...你必须回来，因为它还不存在。)~~

## 目标

我的目标是创建一个简短的系列帖子，分享我对 GraphQL、Prisma 和 Apollo 的了解。部分是为了分享我学到的东西，但也是因为作为一名教师，我知道真正学到东西的最好方法之一是尝试把它教给别人。它暴露了你真正不理解的东西。如果你偶然看到这篇文章或以后的文章，看到一些可怕的错误，请随意插话，这样我们可以一起了解更多！如果你对我如何开始学习感兴趣，我通过参加由韦斯·博斯提供的高级反应课程学到了很多。我还深入研究了大量的文档，但功不可没。他有很多很棒的内容。

## 文章不再可用