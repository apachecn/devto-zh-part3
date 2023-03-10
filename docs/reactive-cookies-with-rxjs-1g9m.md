# 带有 RxJs 的反应式 Cookies

> 原文：<https://dev.to/lysofdev/reactive-cookies-with-rxjs-1g9m>

我们需要在客户的设备上安全地存储数据，为此，我们有 cookies。NPM 包`js-cookie`为我们提供了一个简单的接口来存储和检索 cookies。让我们为这个接口构建一个包装器，使我们的 cookies 具有反应性。

将使用密钥创建一个反应式 cookie。

```
import { of, Subject } from 'rxjs';

class ReactiveCookie {

    constructor(key) {
        this.key = key;
    }

} 
```

让我们为该值添加 getters 和 setters。

```
constructor() {
    ...
    this.value = new Subject();
}

getValue() {
    return this.value;
}

setValue(value) {
    this.value.next(value);
} 
```

太好了！我们希望每次设置值时存储的 cookie 都得到更新，以便它总是同步的。我们将订阅`value`主题，并在每个值上设置 cookie。

```
constructor() {
    ...
    this.synchronize();
}

synchronize() {
    this.getValue().subscribe((value) => { Cookies.set(this.key, value); });
} 
```

好吧，如果已经有了一个存储值呢。我们希望将这个存储值设置为初始值，所以我们也将它添加到构造函数中。

```
constructor() {
    this.retrieveStoredValue();
}

getStoredValue() {
    return of(Cookies.get(this.key));
}

retrieveStoredValue() {
    this.getStoredValue().subscribe((value) => { this.value.next(value) });
} 
```

呜！现在，每当我们用一个已经存储了值的键创建一个 Cookie 时，该值将自动加载到 ReactiveCookie `value`中。此外，设置新值将立即更新存储的 cookie 的值。最后，让我们添加`clear()`和`remove()`方法来完成我们的功能。

```
clear() {
    this.setValue(null);
}

remove() {
    Cookies.delete(this.key);
    this.value.complete();
} 
```

现在，出去储存一些价值吧！