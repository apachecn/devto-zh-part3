# 在 CircleCI 上用 Jest 测试分裂

> 原文：<https://dev.to/drazisil/test-splitting-with-jest-on-circleci-1i0c>

我们的一个客户在推特上问，“我如何在 [CircleCI](https://circleci.com/) 和 [Jest](https://jestjs.io/en/) 上设置测试分割？”

这在 [CircleCI 文档](https://circleci.com/docs/2.0/parallelism-faster-jobs/#running-split-tests)中有简要解释，但我能看出它可能会令人困惑的地方。

我不是最好的作者，所以我会让我的代码来说话。这应该也是代码文档中的一个很好的练习。

我的一部分`package.json`

```
 "scripts": {
    "test": "jest",
  },
  "jest": {
    "reporters": [
      "default",
      "jest-junit"
    ]
  },
  "jest-junit": {
    "outputDirectory": "test_results/junit"
  } 
```

Enter fullscreen mode Exit fullscreen mode

我的`.circleci/config.json`文件

```
version: 2
jobs:
  build:
    parallelism: 3
    docker:
      # I want to make sure I know exactly what version I'm using
      - image: circleci/node:10.15.3

    working_directory: ~/repo

    steps:
      - checkout

      # Npm CI uses the package-lock.json file to ensure exact versions
      # This prevents changes and compromised upstream dependencies
      # from changing your project. It wipes the node_modules directory
      # so there is no need for a dependencies cache here.
      - run: npm ci

      # First we save the split test file names into an env
      # Then we pass that to jest
      - run:
          name: Run Jest tests using test splitting
          command: |
            TESTFILES=$(circleci tests glob "test/**/*.test.js" | circleci tests split --split-by=timings)
            npm test $TESTFILES

      - store_test_results:
          path: test_results

      - store_artifacts:
          path: test_results
          destination: test_results 
```

Enter fullscreen mode Exit fullscreen mode

希望这有所帮助！