# 以角度呈现大型集合的性能提示

> 原文：<https://dev.to/thllbrg/performance-tip-for-rendering-large-collections-in-angular-3o24>

**问题:**
为了在 Angular 中渲染集合，我们使用了内置指令`*ngFor`，只要我们只是显示或改变集合，它就能很好地工作。当我们*重新分配*保存集合的变量时，一个潜在的问题出现了。Angular 必须销毁所有旧的 DOM 节点并重新创建它们，即使只有少数元素发生了变化，对于大型集合来说，这可能会产生性能问题。

**解决方案:**
创建一个函数，为集合中的每个元素返回一个惟一的 id，然后将该函数传递给我们与`*ngFor`一起使用的指令 TrackedBy。

## 之前/未到期:

**分量**

```
items = [
  {title: 'Cinnamon buns'},
  {title: 'Cup cakes'},
  {title: 'Croissants'}
];

update() {
  this.items = [
    {title: 'Cinnamon buns'},
    {title: 'Cup cakes'},
    {title: 'Croissants'},
    {title: 'Macarons'}
  ];
} 
```

**模板**

```
<div *ngFor="let item of items">
  <app-item [text]="item.title"></app-item> </div> 
```

**日志**

```
naive - Cinnamon buns initialized
naive - Cup cakes initialized
naive - Croissants initialized
Updated data
naive - Cinnamon buns initialized
naive - Cup cakes initialized
naive - Croissants initialized
naive - Macarons initialized 
```

## 后:

**分量**

```
items = [
  {title: 'Cinnamon buns'},
  {title: 'Cup cakes'},
  {title: 'Croissants'}
];

update() {
  this.items = [
    {title: 'Cinnamon buns'},
    {title: 'Cup cakes'},
    {title: 'Croissants'},
    {title: 'Macarons'}
  ];
}

itemsTrackedBy(index, item) {
  return item.text;
} 
```

**模板**

```
<div *ngFor="let item of items; trackBy: itemsTrackedBy">
    <app-item [text]="item.title"></app-item> </div> 
```

**日志**

```
trackedBy - Cinnamon buns initialized
trackedBy - Cup cakes initialized
trackedBy - Croissants initialized
Updated data
trackedBy - Macarons initialized 
```

自己试试:
[https://stackblitz.com/edit/thllbrg-angular-fika-2](https://stackblitz.com/edit/thllbrg-angular-fika-2)

官方文件:
[https://angular.io/guide/template-syntax#ngfor-with-trackby](https://angular.io/guide/template-syntax#ngfor-with-trackby)