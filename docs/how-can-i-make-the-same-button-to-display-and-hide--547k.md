# 如何让同一个按钮显示和隐藏？

> 原文：<https://dev.to/rohit88rp/how-can-i-make-the-same-button-to-display-and-hide--547k>

* * * * * * * * * * * * * * *** * * * * * * * * * * * * * * *
<！DOCTYPE html >* 

 *Ajax

<button id="btn">点击显示</button>
<button id="btn-2">点击隐藏</button>

* * * * * * * * * * * * * * * * * * * * * **JavaScript；*T3；* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * T4]文件。getElementById('btn ')。addEventListener('click '，load function)；

函数 loadFunction(){

```
let xhr = new XMLHttpRequest();

xhr.open('GET','Data.txt', true);

xhr.onload = function(){
    if(this.status === 200){
        document.getElementById('output').innerHTML = `<h1>${this.responseText}</h1>`;
    }
}
xhr.send();
document.getElementById('output').style.visibility = 'visible'; 
```

}
document . get element byid(' BTN-2 ')。addEventListener('click '，load function 2)；

函数 load function 2(){
document . getelementbyid(' output '). style . visibility = ' hidden '；

}*