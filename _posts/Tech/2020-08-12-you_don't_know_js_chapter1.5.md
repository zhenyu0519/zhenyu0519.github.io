---
layout: post
title: 第一部分 作用域和闭包 第五章 作用域闭包 
categories: Tech-Post JavaScript 你不知道的JavaScript
description: 从你不知道的JavaScript书中总结复习知识点，力求精通JavaScript
keywords: 你不知道的JavaScript 第五章 作用域闭包  第一部分 作用域和闭包
---

# 第五章 作用域闭包 

![book](/images/blog/you_dont_know_js.png)


### 实质问题

闭包的直接定义： 当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。直接看代码

```javascript
function foo(){
    var a = 2

    function bar(){
        console.log(a);// 2
    }
    bar()
}

foo()
```
以上这段代码看似符和闭包定义，但其实并不是真正意义上的闭包，更确切地说是bar()对a的引用方法是词法作用域的查找规则，它只是闭包的一部分。

接下来看一下真正的闭包是怎样的。

```javascript
function foo(){
    var a = 2

    function bar(){
        console.log(a);// 2
    }
    return bar
}

var baz = foo();
baz(); //2 这才是闭包的效果
```
函数bar()的词法作用域能够访问foo()的内部作用域。然后将bar()函数本身当作一个值类型进行传递。在这个例子中，bar所引用的函数对象本身当作返回值。

在foo()执行后，其返回值赋给变量baz并调用baz()，实际上是通过不同的标识符引用调用了内部的函数bar。在这个例子中bar()在自己定义的词法作用域以外的地方执行。

在foo()执行后，通常会期待foo()的整个内部作用域都被销毁，因为引擎的垃圾回收机制。由于看上去foo()的内容不会再被使用，所以很自然地会考虑对其进行回收。

而闭包的神奇之处是可以做值垃圾回收。事实上，内部作用域依然存在，因此没有被回收。因为bar()本身在使用。由于bar()声明的位置涵盖foo()内部作用域的闭包，所以该作用域会一直存活，以供bar()再之后任何时间进行引用。bar()依然持有对该作用域的引用，而这个引用就叫作闭包。

这个函数在定义时的词法作用域以外的地方被调用。闭包使得函数可以继续访问定义时的词法作用域。

下面多给几个关于闭包的例子自己体会

```javascript
function foo(){
    var a = 2

    function bar(){
        console.log(a);// 2
    }
    bar(baz)
}

function bar(fn){
    fn()
}
```

```javascript
var fn;

function foo(){
    var a = 2

    function bar(){
        console.log(a);
    }
    fn = baz; //将baz分配给全局变量
}

function bar(fn){
    fn()
}

foo()

bar()// 2
```
看完这些例子之后再想想，感觉闭包就是，函数在自己的词法作用域以外执行并且词法作用域因需要被外部调用而被完整保持。换句话说，我们可以把闭包理解为一个手提包，手提包里面装了很多日用品，平时这个手提包是关上的，我们看不到里面，但是当我们需要不同的日用品的时候，我们就打开手提包把需要的东西取出来。闭包创建了一个封闭的作用域，外界需要执行的时候，就进入这个作用域中执行代码。


### 现在我懂了

闭包的引用非常广泛，并非是罕见的。常用的有setTimeout()等，当然闭包在实际开发中的应用涵盖了各个方面，例如定时器，事件监听，ajax请求，跨窗口通信，或者任何其他异步的任务中，只要用到了回调函数，实际上就是在使用闭包。

至于立即执行函数，很多人认为是闭包，其实不是的，原因是立即执行函数并非是在它本身的词法作用域以外执行的，而是在他定义是所在的作用域中执行的。


### 循环和闭包

这一小节主要讲了一道经典的面试题，由此来理解闭包在循环中的执行。直接上代码。
```javascript
for (var i = 1; i<=5; i++){
    setTimeout(function timer(){
        console.log(i)
    }, i * 1000);
}

console.log(i) //6
```
这段代码我们希望分别输出1 ～ 5， 每秒一次，每次一个。但是实际输出是会以每秒一次的频率输出五次6。导致这个结果的原因是看似循环中的每次迭代在运行时都会给自己捕获一个i的副本。实际上尽管循环中的五个函数在各个迭代中是分别定义的，但是他们都被封闭在一个共享的全局作用域中（因为var），因此实际上只有一个i。注意i存在于全局作用域中，因此可以打印出i。

正确的修改方法是:
```javascript
for (let i = 1; i<=5; i++){
    setTimeout(function timer(){
        console.log(i)
    }, i * 1000);
}

console.log(i) // i is not defined
```
这时候输出的就是预期的结果，注意在全局作用域中i变成了undefined说明i不存在于全局作用域，而是存在于每次迭代的块作用域中，原因是因为let将代码块封闭在了块作用域中。

这一段可以细细品味一番。

### 模块

闭包的另一个强大作用是模块。
我们先看一下函数作用域嵌套的函数是如何转换成模块的。

作用域嵌套的代码
```javascript
function foo(){
    var something = 'cool';
    var another = [1,2,3];

    function doSomething(){
        console.log(something);
    }

    function doAnother(){
        console.log(another.join('!'));
    }
}
```

变成模块
```javascript
function CoolModule(){
    var something = 'cool';
    var another = [1,2,3];

    function doSomething(){
        console.log(something);
    }

    function doAnother(){
        console.log(another.join('!'));
    }

    return {
        doSomething: doSomething,
        doAnother: doAnother
    };
}

var foo = CoolModule();
foo.doSomething() // cool
foo.doAnother() // 1!2!3!
```

* CoolModule()只是一个函数，必须要通过调用它来创建模块实例，如果不执行外部，那么作用域和闭包都无法被创建。
* CoolModule()返回一个用对象字面量语法来表示的对象，这个返回对象中含有对内部函数的引用。内部变量是私有且隐藏的状态。可以讲这个对象类型的返回值看作是该模块的API。
* 这个对象类型的返回值最终被赋值给外部的变量foo，然后通过它来访问API中的方法。

因此我们总结出模块的两个必备条件
1. 必须有外部的封闭函数，该函数必须至少被调用一次，用来创建一个新的模块实例
2. 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有状态

模块也是函数，也可以接受参数
```javascript
function CoolModule(id){

    function identify(){
        console.log(id);
    }

    return {
        identify: identify
    };
}

var foo1 = CoolModule(1);
var foo2 = CoolModule(2);
foo1.identify() // 1
foo2.identify() // 2
```

最后说一下 import 和 export, import 可以将一个模块中的一个或多个API导入到当前作用域中，并分别绑定在一个变量上。module会将整个模块的API导入并绑定到一个变量上。export会将当前模块的一个标识符导出为公共API。这些操作可以在模块定义中根据需要使用任意多次。