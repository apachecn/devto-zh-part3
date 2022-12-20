# Javascript 流与生成器

> 原文：<https://dev.to/micahriggan/streams-and-generators-36e1>

在 javascript 中，我们有时不得不处理大量异步输入的数据。

我们可以使用的两个工具是流和生成器。

生成器生成数据，并在每个生成的项目被消耗后继续逻辑。

ReadableStreams 的相同之处在于，当您使用数据时，数据会继续流动。

这两者都非常适合在数据进来时消费数据，并创建数据流。

在这篇文章中，我将展示这两种形式的一些例子，并讨论如何创建和使用这两种形式。

## 例 1:创造和消费

在这个例子中，我们大约每 100 毫秒生成一个随机字符串。我们将拥有一个可以订阅的可读流，以及一个可以迭代的生成器。

我们将调用`getStream`和`getGenerator`来获取这些。，

```
const stream = require('stream');

function getRandomString() {
  return (Math.random() * 16).toString(16)
}

function getStream(time) {
  class TimeStream extends stream.Readable {
    constructor(time) {
      super();
      this.setMaxListeners(100);
      this.streamEnds = Date.now() + time;
    }
    _read(size) {
      setTimeout(() => {
        if(Date.now() < this.streamEnds) {
          this.push(getRandomString());
        } else {
          this.push(null);
        }
      }, 100);
    }
  }
  return new TimeStream(time);
}

function* getGenerator(time) {
  const streamEnds = Date.now() + time;
  while(Date.now() < streamEnds) {
    yield new Promise(resolve => {
      setTimeout(() => {
        resolve(getRandomString());
      }, 100)
    });
  }
}

function testStream() {
  return new Promise(resolve => {
    let i = 0;
    console.time('stream');
    const ds = getStream(1000);
    ds.on('data', (data) => console.log(i++, data.toString()));
    ds.on('end', () => {
      console.log(i++, 'end');
      console.timeEnd('stream')
      resolve();
    });
  });
}

async function testGenerator() {
  let i = 0;
  console.time('generator');
  const generator = getGenerator(1000);
  for(const asyncData of generator) {
    const data = await asyncData;
    console.log(i++, data)
  }
  console.timeEnd('generator');
}

async function main() {
  console.log('Testing stream...');
  await testStream();
  console.log();
  console.log('Testing async generator...');
  await testGenerator();
}

if(require.main === module) {
  main();
}
module.exports = getStream; 
```

### 输出

```
Testing stream...
0 'e.d244c32e8623'
1 '5.b1684670615e'
2 '7.a3ccc5ad7cd5'
3 '1.30698269b999'
4 'a.0df84371e841'
5 'e.04b9ee941ba'
6 'f.b4e328ecf36f'
7 '2.a3834203577d'
8 'f.472469520bcf'
9 'end'
stream: 1027.421ms

Testing async generator...
0 'c.a0345feebe33'
1 '1.3feb27ad4b78'
2 'e.de0f74e641c4'
3 'b.dc5b66f56322'
4 '1.ec8915a4b07c'
5 '2.d94dde53ff09'
6 'd.e8b57a3b028d'
7 '9.6454bafaf36b'
8 '2.01d0ada9e78a'
9 '7.5142faf39563'
generator: 1034.700ms 
```

从这个例子中，我们可以看到异步生成器很难定义。

生成器消耗也非常适合异步 await 和 for 循环。

我们还可以从输出中看到，100ms 迭代中的一次被用于通过按 null 来关闭流。

## 例 2:将流转换为生成器

在最后一个例子中，我们可以对生成器使用 for 循环。在下一个例子中，我们将构建一个从 ReadableStream 到 Generator 的适配器。这将允许我们在流上使用 for 循环。

我们将有一个名为`getStreamAdapter`的函数，它接收一个流，并产生承诺，当下一个项目出现在流中时，这些承诺将被解析。

```
const getStream = require('./data-stream');

function* getStreamAdapter(stream) {
  let done = false;
  stream.on('end', d => {done = true})
  while(!done) {
    yield new Promise((resolve, reject) =>{
      stream.once('data', resolve);
      stream.once('end', resolve);
    });
  }
}

async function testDataGenerator() {
  let i = 0;
  console.time('generator');
  const stream = getStream(1000)
  for (const asyncData of getStreamAdapter(stream)) {
    const data = await asyncData;
    if(data) {
      console.log(i++, data.toString());
    }
  }
  console.timeEnd('generator');
}

if(require.main === module) {
  console.log("Creating a async Generator from a Stream");
  testDataGenerator();
}

module.exports = getStreamAdapter; 
```

### 输出

```
Creating a async Generator from a Stream
0 '6.91038da597eb'
1 '3.ffab4d6c03c4'
2 'c.4d1902e3275f'
3 '3.3b9299bc7c4f'
4 'b.d20e4a03ee2a'
5 '2.9990aca111e6'
6 '5.55a87b7f0c29'
7 '0.a79c544e914d'
8 'e.1cb8d9d24eea'
generator: 1035.196ms 
```

注意，在这个例子中，如果我们不在循环中等待，生成器将继续产生承诺。当流中的下一个值到来时，所有这些承诺都将解决。

如果我们尝试一个典型的 Promise.all 并行化，我们最终会得到一个相同值的数组。

## 例 3:转换流+转换生成器

转换流是我最喜欢的流的用法之一。在这个例子中，我将尝试展示生成器的等价物。

在这个例子中，我们将创建一个转换流，当它流过时输出每个项目的长度

我们还将创建一个生成器，它消耗另一个生成器并输出同样的东西。

```
const {Transform} = require('stream');
const getStream = require('./data-stream');
const toGenerator = require('./stream-conversion');

function getLengthTransformStream(stream) {
  return stream.pipe(new Transform({
    objectMode:  true,
    transform: (data, encoding, cb) => {
      cb(null, data.toString().length);
    }
  }));
}

function* getLengthTransformGenerator(generator) {
  for(const item of generator) {
    if(item.then) {
      yield item.then(i => i && i.toString().length);
    } else {
      yield item && item.toString().length;
    }
  }
}
function testTransformStream() {
  return new Promise(resolve => {
    const timedStream = getStream(1000);
    getLengthTransformStream(timedStream)
      .on('error', console.log)
      .on('data', console.log)
      .on('end', resolve);
  });
}

async function testTransformGenerator() {
  const timedGenerator = toGenerator(getStream(1000));
  const lengthGenerator = getLengthTransformGenerator(timedGenerator);
  for(const asyncLength of lengthGenerator) {
    const length = await asyncLength;
    if(length !== undefined) {
      console.log(length);
    }
  }
}

async function main() {
  console.log('Testing TransformStream...');
  await testTransformStream();
  console.log();
  console.log('Testing TransformGenerator...');
  await testTransformGenerator();
}

if(require.main === module) {
  main();
} 
```

### 输出

```
Testing TransformStream...
14
14
14
14
14
14
14
13
14

Testing TransformGenerator...
13
14
14
14
14
14
14
13
14 
```

## 结论

流和生成器是处理数据的强大方法。如果你需要一些快速的东西供异步 await 和 for 循环使用，那么就使用生成器。如果你的代码与其他流接口，那么使用一个流。管道和转换使得流在一起非常有用。

[从我的博客上传的十字](https://micahr.me/streams-and-generators/)