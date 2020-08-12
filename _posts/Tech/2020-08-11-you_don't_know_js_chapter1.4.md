---
layout: post
title: 第一部分 作用域和闭包 第四章 提升
categories: Tech-Post JavaScript 你不知道的JavaScript
description: 从你不知道的JavaScript书中总结复习知识点，力求精通JavaScript
keywords: 你不知道的JavaScript 第四章 提升 第一部分 作用域和闭包
---

# 第四章 提升

![book](/images/blog/you_dont_know_js.png)


### 先有鸡还是先有蛋

我们一般认为JavaScript代码是自上而下，逐行执行的，其实不然，这里存在一种特殊情况。给出两个例子来引入提升这个话题。

```javascript
a = 2;
var a;
console.log(a);
```
输出的结果并非undefined而是2。

```javascript
console.log(a);
var a = 2
```
输出的结果并非抛出ReferenceError而是undefined。
声明和赋值之间存在微妙的关系


### 编译器再度来袭

引擎在解释JavaScript代码之前首先对其进行编译。编译阶段中的一部分工作就是找到所有的声明，并用合适的作用域将他们关联起来。这也是词法作用域的核心内容。因此变量和函数在内的所有声明都会在任何代码执行前首先被处理。例如，var a = 2 我们认为这是一个声明， 而JavaScript会将它看作两个声明 var a 和 a = 2。第一个定义声明是在编译阶段进行的。第二个赋值声明会被留在原地等待执行阶段。结合两个例子了解一下。

```javascript
var a
a = 2
console.log(a);//2
```
 第一部分是编译，第二部分是执行。

 ```javascript
var a
console.log(a);//undefined
a = 2
```
 第一部分是编译，第二部分是执行。

 这个过程好像变量和函数声明从他们在代码中出现的位置被移动到了最上面，这个过程就叫做提升（Hoisting）。所以声明和赋值的关系是先有声明，再来赋值。只有声明本身会被提升，而赋值或者其他运行逻辑则会留在原地。如果提升改变了代码执行顺序会造成严重的破坏。提升存在于每个作用域中。

此外函数声明会被提升，但是函数表但是却不会被提升，看下面的例子。
```javascript
foo();//TypeError
bar();//ReferenceError
var foo = function bar(){
    //....
}
```
注意此处foo()抛出的是TypeError而不是ReferenceError。这说明变量foo的声明被成功的提升并且分配到全局作用域中了，所以foo()不会导致ReferenceError。但是foo此时并没有赋值，你当然不可以直接函数调用，foo()相当于对一个undefined的值进行函数调用，所以会抛出TypeError。

### 函数优先

函数声明和变量声明都会被提升，但是函数会首先被提升，然后才是变量。

```javascript
foo();// 1
var foo;
function foo(){
    console.log(1);
}

foo = function(){
    console.log(2);
};

```

会输出1而不是2！