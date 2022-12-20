# 在 Three.js 中创建自定义着色器

> 原文：<https://dev.to/maniflames/creating-a-custom-shader-in-threejs-3bhi>

浏览器中的 3D 内容棒极了。在和 threejs 玩了一段时间并在学校制作了一个迷你游戏后，我开始非常喜欢它。一位对图形编程非常感兴趣的同学告诉了我一些关于 WebGL 和着色器的知识。这似乎真的很酷，我答应自己，我会做自己的着色器。当然，一些其他闪亮的东西引起了我的注意，我忘记了它，但是，从今天开始，我终于可以说，我已经创建了一个着色器，并在三个 js 中使用它。

# 三个 j

在全面了解着色器之前，解释一下什么是三个 js 可能是个好主意。Threejs 是一个 javascript 库，用于简化在画布上创建 3D 场景的过程。其他流行的解决方案如 [a-frame](https://aframe.io/) 和 [whitestorm js](https://whs.io/) 都是基于它构建的。如果你曾经玩过这些，但是想要更多的控制，那就试试吧！(如果你是一个打字爱好者，三个 js 有[类型定义](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/three)😉).

这个库最受欢迎的介绍是创建一个立方体并让它旋转。在 threejs 文档中有一个书面教程，还有一个由 CJ Gammon 编写的精彩的 youtube 教程，是他的“潜水:three js”系列的一部分。

[https://www.youtube.com/embed/biZgx45Mzqo](https://www.youtube.com/embed/biZgx45Mzqo)

创建这个立方体基本上就是准备一个电影布景并把它放在布景里。你创建一个场景和一个摄像机，然后把它们传递给一个渲染器说:“嘿，这是我的电影布景”。然后，您可以在场景中放置网格，它基本上是一个对象。该网格由几何体(对象的形状)和材质(颜色、对光的行为等)组成。基于你所选择的材质，你可能想给场景添加不同种类的灯光。为了使对象有动画效果并真实地显示一切，你创建了一个循环。在这个循环中，你告诉渲染器显示场景。您的代码可能如下所示:

```
 window.addEventListener('load', init)
let scene
let camera
let renderer
let sceneObjects = []

function init() {
  scene = new THREE.Scene()

  camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
  camera.position.z = 5

  renderer = new THREE.WebGLRenderer()
  renderer.setSize(window.innerWidth, window.innerHeight)

  document.body.appendChild(renderer.domElement)
  adjustLighting()
  addBasicCube()
  animationLoop()
}

function adjustLighting() {
    let pointLight = new THREE.PointLight(0xdddddd)
    pointLight.position.set(-5, -3, 3)
    scene.add(pointLight)

    let ambientLight = new THREE.AmbientLight(0x505050)
    scene.add(ambientLight)
}

function addBasicCube() {
  let geometry = new THREE.BoxGeometry(1, 1, 1)
  let material = new THREE.MeshLambertMaterial()  

  let mesh = new THREE.Mesh(geometry, material)
  mesh.position.x = -2
  scene.add(mesh)
  sceneObjects.push(mesh)
}

function animationLoop() {
  renderer.render(scene, camera)

  for(let object of sceneObjects) {
    object.rotation.x += 0.01
    object.rotation.y += 0.03
  }

  requestAnimationFrame(animationLoop)
} 
```

# 着色器

着色器基本上是由 GPU 执行的函数或小脚本。这就是 [WebGL](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Tutorial/Getting_started_with_WebGL) 和 [GLSL (OpenGL 着色语言)](https://developer.mozilla.org/en-US/docs/Games/Techniques/3D_on_the_web/GLSL_Shaders)发挥作用的地方。WebGL 是一个浏览器 API，允许 javascript 在 GPU 上运行代码。这可以提高某些脚本的性能，因为您的 GPU 针对图形相关计算进行了优化。WebGL 甚至允许我们用 GLSL 语言编写直接由 GPU 执行的代码。这些 GLSL 代码是我们的着色器，因为 threejs 有一个 WebGL 渲染器，我们可以编写着色器来修改我们的网格。在 threejs 中，你可以使用“着色器材质”来创建自定义材质。该材质接受两个着色器，一个顶点着色器和一个片段着色器。让我们尝试制作‘渐变材质’。

## 顶点着色器

顶点着色器是应用于网格的每个顶点(点)的函数。它通常用于扭曲或激活网格的形状。在我们的脚本中，它看起来像这样:

```
function vertexShader() {
  return `
    varying vec3 vUv; 

    void main() {
      vUv = position; 

      vec4 modelViewPosition = modelViewMatrix * vec4(position, 1.0);
      gl_Position = projectionMatrix * modelViewPosition; 
    }
  `
} 
```

你可能注意到的第一件事是，我们所有的 GLSL 代码都在一个字符串中。我们这样做是因为 WebGL 会将这段代码传递给我们的 GPU，而我们必须在 javascript 中将代码传递给 WebGL。你可能注意到的第二件事是，我们使用了不是我们创建的变量。这是因为 threejs 为我们把那些变量传递给了 GPU。

在这段代码中，我们计算网格点应该放在哪里。我们通过将网格在场景中的位置(modelViewMatrix)与点的位置相乘来计算点在场景中的位置。之后，我们将这个值乘以相机与场景的关系(projectionMatrix ),这样我们的着色器就会考虑三个 js 中的相机设置。gl_Position 是 GPU 用来绘制我们的点的值。

现在这个顶点着色器并没有改变我们的形状。那么，为什么还要费心去创造这个呢？我们将需要我们的网格部分的位置来创建一个不错的梯度。通过创建一个“可变”变量，我们可以将位置传递给另一个着色器。

## 片段着色器

片段着色器是一个应用于网格中每个片段的函数。碎片是光栅化过程的结果，光栅化过程将整个网格变成三角形的集合。对于被我们的网格覆盖的每个像素，至少会有一个碎片。片段着色器通常用于对像素进行颜色变换。我们的片段着色器看起来像这样:

```
 return `
      uniform vec3 colorA; 
      uniform vec3 colorB; 
      varying vec3 vUv;

      void main() {
        gl_FragColor = vec4(mix(colorA, colorB, vUv.z), 1.0);
      }
  `
} 
```

如你所见，我们采用了顶点着色器传递的位置值。我们希望根据碎片在网格 z 轴上的位置应用颜色 A 和 B 的混合。但是颜色 A 和 B 是从哪里来的呢？这些是“统一”变量，这意味着它们是从外部传入着色器的。mix 函数将计算我们想要为这个片段绘制的 RGB 值。这个颜色和不透明度的附加值被传递给 gl_FragColor。我们的 GPU 会把一个片段的颜色设置成这个颜色。

## 创建素材

现在我们已经创建了着色器，我们终于可以使用自定义材质来构建我们的三个 js 网格了。

```
function addExperimentalCube() {
  let uniforms = {
        colorB: {type: 'vec3', value: new THREE.Color(0xACB6E5)},
        colorA: {type: 'vec3', value: new THREE.Color(0x74ebd5)}
    }

  let geometry = new THREE.BoxGeometry(1, 1, 1)
  let material =  new THREE.ShaderMaterial({
    uniforms: uniforms,
    fragmentShader: fragmentShader(),
    vertexShader: vertexShader(),
  })

  let mesh = new THREE.Mesh(geometry, material)
  mesh.position.x = 2
  scene.add(mesh)
  sceneObjects.push(mesh)
} 
```

这是一切汇集的地方。我们的“制服”colorA 和 colorB 被创建并与顶点着色器和片段着色器一起传递到着色器材质中。材质和几何体用于创建网格，网格被添加到场景中。

[https://glitch.com/embed/#!/embed/three-cube-experiment?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/three-cube-experiment?previewSize=100&path=index.html)

我在 glitch 里建这个。一个朋友推荐的，很棒！一些添加拦截器会阻止你加载嵌入，所以[这里有一个直接链接](https://three-cube-experiment.glitch.me/)以防万一。

左立方体是一个立方体使用网格朗伯材料，右立方体使用我们自己的'梯度材料'。正如你所看到的，我们的材质看起来很可爱，但是忽略了场景中的灯光设置。这是因为我们没有在片段着色器中进行数学计算来考虑光线。希望这是我很快就会明白的事情😝。

# 资源

我花了一些时间才弄明白这一点，如果你喜欢这个，你真的应该看看我用来学习和理解这个的资源:

*   [CJ Gammon - Three.js 第五部分:着色器](https://www.youtube.com/watch?v=uD4GnMsAH1U&index=5&list=PL08jItIqOb2qyMOhtEUoLh100KpccQiRf)
*   [着色器之书-颜色](https://thebookofshaders.com/06/)