---
layout: post
title: Async与Sync比较以及JavaScript中callback, promise和async/await的理解
categories: JavaScript
description: Async与Sync比较以及JavaScript中callback, promise和async/await的理解
keywords: Async,Sync,同步,异步,比较,JavaScript,callback,回调函数,promise,承诺,async,等待,await,理解,形象的理解
---

# Async与Sync比较以及JavaScript中callback, promise和async/await的理解

![Async vs Sync](/images/blog/async_vs_sync.jpeg)

## 前言 

说真的写这篇帖子是在计划之外，因为看了一些知名度比较高的技术博主写的相关文章非但没有让人理解透彻反而让读者更加迷惑。所以打算稍稍理清一下思路。

## 同步与异步

首先要理解同步(Sync)和异步(Async)的概念。

同步：就是完成一个任务再再开始执行下一个任务，任务按照先后顺序完成。比如你列了一个购物清单，要去超市买西红柿，苹果，草莓。严格按照购物清单上的顺序来寻找购买物品。先买西红柿，再买苹果，最后买草莓。尽管你先看到了草莓，但是因为还没有买到西红柿，所以你决定先去找西红柿，然后再找苹果，然后再回来买草莓。总结起来就是你有强迫症。

异步：就是你执行一个任务的同时也在执行另外一个任务，任务并非按照先后开始的顺序依次完成。还是上面的例子，你的目的是买到西红柿，苹果和草莓。所以你进入超市，按照购物清单的顺序，首先开始寻找西红柿。结果你先看到了草莓，于是你把草莓放到购物篮里，继续去找西红柿。好巧不巧，你又看到了苹果，于是你把苹果放到篮子里，继续去找西红柿，最终你找到西红柿，买到了购物清单的所有东西。总结起来就是你脑筋灵活。

理解了同步异步的概念。说说同步和异步的优劣对比。

同步是从头到尾执行，按照顺序执行的好处就是好维护，每个任务都在我的掌握中，我随时知道进行到哪里了。同时如果出了问题，我也知道在哪里解决。坏处也很明显就是当其中的一个任务出了问题，就会导致下一个任务没法开始执行，形成堵塞。

异步的执行不按照顺序来，好处是不会有堵塞，一个任务出了问题，先做下一个任务就好。坏处也很明显，例如好几个任务都出了问题，你解决起来就比较困难了。有时候你可能不知道到底是哪个环节出了问题。

同步异步的概念介绍到这里。

再来说一下，JavaScript中是如何应对同步异步问题的。

最常见的案例就是，你需要先获取数据，然后将数据渲染到页面上。这个过程必须是异步的，因为获取大量数据相对其他操作来讲耗费时间比较长，为了不阻塞其他的操作，你只能边做接下来的事情边等待数据返回。那么如果数据返回了我该怎么去渲染？这时候就要用到异步函数，所谓的异步函数说白了就是等待结果返回后执行所需要的操作的意思。我们把异步函数安插在获取数据的函数上，一旦数据返回，触发了异步函数进而渲染页面，这个过程不会阻塞其他操作。

## 解决异步问题的方式---异步函数

### 回调函数（callback）
接下来说一下，异步函数有哪些实现方式。

最早使用的就是回调函数，**首先这里划重点，回调函数和异步同步没有关系。它只是一种特殊的函数而已。回调函数就是把函数当作参数传到另外一个函数里面，仅此而已。**但是我们可以使用回调函数来实现异步操作。如下：

```javascript
const posts = [
  { title: 'Post One', body: 'This is post one' },
  { title: 'Post Two', body: 'This is post two' }
];

function getPosts() {
  setTimeout(() => {
    let output = '';
    posts.forEach((post, index) => {
      output += `<li>${post.title}</li>`;
    });
    document.body.innerHTML = output;
  }, 1000);
}

function createPost(post,callback) {
    setTimeout(() => {
      posts.push(post);
      callback
    }, 2000);
}

createPost({ title: 'Post Three', body: 'This is post three' },getPosts)

console.log('next')
```

从上面的程序可以看出，我们从头开始运行，当遇到需要创建新的post的时候，我们把回调函数传进createPost里面去，而这时任务继续进行，此时会打印出‘next’字眼，过了三秒后，post添加完成。页面上渲染出新的post，整个过程没有阻塞。尽管我们最后才执行打印‘next’操作，但实际上，打印操作比渲染页面先一步完成。这就是异步函数的实际应用。

### 承诺对象（promise object）

接下来说一下Promise，和回调函数一样，promise也是用来处理异步操作的。那么既然callback已经解决了异步操作的问题为什么还需要promise? 答案是为了代码的可读性和可维护性以及方便纠错。

callback实际上可以根据需求变得异常复杂。例如回调函数嵌套回调函数再嵌套回调函数，最终会形成所谓的回调地狱。由于多层嵌套的原因，代码变得非常难看，同时如果出了问题，需要一层一层的debug，维护性是非常差的。callback本身需要自己手动添加错误处理，这也提高了纠错的难度。因此promise应运而生.

**与回调函数不同，promise是个构造函数，返回一个promise对象，这个对象里面有两个方法resolve和reject，分别用来处理返回成功和返回失败。**同时如果返回成功，我们可以继续对结果进行操作这时候需要使用then(),如果返回失败，我们可以使用catch()让结果返回对应的错误信息。引入了ES6后，写法更是大大简化，因此可读性提高，维护性提高，并且也自带错误处理机制。美中不足的是，性能跟回调函数比要差一点，但是研发成本降低了，所以promise依然是个更好的解决方案。

写法如下：

```javascript
const posts = [
  { title: 'Post One', body: 'This is post one' },
  { title: 'Post Two', body: 'This is post two' }
];

function getPosts() {
  setTimeout(() => {
    let output = '';
    posts.forEach((post, index) => {
      output += `<li>${post.title}</li>`;
    });
    document.body.innerHTML = output;
  }, 1000);
}

function createPost(post) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      posts.push(post);
      const error = false;
      if (!error) {
        resolve();
      } else {
        reject('Error: Something went wrong');
      }
    }, 2000);
  });
}

createPost({ title: 'Post Three', body: 'This is post three' })
  .then(getPosts)
  .catch(err => console.log(err));
```
### Async/await (异步/等待)
最后简单说一下async/await。
其实**async/await函数就是promise的语法糖**，ES7引入的作用主要是让code更加美观易读，async 放在函数定义前代表这个函数是异步操作函数，await 放在函数调用前表必须等待当前函数返回才可以继续执行下异步操作。直接看代码吧：

```javascript
const posts = [
  { title: 'Post One', body: 'This is post one' },
  { title: 'Post Two', body: 'This is post two' }
];

function getPosts() {
  setTimeout(() => {
    let output = '';
    posts.forEach((post, index) => {
      output += `<li>${post.title}</li>`;
    });
    document.body.innerHTML = output;
  }, 1000);
}

function createPost(post) {
    setTimeout(() => {
      posts.push(post);
    }, 2000);
}

async function init() {
  await createPost({ title: 'Post Three', body: 'This is post three' });
  getPosts();
}
```

祝读者开心！