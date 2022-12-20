# 假设比依赖更重要

> 原文：<https://dev.to/hrbrmstr/assumptions-matter-more-than-dependencies-94k>

最近在 R 宇宙中有很多关于“依赖性”的讨论。这并不是一个关于这个的帖子，但更像是一个“真的，不要这样做”的帖子，如果你决定通过试图建立一个有严重缺陷的 CRAN 包元数据模型来戳破依赖熊。

起重机包裹接受检查。下面是`akima` 的[(我:heart: me 一些网格插值函数，加上这个包现在不在任何热键 R 部落):](https://cran.rstudio.com/web/checks/check_results_akima.html)

| 风味 | 版本 | T <sub>安装</sub> | T <sub>检查</sub> | T <sub>总计</sub> | 状态 | 旗帜 |
| --- | --- | --- | --- | --- | --- | --- |
| r-devel-Linux-x86 _ 64-debian-clang | 0.6-2 | Nine point eight three | Thirty-two point six seven | Forty-two point five | [OK](https://www.R-project.org/nosvn/R.check/r-devel-linux-x86_64-debian-clang/akima-00check.html) |  |
| r-devel-Linux-x86 _ 64-debian-gcc | 0.6-2 | Eight point five three | Twenty-six point five six | Thirty-five point zero nine | [OK](https://www.R-project.org/nosvn/R.check/r-devel-linux-x86_64-debian-gcc/akima-00check.html) |  |
| r-devel-Linux-x86 _ 64-fedora-clang | 0.6-2 |  |  | Fifty-three point three three | [注](https://www.R-project.org/nosvn/R.check/r-devel-linux-x86_64-fedora-clang/akima-00check.html) |  |
| r-devel-Linux-x86 _ 64-fedora-gcc | 0.6-2 |  |  | Fifty-one point zero three | [注](https://www.R-project.org/nosvn/R.check/r-devel-linux-x86_64-fedora-gcc/akima-00check.html) |  |
| r-devel-windows-ix86+x86_64 | 0.6-2 | Fifty-three | Seventy-six | One hundred and twenty-nine | [OK](https://www.R-project.org/nosvn/R.check/r-devel-windows-ix86+x86_64/akima-00check.html) |  |
| r 修补的 linux-x86_64 | 0.6-2 | Nine point two six | Twenty-eight point three two | Thirty-seven point five eight | [OK](https://www.R-project.org/nosvn/R.check/r-patched-linux-x86_64/akima-00check.html) |  |
| r 修补的 solaris-x86 | 0.6-2 |  |  | Sixty-six point two | [OK](https://www.R-project.org/nosvn/R.check/r-patched-solaris-x86/akima-00check.html) |  |
| r 版本-linux-x86_64 | 0.6-2 | Eight point five nine | Twenty-eight point two five | Thirty-six point eight four | [OK](https://www.R-project.org/nosvn/R.check/r-release-linux-x86_64/akima-00check.html) |  |
| r 版本-windows-ix86+x86_64 | 0.6-2 | Thirty-nine | Sixty-nine | One hundred and eight | [OK](https://www.R-project.org/nosvn/R.check/r-release-windows-ix86+x86_64/akima-00check.html) |  |
| r 版本-osx-x86_64 | 0.6-2 |  |  |  | [OK](https://www.R-project.org/nosvn/R.check/r-release-osx-x86_64/akima-00check.html) |  |
| r-oldrel-windows-ix86+x86_64 | 0.6-2 | Twenty-eight | Seventy-one | Ninety-nine | [OK](https://www.R-project.org/nosvn/R.check/r-oldrel-windows-ix86+x86_64/akima-00check.html) |  |
| r-oldrel-osx-x86_64 | 0.6-2 |  |  |  | [OK](https://www.R-project.org/nosvn/R.check/r-oldrel-osx-x86_64/akima-00check.html) |  |

```
Check DetailsVersion: 0.6-2 Check: compiled code Result: NOTE File ‘akima/libs/akima.so’: Found no call to: ‘R\_useDynamicSymbols’ It is good practice to register native routines and to disable symbol search. See ‘Writing portable packages’ in the ‘Writing R Extensions’ manual. 
```

Enter fullscreen mode Exit fullscreen mode

`Status`字段可以是“正常”、“注意”、“警告”、“错误”或“失败”。

你还会注意到对未来的检查，甚至更酷的 R ("devel ")，漂亮的 R ("release"/"patched ")和:哈欠:R ("oldrel ")。记住这些，它们很重要。

现在，假设您想要执行一个诚实的评估，看看具有更多依赖项的包是否更有可能具有一个或多个“坏的”CRAN 检查条件。你可能会把“注意”和“确定”混为一谈，而不在包裹上标记那张特定的支票。这样就剩下“警告”(“警告”的原因是不同的检查 RDS 文件包含/放弃了一致性？)“错误”和“失败”。很明显，我们可以直接使用它们，而不用担心其他问题，对吗？

### 警告【ING】威尔罗宾逊！

你可以在[https://cran.r-project.org/web/checks/check_details.rds](https://cran.r-project.org/web/checks/check_details.rds)拿到支票详情的复印件。我碰巧有一个本地副本(使用“3 月 18 日 02:47”版本),现在我的 CRAN mirror 再次很好地重新运行。让我们确定它是在 OK:
中被读入的

```
library(tidyverse)

det <- as_tibble(readRDS("check_details.rds")) # it's got tons of classes & I like readable data frame prints

nrow(distinct(det, Package))
## [1] 13094 
```

Enter fullscreen mode Exit fullscreen mode

好的，这个数字跟踪最后一次 rsync 的计数。那么，这些警告看起来像什么？

```
filter(det, Status == "WARNING") %>% 
  select(Output)
## # A tibble: 2,299 x 1
## Output                                                             
## <chr>                                                              
## 1 "Found the following significant warnings:\n Warning: unable to r…
## 2 "Found the following significant warnings:\n Warning: unable to r…
## 3 "Found the following significant warnings:\n Warning: unable to r…
## 4 "Warning in parse(file = files, n = -1L) :\n invalid input found …
## 5 "Warning in parse(file = files, n = -1L) :\n invalid input found …
## 6 "Found the following significant warnings:\n Warning: unable to r…
## 7 "Found the following significant warnings:\n Warning: unable to r…
## 8 "Found the following significant warnings:\n Warning: unable to r…
## 9 "Found the following significant warnings:\n track_methods.cpp:22…
## 10 "Found the following significant warnings:\n Warning: unable to r…
## # … with 2,289 more rows 
```

Enter fullscreen mode Exit fullscreen mode

EEK！嗯，实际上不是。检查是自动的，我们可以使用一些子串机制来尝试得到更好的组:

```
filter(det, Status == "WARNING") %>% 
  mutate(bits = substring(trimws(Output), 1, 30)) %>% 
  count(bits, sort=TRUE) %>% 
  mutate(pct = n/sum(n))
## # A tibble: 29 x 3
## bits n pct
## <chr> <int> <dbl>
## 1 Found the following significan 1316 0.572  
## 2 Error in re-building vignettes 702 0.305  
## 3 Error(s) in re-building vignet 90 0.0391 
## 4 Output from running autoreconf 56 0.0244 
## 5 Warning in parse(file = files, 37 0.0161 
## 6 Missing link or links in docum 15 0.00652
## 7 "network.dyadcount:\n function(" 10 0.00435
## 8 Found the following executable 9 0.00391
## 9 dyld: Library not loaded: /Bui 8 0.00348
## 10 Errors in running code in vign 8 0.00348
## # … with 19 more rows 
```

Enter fullscreen mode Exit fullscreen mode

好的，所以对于那些“重要的”发现来说，一些“窥探”是有根据的，但是这些发现中有 30%是关于小插曲的。当然，小插图很重要，理想情况下它们建得很好，但有*吨*的原因，他们不在克兰不断变化的基础设施。我认为他们应该被排除在外。在评论中写下不同的观点，因为*这是分析师的观点*。但是我碰巧对 CRAN *非常了解，并严肃地建议，在关于高依赖包功效的*问题*的背景下，这应该被忽略，除非进一步单独调查。*

那么，这些“重要的”警告在哪里呢？

```
filter(det, Status == "WARNING") %>% 
  filter(grepl("significant", Output, ignore.case = TRUE)) %>%
  mutate(flavor_flav = case_when(
    grepl("devel", Flavor) ~ "devel",
    grepl("oldrel", Flavor) ~ "oldrel",
    TRUE ~ "current"
  )) %>% 
  count(flavor_flav, sort=TRUE) %>% 
  mutate(pct = n/sum(n))
## # A tibble: 3 x 3
## flavor_flav n pct
## <chr> <int> <dbl>
## 1 devel 904 0.686
## 2 current 280 0.213
## 3 oldrel 133 0.101 
```

Enter fullscreen mode Exit fullscreen mode

我假设如果目标真的是创建一个模型来帮助决定你是否应该承担具有多个++依赖的包的风险，你就不能包含“devel”。没有人在生产中运行“devel ”,这才是真正的目标:一个安全的生产环境。因此，实际上你也必须扔掉其中的 68%(有些人还在纠结:打哈欠:在 IT 实践严苛或工作流系统脆弱的组织中“陈旧”了)。我们还没有达到“0 ”,那么这些问题是什么呢？

```
filter(det, Status == "WARNING") %>% 
  filter(grepl("significant", Output, ignore.case = TRUE)) %>%
  mutate(flavor_flav = case_when(
    grepl("devel", Flavor) ~ "devel",
    grepl("oldrel", Flavor) ~ "oldrel",
    TRUE ~ "current"
  )) %>% 
  filter(flavor_flav != "devel") %>% 
  mutate(Output = gsub("Found the following significant warnings:\n ", "", trimws(Output))) %>% 
  mutate(bits = substring(trimws(Output), 1, 50)) %>% 
  count(bits, sort=TRUE) %>% 
  mutate(pct = n/sum(n))
## # A tibble: 110 x 3
## bits n pct
## <chr> <int> <dbl>
## 1 "Warning: S3 methods '[.fun_list', '[.grouped_df', " 158 0.383  
## 2 Warning: 'rgl_init' failed, running with rgl.useNU 60 0.145  
## 3 "Found the following significant warnings:\n\n Warn… 9 0.0218 
## 4 Warning: package ‘dplyr’ was built under R version 9 0.0218 
## 5 "bgc_hmm.c:241:31: warning: ‘, ’ directive writing " 4 0.00969
## 6 driver.c:381:26: warning: cast from pointer to int 4 0.00969
## 7 hash.c:144:5: warning: ‘strncpy’ specified bound 2 4 0.00969
## 8 RngStream.c:347:4: warning: ‘strncpy’ specified bo 4 0.00969
## 9 Warning: ‘__var_1_mmb.offset’ is used uninitialize 4 0.00969
## 10 /home/hornik/tmp/R.check/r-patched-gcc/Work/build/ 3 0.00726
## # … with 100 more rows 
```

Enter fullscreen mode Exit fullscreen mode

有趣的事实:从 CRAN 下载的 2019-03-19 RDS 的重新运行显示 79 对 158(这 79 个包没有神奇地重新提交)。这通常是 Windows 上的一个启动检查“打嗝”:

```
filter(det, Status == "WARNING") %>% 
  filter(grepl("significant", Output, ignore.case = TRUE)) %>%
  mutate(flavor_flav = case_when(
    grepl("devel", Flavor) ~ "devel",
    grepl("oldrel", Flavor) ~ "oldrel",
    TRUE ~ "current"
  )) %>% 
  filter(flavor_flav != "devel") %>% 
  mutate(Output = gsub("Found the following significant warnings:\n ", "", trimws(Output))) %>% 
  mutate(bits = substring(trimws(Output), 1, 50)) %>% 
  filter(grepl("S3 methods", bits)) %>% 
  count(bits, Flavor, sort=TRUE) %>% 
  mutate(pct = n/sum(n))
## # A tibble: 6 x 4
## bits Flavor n pct
## <chr> <chr> <int> <dbl>
## 1 "Warning: S3 methods '[.fun_list'… r-oldrel-windows-i… 79 0.485  
## 2 "Warning: S3 methods '[.fun_list'… r-release-windows-… 79 0.485  
## 3 Warning: S3 methods 'as_mapper.ch… r-oldrel-windows-i… 2 0.0123 
## 4 Warning: S3 methods '.DollarNames… r-oldrel-windows-i… 1 0.00613
## 5 Warning: S3 methods 'as.promise.F… r-release-windows-… 1 0.00613
## 6 Warning: S3 methods 'format.stati… r-oldrel-windows-i… 1 0.00613 
```

Enter fullscreen mode Exit fullscreen mode

没错。因此，我们必须忽略其中的一部分，但不是全部(记住，这些是测试计数，而不是封装计数)。

也许我们会有更好的运气~~开始关注“错误”的分析~~！

### 错误肯定是人类在假设有缺陷时产生的

让我们来看看这些错误:

```
filter(det, Status == "ERROR") %>%
  mutate(flavor_flav = case_when(
    grepl("devel", Flavor) ~ "devel",
    grepl("oldrel", Flavor) ~ "oldrel",
    TRUE ~ "current"
  )) %>% 
  filter(flavor_flav != "devel") %>% 
  mutate(bits = substring(trimws(Output), 1, 20)) %>% 
  count(bits, sort=TRUE) %>% 
  print(n=66)
## # A tibble: 66 x 2
## bits n
## <chr> <int>
## 1 Installation failed. 437
## 2 "Running examples in " 424
## 3 Package required but 213
## 4 Running ‘testthat.R’ 150
## 5 Packages required bu 102
## 6 Running 'testthat.R' 58
## 7 Package required and 31
## 8 Running ‘test-all.R’ 17
## 9 Packages required an 12
## 10 Errors in running co 7
## 11 Running ‘spelling.R’ 6
## 12 Running ‘test-that.R 5
## 13 Running 'activate_te 4
## 14 Running 'test-all.R' 4
## 15 Running ‘activate_te 3
## 16 Running 'Bernstein-E 2
## 17 Running 'Class+Meth. 2
## 18 Running 'dist_matrix 2
## 19 Running 'Frechet-tes 2
## 20 Running 'spelling.R' 2
## 21 Running 'test-as-dgC 2
## 22 Running 'valued_fit. 2
## 23 Running ‘allier.R’ [ 2
## 24 Running ‘aunitizer.R 2
## 25 Running ‘autoprint.R 2
## 26 "Running ‘bdstest.R’ " 2
## 27 Running ‘build-tools 2
## 28 Running ‘exporting-m 2
## 29 Running ‘restfulr_un 2
## 30 Running ‘run_test.R’ 2
## 31 Running ‘test_change 2
## 32 Running ‘test-as-dgC 2
## 33 Running ‘testGBHProc 2
## 34 Running ‘tests-nlgam 2
## 35 Running ‘testthat-pr 2
## 36 Running ‘TimeIn_Data 2
## 37 Running '000.session 1
## 38 Running '001.setupEx 1
## 39 Running 'bug1.R' [1s 1
## 40 "Running 'failure.R' " 1
## 41 Running 'fold.R' [5s 1
## 42 Running 'Rgui.R' [3s 1
## 43 Running 'Rgui.R' [4s 1
## 44 Running 'SP500-ex.R' 1
## 45 Running ‘aggregate.R 1
## 46 Running ‘as.edgelist 1
## 47 "Running ‘bdstest.R’\n" 1
## 48 Running ‘Class+Meth. 1
## 49 "Running ‘config.R’\n " 1
## 50 Running ‘cX-ui-funct 1
## 51 Running ‘DevEvalFile 1
## 52 "Running ‘emTests.R’\n" 1
## 53 "Running ‘group01.R’ " 1
## 54 Running ‘loop_genera 1
## 55 Running ‘LTS-special 1
## 56 Running ‘rsolr_unit_ 1
## 57 "Running ‘run-all.R’\n" 1
## 58 Running ‘runTests.R’ 1
## 59 "Running ‘sleep.R’\n " 1
## 60 Running ‘SP500-ex.R’ 1
## 61 Running ‘test_bccaq. 1
## 62 Running ‘test_scs.R’ 1
## 63 Running ‘test-cluste 1
## 64 "Running ‘test.R’\nRun" 1
## 65 Running ‘tests.R’ [4 1
## 66 Running ‘testthat.r’ 1 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做更多的调查，所以我们来分组:

```
filter(det, Status == "ERROR") %>%
  mutate(flavor_flav = case_when(
    grepl("devel", Flavor) ~ "devel",
    grepl("oldrel", Flavor) ~ "oldrel",
    TRUE ~ "current"
  )) %>% 
  filter(flavor_flav != "devel") %>% 
  mutate(Output = trimws(Output)) %>% 
  mutate(output_grp = case_when(
    grepl("^Running ", Output) ~ "Test/example run",
    grepl("^Installation fail", Output) ~ "Install failed",
    grepl("Package[s]* requir", Output) ~ "Missing pacakge(s)",
    grepl("Errors in running code in vig", Output) ~ "Vignette issue",
    TRUE ~ Output
  )) %>% 
  count(output_grp, sort=TRUE)
## # A tibble: 4 x 2
## output_grp n
## <chr> <int>
## 1 Test/example run 743
## 2 Install failed 437
## 3 Missing pacakge(s) 358
## 4 Vignette issue 7 
```

Enter fullscreen mode Exit fullscreen mode

好多了。现在，让我们看看*在哪里*那些是:

```
filter(det, Status == "ERROR") %>%
  mutate(flavor_flav = case_when(
    grepl("devel", Flavor) ~ "devel",
    grepl("oldrel", Flavor) ~ "oldrel",
    TRUE ~ "current"
  )) %>% 
  filter(flavor_flav != "devel") %>% 
  mutate(Output = trimws(Output)) %>% 
  mutate(output_grp = case_when(
    grepl("^Running ", Output) ~ "Test/example run",
    grepl("^Installation fail", Output) ~ "Install failed",
    grepl("Package[s]* requir", Output) ~ "Missing package(s)",
    grepl("Errors in running code in vig", Output) ~ "Vignette issue",
    TRUE ~ Output
  )) %>% 
  filter(!grepl("solaris", Flavor)) %>% # I love ya Solaris, but you're not relevant anymore
  count(output_grp, Flavor, sort=TRUE) %>% 
  mutate(pct = n/sum(n))
## # A tibble: 16 x 4
## output_grp Flavor n pct
## <chr> <chr> <int> <dbl>
## 1 Install failed r-oldrel-osx-x86_64 256 0.197  
## 2 Test/example run r-oldrel-windows-ix86+x86_64 218 0.168  
## 3 Missing package(s) r-oldrel-osx-x86_64 156 0.120  
## 4 Missing package(s) r-release-osx-x86_64 133 0.102  
## 5 Test/example run r-oldrel-osx-x86_64 110 0.0847 
## 6 Test/example run r-release-osx-x86_64 80 0.0616 
## 7 Install failed r-release-windows-ix86+x86_64 73 0.0562 
## 8 Test/example run r-patched-linux-x86_64 57 0.0439 
## 9 Test/example run r-release-linux-x86_64 57 0.0439 
## 10 Install failed r-oldrel-windows-ix86+x86_64 46 0.0354 
## 11 Test/example run r-release-windows-ix86+x86_64 46 0.0354 
## 12 Install failed r-release-osx-x86_64 36 0.0277 
## 13 Missing package(s) r-oldrel-windows-ix86+x86_64 19 0.0146 
## 14 Missing package(s) r-release-windows-ix86+x86_64 5 0.00385
## 15 Vignette issue r-release-osx-x86_64 4 0.00308
## 16 Vignette issue r-oldrel-osx-x86_64 3 0.00231 
```

Enter fullscreen mode Exit fullscreen mode

让我们戳得更深一点，但让我们也意识到这样一个事实，即“oldrel”上的一些(许多)错误是由于像这样的条件[造成的](https://www.r-project.org/nosvn/R.check/r-oldrel-osx-x86_64/tidystats-00install.html)，其中包指定它只能在 r 的 release++版本中使用。所以我们*有点*必须对每个错误都使用 Columbo 或排除“oldrel”(我们将做后者，因为这篇文章已经很长了)，我们也应该忽略丢失的包，因为这很可能是一个 CRAN 问题。

```
filter(det, Status == "ERROR") %>%
  mutate(flavor_flav = case_when(
    grepl("devel", Flavor) ~ "devel",
    grepl("oldrel", Flavor) ~ "oldrel",
    TRUE ~ "current"
  )) %>% 
  filter(!(flavor_flav %in% c("oldrel", "devel"))) %>% 
  filter(!grepl("solaris", Flavor)) %>%  
  mutate(Output = trimws(Output)) %>% 
  mutate(output_grp = case_when(
    grepl("^Running ", Output) ~ "Test/example run",
    grepl("^Installation fail", Output) ~ "Install failed",
    grepl("Package[s]* requir", Output) ~ "Missing package(s)",
    grepl("Errors in running code in vig", Output) ~ "Vignette issue",
    TRUE ~ Output
  )) %>% 
  filter(output_grp != "Missing package(s)") %>% 
  distinct(Package)
## # A tibble: 254 x 1
## Package          
## <chr>            
## 1 AER              
## 2 archdata         
## 3 atlantistools    
## 4 BAMBI            
## 5 biglmm           
## 6 biglm            
## 7 BIOMASS          
## 8 blockingChallenge
## 9 broom            
## 10 clusternomics    
## # … with 244 more rows 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个目标包列表(“失败”中大约有 26 个)，这些包很有可能存在问题。我们将让比我更实际的数据科学家为他们计算出依赖树成员数，然后确定适当的特征和模型选择，以得出一个更合理的“风险度量”。

### 还有一件事

你读过关于 03-18 RDS 和 03-19 RDS 有很大不同的部分吗？是的，所以也许任何模型都需要运行几次，或者在一段时间内收集数据，以确保我们使用尽可能干净的数据集。你知道，问一些实用的数据科学问题，比如:

*   我可以获得哪些数据？
*   会帮我解决问题吗？
*   够不够？
*   数据质量够好吗？

### 鳍

永远不要为了迎合你喜欢的信息而设计分析。

假设*事关*。分析设置*事项*。领域专长*事关*。数据集知识*很重要*。

我可以把它总结为*正念很重要*，如果你以这种方式对待一个项目，那么就向前迈进，把你完成目标所需的所有东西都收集起来。