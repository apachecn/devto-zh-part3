# 使用节点和链接创建网络图(循环)D3

> 原文：<https://dev.to/aroub72/creating-a-network-graph-using-nodes-and-links-loop-d3-2hmc>

我试图用节点和链接来绘制一个网络图。到目前为止，我只能画出节点。

节点图:

为了绘制这些节点之间的链接，我必须遍历数据来提取节点的位置。以下是数据示例:

```
"links": [
    {"node01": "site05", "node02": "site08", "amount": 10},
    {"node01": "site05", "node02": "site02", "amount": 120},
    {"node01": "site05", "node02": "site03", "amount": 50},
    {"node01": "site05", "node02": "site07", "amount": 80},
    {"node01": "site05", "node02": "site09", "amount": 210},
    {"node01": "site05", "node02": "site10", "amount": 350}]

"nodes": [
    {
      "id": "site09",
      "x": 317.5,
      "y": 282.5
    },
    {
      "id": "site01",
      "x": 112,
      "y": 47
    },
    {
      "id": "site03",
      "x": 69.5,
      "y": 287
    }] 
```

我尝试使用 for 循环，但我认为我的方法不正确。

```
window.onload = function(){
var svgCanvas = d3.select("svg") //setting up the canvas
.attr("width", 960) 
.attr("height", 540) 
.attr("class", "svgCanvas");

    d3.json("map.json", function(data) {
        console.log(data);

 svgCanvas.selectAll("circle.nodes")
        .data(data.nodes).enter() 
        .append("circle") 
        .append("svg:circle")
        .attr("cx", function(data){
            return data.x;})
        .attr("cy", function(data){
            return data.y;})
        .attr("r", 10);

var myMap=new Map();
myMap.set(links.data,nodes.data);
for (var [key,value] of myMap){
    console.log(key+'='+value);
}
    })
} 
```