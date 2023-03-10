# 深入 javascript 中的大观察者模式

> 原文：<https://dev.to/duranenmanuel/diving-into-the-great-observer-pattern-in-javascript-42fg>

*最初发表于[EnmaScript.com](https://enmascript.com/articles/2019/03/09/diving-into-the-great-observer-pattern-in-javascript)为了更好的阅读体验，去那里吧。*

设计模式是一种国际语言，它们也是处理问题的一种很好的方式，有时你能够注意到什么时候一个特定的模式是有用的，而其他时候你将不得不更加努力地去思考使用哪一个(或者你是否需要在那个特定的情况下使用一个)。在这篇文章中，我们将探索一种最实用和最强大的行为模式，**观察者模式**。

## 定义

观察者模式毫无疑问是最强大的设计模式之一，它的论点基于使用订阅机制来通知感兴趣的对象**(观察者)**当某个事件发生时，负责通知这种变化的对象通常被称为**主题**，通过这样做，对事件感兴趣的对象**(观察者)**不必定期检查事件是否已经发生或是否即将发生，他们只需订阅并等待事件/动作发生，这样他们就会得到通知

**这可能是观察者如何工作的简单直观表示:**

[![](img/1614c28aafa5e448aff0dbbea4811697.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8DCI8GNv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://enmascript.cimg/2019-03-09-observer-flow.png)

## 用用例举例说明

我总是说，了解事物如何工作的最好方法是亲自尝试，所以，让我们通过做一个例子来更好地理解观察者模式。

假设你在一家写新闻的公司工作，你被分配了以下任务:

创建一个应用程序，作者可以在线程上发布简短的信息，用户可以*关注*这些线程。

**具体要求:**

*   作家应该能够写一篇短文，然后张贴在一个线程(目前只有两个线程，技术和设计)
*   用户应该能够订阅线程
*   用户应该能够取消订阅线程

好的，首先，我们开始创建一个包含几个输入的部分，这样**作者**能够在线程**技术**和**设计**上发布信息，这两个输入将有单独的按钮来发布信息。

我们把这个文件叫做 **index.html:**

```
<h1>Threads</h1>

<!-- Writers will write Tech related posts here -->
<div class="thread tech">
    <input type="text" class="js-tech-text" placeholder="Write tech info here...">
    <button class="js-post-tech">Post on Tech</button>
</div>

<!-- Writers will write Design related posts here -->
<div class="thread design">
    <input type="text" class="js-design-text" placeholder="Write design info here...">
    <button class="js-post-design">Post on Design</button>
</div> 
```

以下是它的视觉表现(添加了一些 CSS):

[![](img/3e922ed3a092eb7790e85745788dca93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AA1J_oN8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://enmascript.cimg/2019-03-07-threads.png)

我们还将在同一个【index.html】的**中为*用户*添加另一个部分，这将在*线程*部分下面，在这里我们将模拟用户，并将显示一些按钮，以便他们可以订阅和取消订阅每个单独的线程。**

```
<h1>Users</h1>
<div class="user js-user-1">
    <div class="head">
        <h2>User 1</h1>

        <!-- This shows the current threads to which the user is subscribed -->
        <div class="status js-stat-tech">Unsubscribed to tech</div>
        <div class="status js-stat-design">Unsubscribed to design</div>
    </div>

    <!-- User 1 can subscribe and unsub from the threads -->
    <button class="js-sub-tech tech">Subscribe to tech</button>
    <button class="js-unsub-tech tech">Unsubscribe to tech</button>
    <button class="js-sub-design design">Subscribe to design</button>
    <button class="js-unsub-design design">Unsubscribe to design</button>

    <!-- The updates from each thread will be shown on the list below for user 1 -->
    <ul class="js-updates"></ul>
</div>

<div class="user js-user-2">
    <div class="head">
        <h2>User 2</h1>

        <!-- This shows the current threads to which the user 2 is subscribed -->
        <div class="status js-stat-tech">Unsubscribed to tech</div>
        <div class="status js-stat-design">Unsubscribed to design</div>
    </div>

    <!-- User 2 can subscribe and unsub from the threads -->
    <button class="js-sub-tech tech">Subscribe to tech</button>
    <button class="js-unsub-tech tech">Unsubscribe to tech</button>
    <button class="js-sub-design design">Subscribe to design</button>
    <button class="js-unsub-design design">Unsubscribe to design</button>

    <!-- The updates from each thread will be shown on the list below for user 2 -->
    <ul class="js-updates"></ul>
</div>

<div class="user js-user-3">
    <div class="head">
        <h2>User 3</h1>

        <!-- This shows the current threads to which the user 3 is subscribed -->
        <div class="status js-stat-tech">Unsubscribed to tech</div>
        <div class="status js-stat-design">Unsubscribed to design</div>
    </div>

    <!-- User 3 can subscribe and unsub from the threads -->
    <button class="js-sub-tech tech">Subscribe to tech</button>
    <button class="js-unsub-tech tech">Unsubscribe to tech</button>
    <button class="js-sub-design design">Subscribe to design</button>
    <button class="js-unsub-design design">Unsubscribe to design</button>

    <!-- The updates from each thread will be shown on the list below for user 3 -->
    <ul class="js-updates"></ul>
</div> 
```

再一次，这是一个使用 CSS 的整体外观的可视化表示:

[![](img/6028c3e770dd76c64af9a2eb6ed43704.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Coau7xVO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://enmascript.cimg/2019-03-07-users.png)

太好了，从视觉角度来看，一切似乎都被涵盖了，让我们看看我们的列表:

*   [x]作者应该能够写一篇短文，然后张贴在一个线程上(目前只有两个线程，技术和设计)
*   [x]用户应该能够订阅线程
*   [x]用户应该能够取消订阅线程

另外，用户可以看到他们当前是否订阅了某个特定的线程(他们名字右边的灰色方框，我认为显示这个会很好，这样例子的 UX 会更好)。

## 在 Javascript 中实现观察者模式

太棒了，我们有一个“漂亮”的界面，什么也不做， **YAY(？)**...现在，让我们严肃起来，添加一些 javascript，这些 javascript 将最终完成所有工作，并向我们展示如何实现 observer 模式来完成这项任务。

首先，我们将实现 main **Subject.js** 该文件将包含通知观察者所需的事件，在本例中是订阅用户的列表，该文件还将包含能够订阅和取消订阅线程的事件，该文件也可以称为 Publisher 或 EventManager。

**subjects/Subject.js** 看起来是这样的(阅读代码中的注释可以获得更好的解释):

```
class Subject {
    constructor() {
        /**
         * The list of threads observed based on each user's instance.
         * this will contain a list of observers.
         */
        this.observers = [];
    }

    isSubscribed(f) {
        /* Help us check if the observer for an user is already subscribed */
        return this.observers.filter(subscriber => subscriber === f).length;
    }

    subscribe(f) {
        /* Verifies that the user is not subscribed already */
        if (this.isSubscribed(f)) return;

        /* If the user is not subscribed adds the function to the list */
        this.observers.push(f);
    }

    unsubscribe(f) {
        /**
         * returns a new array of functions without the one passed as argument,
         * Basically unsubscribing the user from that thread.
         */
        this.observers = this.observers.filter(subscriber => subscriber !== f);
    }

    notify(data) {
        /**
         * notifies the user, it passes the data to each observer
         * set in the list so that it's updated.
         */
        this.observers.forEach(observer => observer.update(data));
    }
}

export default Subject; 
```

如果你现在有一点困惑，即使是在看完评论之后，也不要担心...随着我们继续前进，一切都将开始成形。

### 添加具体科目

酷！现在我们需要添加两个 subjectss 来扩展 main `Subject`类的功能，用户可以关注的每种类型的线程都有一个 subject。正如我们之前所说的，主题是在变化发生时通知观察者的对象。

第一个将是 **subjects/TechThread.js** ，实现它的一种方式如下:

```
import Subject from './Subject.js';

class TechThread extends Subject {
    constructor() {
        super();

        this.bindArticlePost();
    }

    bindArticlePost() {
        /* Saves the "Post on Tech" button as well as the input text */
        const postTechBtn = document.querySelector('.js-post-tech');
        const jsTechText = document.querySelector('.js-tech-text');

        /* notifies that new information was post when clicking the post button */
        postTechBtn.addEventListener('click', () => {
            this.notify(jsTechText.value);
        });
    }
}

export default TechThread; 
```

`DesignThread`类看起来完全一样，**subjects/design thread . js:**

```
import Subject from './Subject.js';

class DesignThread extends Subject {
    constructor() {
        super();
        this.bindArticlePost();
    }

    bindArticlePost() {
        const postDesignBtn = document.querySelector('.js-post-design');
        const jsDesignText = document.querySelector('.js-design-text');

        postDesignBtn.addEventListener('click', () => {
            this.notify(jsDesignText.value);
        });
    }
}

export default DesignThread; 
```

很简单，如果我们需要更多的主题，我们会继续将它们添加到这个文件夹中。

### 添加观察员

观察者基本上订阅主题，他们得到主题的一个实例，这样他们可以**订阅/取消订阅**，在我们的例子中，我们将创建`TechThreadObserver`和`DesignThreadObserver`，这些类将负责拥有一个更新方法，该方法将使用来自线程的信息更新用户的。

**observers/techthreadobserver . js**(查看评论了解解释):

```
class TechThreadObserver {
    /**
     * We get the subject techThread and the userId that will observe
     * that particular thread.
     */
    constructor(techThread, { userId }) {
        /* Container for each user based on the ID*/
        this.userContainer = document.querySelector(`.js-user-${userId}`);

        /* Section that will receive the updates from the threads */
        this.userUpdates = this.userContainer.querySelector('.js-updates');

        this._bindEvents(techThread);
    }

    _bindEvents(techThread) {
        /* These two buttons will allow us to add listeners to subscribe/unsubscribe */
        const subTechBtn = this.userContainer.querySelector('.js-sub-tech');
        const unsubTechBtn = this.userContainer.querySelector('.js-unsub-tech');

        /* little grey box that shows if the user is currently subscribed to Tech */
        const techSubStatus = this.userContainer.querySelector('.js-stat-tech');

        /* Add the listener to the button subscribe to tech */
        subTechBtn.addEventListener('click', e => {
            /* Subscribes to the thread */
            techThread.subscribe(this);

            /* Update the status of the user to reflect it's subscribed */
            techSubStatus.classList.add('active');
            techSubStatus.innerHTML = 'Subscribed to tech';
        });
        unsubTechBtn.addEventListener('click', e => {
            /* Unsubscribes to the thread */
            techThread.unsubscribe(this);

            /* Update the status of the user to reflect it's not subscribed */
            techSubStatus.classList.remove('active');
            techSubStatus.innerHTML = 'Unsubscribed to tech';
        });
    }

    /**
     * Function which will be in charge of updating each user when
     * a new post from tech is added, this function is invoked by the Subject class
     * when the notify method is called.
     */
    update(data) {
        const listElement = document.createElement('li');
        listElement.innerHTML = `[Tech Post] - ${data}`;

        this.userUpdates.appendChild(listElement);
    }
}

export default TechThreadObserver; 
```

类似地，我们创建了**observers/Design thread observer . js**，除了设计线程之外，它做了完全相同的事情。

```
class DesignThreadObserver {
    constructor(designThread, { userId }) {
        this.userContainer = document.querySelector(`.js-user-${userId}`);
        this.userUpdates = this.userContainer.querySelector('.js-updates');

        this._bindEvents(designThread);
    }

    _bindEvents(designThread) {
        const subDesignBtn = this.userContainer.querySelector('.js-sub-design');
        const unsubDesignBtn = this.userContainer.querySelector(
            '.js-unsub-design'
        );
        const designSubStatus = this.userContainer.querySelector(
            '.js-stat-design'
        );

        subDesignBtn.addEventListener('click', e => {
            designSubStatus.classList.add('active');
            designSubStatus.innerHTML = 'Subscribed to design';

            designThread.subscribe(this);
        });
        unsubDesignBtn.addEventListener('click', e => {
            designSubStatus.classList.remove('active');
            designSubStatus.innerHTML = 'Unsubscribed to design';

            designThread.unsubscribe(this);
        });
    }

    update(data) {
        const listElement = document.createElement('li');
        listElement.innerHTML = `[Design Post] - ${data}`;

        this.userUpdates.appendChild(listElement);
    }
}

export default DesignThreadObserver; 
```

因此，简而言之，当观察者点击每个用户的订阅按钮时，他们就订阅了相关线程中的作者发布的任何内容。

## 把所有的碎片粘在一起

现在，为了连接所有这些，我们只缺少拼图中的一块，我们的 **main.js** 文件，我们将能够在其中实例化主体和观察者..

```
import TechThread from './subjects/TechThread.js';
import DesignThread from './subjects/DesignThread.js';
import TechThreadObserver from './observers/TechThreadObserver.js';
import DesignThreadObserver from './observers/DesignThreadObserver.js';

function init() {
    /* We instanciate our subjects */
    const techThread = new TechThread();
    const designThread = new DesignThread();

    /**
     * Observers are instanciated for each user and we're
     * passing the subjects needed to each observer.
     **/
    new TechThreadObserver(techThread, { userId: 1 });
    new TechThreadObserver(techThread, { userId: 2 });
    new TechThreadObserver(techThread, { userId: 3 });

    new DesignThreadObserver(designThread, { userId: 1 });
    new DesignThreadObserver(designThread, { userId: 2 });
    new DesignThreadObserver(designThread, { userId: 3 });
}

init(); 
```

非常简单，每个观察者从相关主题接收实例，以便他们可以订阅和取消订阅，他们也接收`userId`，这使得用户的隔离成为可能，允许他们独立地与主题交互。

## 文件夹结构

所有实现就绪后，我们现在得到了这个文件夹结构:

```
├── main.js
├── index.html
├── index.css
├── subjects//
│   ├── TechThread.js
│   └── DesignThread.js
└── observers/
    ├── TechThreadObserver.js
    └── DesignThreadObserver.js 
```

基于这一点，每次你想添加一个新的观察者或主题时，你都要在他们的特定文件夹中进行，然后，你必须在 **main.js** 文件中创建实例，就这样。

## 见其行动

首先，为一个用户订阅一个线程，然后发布一个帖子，看看在您单击的用户框中会发生什么。

参见 [CodePen](https://codepen.io) 上恩马努尔·杜兰
([@恩马努尔·杜兰](https://codepen.io/enmanuelduran))EnmaScript.com 的 Pen [观察者模式中的代码示例。](https://codepen.io/enmanuelduran/pen/GeEvJo/)

你喜欢这篇关于设计模式的文章吗？如果你这样做了，你可能会喜欢我们不久前实现的[工厂模式](https://enmascript.com/articles/2018/10/05/javascript-factory-pattern)。

好了，来自未来的伟大开发者，这就是这篇文章的全部内容，如果你喜欢，我邀请你与你的朋友和同事分享...记住你可以在 twitter 上关注我。

下一集再见！