# 博客文章:工作流 1.46 版(Perl)

> 原文：<https://dev.to/jonasbn/blog-post-release-1-46-of-workflow-perl-5600>

我刚刚发布了 [Workflow](https://jonasbn.github.io/perl-workflow/) 1.46，这是一个用于在 Perl 中构建简单状态机的库。该版本包含一个来自外部贡献者 Oliver Welter 的简单补丁。

很长时间以来，工作流并没有发生太多变化，[最近一次发布是在 2017 年](https://github.com/jonasbn/perl-workflow/releases/tag/1.45)。所以收到[的公关](https://github.com/jonasbn/perl-workflow/pull/16)是一个惊喜。

我必须解决 Travis 配置的一些问题，以观察成功的构建。这个问题是由于我的 [Dist::Zilla](http://dzil.org/) ( `dzil`)配置需要比 Travis 配置中列出的更新的 [Perl](https://www.perl.org/) 的问题。

在一些[牦牛刮毛](https://en.wiktionary.org/wiki/yak_shaving)之后，第二个构建展示了[一个更老的已知 bug](https://github.com/jonasbn/perl-workflow/issues/10) ，它偶尔会弹出来，友好地提醒我必须找时间解决这个特殊的问题。不管怎样，我很快就发布了一个版本，不是因为这个 bug 很关键，只是为了避免让 PRs 逗留太久，也是出于对贡献者的尊重——再次感谢 Oliver。

这让我想强调一下我的软件开发生命周期( [SDLC](https://en.wikipedia.org/wiki/Software_development_process) )中一些有趣的方面，这个版本演示了这些方面。

1.  轻松评估问题报告和变更请求的能力
2.  快速和立即构建*的能力*
3.  能力释放*迅速*并立即

第一部分被绑定到分支策略，并有一个已建立的[工具链](https://en.wikipedia.org/wiki/Toolchain)意思是:审查和巩固变更(合并)和测试，所有这些都使用 Perl、Dist::Zilla、Git/GitHub 和出色的 Perl 测试库。

执行从分支(拉请求)到主分支的传入变更的持续集成(CI)的能力，保持*稳定*并始终处于已知状态。这里使用上面列出的相同工具和特拉维斯。

第三部分，包装和发布可以轻松完成。使用 Dist::Zilla 和[暂停](https://pause.perl.org/pause/query?ACTION=pause_04about) /CPAN。

如果你想了解更多关于上述过程中涉及的[我的反馈循环](//dev.to/jonasbn/blog-post-feedback-loops-1gm5)的信息，我之前已经写过了。