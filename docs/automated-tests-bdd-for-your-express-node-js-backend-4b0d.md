# 自动化测试——针对 Express Node JS 后端的 BDD

> 原文：<https://dev.to/teodeleanu/automated-tests-bdd-for-your-express-node-js-backend-4b0d>

这是我在这里的第一篇帖子，所以向[开发者到](https://dev.to)社区问好。
我是 Teo，有 10 多年的软件开发经验。我写这篇文章的原因是因为我热爱 BDD。这让我作为开发人员的生活轻松了 1000 倍。

只是因为我可以在产品达到 QA 时间之前修复和创建测试。
下面是三种最常见的自动化测试:
**单元测试:**测试一段代码(通常是一个对象或一个函数)，与其他部分隔离
**集成测试:**一起检查多个部分，例如，对照测试数据库测试数据库访问代码
**验收测试:**(也称为功能测试):自动测试整个应用程序，例如使用 Selenium 这样的工具自动运行浏览器。

**BDD 是类固醇的 TDD**

让我们先看看你如何进行 TDD -测试驱动的开发；你需要改变你的发展方式。以下是开始编写功能代码的步骤:

1.  从编写测试开始
2.  运行测试和任何其他测试。此时，您新添加的测试应该会失败。如果它在这里没有失败，它可能没有测试正确的东西，因此有一个错误。
3.  在这里，您开始处理特性:编写通过测试所需的最少代码。
4.  运行测试以检查新的测试通过
5.  选择性地重构您的代码
6.  回到第一步

学好可能需要一些努力，但是花时间会有很大的回报。TDD 项目通常得到 90-100%的代码覆盖率。

BDD——行为驱动的开发——可能是最大的困惑来源。当应用于自动化测试时，BDD 是编写优秀测试的一组最佳实践。BDD 可以而且应该与 TDD 和单元测试方法一起使用。
[实验室测试](https://www.npmjs.com/package/lab)——撰写本文时下载量为 26K 易于使用该库进行 BDD、TDD、单元和集成测试。先从安装开始:

```
npm i --save --only=dev lab 
```

为了向您展示一个示例，您可以在下面看到我们如何在一个完整的堆栈套件的端到端测试中测试注册和登录。

```
suite('[test][User][integrations]', () => {    
    //...some other setup code => read the full article for details
    test('User should be able to register', async (done) => { 
        const email = faker.internet.email();            
        const password = faker.internet.password();            
        const firstName = faker.name.firstName();            
        const lastName = faker.name.lastName();             
        let response = await request(app).
                    post('/api/users/signup').
                    send({email: email, password: password, name: lastName, surname: firstName}).
                    set('Accept', 'application/json');
        expect(response.status).to.equal(200);            
        const user = response.body;            
        expect(user.email).to.equal(email);       
        expect(user.password).to.equal(password);          
        expect(user.name).to.equal(lastName);           
        expect(user.surname).to.equal(firstName);            
        //Now let's see if the user is able to login            
        response = await request(app).
                    post('/api/users/login').
                    send({user:{email: email, password: password}}).
                    set('Accept', 'application/json');        
        expect(response.status).to.equal(200);    

    });}//end of test

});//end of suite 
```

这也可以从前端的角度应用，但将在后面的文章中详细说明。

> 与这个主题无关:在 [Appseed PRO](https://appseed.us/fullstack-apps-generator) 版本中，你也可以在 [VSCode](https://code.visualstudio.com/) 中轻松调试测试。你甚至可以免费获得以上所有代码，自己使用。麻省理工的执照。

每个测试套件都有一些回调，每次在套件之前和之后执行。

```
before(function () {    
    //initialize database    
    //create global variables    
    //mock data    
    //inject
});

after(async (done) => {    
    //do a global cleanup of the database
    //some async stuff here
    done();
}); 
```

此外，在每次测试之前和之后，您可以注册、创建新的实体并清理这些条目。

```
beforeEach(function () {    
    //get the models required for each subtest
    //example: create an account with signup before 
    //checking other features that requires login session
});

afterEach( function () {    
    //do something after each test
}); 
```

另一个你可以用来测试的库是[faker](https://www.npmjs.com/package/faker)——在撰写本文时每周下载 78 万——它为互联网创建随机数据。例如，您可以生成电子邮件、姓名、密码和位置地址。这很容易模拟真实环境，因此您不会在查找实际数据上浪费宝贵的时间。它也可以给你假身份证，但这不是我会推荐的东西。别忘了在你的 node js app 里安装 faker:

```
npm i --save --only=dev faker 
```

super test——在撰写本文时下载了 720K 帮助您跳过服务器注入，直接从测试中调用 HTTP 方法。拥有一个处理 HTTP 调用的简单端点有助于我们使用 supertest 库。

```
npm i --save --only=dev supertest 
```

要使用 supertest，您必须从 index.js 导出 express app 对象

我们现在只需运行命令:
就可以使用实验室测试了

`npm run test`

这为我们与 Travis 的 CI 和 CD 项目做了准备。2019 年的下一个必备。

PS:本文最早发表于 [appseed.us 博客](https://blog.appseed.us/automated-tests-bdd-adding-tests-with-lab-in-your-project-with-express/)。