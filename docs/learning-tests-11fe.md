# 学习测试

> 原文：<https://dev.to/taylorsilva/learning-tests-11fe>

我正在读肯特·贝克的《TDD By Example》,其中一个红色的条形图案引起了我的共鸣。这叫做学习测试。

情况是这样的。您有一个正在实现的新库或框架。您可以编写一些学习测试，而不是一头扎进实现的 TDD 中。这些测试旨在验证您期望第三方工具如何为您工作。我认为这是在测试你的应用程序和第三方工具之间的契约。

这是我用 github.com/aclindsa/ofxgo 做的一个例子。

现在这个库做了很多事情，而我只关心其中的一小部分。这个库可以与 OFX 服务器交互，但是我现在使用它的原因是为了解析已经下载到本地文件系统的 OFX 文件。

```
package main

import (
    . "github.com/onsi/gomega"
    "testing"
)

func TestReadLocalOFX(t *testing.T) {
    g := NewGomegaWithT(t)  
} 
```

所以在浏览了一些函数之后，我想使用这个 [`ParseResponse()`](https://github.com/aclindsa/ofxgo/blob/3e8a9c5a5382dccc0fd89b4d869e2202a48e8a66/response.go#L275) 函数。

我想在解析后断言一些东西，以验证它是否工作。查看自述文件中的示例，看起来我可以解析 OFX 文件中包含了多少家银行。

```
func TestReadLocalOFX(t *testing.T) {
    g := NewGomegaWithT(t)

    g.Expect(len(banks)).To(Equal(1))
} 
```

现在我需要学习如何获得那个`banks`变量。

从`ofxgo`开始，我不需要初始化任何东西，我可以直接调用`ParseResponse()`并传入一个打开的文件。这似乎是很好的第一步；解析 OFX 文件！

```
import (
    "github.com/aclindsa/ofxgo"
    . "github.com/onsi/gomega"
    "testing"
)

func TestReadLocalOFX(t *testing.T) {
    g := NewGomegaWithT(t)

    resp, err := ofxgo.ParseResponse(ofxFile)

    g.Expect(len(banks)).To(Equal(1))
} 
```

我还需要一份 OFX 的文件来做测试。所以我去我的银行网站下载了一个 OFX/QFX 文件，并把它放在与我的测试相同的目录下。我现在可以从我的测试中打开文件:

```
func TestReadLocalOFX(t *testing.T) {
    g := NewGomegaWithT(t)

    ofxFile, err := os.Open("ofxFile.qfx")
    g.Expect(err).ShouldNot(HaveOccurred())

    resp, err := ofxgo.ParseResponse(ofxFile)
    g.Expect(err).ShouldNot(HaveOccurred())

    g.Expect(len(banks)).To(Equal(1))
} 
```

现在我可以从`ParseResponse()`的`*Reponse`中得到银行变量。

```
func TestReadLocalOFX(t *testing.T) {
    g := NewGomegaWithT(t)

    ofxFile, err := os.Open("ofxFile.qfx")
    g.Expect(err).ShouldNot(HaveOccurred())

    resp, err := ofxgo.ParseResponse(ofxFile)
    g.Expect(err).ShouldNot(HaveOccurred())

    banks := resp.Bank
    g.Expect(len(banks)).To(Equal(1))
} 
```

现在，如果我运行我的测试，它应该工作！

```
=== RUN   TestReadLocalOFX
--- FAIL: TestReadLocalOFX (0.00s)
....    
        Expected error:
            <*errors.errorString | 0xc000091050>: {
                s: "OFX SECURITY header not NONE",
            }
            OFX SECURITY header not NONE
        not to have occurred
FAIL
FAIL    ofxreader   0.018s 
```

失败了！这个测试已经教会了我一些关于这个库的东西。它期望这个`OFX SECURITY header`被设置为`NONE`。在我的 OFX 档案中，我看到了这个标题:

```
OFXHEADER:100
DATA:OFXSGML
VERSION:102
SECURITY:TYPE1
ENCODING:USASCII
CHARSET:1252
COMPRESSION:NONE
OLDFILEUID:NONE
NEWFILEUID:NONE 
```

它说安全是`TYPE1`。我决定在 repo 中搜索这个错误，发现了这个:[https://github . com/aclindsa/of xgo/blob/3 E8 a9 C5 a 5382 DCC c0 FD 89 B4 d 869 e 2202 a 48 E8 a66/response . go # L90-L92](https://github.com/aclindsa/ofxgo/blob/3e8a9c5a5382dccc0fd89b4d869e2202a48e8a66/response.go#L90-L92)

该库仅在安全标题设置为`NONE`时工作。我想把这些知识加入到我的测试中，这样我就不会忘记了。

```
import (
    "github.com/aclindsa/ofxgo"
    . "github.com/onsi/ginkgo"
    . "github.com/onsi/gomega"
    "io/ioutil"
    "strings"
    "testing"
)

func TestReadLocalOFX(t *testing.T) {
    g := NewGomegaWithT(t)

    By("setting OFX security header to NONE")
    file, err := ioutil.ReadFile("ofxFile.qfx")
    g.Expect(err).ShouldNot(HaveOccurred())
    newOFXFile := strings.Replace(string(file), "SECURITY:TYPE1", "SECURITY:NONE", 1)

    ofxFile := strings.NewReader(newOFXFile)

    resp, err := ofxgo.ParseResponse(ofxFile)
    g.Expect(err).ShouldNot(HaveOccurred())

    banks := resp.Bank
    g.Expect(len(banks)).To(Equal(1))
} 
```

现在如果我运行测试:

```
=== RUN   TestReadLocalOFX
STEP: setting OFX security header to NONE
--- PASS: TestReadLocalOFX (0.01s)
PASS
ok      ofxreader   0.022s 
```

它过去了！这是这个图书馆的一个学习测试。

我希望在工作中把学习测试带到我的团队，因为我认为这将有助于在所有团队成员中传播知识，并成为有用的参考点。

你在工作中使用学习测试吗？他们对你和你的团队有帮助吗？