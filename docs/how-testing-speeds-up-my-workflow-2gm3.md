# 测试如何加快我的工作流程

> 原文：<https://dev.to/theringleman/how-testing-speeds-up-my-workflow-2gm3>

所以相对来说，我对编码世界还比较陌生。我成为专业工程师已经三年多了。令人高兴的是，我最近接受了一个中级工程师的新职位，耶！通过担任一个新的职位，我学到了很多东西，在我心目中最突出的项目之一是测试。

在前一份工作中，我们认为测试会让我们慢下来。有些人强烈反对测试。我听到的最突出的论点是测试花费了太多的时间。编写测试需要时间投入。运行测试需要时间投入。维护测试需要时间投入。虽然这些论点有一定道理，但你确实花时间设置了一切。我反驳说，时间投入很少。通过测试获得的好处远远超过前期投入的时间。我已经通过在拉韦尔测试我的模型关系亲身经历了这一点。

我之前测试关系的工作流程有点笨拙。我会首先进行数据库迁移，在我们的情况下，这是对测试数据库的直接 SQL 查询。然后进入模型并创建关系(hasOne、hasMany 等)。然后，为了确保建立正确的关系，我编写了一个测试控制器。这个控制器有一个测试端点，哦，让我们不要忘记测试路线。测试控制器的操作非常简单，我不需要用户的输入，但是我需要一个模型 ID。如果该 id 存在于数据库中，我跳转到数据库为每个模型找到一个 ID，并将它们加入控制器动作...如果没有 ID，我创建一个模型并获取那个 ID。然后，在我设置好测试控制器动作之后，我就可以开始处理这个请求了。看看我的测试动作。嗯，我一定是在模特身上搞砸了什么。进入错误跟踪模式。我在模型中跳来跳去，查看 db 模式，回到 routes 文件，哦，糟糕，在我的测试操作中有一个错误。解决小问题，然后回到邮递员，耶，它的工作。现在，在重要的代码中完成连接关系。然后删除测试控制器和路线，然后继续我的快乐之路。这是一个缓慢而笨拙的工作流程，非常容易出错。

[![An annoyed Meowth](img/6858f3de5d4bb560afbe0ca6154b89bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bNC22yz1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.kym-cdn.com/photimg/newsfeed/001/267/182/58c.png)

快进到新工作，新线索。我们做了大量的测试、单元、集成、特性，甚至 UAT！

我的新工作流程是这样的。我设计了我的模式并编写了一个迁移。然后我为我想要的模型之间的关系写一个测试。然后，我进入模型，对关系进行编码。我做了测试。就是这样。

[![Looney Toons that's all folks](img/08dc67115534fb6367dad4b4545ed006.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2PM8p0hy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/e/ea/Thats_all_folks.svg/1019px-Thats_all_folks.svg.png)

很烂的帖子吧？不，让我们一起来看一个例子。在这个例子中，我使用了 Lumen。所有的代码都在这里，所以如果你想跟着做，安装一个新的 Lumen 项目，让我们制作一些吉他！

从模型模式和迁移开始。在您的项目目录中运行这两个命令:

```
php artisan create:migration create_brands_table
php artisan create:migration create_guitars_table 
```

这是我们迁移的样子。

```
class CreateBrandsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('brands', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('brands');
    }
} 
```

```
class CreateGuitarsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('guitars', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('name');
            $table->string('type');
            $table->unsignedBigInteger('brand_id');
            $table->foreign('brand_id')->references('id')->on('brands');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('guitars');
    }
} 
```

两个非常简单的模型，我们在这里设计的是一对多的关系。一把吉他有一个品牌，一个品牌可以有很多把吉他。

让我们现在创建我们的模型。我们的`Brand`模型看起来是这样的:

```
class Brand extends Model
{
    /**
     * @var string
     */
    protected $table = 'brands';
    /**
     * @var array
     */
    protected $fillable = ['name'];
} 
```

而我们的`Guitar`模型看起来是这样的:

```
class Guitar extends Model
{
    /**
     * @var string
     */
    protected $table = 'guitars';
    /**
     * @var array
     */
    protected $fillable = ['name', 'type'];
} 
```

同样，没什么复杂的，但现在我们进入有趣的部分。我喜欢在写出逻辑之前先写测试。所以让我们从编写测试开始，让`Guitar`有一个`Brand`。

在您的测试目录中创建文件`GuitarTest`。并添加首测`it_should_have_one_brand` :

```
class GuitarTest extends TestCase
{
    /**
     * @test
     */
    public function it_should_have_one_brand()
    {

    }
} 
```

我们需要一个吉他实例来运行我们的测试。在测试雄辩模型时，将它们与数据库结合使用是有意义的。因此，让我们为`Guitar`和`Brand`型号创建一个工厂。

在我们的`ModelFactory`文件中插入这个:

```
$factory->define(App\Models\Guitar::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'type' => $faker->randomElement(['acoustic', 'electric']),
    ];
});

$factory->define(App\Models\Brand::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
    ];
}); 
```

太好了！现在我们可以开始测试了！因此，跳回到我们之前开始的`GuitarTest`，让我们创建我们的模型。

```
 public function it_should_have_one_brand()
    {
        $guitar = factory(\App\Models\Guitar::class)->create();
        $brand = factory(\App\Models\Brand::class)->create();
    } 
```

如果您在这里运行您的测试，您将得到一个 SQL 错误。

```
Illuminate\Database\QueryException : SQLSTATE[HY000]: General error: 1364 Field 'brand_id' doesn't have a default value (SQL: insert into `guitars` (`name`, `type`, `updated_at`, `created_at`) values (Jaydon Daugherty, acoustic, 2019-05-23 22:59:12, 2019-05-23 22:59:12)) 
```

哦，事实证明，如果没有一个`Brand`，我们就无法在此时创建一个`Guitar`。这是一个好消息，我们知道我们的模式是可行的。这里有几个选项可供使用。我们可以实现一个工厂国家，这将为我们创造一个`Brand`。我们可以改变模式，使外键可为空。或者我们不能创建模型并实例化它。

让我们选择后者，不要管我们的模式。

工厂中的`create`方法创建您的模型，并将其持久化到数据库中。`make`方法实例化它并用数据填充它，让您去做持久化。我们得到 SQL 错误的原因是因为 create 方法。那时，我们试图保存模型，但没有给我们创建的外键赋值。我们也没有指定它是可空的。对我来说，在这个过程中有`Guitar`没有`Brand`是没有意义的。所以我们得到一个错误而没有提供一个值。

那我们该怎么办？这:

```
 public function it_should_have_one_brand()
    {
        $guitar = factory(\App\Models\Guitar::class)->make();
        $brand = factory(\App\Models\Brand::class)->create();
        $guitar->brand()->associate($brand);
        $guitar->save();
    } 
```

请注意，我们将`Guitar`工厂呼叫从`create`更改为`make`。

现在，为了完成测试，添加一个断言来确保我们有正确的数据，这是一个很好的测试！

```
 public function it_should_have_one_brand()
    {
        $guitar = factory(\App\Models\Guitar::class)->make();
        $brand = factory(\App\Models\Brand::class)->create();
        $guitar->brand()->associate($brand);
        $guitar->save();
        $this->assertInstanceOf(\App\Models\Brand::class, $guitar->brand);
    } 
```

现在运行您的测试，您应该得到一个错误。

```
BadMethodCallException : Call to undefined method App\Models\Guitar::brand() 
```

这很有道理，我们还没有实现关系。就这么办吧。回到吉他模型，添加:

```
 /**
     * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
     */
    public function brand()
    {
        return $this->belongsTo(Brand::class);
    } 
```

再次进行测试。你现在应该全绿了！

如果我们想确定吉他确实拯救了同一个模型，我们可以更进一步。注意这是可选的，这个测试本身就很好。

完成的测试如下所示:

```
 /**
     * @test
     */
    public function it_should_have_one_brand()
    {
        $guitar = factory(\App\Models\Guitar::class)->make();
        $brand = factory(\App\Models\Brand::class)->create();
        $guitar->brand()->associate($brand);
        $guitar->save();
        $this->assertInstanceOf(\App\Models\Brand::class, $guitar->brand);
        $this->assertEquals($brand->id, $guitar->brand->id);
    } 
```

太好了，所以我们有我们的关系测试，对不对？不对。我们测试了双向关系中的一种方式。让我们把我们的品牌模型和他们的吉他模型联系起来。

在您的测试目录中创建 BrandsTest，并编写第一个测试。

```
class BrandsTest extends TestCase
{
    /**
     * @test
     */
    public function it_should_have_many_guitars()
    {

    }
} 
```

记住我们这里是一对多的关系，所以一个品牌可以有很多吉他。这就是我们要测试的。让我们制作我们的模型:

```
 public function it_should_have_many_guitars()
    {
        $brand = factory(\App\Models\Brand::class)->create();
        $guitars = factory(\App\Models\Guitar::class, 5)->make();
    } 
```

然后，我们需要将它们关联起来，并根据关系运行我们的断言。所以我们最终的测试将会是这样的:

```
 public function it_should_have_many_guitars()
    {
        $brand = factory(\App\Models\Brand::class)->create();
        $guitars = factory(\App\Models\Guitar::class, 5)->make();
        $brand->guitars()->saveMany($guitars);
        $this->assertCount(5, $brand->guitars);
        $this->assertContainsOnlyInstancesOf(\App\Models\Guitar::class, $brand->guitars);
    } 
```

现在，当我们运行这个测试时，像上次一样，我们将得到错误:

```
BadMethodCallException : Call to undefined method App\Models\Brand::guitars() 
```

同样，这非常有意义，我们还没有在我们的模型中实现这个逻辑。将`guitars`方法添加到您的`Brand`模型中。

```
 /**
     * @return \Illuminate\Database\Eloquent\Relations\HasMany
     */
    public function guitars()
    {
        return $this->hasMany(Guitar::class);
    } 
```

现在运行您的测试，如果您遵循了一切，您应该有两个通过的测试！

这些测试对我来说价值连城。您的应用程序将会增长并变得更加复杂。坚持添加您的工厂，然后测试关系。你有测试来确保事情永远不会失败。一旦你进入一个良好的流程，花 10 分钟建立一个工厂，然后建立你的测试和关系。一旦测试通过，就可以围绕这些关系编写任何逻辑了。

当你建立更复杂的关系时，你会感谢你自己。多态的多对多关系很难掌握，但很容易测试。然而，测试与上述相同。现在您知道已经正确地设置了数据库模式。您可以放心地知道，您编写的代码，并将在周五投入生产，至少是经过测试的。😉