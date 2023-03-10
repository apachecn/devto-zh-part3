# 保护您的模板，无需繁琐的* ngIfs

> 原文：<https://dev.to/thllbrg/protect-your-templates-without-tedious-ngifs-5emc>

**问题:**
我们在模板中使用的属性有时是未定义的或空的，这会导致我们的模板无法渲染。我们可以通过用`*ngIf`包装潜在的问题区域来保护我们的代码，但是对于更长的属性路径来说，这很快就变得很麻烦。

**解决方案:**
通过使用 Angulars 安全导航操作符`?.`，也称为 Elvis 操作符，我们可以减少模板中不必要的检查，确保我们只通过安全导航操作符访问可能为空或未定义的属性。

## 之前/未到期:

**分量**

```
pastery = {
    name: 'Cinnamon bun',
    ingredients: {
      eggs: null,
      milk: null
    }
} 
```

**模板**

```
<h2>Ingredients</h2>

<div *ngIf="pastery.ingredients.eggs">
  {{ pastery.ingredients.eggs.qty.amount }} {{ pastery.ingredients.eggs.qty.unit && pastery.ingredients.eggs.qty.unit.name }} {{ pastery.ingredients.eggs.name }}
</div>

<div *ngIf="pastery.ingredients.milk">
  {{ pastery.ingredients.milk.qty.amount }} {{ pastery.ingredients.milk.qty.unit && pastery.ingredients.milk.qty.unit.name }} {{ pastery.ingredients.milk.name }}
</div> 
```

## 后:

**分量**

```
pastery = {
    name: 'Cinnamon bun',
    ingredients: {
      eggs: null,
      milk: null
    }
} 
```

**模板**

```
<h2>Ingredients</h2>

<div>
 {{ pastery.ingredients.eggs?.qty.amount }} {{ pastery.ingredients.eggs?.qty.unit?.name }} {{ pastery.ingredients.eggs?.name }}
</div>

<div>
  {{ pastery.ingredients.milk?.qty.amount }} {{ pastery.ingredients.milk?.qty.unit?.name }} {{ pastery.ingredients.milk?.name }}
</div> 
```

自己试试:
[https://stackblitz.com/edit/thllbrg-angular-fika-3](https://stackblitz.com/edit/thllbrg-angular-fika-3)

官方文档:
[https://angular . io/guide/template-syntax # the-safe-navigation-operator-and-null-property-paths](https://angular.io/guide/template-syntax#the-safe-navigation-operator----and-null-property-paths)