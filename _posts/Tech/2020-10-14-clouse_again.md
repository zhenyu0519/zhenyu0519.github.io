---
layout: post
title: 再谈JavaScript中的闭包
categories: Tech-Post JavaScript
description: The closure in JavaScript
keywords: JavaScript closure
---

![Closure](/images/blog/closure.jpeg)


## 什么是闭包

闭包的概念实在是很绕，知乎，掘金，简书， stack overflow上各种大神给了自以为很专业的解释。然而这些解释其实是建立在你已经理解闭包的基础上才能完全看懂的。我个人在学习闭包初期的时候，看了解释反而更迷惑。索性我们就看官网解释吧。

A closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). In other words, a closure gives you access to an outer function’s scope from an inner function. In JavaScript, closures are created every time a function is created, at function creation time.

翻译过来：

一个函数和对其周围状态（lexical environment，词法环境）的引用捆绑在一起（或者说函数被引用包围），这样的组合就是闭包（closure）。也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域。在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来。

这段翻译中，我个人认为两点最为重要
* 闭包让你可以在一个内层函数中访问到其外层函数的作用域
* 闭包会在函数创建的同时被创建出来

如何理解？

闭包让你可以在一个内层函数中访问到其外层函数的作用域，此处注意是访问到**外层函数的作用域**而不是**外层作用域**说白了，闭包建立在函数嵌套的基础上，允许内层函数访问外层函数作用域，内层函数顾名思义，就是在函数嵌套中，嵌套在函数里面的函数，而外层函数则是针对内层函数而言包裹其他函数的函数，好了已经开始比较绕了。至于怎么访问，我们一会再说。

闭包会在函数创建的同时被创建出来，这句话是这样理解的，闭包的作用域由内层函数创建时所在的作用域决定。后面我也会举例说明。

简单的了解一下闭包的概念，接下来举例说明

## 闭包的形式

闭包的实现形式有两种
* 函数作为参数传入另一个函数。（**注意：作为参数的函数就是内层函数，而接受的该函数作为参数的函数就是外层函数**）
* 函数作为返回值返回。（**注意：返回的函数就是内层函数**）

相信有过前端经验的小伙伴都用过闭包，最典型的就是回调函数（call back）和事件绑定，它们正是以函数作为参数实现闭包的一种形式。至于函数作为返回值，我们也不陌生，前端代码模块化就是其中一种。

如果闭包的概念实在难理解，那么以下的实现形式会让你更容易明白闭包。

接下来看一下代码

### 闭包，函数作为参数
```javascript
let name = 'Lili'

function getStudent(func){
  let name='John'
  let age = 20
  func(age)
  
}

function display(age){
  console.log(name,age)
}

getStudent(display)


// result: "Lili", 20
```
以上函数中，display函数（**内层参数**）作为参数传入到getStudent函数（**外层函数**）中，并且得到了getStudent中的age参数（**外层函数作用域中的参数**），最终显示出了学生的姓名和年龄。现在回看一下这句话“闭包让你可以在一个内层函数中访问到其外层函数的作用域”是不是就不难理解了？还没结束，有的小伙伴可能会问，为什么打印出来的是“Lili”而不是“John”，明明John离display函数更近一些。

这就要说闭包的第二个概念，闭包的作用域由内层函数创建时所在的作用域决定。内层函数（display）创建时或者说定义时所在的作用域位于全局作用域中，全局作用域中存在name参数，自然而然，这个name就被闭包函数征用了。至于外层函数中的name虽然离得比较近，可是却有一道深深的屏障挡在内外层函数中间。


### 闭包，函数作为返回值
```javascript
let name = 'Lili'

function getStudent(){
  let name = 'John'
  return function display(){
    console.log(name)
  }
}

let getInfo = getStudent()
getInfo() 


// result: "John"
```
看完第一个例子，第二个例子也不难理解了。display函数（**内层函数**）作为getStudent函数（**外层函数**）的返回值返回，我们定义一个变量getInfo将返回的内层函数赋值给这个变量，该变量就变成了一个函数，此时执行getInfo()后，就会返回学生信息。同样的，内层函数（display）定义在外层函数（getStudent）的作用域中，所以此时变量name由定义内层函数的作用域提供了，因此name变成了“John”。

## 应用场景

基于闭包的形式，我们在web开发当中会大量的用到，比如是针对dom元素绑定事件的操作，其中触发事件就用到了回调函数（闭包，函数作为参数）例如，onclick， onchange， onload等等还有异步编程例如ajax。

此外闭包可以用来做访问控制，例如在函数中创建局部变量，同时开放一个对外的接口函数（闭包，闭包函数作为返回值）允许外部访问函数内部的局部变量(可以避免污染全局作用域的同时，又有效的防止私有数据泄漏)，这也是模块化编程的基础。

最后还有一个重要的应用，由于闭包可以让变量始终保存在内存中。因而我们可以实现函数缓存，但是有利有弊。少量的缓存可以起到优化的作用（例如防抖，debounce），但是如果乱用闭包作为缓存的话，该释放的内存没有得到释放就会导致内存泄漏。这一点用代码来稍稍演示展示一下

### 一般函数执行完后，会释放内存
```javascript
let a = 100

function display(){
let b = 10
	return a+b
}

console.log(display(a))

// result: 100
```
在一般函数中，函数执行完后，js的垃圾回收机制会认为a和b已经完成了任务，未来不再使用，因此从内存中清空， 因此a,b将从内存中消失（垃圾回收）

### 在闭包中，函数执行完后，不会释放内存
```javascript
let a = 100

function display(){
	return function (num){
		let b = 10
		return num+b
	}
}

let call = display()
console.log(call(a))

// result: 100
```
而在闭包中，函数执行完后，返回了一个新的函数。这意味着，对于返回的这个函数来说，随时都有可能被调用执行。因此，js垃圾回收机制会判定a与b依然有利用的价值，不予回收，所以a和b依然会保留在内存中。

### 经典面试题

创建10个div标签，内容为该标签的index，点击标签，弹出窗口现实该标签对应的index。

```javascript
for (var i = 0; i < 10; i++) {
  var div = document.createElement('div')
  div.innerHTML = i + '<br>'
  div.addEventListener('click', function(e) {
    alert(i)
  })
  document.body.appendChild(div)
}
```
以上为错误的做法！
打印出来的结果将是10个div标签但是点击任何一个标签弹窗只会显示10。
错误的原因和闭包的特性有关。
我们知道，闭包会存在于内存中，不会被回收掉。在上述例子中，dom绑定click事件就是一个闭包，执行完for循环后，每个click事件都继续存在于内存中，以备随时调用。而i在for循环执行完后变成了10， 这时候不管我们调用哪一个click事件都只会显示10，因为此时所有的事件都在同一个作用域中。

正确的做法是每执行一次循环立刻将当前的作用域绑定到事件上去，这样就算执行外for循环后，每个事件的作用域是独立的，不再互相干扰。调用每个事件就会得到正确的值。

方法有两种
### 方法1 自调用函数
```javascript
for (var i = 0; i < 10; i++) {
	(function(i){
    var div = document.createElement('div')
    div.innerHTML = i + '<br>'
    div.addEventListener('click', function(e) {
      alert(i)
    })
    document.body.appendChild(div)
  })(i)
}
```
每循环一次，自调用函数会执行一次，将当前循环作用域中的i绑定到自调用函数的作用域里。这样每个事件的作用域都是分开的。


### 方法2 使用let创建块作用域
```javascript
for (let i = 0; i < 10; i++) {
  var div = document.createElement('div')
  div.innerHTML = i + '<br>'
  div.addEventListener('click', function(e) {
    alert(i)
  })
  document.body.appendChild(div)
}
```
let是es6推出的新语法，它的作用就是规定变量为块作用域内所用（ES6之前用var,但是var创建的是全局变量）其实这种写法和第一种一样。都是把当前循环的作用域规定在一个块内，而这个块就是我们事件绑定所在的作用域。从而达到作用域分割的目的。

## 总结
闭包在开发中应用的极为广泛。是学习模块编程，类，异步，以及各种框架和库等的重要基础。闭包不能滥用，闭包的特性导致是把双刃剑，用得好可以提升性能，用得不好反而会降低性能。用的时候一定要慎重。


