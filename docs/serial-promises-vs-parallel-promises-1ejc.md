# 串行承诺与并行承诺

> 原文：<https://dev.to/micahriggan/serial-promises-vs-parallel-promises-1ejc>

在 javascript 中，我们经常需要做多个异步的事情。

我想通过这篇文章展示一些连续做事的例子，以及与承诺并行的例子。

## 例 1:“等一下”x 3

第一个例子，让我们定义一个连续三次“等待一秒”的函数。

该功能将被称为串行。

之后，我们将调用“等待一秒钟”函数，并行三次

该功能将被称为并行

```
function wait(waitTime) {
  return new Promise(resolve => setTimeout(() => {
    console.log(`waited ${waitTime} ms`)
    resolve()
  }, waitTime));
}

async function serial() {
  console.time('serial');
  await wait(1000);
  await wait(1000);
  await wait(1000);
  console.timeEnd('serial');
}

async function parallel() {
  console.time('parallel');
  await Promise.all([
    wait(1000),
    wait(1000),
    wait(1000)
  ])
  console.timeEnd('parallel');
}

async function test() {
  await serial();
  await parallel();
}

test(); 
```

### 输出

```
waited 1000 ms
waited 1000 ms
waited 1000 ms
serial: 3016.319ms
waited 1000 ms
waited 1000 ms
waited 1000 ms
parallel: 1003.017ms 
```

从输出中我们可以看到 Promise.all 使我们能够同时执行所有的“稍等”调用。

## 例 2:添加两个异步数字

在前面的例子中，我们只是等待了一秒钟。在这个例子中，我们将异步获得两个数字，并串行和并行地将它们相加。

```
function randomNumber() {
  const rand = Math.random() * 100;
  return new Promise(resolve => setTimeout(() => {
    resolve(rand)
  }, 1000))
}

async function addExampleSerial() {
  console.time('add-serial');
  const number1 = await randomNumber();
  const number2 = await randomNumber();
  const result = number1 + number2;
  console.timeEnd('add-serial');
  console.log('serial result: ', result);
}

async function addExampleParallel() {
  console.time('add-parallel');
  const [number1, number2] = await Promise.all([randomNumber(), randomNumber()]);
  const result = number1 + number2;
  console.timeEnd('add-parallel');
  console.log('parallel result: ', result);
}

async function test() {
  await addExampleSerial();
  await addExampleParallel();
}

test(); 
```

### 输出

```
add-serial: 2005.019ms
serial result: 59.0316729944722
add-parallel: 1000.616ms
parallel result: 48.7190841367634 
```

## 例 3:必需的数据依赖

在上一个例子中，我们必须添加异步返回的数字，但是我们还没有一个例子，在检索另一个异步值之前需要一个异步值。

在这个例子中，我们将获得我们的用户名，然后我们将获取依赖于我们的用户名的两条信息。

```
function fetchData(data) {
  return new Promise(resolve => setTimeout(() => {
    resolve(data)
  }, 1000))
}

function getLoggedInUser() {
  return fetchData('user1');
}

async function getDataForUser(userName) {
  const profileData = await fetchData({
    user1: {name: 'Micah', points: 100},
    user2: {name: 'someone else', point: 200}
  });
  return profileData[userName];
}

async function getUserPosts(userName) {
  const posts = await fetchData({
    user1: ['Promises Post'],
    user2: ['Streams Post']
  });
  return posts[userName];
}

async function userDataSerial() {
  console.time('userData-serial');
  const userName = await getLoggedInUser();
  const userData = await getDataForUser(userName);
  const userPosts = await getUserPosts(userName);
  console.timeEnd('userData-serial');
}

async function userDataParallel() {
  console.time('userData-parallel');
  const userName = await getLoggedInUser();
  const [userData, userPosts] = await Promise.all([
    getDataForUser(userName),
    getUserPosts(userName)
  ])
  console.timeEnd('userData-parallel');
}

async function test() {
  await userDataSerial();
  await userDataParallel();
}

test(); 
```

### 输出

```
userData-serial: 3007.785ms
userData-parallel: 2006.665ms 
```

## 结论

为了优化代码的速度，请注意哪些数据是进行调用所必需的，然后使用 Promise.all()将代码组织到尽可能多的并行获取依赖项的位置

注意:在某些时候，你可能试图一次做太多异步的事情。现在，你必须确定你能做多少，并创建这样的批量以防止系统崩溃。那是以后的事了。