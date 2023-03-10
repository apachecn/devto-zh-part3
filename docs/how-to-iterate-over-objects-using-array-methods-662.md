# 如何使用数组方法迭代对象

> 原文：<https://dev.to/stu/how-to-iterate-over-objects-using-array-methods-662>

数组是如此的方便！它们是存储数据的一种很好的方式，并且它们有一些真正令人惊讶的“数组方法”，比如`.forEach()`和`.map()`(以及更多)，允许我们轻松地迭代数组。太棒了。

```
const sampleArray = [“I”, “love”, “technology”];

sampleArray.forEach( (word) => {
    console.log(word);
});

// I
// love
// technology 
```

Enter fullscreen mode Exit fullscreen mode

物体也是超级的，依我拙见。对于键/值对，我们只需要一个键名，就可以得到相关的值。

```
const sampleObject = {
    word1: "Always",
    word2: "and",
    word3: "forever"
};

// We can use either dot- or bracket-notation to access values:

console.log(sampleObject.word1);    // Always

console.log(sampleObject['word3']);    // Forever 
```

Enter fullscreen mode Exit fullscreen mode

嘣！

看起来*不那么*棒的是，像`.forEach()`、`.map()`等数组方法不能在对象上工作。不不。

[在此插入悲伤表情]

但是不要绝望！这里的*是*一种使用数组方法轻松迭代对象的方式，使用的是`Object.keys()`方法。让我解释一下:

**你说 Object.keys()是什么？**

`Object.keys()`是一个内置于 Javascript 方法中的[，它将任何对象作为参数，并返回该对象的键数组。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)

例如:

```
console.log(Object.keys(sampleObject));

//  [“word1”, “word2”, “word3”] 
```

Enter fullscreen mode Exit fullscreen mode

好极了。

因此，作为一种变通方法，我们可以使用返回的键数组，通过使用一个数组方法(如`.forEach()`)来迭代我们的对象。恶心！

我给你看:

```
// here we now have an array of sampleObject’s keys
const arrayOfKeys = Object.keys(sampleObject);  

// we can use the key to call up each piece of Object data 
arrayOfKeys.forEach( key => console.log( sampleObject[key] ); 

// Always
// and
// forever 
```

Enter fullscreen mode Exit fullscreen mode

怎么了？！漂亮！*擦去一滴眼泪*

现在您知道了如何使用至少一个数组方法轻松地迭代一个对象。请负责任地使用你的新能力！

[![I love technology](img/87d7f4663fdd26524da970e364e17551.png)](https://i.giphy.com/media/116wwYf3ajIvrG/giphy.gif)

*   有关数组方法的更多信息，请参见 [MDN webdocs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/prototype#Methods) 。

*   要了解一些最好的数组方法，请查看本文。

非常感谢韦斯·博斯在他的[初学者课程](https://reactforbeginners.com/)中指出这一点。这是一门非常棒的课程。