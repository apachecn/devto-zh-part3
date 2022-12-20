# 不开玩笑地写快照测试

> 原文：<https://dev.to/azu/write-snapshots-testing-without-jest-1pcg>

Jest 是快照测试的有用工具。快照测试在许多库中使用。例如，
使用快照测试。

*   [快照测试笑话](https://jestjs.io/docs/en/snapshot-testing)

我想不开玩笑地写快照测试。在这种情况下，我经常使用下面的模板代码。

## 写快照测试

例如，下面的代码在[摩卡](https://mochajs.org/)上工作。

*   输入:json
*   输出:json

`snapshot-test.js`:

```
const fs = require("fs");
const path = require("path");
const assert = require("assert");
const fixturesDir = path.join(__dirname, "snapshots");
// TODO: your transform function
const transform = require("../transform");

describe("Snapshot testing", () => {
  fs.readdirSync(fixturesDir)
    .map(caseName => {
      const normalizedTestName = caseName.replace(/-/g, " ");
      it(`Test ${normalizedTestName}`, function() {
        const fixtureDir = path.join(fixturesDir, caseName);
        const actualFilePath = path.join(fixtureDir, "input.json");
        const actualContent = JSON.parse(fs.readFileSync(actualFilePath, "utf-8"));
        const actual = transform(actualContent);
        const expectedFilePath = path.join(fixtureDir, "output.json");
        // Usage: update snapshots
        // UPDATE_SNAPSHOT=1 npm test
        if (!fs.existsSync(expectedFilePath) || process.env.UPDATE_SNAPSHOT) {
          fs.writeFileSync(expectedFilePath, JSON.stringify(actual, null, 4));
          this.skip(); // skip when updating snapshots - Mocha's function
          return;
        }
        // compare input and output
        const expected = JSON.parse(fs.readFileSync(expectedFilePath, "utf-8"));
        assert.deepStrictEqual(
          actual,
          expected,
          ` ${fixtureDir}  ${JSON.stringify(actual)} `
        );
      });
    });
}); 
```

### 设置快照输入文件

为`fixturesDir`创建每个测试用例目录，并将`input.json`作为输入，将`output.json`作为输出放入测试用例目录。

```
├── snapshot-test.js
└── snapshots
    ├── TEST_CASE_NAME_1
    │   ├── input.json
    │   └── output.json
    └── TEST_CASE_NAME_2
        ├── input.json
        └── output.json 
```

### 从 input.json 创建 output.json(快照)

快照测试从输入文件(`input.json`)创建输出文件(快照)。使用快照测试是有利的。

使用`UPDATE_SNAPSHOT=1`环境变量运行测试代码，然后在每个测试用例目录中创建`output.json`。

```
UPDATE_SNAPSHOT=1 npm test 
```

如果您已经检查了快照文件(`output.json`)并且它是有效的，则提交快照文件。

接下来，您可以通过运行`npm test`来检查快照:

```
npm test 
```

## 结论

我已经使用了上面的快照测试模式。
快照测试很容易通过放入输入用例来增加测试用例。

示例:

*   [主 azu/分句器处的分句器/夹具测试工具](https://github.com/azu/sentence-splitter/blob/master/test/fixtures-test.ts)
    *   使用解析的 JSON 作为快照
*   [ecmascript-proposals-JSON/snapshot-test . js at master azu/ecmascript-proposals-JSON](https://github.com/azu/ecmascript-proposals-json/blob/master/test/snapshot-test.js)
    *   使用脚本化 html 作为快照
*   [考题/剖析测验. js at master□□□□□□□□□□□□□□□□□□](https://github.com/textlint/textlint/blob/master/packages/%40textlint/markdown-to-ast/test/parsing-test.js)
    *   使用解析的降价 AST 作为快照
*   [主 azu 上的注释断言/快照测试/注释断言](https://github.com/azu/comment-to-assert/blob/master/test/snapshot-test.ts)
    *   使用转换后的 JS 代码作为快照