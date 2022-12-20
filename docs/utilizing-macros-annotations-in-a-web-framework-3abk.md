# 在 Web 框架中使用宏和注释

> 原文：<https://dev.to/blacksmoke16/utilizing-macros-annotations-in-a-web-framework-3abk>

宏，尤其是注释，是 Crystal 中记录最少的特性。正因为如此，我想花点时间写一篇关于我如何使用注释和框架来构建我的 web 框架的小帖子，重点是如何使用它们，而不一定是框架本身。

几个月前，我开始了另一个 web 框架项目。然而，我想采取一种不同的方法，考虑到我从其他兼职项目以及我的工作中获得的经验。这样的结果就是[雅典娜](https://github.com/blacksmoke16/athena)。与其他主要依赖宏来定义路线的框架不同，Athena 使用注释*和*宏。这使得代码可读性很强，路由操作只是类方法。这有几个好处，例如:

*   更容易记录——只是方法，所以 doc 注释工作得很好。
*   更容易测试——只是方法，可以直接调用你的动作来测试。
*   更容易阅读/理解——不需要特殊的语法(除了学习注释)
*   遗产。

注释被大量使用，作为定义路由、回调甚至 CLI 命令的主要机制。我还在我的另一个序列化/验证碎片 [CrSerializer](https://github.com/blacksmoke16/CrSerializer) 中使用了它们，作为管理模型的属性序列化/验证的替代方法。

# 宏

宏被定义为(来自 git 书籍)，“在编译时接收 AST 节点并生成代码粘贴到程序中的方法。”或者换句话说，你写的代码在编译时扩展成晶体代码，并被插入到程序中。宏本身几乎就是一种语言；相当强大灵活。宏允许你通过编写定义类、方法等的代码来减少样板文件。

# 注解

注释，Crystal 中最少记载但最强大的特性之一。简而言之，批注允许类型(类、结构、方法和属性)添加信息，这些信息在编译时可用，并可通过宏读取。

注释的定义类似于类或结构，但是使用了`annotation`关键字。

```
annotation Get; end 
```

Enter fullscreen mode Exit fullscreen mode

我将定义一些我想在开始创建雅典娜时解决的场景。然后回顾一下我的想法，以及我是如何使用宏和注释来解决这些问题的。

1.  开发一种基于特定注释动态注册路线的方法，而不是使用标准的`get "/path" do { }`宏。
2.  通过对每个属性应用注释，而不是使用`validate xxx`宏，能够容易地验证 ORM 模型上的属性。
3.  类似于 1，但是具有自动向`OptionParser`接口公开的自注册 CLI 命令。

## 登记路线

为了解决这第一个障碍，我必须思考。"我怎样才能得到一组定义了路线的控制器？"

我的理想解决方案应该是这样的:

```
@[Athena::Routing::Controller]
class MyController
  @[Athena::Routing::Get(path: "/me")]
  def get_me : String
    "Jim"
  end

  @[Athena::Routing::Post(path: "/user")]
  def new_me(body : String) : String
    body
  end
end

{% for controller in Athena:::Routing::Controller.types %}
  # Iterate over all the classes with the `Controller` annotation
{% end %} 
```

Enter fullscreen mode Exit fullscreen mode

因此，任何具有`Athena:::Routing::Controller`的类都将被认为是控制器，类方法将被注释，以将给定的路由绑定到当路由匹配时应该执行的代码。然而，这目前是不可能的。参见 [#7274](https://github.com/crystal-lang/crystal/issues/7274) 。所以我不得不想出 b 计划。

Crystal 生成的 API 文档在这方面非常有用，尤其是 [TypeNode](https://crystal-lang.org/api/0.27.2/Crystal/Macros/TypeNode.html) 。一个`TypeNode`代表一个程序中的类型，比如`String`，或者`MyController`等。通读可用的方法，我注意到有一个`all_subclasses`方法；它返回该类型的所有子类的数组。啊哈！然后我有了一个想法，类似于 Rails 和他们的`ApplicationController`，我可以创建自己的类，让控制器继承它，并使用`all_subclasses`在编译时迭代所有控制器。太好了！

这一发现导致了今天使用的语法:

```
class MyController < Athena::Routing::Controller
  @[Athena::Routing::Get(path: "/me")]
  def get_me : String
    "Jim"
  end

  @[Athena::Routing::Post(path: "/user")]
  def new_me(body : String) : String
    body
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然而，有一个新的问题。我在哪里做这个？将我的注意力转向各种可用的路由器，例如[琥珀色路由器](https://github.com/amberframework/amber-router)。在示例中，每台路由器都要求用户定义一个树，向其中添加路由，并在同一个文件中找到这些路由。这里的关键问题是我需要一种方法，这样*用户*就不需要*知道*如何做这些事情。我需要一种方法在幕后隐藏树的创建、路由的添加和路由的解析。

我回到了 Crystal API 文档，特别是`HTTP::Handler`类。当包含这个模块时，它需要一个类来定义一个`call`方法，这个方法将在每个 HTTP 请求上执行。此外，由于它只不过是一个类，我想我可以添加一个`initialize`方法，在为`HTTP::Server`创建处理程序时运行一次。

完美！一个简化的版本最终看起来像:

```
class RouteHandler < Athena::Routing::Handlers::Handler
  @routes : Amber::Router::RouteSet(Action) = Amber::Router::RouteSet(Action).new

  def initialize
    {% for c in Athena::Routing::Controller.all_subclasses %}
      {% methods = c.class.methods.select { |m| m.annotation(Get) || m.annotation(Post) || m.annotation(Put) || m.annotation(Delete) } %}
      {% for m in methods %}
        {% if d = m.annotation(Get) %}
          {% method = "GET" %}
          {% route_def = d %}
        {% elsif d = m.annotation(Post) %}
          {% method = "POST" %}
          {% route_def = d %}
        {% end %}

        # Action is a record that contains data about the action.
        # Such as params, the action to execute, path, controller etc. 
        {% action = xxx %}

        @routes.add {{route_def[:path]}}, {{action}}
      {% end %}
    {% end %}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我首先定义了一个实例变量`routes`，它被实例化为我的路由器树的一个新实例。然后我定义了一个`initialize`方法。在这个方法中，我使用了一个宏循环来遍历 controller 基类的子类。然后，我使用`.select`获得在每个控制器上定义的一系列动作，特别是寻找用一个 HTTP 动词注释进行了注释的类方法。这允许在一个类中使用私有类方法，但不期望与路由相关。然后，我根据匹配的注释设置方法和 route_definition。最后，我向路由器注册了路由。

可以使用`[]`方法访问注释字段，对于命名字段使用`String`或`Symbol`，对于基于索引的字段使用`Int32`。

因为这些都是宏代码，所以在编译时添加到程序中的实际代码看起来应该是:

```
class RouteHandler < Athena::Routing::Handlers::Handler
  @routes : Amber::Router::RouteSet(Action) = Amber::Router::RouteSet(Action).new

  def initialize
    @routes.add "/GET/me", RouteAction.new("get_me", xxx)
    @routes.add "/POST/user", RouteAction.new("new_user", xxx)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

酷！现在我可以定义`call`方法来处理路由。同样，一个简化的版本看起来像这样:

```
def call(ctx : HTTP::Server::Context)
  search_key = '/' + ctx.request.method + ctx.request.path
  route = @routes.find search_key

  if route.found?
    action = route.payload.not_nil!
  else
    raise Athena::Routing::Exceptions::NotFoundException.new "No route found for '#{ctx.request.method}  #{ctx.request.path}'"
  end

  call_next ctx
end 
```

Enter fullscreen mode Exit fullscreen mode

这将调用我为 CORS 定义的其他处理程序，并执行与给定路由相关的实际操作。然而，这并不涉及注释/宏的任何其他显著用途，所以我将跳过它。然而，如果你有兴趣了解更多关于雅典娜是如何设置的，请随时在水晶 Gitter 上给我发消息。

这一部分现在已经完成，可以类似于:

```
server = HTTP::Server.new([RouteHandler.new])

server.bind_tcp "127.0.0.1", 8080
server.listen 
```

Enter fullscreen mode Exit fullscreen mode

## 属性验证

我想解决的下一个挑战是验证，也遵循基于注释的方法。想法是你可以给一个属性添加注释，然后在一个 POST 端点上运行这些验证。如果验证通过，将模型的一个实例传递给路由操作，否则返回 400。

我想在`JSON::Serializable`的基础上构建它，这样任何使用它作为序列化/反序列化方法的类/结构的验证都可以开箱即用。

虽然听起来很简单。“只需创建一些验证注释，添加一个`validate`方法来迭代这些注释，就像您对路线所做的那样，然后就可以了。”然而，事情并没有那么简单:P

最初我想到的语法是:

```
@[CrSerializer::Assertions(greater_than_or_equal: 0, nil: false)] 
property age : Int32? 
```

Enter fullscreen mode Exit fullscreen mode

然后，我可以从注释中读取字段，并运行相应的逻辑来运行断言。

然而，很快就发现这种方法有一些缺陷。

*   如果有很多断言，这一行很快就会变长。
*   没有简单的方法来强制必填字段，例如必须提供`min`和`max`。
*   它不可扩展。如果用户想要添加他们自己的断言，我不可能知道他们将定义的键，更不用说用它们做什么了。

所以经过一些思考后，我转而支持这个语法:

```
@[Assert::NotNil] 
@[Assert::GreaterThanOrEqual(value: 0)] 
property age : Int32? 
```

Enter fullscreen mode Exit fullscreen mode

这样做的好处是:

*   更容易阅读，因为行不会变得太长
*   更具可扩展性
*   更容易看到相关字段

当然，这种方法存在一些挑战。目前还没有办法将一组注释应用于一个类型。每个注释都必须按其名称阅读。我现在需要一种方法来知道每个断言注释的名称、从中读取哪些字段，以及一种将给定的注释绑定到某些特定逻辑的方法。

我通过知道常量在编译时可用来解决前两个问题。我定义了一个常量 hash，将注释名映射到一个字段数组，可以从中读取。

```
ASSERTIONS = {
  Assert::Email              => [:mode],
  Assert::IP                 => [:version],
  Assert::Uuid               => [:versions, :variants, :strict],
  Assert::Url                => [:protocols, :relative_protocol],
  Assert::RegexMatch         => [:pattern, :match],
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

因为常量在编译时可用，所以可以在宏循环中使用它们；我这样做是为了迭代每个断言的名称/字段。

```
{% for ivar in @type.instance_vars %}
  {% for t, v in CrSerializer::Assertions::ASSERTIONS %}
    {% ann = ivar.annotation(t.resolve) %}
    {% if ann %}
      assertions << {{t.resolve.name.split("::").last.id}}Assertion({{ivar.type.id}}).new({{ivar.stringify}},{{ann[:message]}},{{ivar.id}},{{v.select { |fi| ann[fi] != nil }.map { |f| %(#{f.id}: #{ann[f]}) }.join(',').id}})
    {% end %}
  {% end %}
{% end %} 
```

Enter fullscreen mode Exit fullscreen mode

这个例子引入了一个新的宏概念`@type`。在宏中使用时，`@type`以`TypeNode`的形式表示当前的作用域或类型。在我的例子中，`@type`将代表包含`CrSerializer`的类。我使用了`@type.instance_vars`来迭代每个实例变量，从中读取注释，并添加断言(如果有的话)。我还使用它将每个实例变量的类型和名称添加到断言实例化中。

**注意:** `@type.instance_vars`目前只在实例/类方法的上下文中起作用。参见 [#7504](https://github.com/crystal-lang/crystal/issues/7504) 。

现在我已经有了处理前两个问题的方法，我仍然需要一种方法来将一个给定的断言绑定到特定于该断言的逻辑上。为此，我使用了`ASSERTIONS`散列的键，加上`Assertion`作为类名来实例化该类型的新类。例如，`Assert::NotNil`的逻辑将在一个名为`NotNilAssertion`的类中处理；看起来像:

```
class NotNilAssertion(ActualValueType) < Assertion
  @message : String = "'{{field}}' should not be null"

  def initialize(field : String, message : String?, @actual : ActualValueType)
    super field, message
  end

  def valid? : Bool
    @actual.nil? == false
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

每个断言类都有实例变量，这些变量从`ASSERTIONS`散列映射到数组中的字段。我使用一个宏来遍历这些字段，找到已设置的字段，然后用这些值创建一个断言实例。我将它添加到一个`validate`方法中，当包含`CrSerializer`模块时，该方法会被添加到类型中。同样，由于这都是宏代码，基于上面的例子，插入到程序中的实际代码应该是这样的:

```
def validate : Nil
  assertions = [] of CrSerializer::Assertions::Assertion
  assertions << NotNilAssertion(Int32?).new
  assertions << GreaterThanOrEqualAssertion(Int32?).new(value: 0)
  @validator = CrSerializer::Validator.new assertions
end 
```

Enter fullscreen mode Exit fullscreen mode

但是，等等，如果`ASSERTIONS`是一个常量，这对于允许用户添加他们自己的断言有什么帮助呢？

使用一个宏，我能够用给定的名称定义注释，并在编译时向散列添加属性，这样散列就包含了标准断言以及用户在运行时定义的任何断言。

```
macro register_assertion(name, fields)
  module CrSerializer::Assertions
    annotation {{name.id}}; end
    {% CrSerializer::Assertions::ASSERTIONS[name] = fields %}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

用户可以像使用`register_assertion Assert::MyCustom, [:value, :high_is_good]`一样使用它，当然也有一个`MyCustomAssertion`类来处理逻辑。

```
@[Assert::MyCustom(value: 100, high_is_good: true)]
property age : Int32 
```

Enter fullscreen mode Exit fullscreen mode

## 自动注册 CLI 命令

这与重新声明路由问题非常相似，但是我将把它作为宏功能的另一个例子。

```
struct Registry
  macro finished
    class_getter commands : Array(Athena::Cli::Command.class) = {{Athena::Cli::Command.subclasses}}{% if Athena::Cli::Command.subclasses.size > 0 %} of Athena::Cli::Command.class {% end %}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这用从父类`Command`继承的类数组的值定义了一个类 getter。数组的值是在编译时通过简单地调用`{{Athena::Cli::Command.subclasses}}`构建的，它返回一个类数组。这必须在一个`finished`宏中完成，这样类型是已知的，以防止类型不匹配。

然后我使用了一个宏来插入一个`OptionParser`，作为列出/运行命令
的接口

```
macro register_commands
  OptionParser.parse! do |parser|
    parser.banner = "Usage: YOUR_BINARY [arguments]"
    parser.on("-h", "--help", "Show this help") { puts parser; exit }
    parser.on("-l", "--list", "List available commands") { puts Athena::Cli::Registry.to_s; exit }
    parser.on("-c NAME", "--command=NAME", "Run a command with the given name") do |name|
      Athena::Cli::Registry.find(name).command.call ARGV
      exit
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
struct MigrateEventsCommand < Athena::Cli::Command
  self.command_name = "migrate:events"
  self.description = "Migrates legacy events for a given customer"

  def self.execute(customer_id : Int32, event_ids : Array(Int64)) : Nil
    # Do stuff for the migration
    # Params are converted from strings to their expected types
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

使用这些命令所要做的就是在你的主应用程序文件中调用`register_commands`宏，并从基本命令结构中创建你的命令结构。这将被`Athena::Cli::Command.subclasses`获取并在编译时注册。

最终它可以像`./MyApp -c migrate:events --customer_id=83726 --event_ids=1,2,3,4,5`一样被使用。它将在注册表中搜索该命令，如果定义了该命令，则将参数传递给它。

# 附录

我真的不知道要涵盖什么，因为很难突然给出例子。我将把这一部分留给将来的补充。如果你想看某个具体的例子或进一步的解释，请随时提问。

## 蠕虫

@jwoertink 让我想到了另一个例子，我可以通过这个例子来回顾“我使用它们的原因”我一直在做[花岗岩](https://github.com/amberframework/granite)的注释版本，主要是为了 lols，我可以给出一些例子。

与简单地使用普通的宏相比，注释需要不同的观点来看待如何构造应用程序。我们都知道用于定义列的宏语法 ORM，即`field name : String`。在幕后，宏将[添加字段](https://github.com/amberframework/granite/blob/master/src/granite/fields.cr#L16)的名称、类型和任何提供给常量的选项，以便在[后期阶段](https://github.com/amberframework/granite/blob/master/src/granite/fields.cr#L32)，一旦宏`inherited`和`finished`挂钩运行，常量可以用于为那些字段创建属性，将适当的类型/选项应用到属性定义。

**开始个人观点**

> 然而，在我看来，这有点复杂，这是一个很好的例子，说明了什么类型的情况注释是有用的。重点是，使用`field name : String`比使用`property name : String`有什么优势？后者允许你像应用程序中的其他类一样拥有语法，继承工作，可以记录属性，并且可以很容易地应用第三方注释(比如 JSON::Field)。注释将允许您删除这些额外的不必要的步骤，从宏到常量到属性，而不是直接到属性。

**结束个人观点**

然而，当采用基于注释的方法时，您必须从不同的角度来看待它。在 ORM 的情况下，主要的挑战变成了“我如何传递列数据，而不把所有东西都添加到一个可变的常量中？”我的解决方案是使用`records`并在编译时构建一个不可变的对象数组来表示模型中的列。这些对象将包含诸如列名、类型、是否为 PK、是否可空等数据。type、name、nullable 都可以直接来自一个实例变量`TypeNode`而不涉及注释。当您想要将非自动生成的数据附加到列(也称为属性)时，注释就发挥作用了。注释将允许在其上定义字段/值，这也可以在编译时构建记录数组时读取。

```
 private record ColumnInfo(T) < ColumnBase, name : String, nilable : Bool, auto : Bool = false, primary : Bool = false, default : T? = nil, type : T.class = T

    protected def self.columns : Array(ColumnBase)
      columns = [] of ColumnBase
      {% begin %}
        {% fields = @type.instance_vars.select { |ivar| ivar.annotation(Granite::Column) } %}
        {% raise "Composite primary keys are not yet supported." if fields.select { |ivar| ann = ivar.annotation(Granite::Column); ann && ann[:primary] }.size > 1 %}
        {% for field in fields %}
          {% type = field.type.union? ? field.type.union_types.reject { |t| t == Nil }.first : field.type %}
          {% col_ann = field.annotation(Granite::Column) %}
          {% auto = col_ann && col_ann[:auto] ? col_ann[:auto] : false %}
          {% primary = col_ann && col_ann[:primary] ? col_ann[:primary] : false %}
          {% auto = col_ann[:auto] == nil && primary %}
          {% raise "Primary key '#{field.name}' of '#{@type.name}' must be nilable." if primary && !field.type.nilable? %}
          columns << ColumnInfo({{type}}).new({{field.stringify}}, {{field.type.nilable?}}, {{auto}}, {{primary}}, {{field.default_value}})
        {% end %}
      {% end %}
      columns
    end 
```

Enter fullscreen mode Exit fullscreen mode

这是我想到的。与构建路由类似，我遍历当前类的实例变量，找到带有`Granite::Column`注释的变量，然后检查各个注释字段，看它们是否被设置，否则使用默认值。如果用户添加了两个主键，或者有一个不可空的主键，就会产生编译时错误。

这个模型可能是什么样子的一个例子。

```
 @[Granite::Model(table: "parents", adapter: "my_db")]
  class Parent < Granite::Base
    @[Granite::Column(primary: true)]
    property id : Int64?

    @[Granite::Column]
    property name : String

    @[Granite::Column(name: "createdAt")]
    property created_at : Time?

    @[Granite::Column(name: "updatedAt")]
    property updated_at : Time?

    # A property that would be read from a request body but _not_ persisted to DB.
    property meta : String
  end 
```

Enter fullscreen mode Exit fullscreen mode

IMHO，干净得多，不依赖于特定的宏语法。

希望这也有帮助。