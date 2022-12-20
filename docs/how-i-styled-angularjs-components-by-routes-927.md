# 我如何通过路线设计 AngularJS 组件

> 原文：<https://dev.to/okevese/how-i-styled-angularjs-components-by-routes-927>

在一个老的项目中，一个基于卡路里输入建议膳食的应用程序，在前端使用 AngularJS，我一直使用`ng-style`指令来设置模板中的 CSS 样式。

控制器是这样的:

```
 (function() {
  angular
    .module('foodApp')
    .controller('calorieCtrl', calorieCtrl);

  function calorieCtrl() {
    var vm = this;

    vm.styleCalorieInput = {
      "background-color": "#3cb9c6",      
      "border-color": "transparent",      
      "border-radius": "10px"
    };

    vm.styleError = {
      "color": "#f3c4a3"
    };
    ...
}(); 
```

在这里，我使用了 [controllerAs](https://docs.angularjs.org/guide/controller) 语法来创建一个视图模型，我可以将我的数据绑定到这个模型，这样我就可以在模板中访问它，如下:

```
...
<div class="well" ng-style="vm.styleCalorieInput">
  <span ng-style="vm.styleError">...</span>
</div>
... 
```

尽管这种方法可行，但我不喜欢在组件中设置样式，我想为每个组件定义一个单独的样式表，就像我可以用 Angular 一样，如果我想的话，仍然可以在一个文件中为整个应用程序定义样式表。

我发现 [angular-route-styles](https://github.com/tennisgent/angular-route-styles) 允许我使用`$routeProvider`服务声明特定于路线的样式，它完美地解决了我的问题。

我通过将`route-styles.js`复制到我的`lib`文件夹中来使用它，并将文件包含在`index.html`中，就像这样:

```
 <!DOCTYPE html>
<html ng-app="foodApp">
...
  <body ng-view>
    <script src="/lib/route-styles.js"></script>
    ...
  </body>
</html> 
```

现在我们需要将`routeStyles`作为模块依赖添加到`app.js`中，并将`css`属性和值(在哪里可以找到 css 文件)添加到`when`路由定义中。

```
(function() {
  // Adds `routeStyles` along with `ngRoute` dependency
  angular.module('foodApp', ['ngRoute', 'routeStyles']);

  function config($routeProvider) {
    $routeProvider
      .when('', {
        templateUrl: '/calorie/calorie.view.html',
        controller: 'calorieCtrl',
        controllerAs: 'vm',
        css: '/calorie/calorie.css'
      })
      .otherwise({ redirectTo: '/' });
  }

   angular
    .module('foodApp')
    .config(['$routeProvider', config]);

})(); 
```

现在我可以从控制器中删除所有样式数据绑定，从视图中删除`ng-style`指令。控制器现在看起来像这样，删除的部分被注释掉:

```
// calorie/calorie.js

(function() {
  angular
    .module('foodApp')
    .controller('calorieCtrl', calorieCtrl);

  function calorieCtrl() {
    var vm = this;

    /**
    vm.styleCalorieInput = {
      "background-color": "#3cb9c6",      
      "border-color": "transparent",      
      "border-radius": "10px"
    };

    vm.styleError = {
      "color": "#f3c4a3"
    }; 
    */
    ...
}(); 
```

在视图中，我将`ng-style`指令更改为具有相似名称的类(有更好的方法来做到这一点):

```
...
<div class="well styleCalorieInput">
  <span class="styleError">...</span>
</div>
... 
```

并将 CSS 属性移动到同一文件夹中新创建的`calorie.css`文件:

```
.styleCalorieInput {
  background-color: #3cb9c6;
  border-color: transparent;
  border-radius: 10px;
}

styleError {
   color: #f3c4a3;
} 
```

如果你好奇，你可以在 StackOverflow 上阅读更多关于 [Tennisgent](https://github.com/tennisgent/angular-route-styles) 如何实现他的解决方案[的内容。](https://stackoverflow.com/questions/15193492/how-to-include-view-partial-specific-styling-in-angularjs)