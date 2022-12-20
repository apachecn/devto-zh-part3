# 泽塔离线评委

> 原文：<https://dev.to/aaahmedaa/zeta-offline-judge-11bn>

希腊字母表中第六个字母🐉是编程竞赛的离线评委🏆。
Githup 链接:[https://github.com/aa-ahmed-aa/Zeta](https://github.com/aa-ahmed-aa/Zeta)T3】

## 💂特征

*   参赛者自动排名。
*   可以为问题添加说明。
*   c/c++解决方案的自动判断(java 即将推出)。

## 🚩评判和排名

#### 📌判断

评判一个问题后，参赛者可以得到 4 个答案中的一个:-

*   AC = >已接受
*   WA = >回答错误
*   TLE = >超过时间限制
*   CE = >编译器错误

[![Alt text](img/e60170cd3403a0231fbb5ca07869ddb1.png "Judge")](https://github.com/aa-ahmed-aa/Zeta/blob/master/screenshots/judge.PNG)

**绿色判断表示建议对该子项目使用这种判断方式**

*   自动的🆚人工判断
    *   自动判断它将运行竞争对手的解决方案，并将其与针对该问题给出的测试用例进行比较，并对用户提交的问题做出响应。
    *   手动判断手动将响应添加到此提交，因为有时用户没有遵守竞赛规则，从 i/o 流而不是文件中读取或写入。

#### 🚖等级

*   提交等级提交是根据时间和问题等级来排列的，因此等级方程式是:-

```
 Tstart - Tsub
 submition_rank =   problem_rank    -    _________________________    -    (    wrongAnswerCount   *   5    )
 60
 Minimum Submmition Rank is = 40% Of problem rank 
```

*   用户排名

    *   根据已接受问题排名的最高总和对用户进行排名。
    *   记分板时间是用户上次提交的时间。

    [![Alt text](img/ce3e390c96552048d3095a9e733f6285.png "Judge")](https://github.com/aa-ahmed-aa/Zeta/blob/master/screenshots/scoreboard.PNG)

    **时间是用户最后一次提交的时间**

## 🚀先决条件

*   xampp(如果你对命名为 String 的类有任何问题，请根据 PHP 版本阅读此处以解决问题:[https://github.com/cakephp/cakephp/issues/7573](https://github.com/cakephp/cakephp/issues/7573)
*   GCC 编译器(我用了 [Mingw](https://nuwen.net/mingw.html) )

## 🔥装置

*   下载回购`git clone https://github.com/aa-ahmed-aa/Zeta`或通过下载按钮。
*   将`Database/zeta_4.sql`中的数据库文件导入到 phpmyadmin 中。
*   转到`app/Config/database.php`并将用户名和密码更新到您的凭证
*   下载 [Mingw](https://nuwen.net/mingw.html) 编译器并复制到你的 Zeta 路径。
*   现在转到[http://localhost/Zeta](http://localhost/Zeta)和用户名:ahmedkhaled，密码:ahmedkhaled123 登录(这是默认的管理员帐户，你可以用它来添加其他管理员和参赛者，然后删除它)

> # Before you start the game, you should set the start time of the game. This time is used to arrange questions and players on the scoreboard

## 🚧贡献

> 编译运行都是基于这个包([宿舍](https://github.com/aa-ahmed-aa/Dorm))所以可以随便看看。

如果您发现任何问题，请随时提出您发现的任何错误或您认为我们可以添加到泽塔的功能，如果您在安装泽塔时有任何问题，请随时通过**[ahmedkhaled36@hotmail.com](mailto:ahmedkhaled36@hotmail.com)**与我联系