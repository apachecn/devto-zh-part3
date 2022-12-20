# 我如何学习用 SeleniumBase 测试互联网

> 原文：<https://dev.to/dowenb/how-i-am-learning-to-test-the-internet-with-seleniumbase-32>

目标:我有一个简单的、雄心勃勃的、令人毛骨悚然的目标:学习如何“用 Python 来自动化所有的(测试)事情”。这是我迈向这个目标的第一部分。

就上下文而言，我认为自己是一名经验丰富的测试人员，但作为一名开发人员相对来说是一名新手。我参与了一个使用 Powershell 的成功的自动化项目，和一个使用。NET 框架和 Selenium。

## 去学 Python 吧！别的地方

在本系列中，我不会触及的是如何安装或使用 Python 的基础知识。这是一个很好的领域，如果这是您的追求，Python 文档是一个很好的起点。[https://docs.python.org/3/](https://docs.python.org/3/)

## 硒基

SeleniumBase 是一个测试框架，它包装了 Selenium 并扩展了 Pytest，还可以与 Nose test 一起工作。经过简短的评估，我选择了 SeleniumBase 作为我的 web 测试框架，原因如下:

*   进入门槛低，编写和运行第一个测试真的很容易
*   竞争主要基于 JavaScript (WebDriver.io 和 NightWatch.js)，我发现它们很不稳定
*   出于个人喜好，我想避免使用 Java。维护者通过 Twitter 和 Git 问题做出了非常积极的回应，帮助了我并增加了功能。我怎么强调这给了我多大的信心都不为过。
*   最后，也可能是最重要的，我马上就要选择另一个方向，并且得到了测试部的好心人的推荐。聊天 slack 社区利用 Python 和 SeleniumBase 开始我的自动化之旅，100 次谢谢！

我发现启动和运行 SeleniumBase 很容易，只需遵循他们自己的说明，所以我不打算介绍这个领域。[http://seleniumbase.com/](http://seleniumbase.com/)

## 互联网

当然，我可以，也已经尝试过针对客户项目创建一些自动化。但是我在发展技能方面遇到了一些问题:

1.  我不能与他人分享这项工作的细节，我的性质是保密的
2.  我仅限于解决给定项目的挑战
3.  事情在变化，所以我学到的很多东西都是关于维护对页面元素的引用

解决办法？互联网！不，不是互联网。[http://the-internet.herokuapp.com](http://the-internet.herokuapp.com)。

### 容易吧？

乍一看，独立示例的单个页面列表看起来应该非常容易用任何基于 Selenium 的框架实现自动化。结果是，至少对我来说，到目前为止我尝试自动化的每个例子都抛出了一些有趣的小挑战，我不得不花时间思考和研究。这是个好消息！因为这证明了它是磨练我技能的有用资源。

## 跟着一起走，上 GitHub

如果你想跟随我的旅程，请随时查看我的 GitHub 资源库:[https://GitHub . com/dowenb/selenium base-example/tree/master/the _ internet](https://github.com/dowenb/seleniumbase-example/tree/master/the_internet)。

## 例子至此

在撰写本文时，我已经对互联网上的 3 个例子进行了自动化测试。让我们仔细看看每一个。作为一些背景，我的工作包含在两个文件中:

*   互联网对象(页面对象模型)
*   互联网测试(测试，在 Pytest 中运行)

### Helper _url(路径)函数

```
def _url(path, prefix='http://'):
    base_url = 'the-internet.herokuapp.com'
    return prefix + base_url + path 
```

Enter fullscreen mode Exit fullscreen mode

使用这个助手功能，我可以在一个地方管理我的基本 URL。这可能有点复杂，但你会明白为什么我在下面的第一个例子中这样做，基本认证。

### 基本认证

```
#the_internet_objects.py class BasicAuth(object):
    url = _url('/basic_auth', prefix='http://admin:admin@')
    success_text = '#content > div > p' 
```

Enter fullscreen mode Exit fullscreen mode

对于这个例子，我必须解决的基本问题是如何传递基本身份验证的用户名和密码。我明白，我选择的方法离生产还很远。如果我发现自己在测试使用基本验证的预生产代码，祝你快乐。

```
#the_internet_test.py from seleniumbase import BaseCase
from the_internet_objects import BasicAuth, BrokenImages, CheckBoxes

class MyTestClass(BaseCase):

    def test_basic_auth(self):
        self.open(BasicAuth.url)
        self.assert_text(
            'Congratulations! You must have the proper credentials.',
            BasicAuth.success_text
            ) 
```

Enter fullscreen mode Exit fullscreen mode

注意从 BaseCase、self.open 和 self.assert 继承的类。有关基本 SeleniumBase 功能的更多详细信息，请阅读文档中的示例:[https://github . com/selenium base/selenium base/tree/master/examples](https://github.com/seleniumbase/SeleniumBase/tree/master/examples)

有一个问题，如果你不把你的测试定义命名为‘test _ thing’，Pytest 就找不到它。

### 破碎的画面

```
#the_internet_objects.py class BrokenImages(object):
    url = _url('/broken_images')
    image1 = '#content > div > img:nth-child(2)'
    image2 = '#content > div > img:nth-child(3)'
    image3 = '#content > div > img:nth-child(4)' 
```

Enter fullscreen mode Exit fullscreen mode

我再次使用了页面对象模型模式。按照 SeleniumBase 示例，我所做的就是存储字符串，这些字符串是我将在测试中找到的元素的选择器。我在这里没有返回对象，但是我抽象了定位器，所以当我测试的 web 应用程序更新时，我有一个“真实的单一来源”来更新，并且我的引用中断。

```
#the_internet_test.py from seleniumbase import BaseCase
from the_internet_objects import BasicAuth, BrokenImages, CheckBoxes

class MyTestClass(BaseCase):
    def test_broken_images(self):
        self.open(BrokenImages.url)
        self.assertGreater(int(self.get_attribute(BrokenImages.image1, 'naturalWidth')), 0)
        self.assertGreater(int(self.get_attribute(BrokenImages.image2, 'naturalWidth')), 0)
        self.assertGreater(int(self.get_attribute(BrokenImages.image3, 'naturalWidth')), 0) 
```

Enter fullscreen mode Exit fullscreen mode

这是我第一次使用 get_attributes 函数进行实验。我采用了我在网上搜索挑战时找到的一个等效方法。将 naturalWidth 设置为 0 似乎是一种被广泛接受的判断图像是否破损的方法。请注意，这是一个失败的测试，这是意料之中的，其中两个图像被破坏了，我断言它们不应该被破坏。

我能想到至少有两件事我没有做，以使这个例子优雅:

*对损坏图像的检查可以抽象成一个帮助器方法
*代替对三个图像的静态引用，所有图像都可以被迭代

如果你有时间做这些改进，请随时给我发一个拉请求，或者收集我的例子，为你自己做这件事。不管怎样，让我知道！

### 复选框

```
#the_internet_objects.py class CheckBoxes(object):
    url = _url('/checkboxes')
    checkbox1 = '#checkboxes > input[type="checkbox"]:nth-child(1)'
    checkbox2 = '#checkboxes > input[type="checkbox"]:nth-child(3)' 
```

Enter fullscreen mode Exit fullscreen mode

这里没有什么令人兴奋的，只是另一个基本的对象模型。唯一的问题是复选框 2 是第三个孩子，而不是第二个。我认为这是因为第二个子项是复选框 1 的文本标签。明信片上的答案。

```
#the_internet_test.py from seleniumbase import BaseCase
from the_internet_objects import BasicAuth, BrokenImages, CheckBoxes

class MyTestClass(BaseCase):

    def isChecked(self, checkBox):
        try:
            self.get_attribute(checkBox, 'checked')
            return True
        except:
            return False

    def test_checkboxes(self):
        self.open(CheckBoxes.url)
        #checkbox1 should default unchecked, and a click should leave it checked
        self.assert_false(self.isChecked(CheckBoxes.checkbox1), 'Checkbox1 should be unchecked')
        self.click(CheckBoxes.checkbox1)
        self.assert_true(self.isChecked(CheckBoxes.checkbox1), 'Checkbox1 should be unchecked')
         #checkbox2 should default checked, and a click should leave it unchecked
        self.assert_true(self.isChecked(CheckBoxes.checkbox2), 'Checkbox2 should be checked')
        self.click(CheckBoxes.checkbox2)
        self.assert_false(self.isChecked(CheckBoxes.checkbox2), 'Checkbox2 should be unchecked') 
```

Enter fullscreen mode Exit fullscreen mode

与破碎图像示例不同，这里我创建了一个助手方法。我在用。get_attribute()，这一次我需要解决这样一个问题:如果没有找到属性，方法会抛出一个例子。结果是，当复选框被选中和取消选中时，元素中的“checked”属性被添加和删除。

如果你认为我可以用复选框测试更有趣的东西，或者用一种更优雅的方式迭代它们，请告诉我！

## 感谢

非常感谢 SeleniumBase 的人们，他们可以在 Twitter 上找到[https://twitter.com/SeleniumBase](https://twitter.com/SeleniumBase)T2【再次向测试部[https://www.ministryoftesting.com](https://www.ministryoftesting.com)的人们大声欢呼，感谢他们鼓舞人心的社区和出色的资源收集。
最后，感谢戴夫·海夫纳、[http://davehaeffner.com/](http://davehaeffner.com/)，感谢互联网[https://github.com/tourdedave/the-internet](https://github.com/tourdedave/the-internet)

最初发布于我的个人博客:
[https://www . dowen . me . uk/how-I-am-learning-to-test-the-internet-with-selenium base/](https://www.dowen.me.uk/how-I-am-learning-to-test-the-internet-with-seleniumbase/)