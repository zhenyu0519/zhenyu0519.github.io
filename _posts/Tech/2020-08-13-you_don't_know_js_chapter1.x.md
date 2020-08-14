---
layout: post
title: 第一部分 作用域和闭包 附录
categories: Tech-Post JavaScript 你不知道的JavaScript
description: 从你不知道的JavaScript书中总结复习知识点，力求精通JavaScript
keywords: 你不知道的JavaScript 附录 第一部分 作用域和闭包 动态作用域
---

# 附录

![book](/images/blog/you_dont_know_js.png)


### 动态作用域

我们一直在在讨论JavaScript中的词法作用域，其实除了词法作用域以外还有动态作用域，然而JavaScript并不具备动态作用域，但是它的某些特点却具有动态作用域的特征。所以还是需要了解一下什么是动态作用域。下面一段代码可以说明词法作用域和动态作用域。

```javascript
function foo(){
    console.log(a); // 词法作用域会输出 2； 而动态作用域会输出 3
}

function bar(){
    var a = 3
    foo()
}

var a = 2
bar()
```
输出的区别很好的解释词法作用域和动态作用域。简单说词法作用域，由于函数foo是定义在全局作用域中的，因此它引用到的是全局作用域中的a。而动态作用域更关心的是从何处调用这个函数，因此动态作用域则引用的是bar函数的作用域里的a。

主要区别： 词法作用域是在写代码或者说定义时确定的，而动态作用域是在运行时确定的。JavaScript里面没有动态作用域，但是this机制域动态作用域很相似，this关注的是函数从何处调用。


### this词法和箭头函数

ES6添加了特殊的语法形式用于函数声明，叫做箭头函数。

```javascript
var foo = a => {
    console.log(a)
}

foo(2); // 2
```

箭头函数并不是简单的语法糖，他还有一个更加重要的作用。

```javascript
var obj = {
  id:'good',
  cool: function print(){
    console.log(this.id)
  }
}

obj.cool() // good

var id = 'bad'

setTimeout(obj.cool, 100) // bad
```

两次调用cool()函数得到的结果完全不一样，第一个得到是obj作用域内的id， 而第二个得到确实全局作用域中id。原因是cool()函数丢失了同this之间的绑定。我们可以通过下面的方法来解决。

```javascript
var obj = {
  id:'good',
  cool: function print(){
    var self = this
    console.log(self.id)
  }
}

obj.cool() // good

var id = 'bad'

setTimeout(obj.cool, 100) // bad
```
简单来说，箭头函数在涉及this绑定时的行为和普通函数的行为完全不一致。它放弃了所有普通this绑定的规则，取而代之的是用当前的词法作用域覆盖了this本来的值。因此，这个代码片段中的箭头函数并非是以某种不可预测的方式同所属的this进行了解绑定，而只是“继承”了cool()函数的this绑定（因此调用它并不会出错）。