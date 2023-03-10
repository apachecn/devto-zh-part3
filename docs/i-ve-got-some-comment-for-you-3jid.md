# 我有一些意见给你

> 原文：<https://dev.to/smoogie/i-ve-got-some-comment-for-you-3jid>

代码的注释总是被忽略的主题。很多时候开发者乱加，忽略或者误用。不好的评论可能是有害的，而好的评论可能是非常有用的工具。在这篇文章中，我想总结一下我与公司其他开发人员讨论过的最重要的事情。我还想介绍一下我个人对注释代码的方法。

**TLDR**

您应该在少数情况下使用注释:

*   使用注释和停靠块来创建 API 的文档
*   解释不直观的代码解决方案——说为什么，而不是怎么做
*   解释复杂的算法——主要是说为什么，而不是如何。如果情况需要，添加深入解释算法的材料的链接
*   描述配置选项

有时，注释会比任何单独创建的文档更好、更准确。好的注释将帮助你回到你的旧代码，并有助于其他人理解你的代码。

您可以使用注释作为您发展的工具:

*   添加待办事项列表
*   对技术债务进行评论
*   在函数中添加注释，以便在 ide 中获得更好的自动完成和代码支持

如果您有 WIP 提交/分支，您甚至可以将它们推入代码库。请记住在发送合并请求之前或在将您的代码添加到主分支之前删除这些注释。

不要添加太多评论。这可能是问题或不良做法的指标。很多时候你可以找到一些更好的等价物。以下是一些你应该避免的情况:

*   不要使用评论进行讨论，而是使用[松弛](https://slack.com/)或其他工具
*   不要使用注释进行代码审查——有更好的解决方案，例如 gitlab 中关于合并请求的的[讨论、](https://docs.gitlab.com/ee/user/project/merge_requests/#commenting-on-any-file-line-in-merge-requests) [Jetbrains Upsource](https://www.jetbrains.com/upsource/) 等。
*   不要描述所有的变量和代码的每一步——正确命名变量/函数/类，并使用类型提示
*   不要使用注释来划分代码——将代码划分为多个函数/类/文件
*   不要描述每一个用例以及功能的每一步——如果你有多个用例，写多个测试，这将是比注释中的大墙文本更好的文档

一个完全不同的话题是如何使用注释。像代码一样，注释应该在整个项目中保持一致。与您的团队讨论它们并创建指导方针。决定何时使用批注以及如何格式化批注。记住在编码期间而不是之后创建注释。当您使用任何绑定器或预处理程序时，添加最小化和注释删除的步骤，例如当您解析 css 时，或者在使用 [webpack](https://webpack.js.org/) 绑定 js 时。

## 注释为文档

作为开发人员，你可能听说过“你的代码应该是自我解释的”。这是事实，但这并不意味着你不应该使用注释。它们可以显著提高代码的可理解性。您应该将它们用作我们代码的文档。让我解释一下我是如何使用它的。

在大多数 IDE 中当你从[模板](https://www.jetbrains.com/help/phpstorm/using-file-and-code-templates.html)生成文件时，IDE 会自动添加注释。注释描述了谁、何时以及如何生成文件。虽然我更喜欢更改模板并删除注释，但添加一些文件内容的一般描述也是一个好主意。如此高的文件视图可以显著加快代码分析的速度。描述应该简短地解释文件包含什么以及所包含元素的用途。在大多数情况下，谁在何时创建文件并不重要，因为多个用户稍后会更改它，原始代码可能会被遗忘很久。如果您使用任何控制版本系统，您将在那里得到这些信息。另一方面，当您构建一些库并创建发行版文件时，在文件开头包含关于作者的信息仍然是一个好主意。

在我们公司，我们希望在大多数类和函数中加入注释块。这种评论采取注释和简短描述的形式。虽然很多时候阅读代码就足够了，但在大多数情况下，开发人员没有时间去阅读几十行代码来找出函数中的具体参数是做什么用的。最好对函数中的每个参数都有解释:它是什么，什么是可接受的值。我们还添加了关于返回值的信息和简短描述，函数做什么，函数有什么副作用，它会抛出什么错误等等。我们可以说代码本身已经覆盖了这一部分，例如通过类型提示，但是有时你接受多种类型的值，返回多种类型的值(这是不应该发生的)，或者你在代码的许多地方抛出许多不同的异常，注释有助于组织这些信息。下面是一个简单的 php 示例:

```
/**
* function validate if user has specific premissions
* @param User $user user for whom we check permissions
* @param string[]|Permission[] $perms names of permissions or permissions objects to validate
* @param bool $atLeastOne if true - user can have at least one permission, if false - user must have all permissions
* @return bool true if has permissions
* @throws ObjectDoesNotExistException if permission does not exist in the system
*/
function checkParmission(User $user, array $perms, bool $atLeastOne = true): bool { ... } 
```

对于类，我们希望至少有简短的描述和所有公共方法和公共字段的列表。这种对类的简短描述将有助于您使用新元素，并将成为您的 API 的良好文档。还有很多情况下你有隐藏的方法或者字段。您可以使用 magick 方法隐藏它之前的用户，或者您创建模型来保持数据是自动隐藏在它从数据库。Laravel 雄辩模型就是很好的例子。这里有一个简单的例子[拉勒维尔雄辩模型](https://laravel.com/docs/5.8/eloquent) :

```
* Class which represent user model
*
* @property int $id user id - autoincremental
* @property Carbon $created_at
* @property Carbon $updated_at
* @property string $email
* @property Role $role
* @property int $role_id
* @property int $status status of user: 0 - deleted; 1 - active; 2 - not verified
*
*/
class User extends Illuminate\Database\Eloquent\Model {...} 
```

很多时候你需要为你的代码建立一些文档。编写单独的文档并使其保持最新是一项非常耗时的任务。在这种情况下，正确注释的代码会好得多，因为它会在重构、修复错误和构建新东西的过程中自动更新。它也将更容易使用——你可以立即检查什么是什么以及如何使用它，而不用在文档中找到正确的位置。如果你需要创建用户可读的文档，你可以使用像 [PHPDocumentor](https://www.phpdoc.org/) 、 [JSDocs](http://usejsdoc.org/) 、 [DocFX](https://dotnet.github.io/docfx/) 或者 [JavaDoc](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html) 这样的工具。他们会根据你的评论创建描述你的 API 的网页。

在文档的上下文中，正确地呈现您的配置也是非常重要的，用户/开发人员了解所有可用的配置选项。添加一些好的自述文件总是一个好主意，但是没有比在配置示例中注释配置更好的解决方案了。您可以添加所有具有默认值和简短描述的可用选项。注释掉所有不需要的选项，但不要从示例文件中删除它们。我相信这是注释中代码唯一有意义的地方。下面以部分默认 [jest](https://jestjs.io/) 配置文件为例:

```
module.exports = {
  // All imported modules in your tests should be mocked automatically
  // automock: false,
// Stop running tests after `n` failures
  // bail: 0,
// Respect "browser" field in package.json when resolving modules
  // browser: false,
// The directory where Jest should store its cached dependency information
  // cacheDirectory: "C:\\Users\\Łukasz\\AppData\\Local\\Temp\\jest",
// Automatically clear mock calls and instances between every test
  clearMocks: true,
// Indicates whether the coverage information should be collected while executing the test
  // collectCoverage: false,
// An array of glob patterns indicating a set of files for which coverage information should be collected
  // collectCoverageFrom: null,
// The directory where Jest should output its coverage files
  coverageDirectory: "coverage",
...
} 
```

## 注释作为解释

我们讨论了带有注释的文档，但是解释事物如何工作或做什么的注释又是怎么回事呢？首先，不要描述下一行代码将做什么(这仅在函数/类开头的 doc 块中有意义)。相反，描述你为什么使用特定的值、算法或一些特定的解决方案。当你需要修改某些东西或者调试代码时，这可以节省你大量的时间。有时你会使用特定的解决方案，这并不直观或似乎有点奇怪，但出于某种特定的原因，它必须保持这种方式，例如，你为支持旧浏览器的应用程序编写一些 js，你需要添加一点黑客来使其与旧 IE 兼容。如果你不恰当地评论它，你会失去时间去理解或提醒你自己，为什么特定部分是这样做的。有时，为了避免潜在的错误，您将决定不更改代码的特定部分。在最坏的情况下，有人看不到特定解决方案的意义，稍后会更改或删除它，这将导致应用程序崩溃。

当你在代码中使用复杂的算法时，情况就有点不同了。这将有助于描述算法和增加一些链接，以便将来阅读和更深入地理解问题。

这种方法主要对代码维护人员和我们自己有用。当然，你知道你现在在做什么，但是如果你几个月后再去写代码，你会记得吗？您这样做，但是其他人会知道为什么使用了特定的值吗，您覆盖了什么用例？

同时，不要添加过多的描述和解释。注释应该简短地解释为什么做某事，什么类或函数可以用于，但不需要解释实现的细节。你不应该描述你的每一行代码，甚至你的每一个代码块。也有一些例外。如果你创建了成千上万人重复使用的复杂库，最好添加一些注释，帮助用户理解正在发生的事情。如果你是在特定的应用程序上工作，你不需要它，只解释特定的情况，并添加代码的文档。

## 注释作为开发者的工具

您已经知道注释对于文档记录非常有用，有助于解释复杂的情况，从而避免添加新的错误。你也可以把它看作是你的工具，在开发过程中支持你构建应用程序。有三个主要的地方可以使用注释。

首先，你可以在开发者工具中使用注释。许多 IDE 支持代码提示。大多数提示和自动完成都是基于上面提到的注释。此外，许多 linters、代码嗅探器、测试工具使用注释来改变默认行为。例如，您可以选择不应该测试的特定代码部分，也可以更改特定代码部分的规则。以下是 ESLint 排除和规则更改的示例:

```
/* eslint-disable */

thisIsNotCheckedByLint();

/* eslint-enable */
/* eslint eqeqeq: 2 */
$thisLineReturnESLintError == true; 
```

第二个有用的技巧是使用注释来规划你的代码。很多时候，当我编写代码时，我会从构建具体事物的框架开始。它包括带有接口、抽象类、类等的文件夹和文件。每个类中都声明了方法和字段。添加了类型提示和注释，以声明每个方法应该获取、返回和可能抛出的内容。在每个类中，我为每个步骤添加了注释，这些注释将用代码编写。这种框架表示数据流和一些逻辑，代码中带有注释。后来我用正确的代码交换意见。下面是一个简单的例子:

```
class TimeReport {
  constructor(projectId) {
    this.projectId = projectId;
  }
  generateReport() {
    this.initData();
    this.analizeData();
    this.getFile();
  }
  initData() {
    //find project in DB if notexist throw ObjectNotExistsError
    //find all stages and corresponding tasks for project
    //get schedule for project
  }
  analizeData() {
    // foreach task calculate spent time and compare to estimation
    // foreach stage sort tasks by status, count them
    // foreach stage check timeline and verify if stage is closed 
    // calculate when stage ended (if ended) or can end based on task's statuses and estimations
    // sum up how many hours we are after/before schedule
  }
  getFile() {
    //get file template and propagate data
    //create PDFFile
    //encode file to base64
    //return base64
  }
} 
```

第三种情况发生在我处理代码时，我发现以后需要修改或添加一些东西。例如，有些功能尚未完成，但我知道以后会添加。注释不仅指出了哪些工作必须完成，还添加了有关技术债务的信息。在这种情况下，我在注释中使用标记 TODO。当你在发展过程中休息时，例如当你离开办公室或去吃午饭时，用它们来表明你的精神状态也是一个好主意。这样的注释可以帮助你在中断后回到代码中。

使用注释作为工具有一点非常重要——在本地使用它们。如果您将其推送到 git for WIP 分支，这是没问题的，但是通过解决问题将它们从您的代码的最终版本中移除。唯一能留下来的是稀有的托多。您的代码不应包含任何 WIP 注释。

## 评论为代码气味和不良做法

现在你知道你可以从使用注释中得到什么，以及什么时候应该使用它们。什么时候不使用注释？在代码审查或检查别人的代码(甚至是你自己的代码)时，你会发现很多情况。这里有几个例子，你可以使用比注释更好的解决方案:

*   不要在讨论中使用评论。有更好的地方来讨论一些事情——安排会议、在代码评审期间交谈、使用 Slack 等等。如果你需要讨论代码的某个部分，有很好的工具，比如 git lab 有很好的合并请求 CR 工具。
*   评论不是版本控制系统！很多次我偶然发现旧代码，这些代码是开发人员留下的注释。使用版本控制系统并推送多个提交。提交的代码不会丢失。
*   不要使用注释将代码分成几个部分。如果你需要这样的东西，你有不好的代码。如果是配置/翻译文件，将其分成较小的文件。如果它是一个大的类或函数，也要把它分成更小的函数/类。
*   不要重复你自己。这毫无意义。以下是我所说的例子:

```
//Here I set avatar for user
$user->avatar = $avatar; 
```

*   不要解释每个变量。如果你需要这样做，改变它的名字。如果你需要解释具体的行，他们做什么，重构代码。正确的命名和更好的代码会减少评论的数量。以下是重构前的简单代码示例:

```
const articles = (new Date(user.membershipEndDate) > new Date() && user.membershipLevel >= User.GOLD_LEVEL) ?
await db.execute('SELECT * FROM articles WHERE published = 1') //IF USER HAS ACTIVE GOLD MEMBERSHIP RETURN ALL ARTICLES
: await db.execute('SELECT * FROM articles WHERE published = 1 AND public = 1') //ELSE RETURN ONLY PUBLIC ARTICLES 
```

在
之后

```
class User {
  ...
  get isActiveGoldMember() {
    const now = new Date();
    const membershipEnd = new Date(this.membershipEndDate);
    const isGoldMember = this.membershipLevel >= User.GOLD_LEVEL;
    return membershipEnd > now && isGoldMember;
  }
}
--------------------------------------------------------
let query = 'SELECT * FROM articles WHERE published = 1';
if (!user.isActiveGoldMember) {
  query += ' AND public = 1';
}
const articles = await db.execute(query); 
```

*   为你的方法/函数使用类型提示，并添加注释，这将为你节省大量的行内注释。如果您需要带注释行内注释，这意味着您做错了。这就是我所说的例子:

```
/** @var User $user **/
$user = app->auth()->user();
/** @var Company $sompany **/
$company = $user->getCompany(); 
```

*   当有多个依赖项或复杂的逻辑时，有时你需要描述代码做什么。最好切断松散的依赖关系，并考虑您的代码是否不需要重构。如果你需要太多的解释，你应该重写部分代码。
*   使用注释来描述你的 API，但是不要描述所有可能的用例。在测试中提供详细的用例要好得多，因为这将比大的文本墙(带有抽象描述)更好地解释您的代码。
*   完成后清理干净，不要留下开发注释，这些注释应该在开发过程中对你有所帮助。如果你有一些意见，很可能你没有完成任务或你创造了技术债务。
*   把所有的导入放在你的文件上面，使用名称空间等等。这也可以节省一些评论。

## 注释作为指南的主题

下一件重要的事情是如何使用注释——它们应该是可理解的和一致的。用户应该知道注释是用来做什么的，只要看着它。首先，你要讨论具体项目中的评论策略。决定你在什么时候使用什么样的评论。定义如何格式化每种注释类型。在整个项目中保持一致性。当你第一次处理问题时，这里有一些有用的东西:

*   在大多数情况下，行内注释有代码味道。
*   使用块注释。它们应该在文件的开头，在作为文档的类和函数之前。单行注释可以用于 TODO 之类的短标记，或者函数内部的简单描述。
*   定义注释的顺序以保持一致。对于类，我从描述开始，然后介绍方法和字段。对于函数/方法，我也从描述开始，然后列出参数、返回，最后是函数抛出的所有错误/异常。
*   记住使用正确的意图。注释应该和它所注释的代码一样。例如

```
/**
 * Here is comment for Class
 */
class MyClass
{
    /**
     * Here is comment for my method
     */
    public function myMethod() {}
} 
```

*   将注释放在单独一行的注释代码之前。不要在注释和注释代码之间添加空行。放在评论之前。例如:

```
//this is good place to comment why I use console.log()
console.log(serviceResponse)

console.log(oops)
//this is bad place to comment console.log(oops)
console.log(ouch) 
```

*   使用 doc 块时，以/**开头。在每一新行中，在注释行的开头添加*。例如:

```
/**
 * This is
 * multiline
 * doc block
 *
 * @param string $text
 * @return bool
 */ 
```

*   让你的评论更专业——没有愚蠢的笑话，没有争议性的词语等等。确保你使用正确的拼写。在代码的所有注释中使用相同的语言——我使用英语。
*   不要嵌套注释，例如:

```
/**
 * So you should not comment this comment
 * // but you do
 * /* And it is wrong */
 */ 
```

最后，有两个额外的提示值得一提。首先，试着在你编码的时候创建注释，而不是在你完成之后。这样他们将是最准确和新鲜的。Secondary —当您使用预处理器、编译器或捆绑器时，在创建分发文件之前，检查是否有一些选项可以最小化代码并删除注释。

## 总结

值得记住的是，当你和其他开发人员维护大型代码库时，注释是非常有用的。将它们用作代码的文档。你甚至可以从评论中构建一些 html 文档。在开发过程中使用它们作为工具。同时记住，代码仍然比注释更重要。是工具，不是目标。

当你使用太多注释时——可读性下降，文件变大等等。他们应该帮助你，而不是拖你的后腿。你不应该有太多的注释来一步一步地解释事情是如何工作的，你不应该对每个函数调用、变量等都有解释。如果太多，说明你的代码有问题。

我希望这篇阅读对你有用，并帮助你重新思考代码中的注释。

[媒体上的原始帖子](https://medium.com/@LPawlowski/ive-got-some-comment-for-you-a259218b8b68)