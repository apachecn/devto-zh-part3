# 战争故事:难以捉摸的服务

> 原文：<https://dev.to/martinhaeusler/war-stories-the-elusive-service-5605>

如果你喜欢“战争故事”,那些在开发人员中讲述的故事，他们遇到了非常模糊的问题，在最终找到解决方案之前感觉快要疯了——继续读下去，这是其中之一。

# 从前...

我在 Spring Boot 应用程序中编写了一个新的 REST 端点——我以前已经做过上百次了。可能会出什么问题。因为我最近真的很喜欢用 Kotlin 编程，所以我决定尝试一下。这是控制器的样子:

```
@RestController
open class MyRestController{

    @Autowired
    private lateinit var service: MyService

    @ResponseBody
    @RequestMapping(method = HttpMethod.GET, path = "/yadda-yadda")
    fun getSomeData(): MyDTO{
        val data = this.service.produceData()
        return MyDTO(data)
    }

} 
```

这是一个简单的 REST 端点——它从服务中获取一些数据，将其包装在 DTO 中，并作为响应发送出去。这里甚至没有涉及数据库事务，这是最基本的。

不管怎样，我决定为它写一个小测试:

```
class MyTest {

   @Test
   fun canGetDataViaREST(){
       val mvc = ... // spring mock MVC setup, not important here
       val objectMapper = ... // Jackson setup, not important here
       val responseJson = mvc.perform(
           get("/yadda-yadda")
       ).andExpect(status().isOk)
        .andReturn().response.bodyAsString
       val dto = objectMapper.readValue<MyDTO>(responseJson)
       assertNotNull(dto)
   }

} 
```

所以测试相当简单:它使用 Spring [MockMVC](https://spring.io/guides/gs/testing-web/) 来模拟对我们新端点的 REST 调用，以 JSON 的形式获取结果，并用 [Jackson](https://github.com/FasterXML/jackson) 对象映射器反序列化它。那里没有什么新奇的东西，一点也不像*。这是[弹簧座 101](https://dev.to/martinhaeusler/java-enterprise-101-building-a-rest-server-with-spring-boot-m7k) 。然而，果然，测试**失败了**。*

 *# 失败

测试失败。不像您预期的那样，没有出现断言错误。或者一些 JSON 序列化或反序列化问题，不。这是一个 **NullPointerException** 。奇怪的是，这些东西在科特林很难生产。这是由异常指示的行:

```
val data = this.service.produceData() // NullPointerException: lateinit var 'service' is not initialized! 
```

太奇怪了。**非常**古怪。失败通常意味着缺少一颗春豆，或者其他类似的事情。然而，正如你们当中的 Spring 用户所知道的，如果 Spring 无法设置某些东西，它就会失败，并且它会很大声地*失败*，你会得到大约 7 公里长的堆栈跟踪。不，在这种特殊情况下不会。春天说一切都好。然而我的`@Autowired`没用。

你能指出我哪里出错了吗？我没有。我花了很长时间寻找原因。

# 下兔子洞

被这种行为吓了一跳，我开始调查。我检查了所有相关类的所有注释，检查了配置，一切似乎都很好。我在项目中有几十个类似的场景，它们都完美地工作着。然而，这次失败的原因令人费解。

然后我开始怀疑 Kotlin 是罪魁祸首，因为它是这个案例(失败)和所有其他案例(成功)的主要区别。我查看了生成的字节码，并将其反编译成 Java，稍微清理了一下，得到了这样的结果:

```
@RestController
public class MyRestController{

    @Autowired
    private MyService service;

    @ResponseBody
    @RequestMapping(method = HttpMethod.GET, path = "/yadda-yadda")
    public final MyDTO getSomeData() {
        Data data = this.service.produceData();
        return new MyDTO(data);
    }

} 
```

这看起来像我到目前为止写的其他 1000 个 rest 端点。然而，即使在 Java 中，当试图访问`@Autowired`服务字段时，错误仍然存在:`NullPointerException`。*神圣的狗屎。*

我终于启动了调试器，并在 REST 控制器中创建了一个`@PostConstruct`块:

```
@PostConstruct
private void init(){
    System.out.println("Test!");
} 
```

我在`println`语句上放了一个断点。果然，在执行测试时达到了。我检查了`this`对象，您瞧，`service`字段被填充了一个值！正如我所希望的那样，服务被分配了。一切似乎都很好。我继续执行死刑。测试**失败**。精神错乱的定义是一遍又一遍地做同样的事情，希望得到不同的结果...

我在产生异常的那一行插入了一个额外的断点。`this.service`已在 init 方法中正确填充...但是场地是在`getSomeData()`的`null`。此时我不得不去喝杯咖啡。

当我回来再次运行时，我注意到了一些事情:在第二个断点处，`this`引用了与第一个不同的对象 id。但更重要的是:它也指的是另一个**级** : `MyRestController$$_EnhancerByCGLIB_$$`。

简单地说:Spring 不仅广泛使用 JDK 动态代理来完成它的工作，而且在运行时使用 [CGLib](https://github.com/cglib/cglib) 给**子类**你的类，生成字节码并动态插入更多的代码行。我和一位同事谈论这个问题，当时我突然意识到了问题的根源。

# 解

仔细看看科特林生成了什么:

```
public final MyDTO getSomeData() { ... } 
```

再看看。看到了吗？`final`。按照设计，科特林隐式地将所有东西声明为`final`，除非你将其声明为`open`。这与 Java 正好相反，在 Java 中一切都是可重写的，除非声明为`final`。以前和 Hibernate 这样的框架一起工作过，我知道`final`和 CGLib 不能很好地合作- *根本不能*。

所以我去掉了`final`修饰符。

测试成功了。

# 到底他妈的***发生了什么？

Spring 生成了一个`MyRestController`的子类。这个子类是实际使用的子类。默认情况下，Spring 覆盖所有 REST 端点方法。除了在这种情况下，它不能这样做，因为有了`final`修饰符。结果是`getSomeData()`在*基类*上被调用(我手写的那个)，但是`@Autowired`字段值只在 *sub* 类中可用(是的，这些字段也被 Spring/CGLib 复制)。这就是为什么(非`final` ) `init`方法可以看到字段值，而`getSomeData()`不能。

# 吸取教训

这里有一些启示:

1)将 Kotlin 与 Spring 配合使用时要格外小心。将万物及其祖母声明为`open`。如果在使用 Kotlin 时有疑问，请查看生成的 Java 代码。
虽然春天带来了很多问题，但一些意想不到的恶作剧可能会毫无征兆地溜走。
4)事情可以变得再简单不过了，如果不是*测试过*，就是**要**在某个时候坏掉。没有例外。编写代码时，即使是在日常工作中，也要保持警惕，不要过于自信。一件你做对了 100 次的事情，可能在第 101 次尝试时就出错了。或者 103 团。但最终会的。

另外，我仍然*从根本上*不同意 Kotlin 中`open`关键字的设计决策。这只是另一个例子，说明为什么这是一个坏主意。

如果一个 Spring 开发人员碰巧读到这个:*如果一个方法由于某种原因不能被 CGLib 覆盖，请*打印一个大大的警告(在这种情况下，原因是`final`关键字)。

希望这能帮助其他人不要重复我的错误。我无意冒犯 Spring、Kotlin 或任何其他提到的技术——我喜欢和他们一起工作。只是有时候，他们不太认同。然后这样的事情就发生了。

我希望你喜欢阅读！*