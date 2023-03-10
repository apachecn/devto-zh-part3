# Three.js +交互式文本示例/教程简介

> 原文：<https://dev.to/emurtzle/intro-to-threejs--interactive-text-exampletutorial-4d1k>

# 什么是 Three.js？

来自[维基百科文章](https://en.wikipedia.org/wiki/Three.js):
*“three . js 是一个轻量级跨浏览器 JavaScript 库/API，用于在 Web 浏览器上创建和显示动画 3D 计算机图形。Three.js 脚本可以与 HTML5 canvas 元素、SVG 或 WebGL 结合使用。*

简单地说，这是一种以(相对)简单的方式显示 3D 内容的方式，不需要各种插件。

它建立在 WebGL 之上，而 web GL 又建立在 OpenGL 之上。它本质上简化了 WebGL 的使用

# 是用来做什么的？

1.  显示 3D 内容
2.  多媒体项目
3.  音乐可视化
4.  数据可视化
5.  视频游戏
6.  为了好玩(很好玩！！！)

# 为什么要用 Three.js？

1.  基于 WebGL 还有几个类似的库。
    *   GLGE
    *   景色
    *   菲洛格尔
2.  这是一个有据可查的大型图书馆。
3.  它有几十个示例项目和概念验证，可以从中获得灵感和参考
4.  这是一个单一的 JavaScript 文件，你不需要加载一大堆奇怪的插件
    *   这增加了浏览器之间的安全性和兼容性，同时也使您的代码更干净、更高效。
5.  活跃的社区，有 14，000 个关于堆栈溢出的问题(并且还在不断增加)
    *   [文档](https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene)
    *   [例题](https://threejs.org/examples/)
    *   [论坛](https://discourse.threejs.org/)
    *   [松弛](https://threejs-slack.herokuapp.com/)
    *   [IRC](http://webchat.freenode.net/?channels=three.js)
    *   [堆栈溢出](https://stackoverflow.com/questions/tagged/three.js)
6.  它可以通过麻省理工学院的许可获得
    *   再次引用维基百科的文章:
        *   “麻省理工许可证是一个许可的自由软件许可证，起源于麻省理工学院(MIT)。作为一个许可许可证，它对重用只施加了非常有限的限制，因此具有极好的许可证兼容性。MIT 许可证允许在专有软件中重复使用，前提是许可软件的所有副本都包含 MIT 许可证条款和版权声明的副本。MIT 许可证也兼容很多 copyleft 许可证，比如 GNU 通用公共许可证(GPL)；麻省理工学院许可的软件可以集成到 GPL 软件中，但不能反过来。
    *   其他几个使用 MIT 许可证的大型程序包括
        *   Ruby on Rails
        *   节点. js
        *   JQuery
        *   x 窗口系统

# 教程(JSFiddle 上的简单场景)

让我们做一个超级简单的例子，创建一个立方体并使其旋转。下面是它的 JSFiddle，让我们一行一行地检查代码。除了一个导入 Three.js 的脚本标签之外，没有 html 或 css。其他的都是纯 JavaScript！
[https://jsfiddle.net/Emurtzle/02dbuvxf//embedded/result,js,html//dark](https://jsfiddle.net/Emurtzle/02dbuvxf//embedded/result,js,html//dark)

## 第 1 行

```
var camera, scene, renderer, geometry, material, mesh; 
```

让我们来分解一下:

*   `camera`是一个虚拟摄像头，这个摄像头看到的就是渲染出来的。
*   `scene`包含你想要渲染的一切。摄像机- >视角- >场景
*   是渲染引擎，它渲染相机看到的东西，并将其转换成 DOM 可以显示的东西
*   `geometry`是我们正在渲染的对象的*形状*
*   是我们正在渲染的对象的颜色和/或纹理
*   是实际的可渲染对象，它需要一个形状(几何体)和一个颜色(材质)

## 第 3-4 行

```
init();
animate(); 
```

这调用了我们下面定义的两个函数

*   初始化我们的 3D 场景。
*   开始旋转立方体的动画循环

## 第 6-24 行

```
function init() {

    scene = new THREE.Scene();

    camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 1, 10000);
    camera.position.z = 500;

    geometry = new THREE.CubeGeometry(200, 200, 200);
    material = new THREE.MeshNormalMaterial();

    mesh = new THREE.Mesh(geometry, material);
    scene.add(mesh);

    renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);

    document.body.appendChild(renderer.domElement);

} 
```

好了，这可能一次很多，让我们一行一行来。这是我们档案的大部分

*   简单地创造一个新的场景
*   `camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 1, 10000);`创建一个新的*透视相机*有几种不同类型的相机，但你可能会最常用透视相机。它需要四个参数
    *   视野
    *   纵横比，`window.innerWidth / window.innerHeight`适用于大多数项目
    *   Near(float)，任何比 Near 更靠近相机的东西都不会被渲染
    *   Far(float)，任何比 Far 更远的物体都不会被渲染
*   `camera.position.z = 500;`将相机的位置设置为 z 轴上的 500 个单位
*   `geometry = new THREE.CubeGeometry(200, 200, 200);`创建一个宽 200 单位、高 200 单位、深 200 单位的新立方体形状
*   `material = new THREE.MeshNormalMaterial();`创建一个新的简单材质应用于立方体。有许多不同类型的材质，我们使用的是一种基于面(立方体的侧面)指向的法线(方向)改变颜色的材质。
*   `mesh = new THREE.Mesh(geometry, material);`创建一个新的网格，将`material`应用到我们上面定义的`geometry`
*   然后将网格添加到场景中，这样摄像机就可以看到它了
*   `renderer = new THREE.WebGLRenderer();`创建新的 WebGL 渲染引擎
*   `renderer.setSize(window.innerWidth, window.innerHeight);`将渲染引擎的宽度和高度设置为包含它的窗口的宽度和高度
*   渲染器获取渲染结果并将其转换成 dom 可以使用的格式，然后我们将它附加到文档体(DOM)

那里！还不算太糟吧？我们所做的就是创建对象和建立父/子关系。就像我们对任何 JavaScript 驱动的网站所做的一样！

## 第 26-36 行

```
function animate() {

    requestAnimationFrame(animate);

    mesh.rotation.x += 0.01;
    mesh.rotation.y += 0.02;

    renderer.render(scene, camera);

} 
```

这个函数告诉渲染器实际渲染场景，它驱动我们立方体的动画。

*   这个函数很奇怪，文档中甚至没有关于它的条目。它告诉 Three.js 将它下面的代码设置成一个循环，该循环在每次显示刷新时运行(通常为 60hz 或每秒 60 次)
*   我们将立方体的 x 旋转增加一点点，记住这是每秒 60 次，大的值会使立方体旋转得非常快
*   我们将立方体的 y 轴旋转增加一点点
*   我们告诉 render 使用我们的相机渲染场景，我们必须提供场景和相机，因为你可以在一个项目中同时拥有两者。

这是驱动我们场景的动画和渲染的主要循环，所以如果你有一个复杂的场景，这个函数可以很容易地变成数百行，我强烈建议尽可能地抽象你的动画。

这就对了。这就是这个项目的全部内容！你现在应该有一个颜色变化，旋转立方体

# 互动示例

下面是一个更深入的例子，说明如何在网页和 Three.js 内容之间添加交互。我故意保持简单，以确保它可以在尽可能多的机器上运行。我不会在这里解释每一行，但是这里有解释几乎每一行代码的注释！

[https://jsfiddle.net/Emurtzle/9a4fmr8b/embedded/result,js,html//dark](https://jsfiddle.net/Emurtzle/9a4fmr8b/embedded/result,js,html//dark)