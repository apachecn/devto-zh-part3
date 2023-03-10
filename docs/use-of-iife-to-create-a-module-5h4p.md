# 用生命创造一个模块

> 原文：<https://dev.to/vish448/use-of-iife-to-create-a-module-5h4p>

立即调用的函数表达式(IIFE)通常用于将相关功能组合到单个对象或模块中。例如，假设我们需要将这两个 mixins 包装在一个模块中。

```
 function glideMixin(obj){
    obj.glide = function(){
        console.log("Gliding on the water");
    };    
}

function flyMixin(obj){
    obj.fly = function(){
        console.log("Flying Woohoo");
    };
} 

```

我们可以使用 IIFE 将这两个 mixin 组合在一个模块中。请看下面我们如何做到这一点。

```
 let motionModule = (function(){
    return {
        glideMixin: function(obj){
            obj.glide = function(){
                console.log("Gliding on the water");
            };
        },
        flyMixin: function(obj){
            obj.fly = function(){
                console.log("Flying Woohoo");
            };
        }
    }
})(); 

```

请注意，您有一个立即调用的函数表达式(IIFE ),它返回一个对象 motionModule。这个返回的对象包含所有 mixin 行为作为对象的属性。

模块模式的优势在于，所有的动作行为都可以打包到一个单独的对象中，然后由代码的其他部分使用。下面是一个使用它的例子:
 `motionModule.glideMixin(anyObject);
anyObject.glide();`