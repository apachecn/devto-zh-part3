# MongoDB 聚合管道阶段到救援！

> 原文：<https://dev.to/tushark1/mongodb-aggregation-pipeline-stages-to-the-rescue-11am>

### 假设有一个一对多关系的模式

```
//customer schema
const customerSchema = new mongoose.Schema(
  {
    name: {
      type: String
    },
    email: {
      type: String
    }
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

```
//consumable's schema
const consumableSchema = new mongoose.Schema(
  {
    name: {
      type: String
    },
    email: {
      type: String
    }
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

```
//payments schema
const consumableSchema = new mongoose.Schema(
  {
    amount: {
      type: Number,
      required:true
    },
    paid:{
        type:Number,
        required:true
    }
    customer: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "Customer",
      required: true
    },
    consumable : {
       type: mongoose.Schema.Types.ObjectId,
      ref: "Consumable",
      required: true
    }
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

所以现在，如果你想得到一个客户的付费总额，mongoose 聚合管道是一个很好的有效方法。你得到总和的方法如下

*   匹配客户 id
*   按 id 分组(如果不需要 id，只需使用 null)
*   获取已付金额/金额

```
//query to get the above result
const {customerID} = args;
const result = await Payment.aggregate([
    {
      $match: { customer: customerID }
    },
    {
      $group: {
        _id: null,
        paidTotal: { $sum: "$paid" },
        amountTotal: { $sum: "$amount" }
      }
    }
  ]);
  console.log(result)
  // logs [{ _id: null, amount: 800, paid: 600 }] which is an array of objects. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看消耗品，把消耗品看作食物[汉堡、比萨饼、薯条]...等等]。
您的客户需要每天/每月/每季度/每年销售/消耗的耗材的统计视图。
在这种情况下，查询应该是这样的

```
const { from, to } = args;

  const result = await Payment.aggregate([
    {
      $match: { createdAt: { $gte: from, $lt: to } }
    },
    {
      $lookup: {
        from: "consumables",
        localField: "consumable",
        foreignField: "_id",
        as: "consumable"
      }
    },
    {
      $unwind: "$consumable"
    },
    {
      $replaceRoot: { newRoot: "$consumable" }
    },
    {
      $group: {
        _id: "$_id",
        name: { $first: "$name" },
        count: { $sum: 1 }
      }
    },
    { $sort: { name: 1 } } // can use count for sorting as well
  ]);
  console.log(result);
  //logs 
  [
      { _id: 5ca5c352ccf2241169f9c8ab, name: 'Burger', count: 30 },
      { _id: 5ca5c367ccf2241169f9c8ad, name: 'Fries', count: 24 },
      { _id: 5ca5c361ccf2241169f9c8ac, name: 'Pizza', count: 15 }
  ] 
```

Enter fullscreen mode Exit fullscreen mode

这里我们用 [$lookup(aggregation)](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/) 得到从消耗品到付款的关系(一对多)。

*   这里的“来自”表示我们所指的模式，在这种情况下，我们指的是“**消费模式** *来自* **支付模式**”。
*   本地字段是指我们用来与消耗性模式相关联的字段的名称，它是**支付模式**中的**消耗性**字段
*   外来字段指的是我们试图关联的模式中的字段，它是**可消费模式**，字段是 **_id**
*   而**作为**可以理解为存储关系查询结果的常数。
    *   在这种情况下，查找聚合的结果是一个数组，我们使用 [$unwind(aggregation)](https://docs.mongodb.com/manual/reference/operator/aggregation/unwind/) 解构该数组。
    *   然后我们使用[$ replace root(aggregation)](https://docs.mongodb.com/manual/reference/operator/aggregation/replaceRoot/)将根更改为$ consumable，虽然这不是必需的，但会使查询看起来更漂亮。这将替换查询的根，并将$ consumable 移动到最顶层，并替换所有字段。如果你不想使用$replaceRoot，你可以像这样使用它`name: "$$ROOT.consumable.name"`，看看它与代码`name: {$first:"$name"}`相比如何，看起来很整洁。

总之，您总是可以使用 mongoose 聚合管道阶段来生成统计数据或任何类似的情况。一个例子是基于时间/天/日期/月/季度/年为用户注册生成统计数据。

希望你有耐心读完所有这些:)