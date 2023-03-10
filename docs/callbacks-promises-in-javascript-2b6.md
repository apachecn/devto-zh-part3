# Javascript 中的回调和承诺

> 原文：<https://dev.to/farahanjum/callbacks-promises-in-javascript-2b6>

Javascript 是一种单线程异步语言。那是什么意思？这意味着它不能同时处理多项任务，但可以暂停一项任务的执行来做其他事情，并在任务之间来回切换，以最大限度地提高性能。这是有意义的，因为 javascript 是一种浏览器语言，浏览器需要进行网络调用，这是任何 web 应用程序速度的最大瓶颈之一。

很多时候我们想做的事情是一个函数的结果决定下一个函数的输出，等等。例如，假设我们想让一个用户登录，给出他们的用户名和密码。因此，首先，我们检查用户名和密码是否匹配，只有当它们匹配时，我们才获取用户的配置文件，只有当用户的配置文件被正确获取时，我们才让他们登录。如果流程的任何一步失败，我们不会执行下一步并取消它。

为了实现这一点，开发人员习惯于使用回调函数的概念。顾名思义，回调函数只有在前一个被调用函数的执行结束时才会被执行。

因此，为了实现我们登录用户的目标，我们应该这样做:

```
getUser(username, password, (err, user) => {
    if(err) console.log("Error fetching the user");
    else {
        getUserProfile(user, (err, profileData) => {
            if(err) console.log("error fetching user profile");
            else {
                logUserIn(profileData, (err, result) => {
                    if(err) console.log("Error logging in the user");
                    else console.log("User successfully logged in");                                  
                })
            }
        })
    }
});

 getUser = (username, pwd, callback) => {
    let user = db.getUser(username, pwd);
    if(user) callback(null, user);
    else callback(new Error("username and pwd dont match"));
}

getUserProfile = (user, callback) => {
    let profileData = db.getUserProfile(user);
    if(profileData) callback(null, profileData);
    else callback(new Error("Profile couldnt be fetched"));
} 
```

这种方法有两个问题。首先，为了理解逻辑，我们需要上下移动代码几次。代码读起来不是线性的。此外，代码还会遭遇开发人员俗称的“回调地狱”。如果我们在登录用户后执行更多的步骤，并且所有步骤都是异步的，每个步骤都有回调，那么可以想象上面的代码块会是什么样子。不漂亮。还要注意三角形的空白区域，它随着我们不断深入地狱而不断增加。它被亲切地称为末日金字塔。没有人喜欢浪费那么多的空间，尤其是当它与可读性相反，与它最初的意图相反的时候。

这个代码的第二个实际问题(imo)是它多次违反了 DRY 原则。注意错误处理是如何在每个回调函数中重复的。

所以为了克服可读性和缺乏简洁性这两个问题，引入了承诺的概念:

```
getUser(username, password)
    .then(user => {
        return getUserProfile(user);
    })
    .then(profileData => {
        return logUserIn(profileData);
    })
    .then(
        console.log("User successfully logged in")
    )
    .catch(err => 
        console.log(err)
    )

getUser = (username, password) => {
    return new Promise((resolve, reject) => {
        let user = db.getUser(username, password);
        if (user) resolve(user);
        else reject(new Error("Username and pwd do not match"));
    });
}

getUserProfile = (user) => {
    return new Promise((resolve, reject) => {
        let profileData = db.getUserProfile(user);
        if (profileData) resolve(profileData);
        else reject(new Error("User profile could not be fetched"));
    });
} 
```

函数的执行看起来是线性的:首先是我们`getUser()`，然后是我们`getUserProfile()`，然后是`logUserIn()`，如果在任何一个步骤中出现任何错误，我们都可以一次捕捉到它。Promises 有一个叫做错误传播的概念，其中任何一步发生的错误都会导致执行跳转到 catch 语句。所以一次就够了。

`getUser()`和`getUserProfile()`返回一个承诺，这意味着当我们调用这两个函数时，它们让我们等待(因为它们将执行耗时的异步操作)，并“承诺”我们它们将返回一些东西，而不会永远让我们等待。如果这些函数执行的异步操作成功，它们“解决”，或者返回值，然后我们继续下一步，否则它们拒绝错误，错误将直接进入 catch 语句。

这就是:承诺只是作为传统回调方法的语法糖，具有错误传播的额外好处，使我们避免重复捕捉错误。有人说 JS 中新的“async-await”范例比这两种方法更有优势，但是我们改天再讨论这个问题:)