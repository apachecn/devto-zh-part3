# “这是你在 JavaScript 上的大脑”的制作

> 原文：<https://dev.to/krofdrakula/the-making-of-this-is-your-brain-on-javascript-3o7k>

*这篇文章最初发表在 [CodePen](https://codepen.io/KrofDrakula/post/the-making-of-this-is-your-brain-on-javascript) 上。*

[https://codepen.io/twhite96/embed/zBvyKg?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/zBvyKg?height=600&default-tab=result&embed-version=2)

自从我开始跟随安德斯·霍夫研究自然生长模式和涌现结构的工作以来，这支笔已经在我的脑海里很长时间了。当模拟简单的物理和生物规则时，有序的结构和有趣的模式是如何出现的，这让我着迷。这看起来不太直观，所以我决定探索一些规则，这些规则会产生像安德斯在他自己的作品中产生的图像一样的东西。

## 解构系统

当我开始考虑如何实现这样一个系统时，我首先列出了一些公理来帮助我建立这样一个系统的模型。我想到了以下几点:

1.  一个单独的细胞对其近邻以外的世界一无所知。
2.  所有细胞都是一样的。
3.  除了牛顿物理学和生物生长之外，没有指导细胞组织的力量。

有了这些公理，我开始思考多细胞生物在生长方面是如何工作的。每个有机体都是从一个单细胞开始，然后分裂成两个，通过嵌入每个细胞膜的结合蛋白结合在一起。为了简单起见，让我们通过创建一个新细胞来模拟细胞分裂，这个新细胞通过一个弹簧连接到它的父细胞，这个弹簧代表两个新细胞之间的连接:

```
 o  ==(division)==>  o~o   ==(physics)==>  o—o
  (cell)           (the new pair)       (the spring relaxes,
                                        separating the cells) 
```

Enter fullscreen mode Exit fullscreen mode

在这个图中，一条弯曲的线代表处于张力下的弹簧，它在松弛后将细胞推开。听起来很简单。但是后续的分裂呢？我们来看看下一个发展阶段:

```
 o—o   ==(division)==>   o~[o~o]
      ^(this cell divides)     ^(the new cell appears) 
```

Enter fullscreen mode Exit fullscreen mode

随后的分裂导致两个弹簧都产生张力，这将导致细胞相互推压以消除张力。如果我们用我们的直觉来模拟物理学，我们期望紧张以两种方式之一解决

```
 o
                   / \
   o~[o~o]   =>   o   o   (when the new cell is a little bit
                                    above the line)

   o~[o~o]   =>  o—o—o    (when the new cell is exactly along
                          the line between the original cells) 
```

Enter fullscreen mode Exit fullscreen mode

**注:** *这里我们假设弹簧的方向可以自由改变，就像蛋白质可以沿着膜向任何方向自由流动一样。*

到目前为止，这似乎是对一系列细胞生长的合理描述。我们确实需要为系统设置一些物理规则来合理地建模:

1.  生长发生在粘性介质中，这种介质阻止细胞无摩擦地滑过空间。
2.  细胞不能同时占据同一个空间，所以没有连接的细胞如果靠得太近还是会互相排斥。

## 把所有的东西放在一起

让我们来回顾一下规则:

1.  细胞可以通过弹簧连接，使连接的细胞保持固定的距离。
2.  一个细胞可以分裂，这就产生了一对相互连接的细胞，附着在它们自身和它们原来的邻居身上。
3.  细胞被装在粘性介质中。

我们现在有了构建模型和方程所需的所有构件，这些模型和方程将支配这个系统的演化。

### 单元格

让我们从定义单元格的所有属性开始:

```
class Particle {
  constructor(position) {
    this.position = position;
    this.force = new Vec2d(0, 0);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`position`和`force`属性都是我用于矢量数学的 [`Vec2d`](https://gist.github.com/KrofDrakula/438fb6b07a8179040450f638bfa17ed2) 的实例。我们只需要这两个，因为我们将假设细胞嵌入的介质非常粘，一旦力不再作用于细胞，粒子的速度就会下降到 0(想想*蜂蜜*或*糖蜜*)。在模拟步骤中，我们对作用在单元上的所有力求和，在力的方向上移动粒子，然后为下一步将`force`属性重置回 0。

### 世界

我们还想创造一个细胞生存的环境:

```
class Simulation {
  constructor(particles) {
    this.particles = particles;
  }
  // ... 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们将跟踪世界中包含的粒子。为了简单起见，我们假设阵列中相邻的粒子也由弹簧连接。这样，当我们在数组中间插入一个元素来模拟细胞分裂时，这个细胞将隐式地连接到它的相邻细胞:

```
[a,b,c,d] ==> a—b—c—d 
```

Enter fullscreen mode Exit fullscreen mode

但是让我们把事情变得有趣一些，假设单元格连接成一个圆形结构，数组中的最后一个元素连接到第一个元素:

```
 a—b
[a,b,c,d] ==> | |
              d—c 
```

Enter fullscreen mode Exit fullscreen mode

所以这意味着对于每个元素`n`，我们有两个邻居`n-1`和`n+1`通过弹簧连接。如果值超出了范围`[0, particles.length - 1]`，我们将值换行，使它们适合数组。

#### 常数

我们在模拟中定义了几个静态常数，它们定义了几个重要的值:

```
 static get NODE_DISTANCE()      { return 2; }
  static get DISTANCE_CUTOFF()    { return this.NODE_DISTANCE * 8; }
  static get SPRING_COEFFICIENT() { return 0.02; }
  static get REPULSION_FORCE()    { return 4; } 
```

Enter fullscreen mode Exit fullscreen mode

让我们回顾一下这些常数，以便更好地理解它们的含义:

*   `NODE_DISTANCE`:表示导致完全松弛弹簧的单元格之间的距离(以像素为单位)
*   `SPRING_COEFFICIENT`:表示弹簧的刚度；数字越大，弹簧拉伸或压缩时施加的力越大
*   `REPULSION_FORCE`:单个细胞之间的排斥力，不管细胞是否连接
*   `DISTANCE_CUTOFF`:表示细胞间相互排斥的最大距离(以像素为单位)；这是为了防止产生大量粒子时斥力显著增加。

当然，这些值是完全任意选择的，并且是通过反复试验得出的；您可能想要体验不同的值，看看它们如何影响结构的最终形状。

#### 计算兵力

我们现在需要定义作用在成对细胞上的两种力:弹簧力和 T2 相互排斥力。

```
 static getSpringForce(a, b) {
    let f = Vec2d.sub(a.position, b.position),
        m = f.mag;
    return f.norm().scale((this.NODE_DISTANCE - m) * this.SPRING_COEFFICIENT);
  } 
```

Enter fullscreen mode Exit fullscreen mode

第一个函数使用两个单元，计算弹簧作用于第一个粒子所产生的力。这个方程就是简单的[胡克定律](https://en.wikipedia.org/wiki/Hooke%27s_law) : `F = k(l - m)`，其中`l`是弹簧(`NODE_DISTANCE`)的松弛长度，`m`是细胞之间的距离。为了计算第二个单元上的力，我们只需对牛顿第三定律规定的力取反，而不必重新计算。

```
 static getRepulsionForce(a, b) {
    let f = Vec2d.sub(a.position, b.position),
        m = f.mag;
    return m > this.DISTANCE_CUTOFF ? f.reset(0, 0) : f.norm().scale(this.REPULSION_FORCE / (m * m));
  } 
```

Enter fullscreen mode Exit fullscreen mode

对于斥力，我们计算细胞之间的距离(`m`)并使用[平方反比定律](https://en.wikipedia.org/wiki/Inverse-square_law)来创建一个细胞相互排斥的力:`F = k / m^2` ( `k == REPULSION_FORCE`)。由于平方反比衰减，单元对其他单元的影响随着距离的增加而迅速下降，但当单元之间的距离向 0 减小时，这种影响会非常强烈。

#### 施加力并移动质点

让我们继续浏览粒子列表，计算力。我们创建了一个`step(dT)`函数，它将允许我们在我们构建的世界中推进时间。

让我们从头说起:

```
 step(dT) {
    let t = dT / 4; 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们只是缩小了时间步长(以毫秒为单位传递到模拟中)。这可以调高或调低，以减慢或加快模拟，但如果步长过大，可能会导致模拟不稳定。(有关误差的详细解释，请参见欧拉方法中的[数值稳定性。)](https://en.wikipedia.org/wiki/Euler_method#Numerical_stability) 

```
 // process mutual repulsion
    for (let i = 0; i < this.particles.length - 1; i++) {
      for (let j = i + 1; j < this.particles.length; j++) {
        // mutual deflection force
        let a = this.particles[i], b = this.particles[j];
        let f = Simulation.getRepulsionForce(a, b);
        a.force.add(f);
        b.force.sub(f);
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们计算所有细胞对之间的排斥力。`getRepulsionForce(a, b)`返回的力是作用在粒子`a`上的力，所以我们把它加到力收集器`a.force`上。根据牛顿第二定律，我们必须从`b.force`中减去相同的力。这种优化允许我们将迭代次数减少一半，这样这部分运行在`O(n^2/2)`时间而不是`O(n^2)`时间。

```
 // process linkage
    for (let i = 0; i < this.particles.length; i++) {
      let a = this.particles[i], b = this.particles[(i + 1) % this.particles.length];
      let f = Simulation.getSpringForce(a, b);
      a.force.add(f);
      b.force.sub(f);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这一部分遍历元素链，从第一个元素开始，计算它与链中下一个元素之间的力。对于最后一个元素，它假设与数组中的第一个元素相连接(因此有了`(i + 1) % this.particles.length`)。同样，第二定律要求我们把力加到第一个细胞上，然后从第二个细胞上减去。

```
 // apply movement
    this.particles.forEach(particle => {
      let f = particle.force.clone().scale(t);
      particle.position.add(f);
      particle.force.reset(0, 0);
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们做了一点(好吧，**很多**)欺骗:不是计算阻力，摩擦力，力，并在时间步长上积分两次来计算新的位置，而是按时间步长缩放粒子上的力的总和，并将单元的力收集器重置为 0。这模拟了介质的粘度，同时仍然允许细胞移动可接受的距离。(该方法类似于欧拉的积分方法，在准确性和简单性之间具有相同的权衡，以及我们希望保持步长较小的原因。)

#### 初始状态

我们决定将细胞串的两端连接成一个圆形，所以我们需要在一个圆内生成细胞的初始配置。我们用一些粒子和给定的半径建立了一个模拟实例:

```
// set up the initial world, just start with a circle of 40 particles
var simulation = (() => {
  let n = 40, particles = [], r = 40;
  for (let a = 0; a < 2 * Math.PI; a += 2 * Math.PI / n) {
    particles.push(
      new Particle(
        new Vec2d(
          Math.cos(a) * r,
          Math.sin(a) * r
        )
      )
    );
  }
  return new Simulation(particles);
})(); 
```

Enter fullscreen mode Exit fullscreen mode

这将返回我们模拟世界的一个实例，在半径为 40 的圆中填充了 40 个粒子。

### 管理模拟的生命周期

我们已经创造了细胞世界，现在我们需要让时间运行。我们设置了一个`requestAnimationFrame`循环(`tick(timestamp)`函数),它将调用我们的模拟步骤来随着时间的流逝更新物理学:

```
// loop stuff
var lastTime = null, lastGenerated = null;

var tick = timestamp => {
  if (!lastTime) lastTime = timestamp;
  let elapsed = clamp(0, 16, timestamp - lastTime);
  lastTime = timestamp; 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们计算自上次调用`tick()`以来经过的时间。我们将`elapsed`限制在 0 到 16 之间，以保持模拟相当稳定。

```
 simulation.step(elapsed);
  update(simulation.particles); 
```

Enter fullscreen mode Exit fullscreen mode

我们调用`step(elapsed)`将模拟提前这个毫秒数。我们还调用了使用 [D3](https://d3js.org/) 渲染粒子阵列的`update`函数。

#### 使细胞分裂

```
 if (!lastGenerated) lastGenerated = timestamp;
  // generate a new particle every 60ms
  if (simulation.particles.length < 800 && timestamp - lastGenerated > 60) {
    let idx = Math.floor(Math.random() * simulation.particles.length),
        nextIdx = (idx + 1) % simulation.particles.length,
        a = simulation.particles[idx],
        b = simulation.particles[nextIdx];
    simulation.particles.splice(
      idx,
      0,
      new Particle(
        a.position.clone().sub(b.position).scale(0.5).add(a.position)
      )
    );
    lastGenerated = timestamp;
  } 
```

Enter fullscreen mode Exit fullscreen mode

这部分处理在粒子阵列中创建一个新的单元。每隔 60 毫秒，它会在数组中选择一个随机索引，在选择的索引和下一个索引之间插入一个新的单元格。新细胞的位置将在两个细胞的中间(`(a + b) / 2`)，这是我们上面描述的(`o—o ==> o~o~o`)，并在细胞插入的链中产生张力，因为三个细胞之间的距离现在是细胞分裂前的一半。

```
 requestAnimationFrame(tick);
};

requestAnimationFrame(tick); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们确保在第一次运行`tick`时调用`requestAnimationFrame`。

### D3 渲染

D3 的东西对于任何习惯于这个库的人来说都非常简单，但是如果你想知道的话，我使用了[迈克·博斯托克](https://github.com/mbostock)推荐使用的[通用更新模式](http://bl.ocks.org/mbostock/3808218)。

```
// D3 stuff

var container = document.querySelector('#display'),
    canvas = d3.select(container)
      .append('svg')
      .attr({ width: container.offsetWidth, height: container.offsetHeight });

var update = data => {
  let w2 = container.offsetWidth / 2, h2 = container.offsetHeight / 2;
  let links = canvas.selectAll('line').data(data);
  let particles = canvas.selectAll('circle').data(data);

  // update the links
  links.enter().append('line');

  links.attr({
    x1: d => d.position.x + w2,
    y1: d => d.position.y + h2,
    x2: (d, idx) => data[(idx + 1) % data.length].position.x + w2,
    y2: (d, idx) => data[(idx + 1) % data.length].position.y + h2,
  });

  links.exit().remove();

  // update the particles
  particles.enter().append('circle');

  particles.attr({
    cx: d => d.position.x + w2,
    cy: d => d.position.y + h2,
    r: 3
  });

  particles.exit().remove();
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

总而言之，所描述的系统只使用了几个非常基本的规则，细胞分裂的随机性使得系统以不可预测的方式做出反应。然而，由此产生的模式看起来就像是“有机体”被告知以某种方式生长，尽管代码本身并没有施加这样的压力。作为自然法则本身的结果，这一切都是自我组织。

该你们了，神创论者。:)