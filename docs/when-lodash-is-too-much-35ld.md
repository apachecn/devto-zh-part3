# 当碘太多的时候

> 原文：<https://dev.to/davidlacarta/when-lodash-is-too-much-35ld>

并不总是需要使用众所周知的 **lodash** 实用程序库来对数组和 javascript 对象进行一些基本操作。

#### 获取安全对象属性

```
function isObject(object) {
  return object && typeof object === "object";
}
function hasKey(object, key) {
  return key in object;
}

function safe(object) {
  return new Proxy(object, {
    get: (target, name) => {
      if (!hasKey(target, name)) {
        return "undefined";
      }
      if (!isObject(target[name])) {
        return target[name];
      }
      return safe(target[name]);
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 让我们试试...

```
const objectDeep = { a: { b: "x" } };
console.log(objectDeep.a.b);
// x
console.log(objectDeep.c.d);
// TypeError: Cannot read property 'd' of undefined
console.log(safe(objectDeep).a.b);
// x
console.log(safe(objectDeep).c.d);
// undefined 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 获取对象的数组唯一

```
function isEqual(objectA, objectB) {
  return JSON.stringify(objectA) === JSON.stringify(objectB);
}

function unique(array) {
  return array.reduce((uniqueArray, currentElement) => {
    const isDuplicated = uniqueArray.find(element =>
      isEqual(element, currentElement)
    );
    return isDuplicated ? uniqueArray : [...uniqueArray, currentElement];
  }, []);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 让我们试试...

```
console.log(unique([{ a: "x" }, { a: "z" }, { a: "x" }]));
// [ { a: 'x' }, { a: 'z' } ] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 得到的数组少了一个维度

```
function flat(array) {
  return [].concat.apply([], array);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 让我们试试...

```
console.log(flat([["a", "b"], ["c", "d"]]));
// [ 'a', 'b', 'c', 'd' ] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 获得克隆对象深度

```
function cloneDeep(object) {
  return JSON.parse(JSON.stringify(object));
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 让我们试试...

```
const objectDeep = { a: { b: "x" } };

const objectDeepClonedAssign = Object.assign(objectDeep);
objectDeep.a.b = "assign";
console.log(objectDeep);
// { a: { b: "assign" } }
console.log(objectDeepClonedAssign);
// { a: { b: "assign" } }

const objectDeepCloned = cloneDeep(objectDeep);
objectDeep.a.b = "deep";
console.log(objectDeep);
// { a: { b: "deep" } }
console.log(objectDeepCloned);
// { a: { b: "assign" } } 
```

Enter fullscreen mode Exit fullscreen mode