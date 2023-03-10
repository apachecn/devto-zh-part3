# 整理你的角度模板

> 原文：<https://dev.to/thllbrg/declutter-your-angular-template-2jc>

**问题:**

**解决方案:**
利用有角的内置`i18nPlural`管道。

## 之前/未到期:

**分量**

```
count = 0; //0, 1, 2 
```

**模板**

```
<span *ngIf="count === 0">You have no cinnamon buns left</span> <span *ngIf="count === 1">You have one cinnamon bun left</span> <span *ngIf="count > 1">You have a {{count}} cinnamon buns left</span> 
```

## 后:

**分量**

```
count:number = 0;
bunsLeftMessages = {
   '=0': 'You have no cinnamon buns left',
   '=1': 'You have one cinnamon bun left',
   'other': 'You have # cinnamon buns left'
}; 
```

**模板**

```
{{ count | i18nPlural:bunsLeftMessages }} 
```

自己试试:
[https://stackblitz.com/edit/thllbrg-angular-fika-1](https://stackblitz.com/edit/thllbrg-angular-fika-1)

官方文件:
[https://angular.io/api/common/I18nPluralPipe](https://angular.io/api/common/I18nPluralPipe)