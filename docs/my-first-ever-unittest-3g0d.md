# 我的第一个单元测试

> 原文：<https://dev.to/bassemmohamed/my-first-ever-unittest-3g0d>

我认为自己已经很晚了。在我职业生涯的第二年，我的第一次单元测试是在几天前。最近，我加入了后端团队，他们有一个测试覆盖政策。因为这个政策，每个从事某项任务的开发人员都必须做单元测试来覆盖他/她的任务。我一直喜欢让测试像朋友一样增加你对工作的信心并帮助你识别错误的想法🐞快速使您的工作变得更简单、更好。总之，我在后端团队的第一个任务是公开一个简单的 GET URL。我们使用 spring 作为开发框架，使用 Mockito 作为模仿框架，使用 JUnit 进行测试。在这篇文章中，我想和你分享我的第一次测试。

# 什么是单元测试？🤔

根据维基百科，它是“一种软件测试方法，通过这种方法，对源代码的单个单元、一个或多个计算机程序模块的集合以及相关的控制数据、使用程序和操作程序进行测试，以确定它们是否适合使用”

为了对您的应用程序进行单元测试，您必须遵循拥有可测试的小单元模块的设计原则。如果您要测试应用程序的一个小组件。它需要与所有其他组件隔离。这就是莫奇托的用武之地。这就是你必须模仿和抽象任何外部依赖的地方。我认为单元测试遵循分而治之的方法:

1.  通过模仿外部依赖来隔离您的代码
2.  考虑每一种可能的情况
3.  为这个特殊的单元编写你的测试，覆盖每一个场景
4.  这样做，直到应用程序中的每个单元都被测试过

# 我的超级简单又超级牛逼的任务。😎

这是我在春天的第一个任务。我选择了我能得到的最简单的一个。下面的代码是一个从服务层检索资源的控制器，根据响应，控制器将使用 200 或 404 响应代码进行响应。

```
@RequestMapping(method = RequestMethod.GET)
public ResponseEntity<List<Resource>> getAllResource() {
    List<Resource> resourceList = resourceService.getAllResource();

    if (resourceList.size() > 0) {
        return new ResponseEntity<List<Resource>>(resourceList, null, HttpStatus.OK);
    }
    return new ResponseEntity<List<Resource>>(null, null, HttpStatus.NOT_FOUND);
} 
```

很简单，不是吗？仔细想想，上面代码中的所有逻辑都可以归结为一个 If 条件。这正是我们要测试的。

# 让我们为这个令人敬畏的任务编写测试。😎

好了，让我们按照上面的步骤来:

1.  隔离:在这种情况下，我们需要通过模拟对服务层的调用来隔离控制器。这样，我们将周期划分为单元，然后独立测试每个单元。在这篇文章中，我只是测试控制器单元。
2.  涵盖每种情况:在这种情况下，我们有两个，要么我们有东西要归还，要么我们没有。如果模拟服务层返回的资源列表不为空，那么输出应该是 200。如果不是，那么输出应该是 404。
3.  编写涵盖每个场景的测试:

### 要么服务层返回一个非空列表，然后控制器会返回一个 200 状态码

```
@Test
public void testGetAllResourceSuccess() {
    List<Resource> resourceList = new ArrayList<Resource>();
    resourceList.add(new Resource());

    // Here we mock the service layer function and set the response to a non-empty list.
    Mockito.when(resourceServiceImpl.getAllResource()).thenReturn(resourceList);
    try {
        // Then we expect the response to be a 200
        mockMvc.perform(get(“/resource")).andExpect(status().isOk());
    } catch (Exception e) {}
} 
```

### 或者服务层返回一个空的列表，那么控制器会返回一个 404 状态码

```
@Test
public void testGetAllResourceFail() {
    List<Resource> resourceList = new ArrayList<Resource>();
    // Here we mock the service layer function and set the response to an empty list.
    Mockito.when(resourceServiceImpl.getAllResource()).thenReturn(resourceList);
    try {
        // Then we expect the response to be a 404
        mockMvc.perform(get("/resource")).andExpect(status().isNotFound());
    } catch (Exception e) {}
} 
```

看起来很容易，简单明了，不是吗？单元测试就是这样。起初，我觉得这不可能。我觉得这太简单了，我永远不会真的失败。然后经过一番研究，一切都有了意义。你实际上不必在一个测试用例中测试整个周期。在每一个测试用例中，你覆盖了一个小单元。在任何地方都这样做意味着每一部分都正常工作。这就是为什么涵盖项目中的每个单元非常重要。

**快乐编码🔥🔥**