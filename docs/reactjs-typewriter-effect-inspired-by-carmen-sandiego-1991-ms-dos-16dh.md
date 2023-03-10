# ReactJS 打字机效果灵感来自卡门·桑迪戈 1991 MS-DOS

> 原文：<https://dev.to/julienverkest/reactjs-typewriter-effect-inspired-by-carmen-sandiego-1991-ms-dos-16dh>

**[剧透:结果](https://codepen.io/julien180/pen/jRQvrY)**

在 90 年代早期，我曾经在世界上的哪个地方玩卡门·桑迪戈游戏。现在我长大了，我不再玩了，但我决定用 ReactJS 克隆 1991 年的 MS-DOS 版本。

## 第一部分:HTML 和 CSS

```
<div class="cs-instructions">
  <div class="cs-instructions-inner">
      <p>****** FLASH ******</p>
      <p>National treasure stolen from Paris.</p>
      <p>The treasure has been identified as the elevator from the Eiffel Tower.</p>
      <button type="button" onClick="nextStep();">Click to continue</button> 
  </div>
</div> 
```

```
.cs-instructions {
  background-image: url('https://i.imgur.com/1woliuF.png');
  background-size: contain;
  background-position: bottom left;
  background-repeat: no-repeat;
  position: relative;
  z-index: 1;
  height:342px;
  width: 467px;
} 

.cs-instructions-inner {
  background-color:#fff;
  background-image: url('https://i.imgur.com/6aOej4c.jpg');
  background-size: contain;
  background-position: bottom left;
  background-repeat:repeat-y;
  font-size:14px;
  line-height:30px;
  padding:10px 30px 10px 30px;
  margin:10px 0 0 38px; 
  min-height:120px;
  width:330px;
  overflow-y:hidden;
  position: absolute;
  color:#000;
  z-index: 2;
  bottom:190px;
} 
```

基本上我们需要两个`<div>`:

*   第一个类名为`cs-instructions`的是打字机，它有一个`position: relative;`属性，带有这个[背景图像](https://i.imgur.com/1woliuF.png) ![bg typewriter](img/f429d267427c1eca617a35b59484d633.png)
*   第二个类名为`cs-instructions-inner`的是`position: absolute;bottom:190px;`上的打字纸，其背景图像[在 y 轴上重复。![bg paper](img/e1d3fb88a49e6d41e6fb7b7a5055ed67.png) ![bg paper](img/e1d3fb88a49e6d41e6fb7b7a5055ed67.png) ![bg paper](img/e1d3fb88a49e6d41e6fb7b7a5055ed67.png)我们加了一个`overflow-y:hidden`来防止指令滚动。](https://i.imgur.com/6aOej4c.jpg)

## 第二部分:打字机和自动卷香草

```
<div class="cs-instructions">
  <div class="cs-instructions-inner">
      <button type="button" onClick="launch();">Click to start the case</button> 
      <div class="typewriter"> </div>
  </div>
</div> 
```

我们添加了一个显示文本的 div。按钮有一个 onClick 事件，它调用一个启动函数。

```
autoScroll = () => {
    const el = document.getElementsByClassName('cs-instructions-inner');
    el.scrollTo(0, el.scrollHeight);
  }

  typeWriter = (string, i=0) => {
    if (i < string.length) {
      document.getElementsByClassName('typewriter')[0].innerHTML += string.charAt(i); // display character one by one
      i++;
      autoScroll;
      setTimeout(() => typeWriter(string, i), 40); // speed
    }
  }

 launch = () => {
   var string = '*** FLASH ***';
   string += 'National treasure stolen from Paris.'; 
   string += 'The treasure has been identified as the elevator from the Eiffel Tower.';
   string += 'Female suspect reported at the scene of the crime. Your assignement: Track the thief from Paris to her hideout and arrested her!';
   typeWriter(string);
 } 
```

在底部我们有 **launch()** 功能。我们将文本存储在一个字符串变量中，并将其传递给带有两个参数的*递归* **typeWriter()** 函数:字符串和初始化为 0 的索引。当字符串的索引`i`等于字符串的长度时，我们脱离递归函数。在条件中，我们调用 **autoScroll()** 函数来确保我们总是在 div `cs-instructions-inner`的底部。

预览此步骤
[https://codepen.io/julien180/embed/MRZwGY?height=600&default-tab=result&embed-version=2](https://codepen.io/julien180/embed/MRZwGY?height=600&default-tab=result&embed-version=2)

## 第 3 部分:带音效的 ReactJs 打字机组件

```
class Typewriter extends React.Component {
  constructor(props){
    super(props);
    this.myDiv = null;
    this.mySound = null;
    this.typewriter = element => {
      this.myDiv = element;
    }; 
    this.setmySound = element => {
      this.mySound = element;
    };

    this.playSound = this.playSound.bind(this);
    this.pauseSound = this.pauseSound.bind(this);
  }

  playSound() {
    if (this.mySound && (this.mySound.duration === 0 || this.mySound.paused)) {
      this.mySound.play();
    }
  } 

  pauseSound() { 
    if(this.mySound){
      this.mySound.pause();
    }
  }

  typeWriter = (s, i=0) => {
    if (this.myDiv && i < s.length) {
      this.myDiv.innerHTML += s.charAt(i);
      i++;
      if(i>5) { 
        this.props.setScroll(); 
      }
      this.playSound();
      setTimeout(() => this.typeWriter(s, i), 40);
    }
    else {
       this.pauseSound();
       if(this.props.nextStep){
        this.props.nextStep();
       } 
    }
  }

  componentDidMount() {
    if(this.myDiv) {
      setTimeout(() => this.typeWriter(this.props.mystring), 200);
    }
  }

  componentWillUnmount() {
    this.pauseSound();
  }

  render() {
    return (
      <div className={this.props.mb}> 
        <audio autoPlay preload="auto" ref={this.setmySound} src="https://freesound.org/data/previews/138/138049_2423928-lq.mp3" type="audio/mpeg" > </audio>
        <span ref={this.typewriter}> </span>
      </div>
    );
  }
} 
```

我们的组件看起来像`<Typewriter mb={'mb-4'} nextStep={this.nextStep} setScroll={this.setScroll} mystring="National treasure stolen from Paris."></Typewriter>`

1.  **JSX** :我们在 div 包装器处传递一个 margin-bottom *mb* `props`，在它里面我们有一个`<audio>`标签和一个`<span>`标签。它们都有一个`ref`属性，因为我们需要访问`DOM nodes`并与之交互。我们使用**回调 refs** ，它对 refs 的置位和复位进行更精细的控制。查看更多官方文件[https://reactjs.org/docs/refs-and-the-dom.html](https://reactjs.org/docs/refs-and-the-dom.html)
2.  **componentDidMount()** :当组件被挂载时，我们启动递归的**打字机()**函数。
3.  **打字机(s，i=0)** 不是纯函数 lol。很多事情都实现了。首先，字符串由`this.props.mystring`道具传递。在 typeWriter()函数中，我们执行 autoScroll()，我们播放声音，并在`myDiv ref`上逐个插入字符。当递归函数开始时，我们执行另一个函数`this.props.nextStep()`，同时我们停止音效`this.pauseSound()`。
4.  playSound() pauseSound() 当然我们添加并处理音效。
5.  **componentWillUnmount()** 当组件被卸载时，我们停止声音。

## 第 4 部分:ReactJs 处理步骤组件

```
class App extends React.Component {
  constructor(){
    super();
    this.state = {
      step: 0
    }
    this.myDiv = null;
    this.instructions = element => {
      this.myDiv = element;
    };
    this.setScroll = this.setScroll.bind(this);
  }

  setScroll() {
    this.myDiv.scrollTo(0, this.myDiv.scrollHeight);
  }

  nextStep = () => this.setState({step: this.state.step + 1});

  render(){
    return(
      <div className="wrapper">
        <div className="cs-instructions">
          <div className="cs-instructions-inner" ref={this.instructions}>
            {
              this.state.step === 0
              ? <button onClick={() => {this.nextStep()}}>CLICK TO START THE CASE</button>
              : ''
            }

            {
              this.state.step > 0 
              ? <Typewriter mb={'mb-4'} nextStep={this.nextStep} setScroll={this.setScroll} mystring="****** FLASH ******"></Typewriter>
              : ''
            }

            {
              this.state.step > 1 
              ? <Typewriter mb={'mb-4'} nextStep={this.nextStep} setScroll={this.setScroll} mystring="National treasure stolen from Paris."></Typewriter>
              : ''
            }

            {
              this.state.step > 2 
              ? <Typewriter mb={'mb-0'} nextStep={this.nextStep} setScroll={this.setScroll} mystring="The treasure has been identified as the elevator from the Eiffel Tower."></Typewriter>
              : ''
            }

            {
              this.state.step > 3 
              ? <button className="mb-4" onClick={() => {this.nextStep()}}>Click to continue</button>
              : ''
            }
            {
              this.state.step > 4 
              ? <Typewriter mb={'mb-0'} nextStep={this.nextStep} setScroll={this.setScroll} mystring="Female suspect reported at the scene of the crime. Your assignement: Track the thief from Paris to her hideout and arrested her!"></Typewriter>
               : ''
            }

             {
              this.state.step > 5 
              ? <button className="mb-4" onClick={() => {this.nextStep()}}>Click to continue</button>
              : ''
            }

            {
              this.state.step > 6 
              ? <Typewriter mb={'mb-0'} setScroll={this.setScroll} mystring="You must apprehend the thief by Sunday, 5 pm. Good luck ! "></Typewriter>
               : ''
            }
          </div>
        </div>
        <div className="credits">Typewriter effect realised with ReactJS inspired by Carmen Sandiego 1991 MS-DOS <br/><a href="https://www.julien-verkest.fr/" target="_blank">Julien Verkest ©️ april 2019 </a> </div>
       </div>
    );
  }
} 
```

1.  **构造函数()**:我们声明了一个**状态**，其中`step` init 为 0，我们有一个`nextStep()`函数，它增加了步骤的数量。我们还有`setScroll`功能和`ref instructions`。
2.  JSX:步骤在条件括号内，也许我们需要重构代码，但这是可行的！

## 第五部分:结果

[https://codepen.io/julien180/embed/jRQvrY?height=600&default-tab=result&embed-version=2](https://codepen.io/julien180/embed/jRQvrY?height=600&default-tab=result&embed-version=2)