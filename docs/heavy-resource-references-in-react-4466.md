# React 中的大量资源引用

> 原文：<https://dev.to/greggman/heavy-resource-references-in-react-4466>

我很好奇有没有用 react 管理大量资源的好例子或最佳实践。我不确定我是否能给出好的例子，但我会努力的。

典型的重资源是图像。图像似乎一般不被直接管理。它们被 URI 引用，并由魔术师在幕后操纵。

```
const MyComponent = () => { return (
  <div class="recipe">
    <div class="title">Fruit Salad</div>
    <div>
      <img src="images/banana.jpg" />
      <img src="images/strawberry.jpg" />
      <img src="images/grape.jpg" />
    </div>
  </div>
  <div class="recipe">
    <div class="title">Banana Split</div>
    <div>
      <img src="images/ice-cream.jpg" />
      <img src="images/banana.jpg" />
      <img src="images/whipped-cream.jpg" />
    </div>
  </div>
)}; 
```

在上面的例子中，react 声明了`<img>`标签，但是资源本身是由浏览器管理的。浏览器神奇地查看`src`属性并加载相应的图像。尽管两个配方都引用了`banana.jpg`，但是图像的实际数据将只被加载一次。如果节点树的这一部分不是活动 DOM 的一部分，浏览器可以从内存中释放所有这些图像。

所以，假设我们想做一些资源密集型的事情。我不确定什么是好的例子。让我们想象这是一张销售图表。

我看到很多图书馆都在做类似的事情

```
const MyComponent = () => { return (
  <MyGraphComponent xaxis="date" yaxis="amount">
    <data>
      <datum xaxis="2012/01" yaxis="145" />
      <datum xaxis="2012/02" yaxis="121" />
      <datum xaxis="2012/03" yaxis="131" />
      <datum xaxis="2012/04" yaxis="152" />
      ... 2000 items ...
    </data>
  </MyGraphComponent>
)}; 
```

与`<img>`的例子相比，这似乎是错误的。按照`<img>`的例子，组件的数据应该在`<MyGraphComponent>`的外部(正交)。像`<img>`一样，数据不是图中的孩子，而是应该以某种方式引用的东西，以便多个组件可以访问相同的数据。

另一个例子可能是 3D 场景

```
const MyComponent = () => { return (
  <scene>
    <node transform="translate(10, 20, 30)">
      <sphere radius="1" radialDivisions="100" verticalDivision="50" />
      <material color="red">
    </node>
  </scene>
)}; 
```

在这里，它看起来足够无辜，但它又一次没有遵循`<img>`树立的榜样。

我们可以想象一个场景，同一个球体有多个版本。按照`<img>`的例子，我们可以想象更像
的东西

```
const MyComponent = () => { return (
  <scene>
    <node transform="translateX(-5)" geometry="100-50-unit-sphere.geo" material="red-material" />
    <node transform="translateX(0)"  geometry="100-50-unit-sphere.geo" material="red-material" />
    <node transform="translateX(5)"  geometry="100-50-unit-sphere.geo" material="red-material" />
  </scene>
)}; 
```

上面的例子可能是错误的模型。

我们可以试试

```
const MyComponent = () => { return (
  const geometry = createSphere(100, 500);
  const material = createMaterial({color: 'red'});

  <scene>
    <node transform="translateX(-5)" geometry={geometry} material={material} />
    <node transform="translateX(0)"  geometry={geometry} material={material} />
    <node transform="translateX(5)"  geometry={geometry} material={material} />
  </scene>
)}; 
```

还不清楚这些是如何工作的。对于 URIs 来说，它们被神奇地引用了，但是对于其他重资源来说，应该发生什么以及如何管理它们就不那么清楚了。

或许通过给事物命名？

```
const MyComponent = () => { return (
  // only creates resource if resource for that name doesn't already exist
  createGeometry('100by500sphere', () => { return new Sphere(100, 500); });
  createMaterial('redMaterial', () => { return new Material({color: 'red'}); });

  <scene>
    <node transform="translateX(-5)" geometry="100by500sphere" material="redMaterial" />
    <node transform="translateX(0)"  geometry="100by500sphere" material="redMaterial"  />
    <node transform="translateX(5)"  geometry="100by500sphere" material="redMaterial"  />
  </scene>
)}; 
```

上面的例子有一个奇怪的特性，那就是我们用名字来制作东西，并且假设没有名字冲突。但是，它确实遵循了`<img>`的例子，并且就像`<img>`一样工作，因为名称(包括路径)必须是唯一的。

也许另一种方法是像资源工厂一样要求尽早声明资源

```
resourceManager.registerResources({
  '100by500sphere': () => { return new Sphere(100, 500); },
  'redMaterial': () => { return new Material({color: 'red'}); },
}); 
```

然后在别处

```
const MyComponent = () => { return (
  <scene>
    <node transform="translateX(-5)" geometry="100by500sphere" material="redMaterial" />
    <node transform="translateX(0)"  geometry="100by500sphere" material="redMaterial"  />
    <node transform="translateX(5)"  geometry="100by500sphere" material="redMaterial"  />
  </scene>
)}; 
```

假设资源的注册发生在一个地方，命名冲突问题就消失了，类似的图像资源被定义在 react 正在管理的部分之外。当然，有些人会说这样做没意思，因为你不能像第一个 3D 例子那样直接声明内容

当然，没有人说`<img>`方式是正确的方式。这只是观察到资源并不是 React 管理的节点树的一部分。他们不是孩子，他们是正交资源。而且，react 中唯一常见的类似例子是图像、音频和视频，所有这些通常都被 URI 引用，而不是作为孩子。

本着 React 的真正精神这么做有什么好的参考吗？