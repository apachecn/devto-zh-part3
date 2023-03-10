# 大测试指南

> 原文：<https://dev.to/rlxdprogrammer/the-big-testing-guideline-9ok>

# 简介

根据我对许多人的经验，他们对软件测试的想法非常混乱，有时甚至对程序员和项目经理也是如此。这就是为什么我决定写一篇文章，对不同的软件测试方法做一个粗略的概述。

当我开始经营我的公司时，我是在为一家小公司工作。软件测试意味着，有人启动软件，做一些随机点击，检查是否有奇怪的事情发生。后来我转向了汽车行业。这个领域的测试非常严格。但我学到的是:软件本身决定了正确的测试过程。这取决于:你的软件的安全性有多重要，谁会使用它，它有多复杂等等。

有几种测试方法，这些方法可以是自动的或手动的，功能性的和非功能性的，黑盒，灰盒或白盒，它可以在几个不同的层次上(单元测试，组件测试等。).对于每个项目，你需要找到最合适的测试方法。当然，你在测试中使用的方法越多，软件中出现 bug 的几率就越低(这种几率永远不会为零)，另一方面，测试当然需要时间，所以也要花钱。这就是为什么你需要找到最适合你的项目的方法。现在，我将尝试向您介绍不同的测试方法。

# 功能和非功能测试

## 功能测试

测试可以是功能性的，也可以是非功能性的。功能测试通常违背功能需求。在这里，你基本上是在检查你的软件的功能是否与要求的行为相同。您还需要始终关注角落案例。

## 非功能性测试

非功能性测试违背了非功能性软件的需求，它可能违背了软件的稳定性，它可以测试软件是否在每个要求的环境下运行(操作系统、网络浏览器、不同的硬件等)。).安全测试也属于这一类，它检查你的软件是否足够安全，这对于基于 web 的应用程序尤为重要。负载测试也属于这里，它测试你的软件在大量请求的情况下是如何反应的。在这里，您还可以测试您的软件在最关键的场景中的运行时间(概要分析)或内存消耗。它也可以是一个可用性测试，如果你的软件对用户足够友好，你可以和目标用户一起测试。

# 自动和手动测试

## 自动化测试

自动化测试通常被称为“编码”测试。它们只实施一次，以后可以随时运行。经典的例子是用一些测试框架(GTest，JUnit 等)对你的代码进行单元测试。).其他自动化测试可以用一些测试框架(比如 Selenium)或者脚本语言(Python 在这个领域也很流行)来实现。这里的共同点是，你向你的程序，或者你的程序的一部分，提供一些输入，然后你让你的程序运行，最后你将结果与所谓的预期结果进行比较。自动化测试应该是确定性的。这意味着同样的代码应该总是通过或者总是失败。自动化测试的最大优势在于，您可以在代码库的每次更改之后运行这样的测试，并且您可以检查这些更改是否没有破坏您已经工作的功能。因此，您可以将它们集成到持续集成系统中，该系统会在每次新提交后运行您的测试。

自动化测试也可以与您的代码并行实现，因此您不需要等待已经完成的软件的测试开发。

## 手动测试

手动测试是可以手动完成的测试。所以你手动启动程序，执行一些预定义的步骤并检查它的行为。对于您不想频繁测试的功能，或者难以用自动化方式测试的功能，最好进行这样的测试。这里重要的是:手动测试也需要基于一个测试计划来完成，这个测试计划用确切的测试步骤和预期的行为来描述不同的测试场景。

# 测试类别基于关于代码的知识

## 黑盒测试

在黑盒测试的情况下，你对代码一无所知。你就像一个简单的用户，你正在尝试软件的不同功能，而不考虑它的实现细节。对于黑盒测试来说，有一个真正了解软件实现细节的测试人员是很好的(所以他没有参与软件的开发)。黑盒测试通常是针对软件需求进行的，这也需要被很好地记录，以便能够定义好的测试用例。在黑盒测试的情况下，你也需要像用户一样思考。黑盒测试可以是自动的，也可以是手动的。

## 白盒测试

在白盒测试的情况下，您可以访问整个代码库，并且您也在您的测试用例定义中考虑它。例如，你知道你的函数的确切参数是什么，你知道，在负参数值的情况下，你的函数必须抛出一个异常，然后你应该定义这样一个测试用例。最典型的白盒测试是单元测试，但是你也可以在不同的层次上进行白盒测试。它既可以自动测试，也可以手动测试。如果你是代码的开发者，你正在用调试器测试你的代码在特定输入下的表现，那么这也算是一个白盒测试。白盒测试的一个重要指标叫做代码覆盖率:它意味着当你运行所有的测试时，你的代码行实际运行的百分比有多大。

## 灰盒测试

在灰色测试的情况下，你对代码的了解有限。例如，你知道组件是什么，使用了哪些接口，接口的参数是什么，但是你不知道组件是如何实现的。

# 等级测试

从测试一小段代码到测试整个系统，有不同层次的测试。每个层次都有自己的优点和缺点。通常情况下，低级别测试的优势是它们可以运行得更快，并且可以更精确地显示 bug 的根本原因。另一方面，有些错误只能通过更高层次的测试才能发现，因为它们是由不同软件组件的错误合作引起的。但是在更高级别的测试中，很难判断失败的测试用例中错误的根源在哪里，通常它们运行的时间更长。

## 单元测试

单元测试是最低级别的测试。单元测试是测试一个单元。一个单元应该足够小，大多数情况下它是一个函数或一个类。在单元测试的情况下，你实际上是在测试这一小段代码。为了避免受到其他单位的影响，其他单位可以被嘲笑。这意味着你正在用一些假的/虚假的行为来改变他们。出于这个原因，有几个模拟框架，像 Mockito 或 GMock。为了能够模仿，你需要有一个清晰和可模块化的代码架构。一种使模仿变得容易的常用方法叫做依赖注入。

一个好的单元测试是小的、快速的、独立的，并且测试一小部分代码。通常单元测试是自动化的，但也可以是手动的。

一种更重要的方法叫做基于测试的开发，在这种方法中，你基本上是与你的代码并行地实现你的单元测试用例。

单元测试通常由代码开发人员完成。从长远来看，找出回归是有用的。

一个好的单元测试是在失败时显示错误的确切位置，这样就不需要长时间的调试来修复你的代码。

## 组件测试

组件测试是关于测试代码的一个组件。如果你的代码足够模块化，并且被组织成接口清晰的组件，你也可以单独测试这些接口。这是灰箱测试的一个很好的候选。因此，您知道要使用的接口是什么(例如在库的情况下)，并且您知道您期望从这些接口得到什么，但是您不知道它们的确切实现是什么。

## 集成测试

集成测试是为了测试几个组件是如何一起工作的。集成测试还可以检测由组件的不正确协作/连接引起的错误。

## 系统测试

系统测试是针对你的整个系统的，所以你的软件运行在它的目标环境中的目标硬件上。这些测试通常在软件发布之前作为最终测试来完成。这些应该在每个软件发布时完成。这通常是一个黑盒测试，可以是手动的也可以是自动的。这一级别的测试通常由独立的测试团队完成。

# 应该考谁？

关于谁应该做这些试验的问题，有几种不同的意见。在传统的测试方法中，规则通常是测试人员和开发人员应该是两个不同的人。因此，如果开发人员以错误的方式思考或者对功能有错误的理解，测试人员仍然可以找到错误。我认为在更高层次的测试中，通常在黑盒测试中，这是一个很好的方法。

在像基于测试的开发这样的现代测试方法中，测试人员和开发人员通常是同一个人。这很好地过渡到了敏捷方法，在敏捷方法中，所有团队成员都应该能够接管任何类型的任务(测试、开发、设计等)。).这种方法使测试稍微快了一点，但是根据我的经验，它也不是很有效。

我认为最安全的方法是两种方法的结合:由开发人员实现单元测试和由独立团队在更高层次上进行黑盒测试。但在所有开发的情况下，需要考虑是否值得花这么多时间进行测试。在像汽车开发或飞机软件开发这样的安全关键系统中，这当然是有意义的。但对于网页或游戏手机应用来说，这可能并不重要。

# 项目生命周期中的测试

在经典的开发方法中，如 V-Model 测试的瀑布，在实现之后就很明显了。但是自动化测试的开发也可以与开发并行进行。在遵循这种经典方法的地方，软件也以循环的方式完成。这意味着有定期的软件发布，并且每个发布的软件都应该被测试。因此，在开发团队开发 N+1 版本之前，测试团队测试 N 版本，并为 N+1 版本准备自动化测试。

在敏捷开发方法中，像 SCRUM 或看板，测试驱动开发经常被遵循。这意味着单元测试和实现是并行进行的。重要的是，在敏捷开发的情况下，你也可以做一些更高层次的测试。您可以将它作为一个单独的用户故事来处理，以实现这样的测试。或者也可能是有一个独立的测试团队也在工作，例如基于 scrum，他们总是在一些 sprints 之后测试开发团队的结果。

在经典和敏捷方法的情况下，我认为将自动化测试集成到 CI 系统中非常重要，这样就能够定期检查软件是否正常工作。当然，在这种情况下，测试也需要定期更新，以满足新实现的需求。因为运行所有测试可能需要很长时间，所以最好为不同级别的测试分别设置 CI。就像在任何新提交的情况下运行所有单元测试一样，每晚运行所有组件和集成测试，并在每个软件发布时运行所有测试。

# 总结

在写作过程中，我意识到这是一个非常复杂的话题，所以我想我不能介绍的足够详细，但是我希望我能给你一个很好的概述。由于不同的公司用不同的方法和方式进行测试，我几乎可以肯定有经验的测试人员不会同意我的一些观点。这可能发生，老实说，我也不是一个测试者。希望我的文章能帮到你。