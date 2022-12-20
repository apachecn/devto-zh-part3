# 我如何自动化我的评分职责

> 原文：<https://dev.to/renegadecoder94/how-i-automated-my-grading-responsibilities-42g3>

*最初发表于 2019 年 3 月 11 日[叛离的编码者](https://therenegadecoder.com/teach/how-i-automated-my-grading-responsibilities/)*

自从我开始教以来，我一直试图找到自动完成我的评分职责的方法。毕竟，这非常耗费时间，而且我不认为这对学生有很大帮助。我因自动化而节省的每一分钟都可以用来提供高质量的反馈，专注于我的教学，并改善我的心理健康。谁能说不呢？

## 分级职责

在这个系列中，我已经几次提到了我的评分职责，但是我认为再次概述一下它们不会有什么坏处。

除了教学之外，我还负责一学期 12 个项目、14 个家庭作业、14 个实验和 3 次考试的评分。将所有这些数字乘以 40，这就是我在一个学期中批改的作业总数。正如你可能想象的，这是一个巨大的教室外的时间水槽。

为了加快速度，我试图找到自动评分的方法。也许最节省时间的机会是那些每周要花 6 个小时来评分的项目。不幸的是，这么长的持续时间是由一些问题造成的:

*   项目最有价值，所以它们需要最多的反馈。
*   项目必须进行测试，这可能需要一些时间，取决于程序的复杂性。
*   项目是以包的形式组织的，所以它们必须以 zip 这样的归档格式传输。
*   项目是由学生写的，所以风格变化很大，使得代码难以阅读。

正如你所看到的，项目有很多严格的要求，这使得评分成为一项非常耗时的任务。雪上加霜的是，学生们有不遵守指令的倾向，所以文件有时必须编辑后才能执行。最糟糕的情况:我必须联系学生，因为他们没有提交所有材料。

## 自动放码

作为一个总是试图从每天的任务中榨取一点点效率的人，我很快就承担起自动完成项目评分的责任。老实说，我无法想象在不发疯的情况下为 40 名学生完成以下程序:

1.  下载学生解决方案。
2.  解压缩学生解决方案。
3.  将文件加载到 IDE。
4.  运行文件(对各种测试用例重复)。
5.  仪表解决方案样式。
6.  基于测试和风格评估解决方案。
7.  给予反馈。

看了这个列表之后，我觉得我已经做出了正确的选择来自动化我的评分，但是自动化到底意味着什么呢？让我们来看看。

### 介绍 JUnit

在我的第一个学期，我当时对自动化的最好选择是 JUnit 测试。在任何给定的一周，我都要花大约 90 分钟来为项目写一个 JUnit 解决方案，再花 2 个小时来完成评分。换句话说，我设法将一个 6 小时的过程缩短到 4 小时左右。我随时都可以接受！

当然，JUnit 可能不是理想的选择。毕竟第 6 周才教方法，所以大部分项目都是海量主方法。此外，学生并不总是遵循相同的类命名约定，所以我必须聪明地调用 main 方法。

结果，我最终编写了一套非常复杂的方法来使用反射猜测类名。例如，下面的方法为强力反射生成一个类名列表:

```
private static ArrayList<String> getTestClasses(int project) {
    ArrayList<String> toTest = new ArrayList<String>();
    toTest.add("osu.cse1223.Project%1$s");
    toTest.add("osu.cse1223.Project%1$sa");
    toTest.add("osu.cse1223.CSEProject%1$s");
    toTest.add("cse1223.Project%1$sa");
    toTest.add("cse1223.Project%1$s");
    toTest.add("project%1$s.Project%1$s");
    toTest.add("Project%1$s");
    toTest.add("Project%1$sA");
    toTest.add("osu.cse1223.DragonsGame");
    toTest.add("Project04.DragonTrainers");
    toTest.add("Main");
    String projectNumberWhole = Integer.toString(project);
    String projectNumberPad = "0" + projectNumberWhole;
    int originalSize = toTest.size();
    for (int i = 0; i < originalSize; i++) {
        String test = toTest.get(i);
        toTest.set(i, String.format(test, projectNumberPad));
        toTest.add(String.format(test, projectNumberWhole));
        toTest.add(String.format(test, projectNumberPad).toLowerCase());
        toTest.add(String.format(test, projectNumberWhole).toLowerCase());
    }
    return toTest;
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，由于许多项目利用了 main 方法和文本格式，我花了很多时间来捕获标准输出并写入标准输入。查看我的安装和拆卸方法:

```
@Before
public void setUp() {
    System.setOut(new PrintStream(outContent));
    System.setErr(new PrintStream(errContent));
}

@After
public void tearDown() {
    System.setIn(System.in);
    System.setOut(System.out);
} 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，JUnit 解决方案相当笨拙，但它完成了任务。

### 解压脚本

虽然 JUnit 确实为我节省了很多时间，但仍然有办法减少浪费的时间。特别是，我发现手动解压文件夹浪费了很多时间。

为了更好地理解问题，我们使用 Canvas 来上传解决方案，这样会造成文件名混乱。因此，独立的 Java 提交最终会破坏它们的文件名。为了解决这个问题，我们要求学生将他们的解决方案从 Eclipse 导出为 zip 文件。这在两个方面有所帮助:

1.  它保护底层的 Java 文件名。
2.  它在需要时保留包结构。

不幸的是，我每周都要解压 41 个文件。当然，我确实用 7-zip 加快了速度，但是我仍然必须手工完成。

最终，我决定使用 Python 和 zipfile 库来自动化这个解包过程:

```
def extract_main_zip() -> str:
    """
    Extracts an archive given by the user.
    :return: the path to the unzipped archive
    """
    archive_name = filedialog.askopenfilename(
        title="Select Zip File",
        filetypes=(("zip files", "*.zip"), ("all files", "*.*"))
    )
    archive = zipfile.ZipFile(archive_name)
    archive_path = os.path.join(os.path.dirname(archive_name), ARCHIVE)
    archive.extractall(archive_path)
    archive.close()
    return archive_path 
```

Enter fullscreen mode Exit fullscreen mode

在这个函数中，我使用`tk`打开一个文件选择 GUI。在那里，我解压所选的 zip 文件，并将路径返回到解压缩站点。

因为 zip 文件包含 zip 文件，所以我决定也自动化这个解包过程:

```
def extract_solutions() -> str:
    """
    Extracts user folders.
    :return: the path to the extraction site
    """
    unzipped_archive = extract_main_zip()
    dump = os.path.join(os.path.dirname(unzipped_archive), DUMP)
    pathlib.Path(dump).mkdir(parents=True, exist_ok=True)
    for file in os.listdir(unzipped_archive):
        file_name = os.fsdecode(file)
        file_path = os.path.join(unzipped_archive, file_name)
        file_path_plus_name = os.path.join(dump, file_name.split("_")[0])
        if file_name.endswith(".zip"):
            zip_file = zipfile.ZipFile(file_path, "r")
            zip_file.extractall(file_path_plus_name)
            zip_file.close()
        else:
            name = file_name.split("_")[0]
            project = file_name.split("_")[-1]
            pathlib.Path(os.path.join(dump, name)).mkdir(parents=True, exist_ok=True)
            new_file_path = os.path.join(dump, name, project)
            os.rename(file_path, new_file_path)
    return dump 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，这个函数调用了前面的函数，并存储了提取站点的路径。从那里，该函数生成一个名为 Dump 的新提取站点。

之后，我们遍历所有的 zip 文件，提取它们，并把它们放在一个新的文件夹中，以学生的名字作为目录名。如果我们遇到一个不是 zip 文件的文件，我们会尝试在将该文件放在一个与所有解压缩的 zip 文件放在一起的文件夹中之前解决名称混乱的问题。

完成后，我们返回到新提取站点的路径。总之，我们将有两个新文件夹。一个包含所有 zip 文件(归档文件)，一个包含所有解压缩文件(转储文件)。此时，归档目录是无用的，所以我们可以删除它。

### 测试自动化

随着提取过程的自动化，我可能为自己节省了大约 30 秒，相当于节省了大约 20 分钟。当然，我随时都可以接受。

也就是说，我觉得还有更多的事情要做。特别是，我发现做以下事情非常耗时:

1.  下载所有学生提交的材料。
2.  运行 Python 提取脚本。
3.  装上爪哇博士。
4.  将测试文件拖放到 IDE 中。
5.  评分学生提交(重复 40 次)。
    1.  检索学生提交的内容并将其放入 IDE 中。
    2.  点击测试。
    3.  分析测试结果。
    4.  评估提交风格。
    5.  给予反馈。

尽管这种新方法很烦人，但与手工评分相比，这是一个不可思议的进步。在任何一周，我可能只花 2 到 3 个小时给项目评分。说到目前为止所有的自动化都不值得是愚蠢的。

然而，在上面的过程中仍然有许多手动步骤，所以我决定再次减少这些步骤:

1.  下载所有学生提交的材料。
2.  运行 Python 提取和测试脚本。
3.  评估提交风格(重复 40 次)
4.  给出反馈(重复 40 次)

为此，我扩展了我的 Python 脚本来支持 JUnit 测试。在高层次上，每个解决方案的等级如下:

```
def grade_file(classes: str, build_file: str, test_class: str, results):
    """
    Grades a file.
    :param classes: a directory contain files under test
    :param build_file: a file to test
    :param test_class: the path to the test file
    :param results: the results file
    :return: None
    """
    classpath = "C:\\Program Files\\JUnit\\junit-4.13-beta-2.jar;C:\\Program Files\\JUnit\\hamcrest-all-1.3.jar;"
    compile_junit(classes, classpath, build_file)
    compilation_results = compile_junit(classes, classpath, test_class)
    execution_results = test_junit(classes, classpath, get_test_name(test_class))
    write_to_file(results, compilation_results, execution_results, build_file) 
```

Enter fullscreen mode Exit fullscreen mode

除了硬编码的类路径之外，这个解决方案将自动编译学生解决方案和我的 JUnit 测试代码，执行测试，并将结果打印到一个文件中。到那时，我所要做的就是浏览学生姓名和他们的测试报告的文件，然后才能评定成绩。

### 未来扩展

虽然新的过程比我上学期做的任何评分都要快好几光年，但仍有改进的余地。例如，可以自动下载学生解决方案。见鬼，甚至有可能把这个过程安排在一个服务器上，在最后期限前把测试结果用电子邮件发给我。

另一方面，创建一个只告诉我成绩的测试报告可能会很好，这样我就不用承担任何将测试用例转换成成绩的认知负荷。如果可能的话，自动上传成绩也是可能的。

从始至终，我们会有一个完全自动化学生成绩的系统。我就没有必要花时间评估成绩了。相反，我可以专注于我所关心的学生反馈。毕竟，分数是一种任意的衡量标准。反馈有助于学生成长。

此外，如果没有评分带来的认知负荷，我可能会编写更好的讲座材料，拥有更好的办公时间，并通过电子邮件提供更好的支持。那将是一个梦想！

### 弊端

最近，我向一个朋友讲述了我为自动评分所做的事情，他们问了我一个很棒的问题:

> 如果你把所有的事情都自动化了，你将如何发现剽窃？
> 
> *   朋友，2019

老实说，这不是我想过的事情。当然，在这一点上，这不是我要担心的事情。毕竟，我确实会为了反馈的目的查看每一个解决方案，所以我应该能够发现抄袭。

但是，扩展当前的解决方案来检测本地抄袭可能会很有趣。换句话说，我可以保存所有的解决方案，并在进行过程中对它们进行比较。那会很有趣的！

也就是说，我从来不是一个不怕惩罚的人。我的核心价值观建立在信任的基础上，所以我倾向于向学生提供同样的价值观。如果我不怀疑任何作弊行为，我就不会去找它。他们会失去我的信任。

## 自动化的力量

每隔一段时间，我就会看到一个迷因在取笑那些宁愿花一个小时写脚本来自动完成一项任务，也不愿花五分钟来完成这项任务的开发人员，对此我深感内疚。也就是说，我不认为我对自动化的追求是一件坏事。毕竟，我总是向公众分享我的解决方案。

例如，你可以免费查看我用来为 CSE 1223 课程自动评分的所有 JUnit 测试代码。例如，项目文件夹包含所有的 JUnit 测试脚本。同时，[我最近把 Python 脚本转移到了自己的 repo](https://github.com/TheRenegadeCoder/auto-grader) 中。为了你自己的利益，你可以随意四处看看，借一些我的作品。这就是为什么我做我所做的！

此外，我应该提到的是，自从我写这篇文章以来，Python 分级脚本已经经历了很多变化。例如，它现在将所有分数转储到一个 JSON 文件中，这允许我将文件的一部分嵌套在 IDE 中，这样更容易扫描。随着 JSON 的改进，我能够对谁做得好，谁做得不好有一个高层次的了解，我用它来连续对类似的评分任务进行评分。

如果你知道任何可能对自动评分感兴趣的老师，为什么不把这篇文章转发给他们。我肯定他们会很感激的！无论如何，感谢您花时间阅读这篇文章。