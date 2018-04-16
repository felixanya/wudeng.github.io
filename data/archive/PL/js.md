


我的理解：

一个文件就是一个模块，每个模块有一个module.exports对象。其他文件reqire这个文件的时候，就获得了这个对象的引用。

可以理解require为下面这个函数：
var require = function(path) {
    // ...
    return module.exports;
}


## anonymous closure: functions are the only way to create scope
(function(){
    ...
}())
注意最外层的括号是必须的。否则function开头的语句被认为是函数声明。


## Global import

(function(globalVariable){
    globalVariable.each = function(){};
    ...
}(globalVariable))



## object interface

var myGradeCalculate = (function(){
    return {
        average : function() {
        },
        failing : function() {
        }
    }
}());


## revealing module pattern

var myGradeCalculate = (function(){
    average = function() {
    };
    failing = function() {
    };

    return {
        average : average,
        failing : failing
    }
}());

## DOM

* document.addEventListener(event, function, useCapture)
    - event 没有on前缀，如`click`，`dblclick`
* document.removeEventListener()

* element.addEventListener()
- click
- dblclick

## CommonJS ans AMD

function myModule() {
  this.hello = function() {
    return 'hello!';
  }

  this.goodbye = function() {
    return 'goodbye!';
  }
}

module.exports = myModule;


使用时：

var myModule = require('myModule');

var myModuleInstance = new myModule();
myModuleInstance.hello(); // 'hello!'
myModuleInstance.goodbye(); // 'goodbye!'



相比上面几种方式有如下好处：
* avoiding global namespace pollution
* making our dependencies explicit

AMD: Asynchronous Module Definition


* Backbone.js MVC框架
* Angular Google公司推出的MVVM前端框架
* Vue MVVM框架，基本思想跟Angular类似，但是用法更简单，而且引入了响应式编程的概念。


## canvas

```
<canvas id="myCanvas" width="200" height="100"></canvas>
```

canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");

左上角为原点，x向右，y向下。

画线：
ctx.moveTo(x1, y1)
ctx.lineTo(x2, y2)
ctx.stroke()

## 框架和库
框架和库的区别在于控制权。框架call你(控制反转)，你call库。框架包含库。
框架如：angular, backbone, vue
库：jQuery, react, underscore

## 构建工具

webpack1 Browserify
webpack2 rollup


某些网站禁止拷贝，可以执行这个函数来取消禁止。
function allow() {return true};document.onselectstart=allow;document.ondragstart=allow;document.oncontextmenu=allow;

* http://www.ruanyifeng.com/blog/2016/11/javascript.html
* http://javascript.ruanyifeng.com/
