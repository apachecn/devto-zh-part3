# 提示#1:没有 flexbox 和自动边距的居中元素

> 原文：<https://dev.to/gablaroche/tip-1-centering-elements-without-flexbox-and-automatic-margins-374l>

这个系列是关于我发现并一直在发现的所有 HTML/CSS/JS 的小技巧。其中大部分是我最初花了很长时间才发现或弄明白的事情，还有一些只是我在日常生活中使用的有用技巧。

这个巧妙的小技巧可以让你在任何情况下都将元素居中。当我试图在容器中垂直居中一个相对元素时，我发现了这个技巧。我发现你可以用这个技巧来水平居中元素。当`margin: 0 auto;`或 flexbox 不工作时有用。不管怎样，这是代码:

**注意仅在万不得已时才使用此方法，如果可以使用到自动边距或 flexbox，则使用那些方法代替**

[https://codepen.io/glaroche/embed/axgvJe?height=600&default-tab=css&embed-version=2](https://codepen.io/glaroche/embed/axgvJe?height=600&default-tab=css&embed-version=2)
我最初发现这个窍门[在这里](http://zerosixthree.se/vertical-align-anything-with-just-3-lines-of-css/)

## 逐位编码:

### 水平居中

```
.container {
  position: relative;
}

.center__horizontal {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 垂直居中

```
.container {
  position: relative;
}

.center__vertical {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 垂直&水平居中

```
.container {
  position: relative;
}

.center__both {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 将一个相对元素对其容器居中

```
.container {
  width: 550px;
  height: 150px;
  background: #eaeaea;
}

.center__relative {
  position: relative;
  width: 50px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
} 
```

Enter fullscreen mode Exit fullscreen mode