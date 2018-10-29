---
layout: post
title:  谈谈对Promise的小理解
date: 2017-11-24
categories: js
tags: [ES6,Promise,js]
---

## 初识Promise
写完callback的一个异步编程小作业之后，去看了看Promise，惊为天人，还以为能把异步函数封装成同步函数（现在想想是思维错误，结果用的时候一堆bug，参考了各路博客和MDN的资料后，才发现自己理解错了，故而小记一下

<!--more--> 

---

## 第一次用
刚开始写时，我是类似这样写的

```js
var p = new Promise(resolve => {
  resolve();
}).then(() => {
  setTimeout(() => {
    console.log(123);
  }, 1000);
}).then(() => {
  console.log(456);
});
```

这样写正是我刚才说的异步变同步的弱智操作。实际异步编程的优越性正在于它的异步性，而我一味追求同步（阻塞线程）无疑是走了误区。

---

## 理解
MDN的资料是这样说的：

> A Promise is in one of these states:
> * pending: initial state, neither fulfilled nor rejected.
> * fulfilled: meaning that the operation completed successfully.
> * rejected: meaning that the operation failed.
<br/>
> <img src="https://cdn.rawgit.com/Vectaio/a76330b025baf9bcdf07cb46e5a9ef9e/raw/26c4213a93dee1c39611dcd0ec12625811b20a26/js-promise.svg" />

其实个人觉得这个没什么卵用，Promise的实际使用其实和这个没什么关系，只是把`return`和`throw`变成了`resolve`和`reject`，对于简单的callback，可以直接传函数参数作为`resolve`和`reject`，而且代码量似乎还多了一点。不过如果是“Callback Hell”或是比较特殊的异步要求，Promise就体现了他的强大。

个人认为Promise的核心其实只有以下一段：

```js
var p = new Promise((resolve, reject) => {
  //async
}).then(() => {
  //...
}).catch(() => {
  //...
});
```

所以如果要实现链式的话，就需要在每个`then`的函数里`return`一个新的`Promise`对象，刚开始我觉得这样很麻烦，似乎没有改变callback的弊端，但再仔细想想，如果把每个异步函数包装成一个函数，则`then`的参数便只是一个简单函数名字，多个`then`放在一起岂不是比`callback hell`赏心悦目且直接？而且对于调试和维护也会更方便。

```js
var p = new Promise(async)
            .then(async1)
            .then(async2)
            .then(async3)
            .catch(reject);
```

至于其他的`Promise.all`和`Promise.race`因为还没用过，所以谈不上理解，但是`all`的好处显而易见，假设有多个异步请求同时进行，当它们都完成时便可以触发某个事件，这用`all`可以说是很轻松了，我之前做作业时是每个请求都判断一次当前状态是否满足条件，相形见绌。

## 结语
这篇文是在做完web作业的晚上随便写写的（毕竟很久没更新了），理解谈不上多深层，只是使用的感受罢了。ES7有`async`和`await`（听说是根据tj大神的[co](https://github.com/tj/co)加的，膜），据说是解决异步的终极方案，然而我还没看。参考了[阮一峰的ES6入门](http://es6.ruanyifeng.com/#docs/promise)和[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)