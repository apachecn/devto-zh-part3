# Django 2.2 测试驱动开发

> 原文：<https://dev.to/highcenburg/django-2-2-test-driven-development-576o>

在这本书的帮助下，我的第一个 Django 网站有了 95%的投入，一些东西进入了我的脑海。测试。

最近，我偶然发现了 Django 的创始人之一雅各布·卡普兰-莫斯的一句话:“没有测试的代码会像设计的那样被破坏。”这让我意识到我不擅长设计，这促使我学习如何测试。

### "编写测试是很重要的，因为它自动化了确认代码按预期工作的过程."- [Django 适合初学者](https://djangoforbeginners.com/pages-app/)

我尝试了两个测试，因为显然我在第一个测试中遇到了问题，并且得到了一个错误:

```
AssertionError: 301 != 200
```

我使用的第一个测试是 [SimpleTestCase](https://docs.djangoproject.com/en/2.2/topics/testing/tools/#django.test.SimpleTestCase) 。它是 [unittest 的子类。增加一些功能的测试用例](https://docs.python.org/3/library/unittest.html#unittest.TestCase)

```
from django.test import SimpleTestCase

class PageTest(SimpleTestCase):

def test_portfolio_page_status_code(self):
    response = self.client.get("/portfolio")
    self.assertEquals(response.status_code, 200)

def test_blog_page_status_code(self):
    response = self.client.get("/blog")
    self.assertEqual(response.status_code, 200)

def test_home_page_status_code(self):
    response = self.client.get("")
    self.assertEquals(response.status_code, 200)

def test_quotes_page_status_code(self):
    response = self.client.get("/quotes/")
    self.assertEquals(response.status_code, 200)

```

但是追溯到:

```
======================================================================
FAIL: test_blog_page_status_code (personal_portfolio.tests.PageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/user/dir/personal_portfolio/tests.py", line 
11, in test_blog_page_status_code
self.assertEqual(response.status_code, 200)
AssertionError: 301 != 200

======================================================================
FAIL: test_portfolio_page_status_code 
(personal_portfolio.tests.PageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/Users/user/dir/personal_portfolio/tests.py", line 
7, in test_portfolio_page_status_code
self.assertEquals(response.status_code, 200)
AssertionError: 301 != 200

```

我去 StackOverflow 寻求帮助，这是我得到的消息:

[很可能你的站点确实返回了一个`301`重定向代码。发生这种情况的原因有很多，例如，如果它需要身份验证并为此目的重定向其他页面。如果是这样的话，一个解决方法是添加一个测试，登录后进入那个页面。如果没有更多关于你的站点如何工作(和代码)的信息，很难确定原因。](https://stackoverflow.com/a/56030700/11292732)

我告诉自己，一定有另一种方法来测试我的代码。我通读了这篇文章，测试了我的耐心，偶然发现了 [TransactionTestCase](https://docs.djangoproject.com/en/2.2/topics/testing/tools/#django.test.TransactionTestCase) ，它继承了 [SimpleTestCase](https://docs.djangoproject.com/en/2.2/topics/testing/tools/#django.test.SimpleTestCase) 来添加一些特定于数据库的特性。

```
from django.test import TransactionTestCase

class Personal_PortfolioTests(TransactionTestCase):

        def test_home_page_status_code(self):
        response = self.client.get('/')
        self.assertEqual(response.status_code,200)

        def test_portfolio_page_status_code(self):
        response = self.client.get('/portfolio/')
        self.assertEqual(response.status_code,200)

        def test_quotes_page_status_code(self):
        response = self.client.get('/quotes/')
        self.assertEqual(response.status_code,200)

        def test_blog_page_status_code(self):
        response = self.client.get('/blog/')
        self.assertEqual(response.status_code,200)

```

## 还有维奥拉。

[![test](img/e1f2dd7cea4e737bcf99052900358d7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jse_k5pe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://highcenburgtech.files.wordpress.com/2019/05/test.png)

Django 的 [`TestCase`](https://docs.djangoproject.com/en/2.2/topics/testing/tools/#django.test.TestCase "django.test.TestCase") 类是`TransactionTestCase`的一个更常用的子类，它利用数据库事务工具来加速在每次测试开始时将数据库重置为已知状态的过程。然而，这样做的后果是，一些数据库行为不能在 Django `TestCase`类中测试。例如，当使用 [`select_for_update()`](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#django.db.models.query.QuerySet.select_for_update "django.db.models.query.QuerySet.select_for_update") 时，您不能测试一个代码块是否在一个事务中执行。在这种情况下，你应该使用`TransactionTestCase`。

在这里阅读更多:[https://docs . django project . com/en/2.2/topics/testing/tools/# django . test . transactiontestcase](https://docs.djangoproject.com/en/2.2/topics/testing/tools/#django.test.TransactionTestCase)