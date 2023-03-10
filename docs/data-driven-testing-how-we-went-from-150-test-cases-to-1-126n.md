# 数据驱动测试——我们如何从 150 多个测试用例减少到 1 个

> 原文：<https://dev.to/asamolion/data-driven-testing-how-we-went-from-150-test-cases-to-1-126n>

这是我的最爱之一。

## 问题

找到测试 150 多个函数的有效方法。

这发生在我致力于增强 Open edX 平台的时候。目前，Open edX 使用自定义的[事件跟踪库](https://github.com/edx/event-tracking)来跟踪用户生成的事件。这些事件是简单的 JSON 对象，它们被输入到 [Open edX Insights](https://edx.readthedocs.io/projects/edx-insights/en/latest/) 应用中。

Open edX 中每个用户生成的事件都通过一系列处理器传递，这些处理器基本上都是 Python 类或函数。事件经过每个处理器(向事件添加特定数据)，最后经过路由器，将事件记录到日志文件中。然而，目前还没有标准化的事件模式。一个事件可以包含任何任意数据，insights 应用程序可以按照它想要的方式处理它。这不是准确和实时分析的理想场景。

我们被指派将这些日志标准化为一致的格式，即 [IMS Global](https://www.imsglobal.org/) 的[井径仪分析规范](https://www.imsglobal.org/sites/default/files/caliper/v1p1/caliper-spec-v1p1/caliper-spec-v1p1.html)。这样，我们将能够对日志进行实时分析，并为每个事件整合一个固定的结构。

例如，这里有一个由 Open edX
生成的事件

```
{  "accept_language":  "en-US,en;q=0.9",  "agent":  "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36",  "context":  {  "course_id":  "",  "org_id":  "",  "path":  "/api/bookmarks/v1/bookmarks/",  "user_id":  17  },  "event":  {  "bookmark_id":  "ali,block-v1:edX+DemoX+Demo_Course+type@vertical+block@vertical_0270f6de40fc",  "component_type":  "vertical",  "component_usage_id":  "block-v1:edX+DemoX+Demo_Course+type@vertical+block@vertical_0270f6de40fc",  "course_id":  "course-v1:edX+DemoX+Demo_Course"  },  "event_source":  "server",  "event_type":  "edx.bookmark.added",  "host":  "9c680a5d44c8",  "ip":  "172.18.0.1",  "name":  "edx.bookmark.added",  "page":  null,  "referer":  "http://localhost:18000/courses/course-v1:edX+DemoX+Demo_Course/courseware/d8a6192ade314473a78242dfeedfbf5b/edx_introduction/1?activate_block_id=block-v1%3AedX%2BDemoX%2BDemo_Course%2Btype%40vertical%2Bblock%40vertical_0270f6de40fc",  "session":  "bf5ee12841d50f244b738e79ada5d1fc",  "time":  "2018-12-07T14:24:26.143437+00:00",  "username":  "ali"  } 
```

这是卡尺兼容格式的同一事件

```
{  "@context":  "http://purl.imsglobal.org/ctx/caliper/v1p1",  "action":  "Bookmarked",  "actor":  {  "id":  "http://localhost:18000/u/ali",  "name":  "ali",  "type":  "Person"  },  "eventTime":  "2018-12-07T14:24:26.143Z",  "extensions":  {  "extra_fields":  {  "accept_language":  "en-US,en;q=0.9",  "agent":  "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.106 Safari/537.36",  "event_source":  "server",  "event_type":  "edx.bookmark.added",  "host":  "9c680a5d44c8",  "ip":  "172.18.0.1",  "org_id":  "",  "page":  null,  "path":  "/api/bookmarks/v1/bookmarks/",  "session":  "bf5ee12841d50f244b738e79ada5d1fc",  "user_id":  17  }  },  "id":  "urn:uuid:d4618c23-d612-4709-8d9a-478d87808067",  "object":  {  "id":  "http://localhost:18000/courses/course-v1:edX+DemoX+Demo_Course/courseware/d8a6192ade314473a78242dfeedfbf5b/edx_introduction/1?activate_block_id=block-v1%3AedX%2BDemoX%2BDemo_Course%2Btype%40vertical%2Bblock%40vertical_0270f6de40fc",  "type":  "Page",  "extensions":  {  "course_id":  "course-v1:edX+DemoX+Demo_Course",  "bookmark_id":  "ali,block-v1:edX+DemoX+Demo_Course+type@vertical+block@vertical_0270f6de40fc",  "component_type":  "vertical",  "component_usage_id":  "block-v1:edX+DemoX+Demo_Course+type@vertical+block@vertical_0270f6de40fc"  }  },  "referrer":  {  "id":  "http://localhost:18000/courses/course-v1:edX+DemoX+Demo_Course/courseware/d8a6192ade314473a78242dfeedfbf5b/edx_introduction/1?activate_block_id=block-v1%3AedX%2BDemoX%2BDemo_Course%2Btype%40vertical%2Bblock%40vertical_0270f6de40fc",  "type":  "WebPage"  },  "type":  "AnnotationEvent"  } 
```

这只是我们必须转换的大约 150 个事件中的一个，我们必须为每个事件编写单独的函数。好吧，所以写一个转换函数不是问题。真正的挑战是如何有效地测试所有的变压器。

## 解

一种方法是将测试的责任加到开发人员身上，这样，选择一个事件进行转换的人也将为它编写测试用例。

由于我们使用 Python，每个开发人员必须首先构建几个输入的"[dict](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)"，然后通过相关的转换函数运行它们，然后验证输出的 dict。如果留给开发人员，这将增加我们团队每个成员相当单调的工作。所以[我们](https://github.com/arslanhashmi)认为应该有更好的方法来做到这一点。

有，它被称为数据驱动测试(DDT)。

数据驱动测试被定义为一种软件测试方法，其中输入不是硬编码在测试用例中，而是通过一些外部资源提供的。这个来源可以是一个数据库表、来自另一个系统的 HTTP 请求，或者(在我们的例子中)简单的 JSON 文件。

我们的解决方案是简单地创建 2 个目录。`current`和`expected`。我们只编写了一个执行以下操作的测试用例。

*   从`current`目录读取一个 JSON 文件
*   解析文件并通过相应的转换器运行它
*   从`expected`目录中读取相应的 JSON 文件
*   断言两个字典是平等的
*   重复

该测试用例的原始 PR 可以在[这里](https://github.com/ucsd-ets/edx-platform/blob/1463384e9c9ca14f5acedc373fe11ede53fcf1a6/openedx/features/caliper_tracking/tests/tests.py)找到，然而这里是主测试用例函数。

```
def test_caliper_transformers(self):
    test_files = [file for file in os.listdir(
        '{}current/'.format(TEST_DIR_PATH)) if file.endswith(".json")]

    for file in test_files:
        input_file = '{}current/{}'.format(
            TEST_DIR_PATH,
            file
        )
        output_file = '{}expected/{}'.format(
            TEST_DIR_PATH,
            file
        )

        with open(input_file) as current, open(output_file) as expected:
            event = json.loads(current.read())
            expected_event = json.loads(expected.read())

            expected_event.pop('id')

            caliper_event = CaliperBaseTransformer(event).transform_event()
            related_function = EVENT_MAPPING[event.get('event_type')]
            caliper_event = related_function(event, caliper_event)

            caliper_event.pop('id')

            self.assertDictEqual(caliper_event, expected_event) 
```

在编写了测试用例之后，每个开发人员只需要在必要的文件夹中添加必要的 JSON 文件，它们就会在运行我们的 [CI](https://www.thoughtworks.com/continuous-integration) 构建时被自动测试。留出更多时间来编写代码和优化性能。

我希望这段代码将被添加到主 [Open edX 平台](https://github.com/edx/edx-platform)中。我认为这是我们对 [Arbisoft](http://arbisoft.com/) 、 [UC San Deigo](https://ucsd.edu/) 和 [Open edX](https://www.edx.org/) 的重大贡献。