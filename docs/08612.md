# 用 AssertJ 测试嵌套结构

> 原文：<https://dev.to/gossie/testing-nested-structures-with-assertj-33mn>

在之前的一篇文章中，我描述了为什么我喜欢用 [AssertJ](http://joel-costigliola.github.io/assertj/) 编写测试。在这篇文章中，我想描述一个很好的模式，它有助于用 [AssertJ](http://joel-costigliola.github.io/assertj/) 测试嵌套结构。假设有一个容器类，由一个名称和一组叶子对象组成。

```
public class Container {

    private String name;
    private List<Leaf> leaves = new ArrayList<>();

    public Container(String name, List<Leaf> leaves) {
        this.name = name;
        this.leaves.addAll(leaves);
    }

    public String getName() {
        return name;
    }

    public List<Leaf> getLeaves() {
        return Collections.unmodifiableList(leaves);
    }
} 
```

叶类由名称和描述组成。

```
public class Leaf {

    private String name;
    private String description;

    public Leaf(String name, String description) {
        this.name = name;
        this.description = description;
    }

    public String getName() {
        return name;
    }

    public String getDescription() {
        return description;
    }
} 
```

对于这两个类，AbstractAssert 的实现都可以很容易地实现，看起来可能是这样的。

```
public class ContainerAssert extends AbstractAssert<ContainerAssert, Container> {

    private ContainerAssert(Container actual) {
        super(actual, ContainerAssert.class);
    }

    public static ContainerAssert assertThat(Container actual) {
        return new ContainerAssert(actual);
    }

    public ContainerAssert hasName(String name) {
        Assertions.assertThat(actual.getName()).isEqualTo(name);
        return this;
    }
} 
```

```
public class LeafAssert extends AbstractAssert<LeafAssert, Leaf> {

    private LeafAssert(Leaf actual) {
        super(actual, LeafAssert.class);
    }

    public static LeafAssert assertThat(Leaf actual) {
        return new LeafAssert(actual);
    }

    public LeafAssert hasName(String name) {
        Assertions.assertThat(actual.getName()).isEqualTo(name);
        return this;
    }

    public LeafAssert hasDescription(String description) {
        Assertions.assertThat(actual.getDescription()).isEqualTo(description);
        return this;
    }
} 
```

编写测试的最简单方法应该是这样的。

```
public class ContainerTest {

    @Test
    public void test() throws Exception {
        Container container = classUnderTest.produceContainer();

        assertThat(container).hasName("containerName");
        assertThat(container.getLeaf(0)).hasName("leaf1").hasDescription("description1");
        assertThat(container.getLeaf(1)).hasName("leaf2").hasDescription("description2");
    }
} 
```

问题是，这种方法并不受益于 [AssertJ](http://joel-costigliola.github.io/assertj/) 的流畅界面。
在 ContainerAssert 和 LeafAssert 之间来回跳转的可能性会很好。为了实现这一点，ContainerAssert 由一个 leaf 方法扩展，该方法返回特定叶的 LeafAssert。

```
public class ContainerAssert extends AbstractAssert<ContainerAssert, Container> {

    private ContainerAssert(Container actual) {
        super(actual, ContainerAssert.class);
    }

    public static ContainerAssert assertThat(Container actual) {
        return new ContainerAssert(actual);
    }

    public ContainerAssert hasName(String name) {
        Assertions.assertThat(actual.getName()).isEqualTo(name);
        return this;
    }

    public LeafAssert leaf(int index) {
        return LeafAssert.assertThat(actual.getLeaves(index), actual);
    }
} 
```

LeafAssert 还获得了一个新方法。parentContainer 方法跳回到对应于该叶的父节点的 ContainerAssert。

```
public class LeafAssert extends AbstractAssert<LeafAssert, Leaf> {

    private Container currentContainer;

    private LeafAssert(Leaf actual, Container currentContainer) {
        super(actual, LeafAssert.class);
        this.currentContainer = currentContainer;
    }

    public static LeafAssert assertThat(Leaf actual) {
        return new LeafAssert(actual, null);
    }

    public static LeafAssert assertThat(Leaf actual, Container currentContainer) {
        return new LeafAssert(actual, currentContainer);
    }

    public LeafAssert hasName(String name) {
        Assertions.assertThat(actual.getName()).isEqualTo(name);
        return this;
    }

    public LeafAssert hasDescription(String description) {
        Assertions.assertThat(actual.getDescription()).isEqualTo(description);
        return this;
    }

    public ContainerAssert parentContainer() {
        return ContainerAssert.assertThat(currentContainer);
    }
} 
```

最终的测试将如下所示。

```
public class ContainerTest {

    @Test
    public void test() throws Exception {
        Container container = classUnderTest.produceContainer();

        assertThat(container)
            .hasName("containerName")
            .leaf(0)
            .hasName("leaf1")
            .hasDescription("description1")
            .parentContainer()
            .leaf(1)
            .hasName("leaf2")
            .hasDescription("description2");
    }
} 
```

我认为这种方法的问题是，LeafAssert 类必须将 currentContainer 作为内部状态来处理。此外，不容易看出哪个断言在哪个实例上执行。

在我看来，这个问题可以通过将一个[消费者](https://docs.oracle.com/javase/8/docs/api/java/util/function/Consumer.html)传递给 ContainerAssert 的叶子方法来很好地解决。[消费者](https://docs.oracle.com/javase/8/docs/api/java/util/function/Consumer.html)接受用请求的叶子初始化的叶子断言。

```
public class ContainerAssert extends AbstractAssert<ContainerAssert, Container> {

    private ContainerAssert(Container actual) {
        super(actual, ContainerAssert.class);
    }

    public static ContainerAssert assertThat(Container actual) {
        return new ContainerAssert(actual);
    }

    public ContainerAssert hasName(String name) {
        Assertions.assertThat(actual.getName()).isEqualTo(name);
        return this;
    }

    public ContainerAssert leaf(int index, Consumer<LeafAssert> consumer) {
        consumer.accept(LeafAssert.assertThat(actual.getLeaves(index)));
        return this;
    }
} 
```

这意味着 LeafAssert 类不需要任何额外的方法。

```
public class LeafAssert extends AbstractAssert<LeafAssert, Leaf> {

    private LeafAssert(Leaf actual) {
        super(actual, LeafAssert.class);
    }

    public static LeafAssert assertThat(Leaf actual) {
        return new LeafAssert(actual);
    }

    public LeafAssert hasName(String name) {
        Assertions.assertThat(actual.getName()).isEqualTo(name);
        return this;
    }

    public LeafAssert hasDescription(String description) {
        Assertions.assertThat(actual.getDescription()).isEqualTo(description);
        return this;
    }
} 
```

在测试类中，特定叶子的断言可以作为 lambda 表达式传递给 lead 方法。

```
public class ContainerTest {

    @Test
    public void test() throws Exception {
        Container container = classUnderTest.produceContainer();

        assertThat(container)
            .hasName("containerName")
            .leaf(0, leaf -> leaf
                .hasName("leaf1")
                .hasDescription("description1"))
            .leaf(1, leaf -> leaf
                .hasName("leaf2")
                .hasDescription("description2")));
    }
} 
```

这样，在 ContainerAssert 和 LeafAssert 类中就不需要额外的状态处理。缩进使得很容易看到断言是在嵌套结构的哪一层上执行的。