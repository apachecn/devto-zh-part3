# 带口袋妖怪的 Mongo 模式

> 原文：<https://dev.to/bgschiller/mongo-schemas-with-pokemon-5adm>

好吧，我们试着组织我们的口袋妖怪。如果你愿意，可以建立我们自己的小口袋。我们将使用 Mongodb，因为我们必须是网络规模的；谁*知道*会有多少个口袋妖怪(在第一个 151 个之后我就不记得了)。

但我们不会把口袋妖怪扔进蒙哥的垃圾堆！我说我们正试图组织口袋妖怪。我们需要一个计划。幸运的是，我们使用的是 MongoDB 版，它支持 JSON 模式。

每一步的代码都可以在[BG Schiller/pokemon-mongo-schemer](https://github.com/bgschiller/pokemon-mongo-schemer)中的提交序列中找到。查看[提交列表](https://github.com/bgschiller/pokemon-mongo-schemer/commits/master)来跳来跳去。

让我们开始吧。我在看 [pokemon.json](https://raw.githubusercontent.com/bgschiller/pokemon-mongo-schemer/master/pokemon.json) ，是根据[ATL-WDI-演习/mongo-pokemon](https://github.com/ATL-WDI-Exercises/mongo-pokemon) 改编的。我添加了一些错误，以便我们可以对我们的模式进行压力测试。我们不想让加里通过添加一些违反规则的口袋妖怪来欺骗我们！

这是我们开始的模式。

```
{
  type: "object",
  properties: {
    element: {
      type: "array",
      items: {
        type: "string",
        enum: [
          // probably there are more, but maybe we've only seen
          // the starters so far!
          "Grass",
          "Poison",
          "Fire",
          "Water",
        ],
      },
    },
    stats: {
      type: "object",
    },
    misc: {
      type: "object",
      properties: {
        sex_ratios: {
          type: "object",
        },
        classification: { type: "string" },
        // and some other properties...
      },
      additionalProperties: true,
    },
  },
  // we'll turn this off this later to make our schema more strict.
  // for now, it lets us get away with loading a partial schema.
  additionalProperties: true,
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们从一个口袋妖怪开始尝试一下。我们将使用下面的节点脚本。

```
const { MongoClient } = require('mongodb');
const pokes = require('./pokemon.json');
const schema = require('./schema.json');

(async function () {
  const client = await MongoClient.connect('mongodb://localhost:27017');
  const db = client.db('pokedex');
  db.createCollection(
    'pokemon',
    { validator: {
        $jsonSchema: schema,
      },
    });
  const pokemon = db.collection('pokemon');
  try {
    console.log('gonna insert the first pokemon');
    const response = await pokemon.insertOne(pokes[0]);
  } catch (e) {
    console.error(e);
  }
  client.close();
}()); 
```

Enter fullscreen mode Exit fullscreen mode

好的，目前为止还不错。现在我们有一点信心，我们的模式至少没有崩溃。我们可以检查妙蛙种子确实是使用`mongo` shell 添加的。

### 好文件成功；糟糕的医生才是真正的考验

我们的模式做了什么吗？让我们试着添加一些无意义的东西，以确保。

```
// ...
 const response = await pokemon.insertOne({
   name: "Norberta",
   element: [
     "Flying",
     "Norwegian Ridge-back",
   ],
   stats: "no thanks",
 });
// ... 
```

Enter fullscreen mode Exit fullscreen mode

好的，如果您的设置工作正常，应该会给您一条错误消息:“文档验证失败”。Sooo，我们的图式是(也许？)工作。但是 Mongo 并没有明确说明问题是什么。

幸运的是，因为 JSON Schema 是一个标准，我们可以使用另一个工具来告诉我们哪里出了问题。有一个很棒的模式验证器叫做 [ajv](https://github.com/epoberezkin/ajv) 。使用命令行界面说明了我们的问题:

```
$ ajv -s schema.json -d norberta.json --errors=text --all-errors
norberta.json invalid
data.element[0] should be equal to one of the allowed values
data.element[1] should be equal to one of the allowed values
data.stats should be object 
```

Enter fullscreen mode Exit fullscreen mode

好吧！那更有用一点。如果我们能在验证失败时从 mongo 得到类似的错误，那就太好了。我们需要:

1.  捕捉`insert`、`insertMany`、`update`和`updateMany`操作中出现的错误。
2.  从集合中提取架构。
3.  将一些 mongo 特有的模式条目转换成`ajv`会理解的东西(比如`bsonType`、`ObjectID`、`date`)。
4.  找出哪个文档没有通过验证(在`*Many`案例中)。
5.  对于更新，合成如果操作成功的话*将被创建的文档*。

### 蒙哥-阴谋家进场，舞台右侧

实际上，所有的艰苦工作都已经完成了！在 [devetry](https://devetry.com/) 时，我们制作并开源了一个库来做这件事: [mongo-schemer](https://github.com/devetry/mongo-schemer) 。让我们把它添加到我们的脚本中。

```
const MongoSchemer = require('mongo-schemer');
// ...
const db = MongoSchemer.explainSchemaErrors(
  client.db('pokedex'), {
    onError: (errors) => console.error(errors),
  });
// ... 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们再次运行我们的 Norberta 脚本。这一次，它报告错误:

```
[ { keyword: 'enum',
    dataPath: '.element[0]',
    schemaPath: '#/properties/element/items/enum',
    params: { allowedValues: [Array] },
    message: 'should be equal to one of the allowed values' },
  { keyword: 'enum',
    dataPath: '.element[1]',
    schemaPath: '#/properties/element/items/enum',
    params: { allowedValues: [Array] },
    message: 'should be equal to one of the allowed values' },
  { keyword: 'type',
    dataPath: '.stats',
    schemaPath: '#/properties/stats/type',
    params: { type: 'object' },
    message: 'should be object' } ] 
```

Enter fullscreen mode Exit fullscreen mode

### 更严格的模式:统计

我们现在确信模式确实在保护我们的集合。让我们把它弄得更严格一点。从那个`stats`属性开始怎么样。stat 是一个介于 0 和 255 之间的数字。我们可以在模式中定义一个“Stat”来表示这个意思。每个口袋妖怪都应该有一个用于`hp`、`attack`、`defense`、`spattack`、`spdefense`和`speed`。

```
// ...
  definitions: {
    Stat: {
      type: "number",
      minimum: 0,
      maximum: 255,
    },
// ...
  stats: {
    type: "object",
    properties: {
      hp: { $ref: "#/definitions/Stat" },
      attack: { $ref: "#/definitions/Stat" },
      defense: { $ref: "#/definitions/Stat" },
      spattack: { $ref: "#/definitions/Stat" },
      spdefense: { $ref: "#/definitions/Stat" },
      speed: { $ref: "#/definitions/Stat" },
    },
    additionalProperties: false,
  },
// ... 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行这个的时候...它崩溃了！也许将来不会。现在，在 3.6 版本中，我得到“MongoError: $jsonSchema 关键字‘definitions’目前不受支持”。哦，那太糟糕了。

但是并没有失去一切！这次，一个名为 [json-schema-ref-parser](https://github.com/BigstickCarpet/json-schema-ref-parser) 的包拯救了我们。正如它所说的那样:获取对定义的任何`$ref`引用并*去引用它们——在每个被使用的地方内联定义。

```
const $RefParser = require('json-schema-ref-parser');
// ...
const inlinedSchema = await $RefParser.dereference(schema);
delete inlinedSchema.definitions;
db.createCollection(
  'pokemon',
  { validator: {
      $jsonSchema: inlinedSchema,
    },
  });
// ... 
```

Enter fullscreen mode Exit fullscreen mode

这还差不多！现在，如果我们运行我们的脚本，我们应该得到一些错误，如“stats.attack 应为 number”。果然，看看 pokemon.json 中的妙蛙种子:他的一些统计数据是假装成数字的字符串。如果我们解决了这些问题，错误就会消失。

我们可以继续使我们的模式更具描述性。例如，除了我们在这里列出的四个元素之外，还有更多元素(口袋妖怪类型),我们可以记录下`moves`对象的结构。我将把这些留给读者作为练习。

MongoDB 对 JSON 模式的支持是有用的，但有两个大漏洞:它不支持模式中的`definitions`,当出现问题时，它不会告诉你是什么。希望这些能在未来的版本中加入，但是 [mongo-schemer](https://github.com/devetry/mongo-schemer) 和 [json-schema-ref-parser](https://github.com/BigstickCarpet/json-schema-ref-parser) 同时填补了这些空白。