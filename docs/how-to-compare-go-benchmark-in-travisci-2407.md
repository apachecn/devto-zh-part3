# 如何比较 TravisCI 中的围棋基准

> 原文：<https://dev.to/dannypsnl/how-to-compare-go-benchmark-in-travisci-2407>

虽然文章是为 Go，但你仍然可以为别人的语言使用概念部分。

首先，我们创建一个名为`bench_compare.sh` :
的脚本

```
if ["${TRAVIS_PULL_REQUEST_BRANCH:-$TRAVIS_BRANCH}" != "master"]; then REMOTE_URL="$(git config --get remote.origin.url)";
    cd ${TRAVIS_BUILD_DIR}/.. && \
    git clone ${REMOTE_URL} "${TRAVIS_REPO_SLUG}-bench" && \
    cd "${TRAVIS_REPO_SLUG}-bench" && \
    git checkout master && \
    go test -bench . ./... > master_bench.out && \
    git checkout ${TRAVIS_COMMIT} && \
    go test -bench . ./... > current_bench.out && \
    go get golang.org/x/tools/cmd/benchcmp && \
    benchcmp master_bench.out current_bench.out;
fi 
```

然后我通常会在本节的`after_success`，`.travis.yml` :
执行它

```
script:
  # testing part
after_success:
  # ignore
  - bash ./bench_compare.sh 
```

附:写下`script`部分只是想告诉你它位于那个缩进处

现在我来解释一下剧本，基本上我们先偷看`${TRAVIS_PULL_REQUEST_BRANCH:-$TRAVIS_BRANCH}`不是`master`。为了确保我们不在`master`分公司，如果我们不在`master`，那么我们开始比较基准结果。这里有一点，`$(git config --get remote.origin.url)`是必选的，你可以写`git checkout master`知道为什么。然后我们检查`master`和`${TRAVIS_COMMIT}`(我想这个变量已经告诉你是什么了)，进行基准测试并存储它们。最后一步我们下载`golang.org/x/tools/cmd/benchcmp`进行比较，并比较两个结果文件。

要了解更多信息，您可以参考:

*   [特拉维斯默认变量](https://docs.travis-ci.com/user/environment-variables/#default-environment-variables)
*   [godoc.org/golang.org/x/tools/cmd/benchcmp](//godoc.org/golang.org/x/tools/cmd/benchcmp)

感谢阅读