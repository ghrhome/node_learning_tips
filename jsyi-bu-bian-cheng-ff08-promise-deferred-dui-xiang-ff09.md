# JS异步编程（promise、deferred对象）

本文主要是阅读了几篇文章之后整理出来的笔记。

## 需要弄清楚的几个概念： {#需要弄清楚的几个概念}

JS代码的运行需要JS引擎来解析，通常都需要一个宿主环境，例如浏览器或Node服务器。宿主环境会单开一个线程来调用JS引擎，用来运行JS。JS本身是单线程的，在处理一个任务的时候不可能去处理别的任务。也就是单凭JS是不可能异步编程的，必须借助一些别的机制。另外，浏览器是多线程的，例如HTTP请求线程、定时器线程、事件处理等…  
所有的异步操作，都可以称之为事件，包括DOM事件、ajax请求事件等。

主线程空闲就是指执行栈清空，此时从消息队列（事件队列，存储了消息和其对应的回调）中拉取消息（回调）入栈。当触发事件后，会将回调放到消息队列中，等待入栈。  
这篇文章对`event loop`的讲解非常清楚：  
[JavaScript异步编程](http://blog.codingplayboy.com/2017/04/25/js_async/)

其次搞清回调函数是什么：  
如果希望一个操作将来再执行，可以将其放到回调函数中，再在合适的时候触发回调函数。  
回调函数不一定是异步执行的，回调函数是指将函数作为参数传递给一个函数。异步也就是说通过某种方式先放到任务队列里，然后在event loop在适当的时候取出来在JS主线程上执行。

在以下几种情况下可能需要异步处理：  
一个是I/O操作，一个是事件，前者通常很费时，如果同步编程的话页面就会出现卡死的现象，通常都是浏览器单开了另一个新线程来处理I/O，JS主线程继承执行后面的代码，当异步任务完成后，就添加回调到任务队列中，浏览器有一个事件分发线程来完成event loop，如果JS主线程空，则从任务队列中取出一个来执行。

## 异步实现的方式 {#异步实现的方式}

主要有这些方式：

### 使用异步API {#使用异步api}

例如`setTimeout`、`setInterval`，这类函数是由运行环境提供的，本身不属于JS的，调用这些函数浏览器会单开一个和JS并行的定时线程，因此JS可以去处理后续的任务，当时间到达后上述函数中的回调函数会被放到事件队列中，等待JS主线程取出来执行。  
例如下列的回调函数实际上是同步执行的：

```
unction f1(cb) {
    // do something
    cb();
}
function f2() {
    // do something
}

f1(cb);
f2();

```

在调用的时候是f1全部执行完了以后再执行f2，如果借用定时器，可以更改为异步的：

```
function f1(cb) {
    // do something
    setTimeout(cb, 0);
}
```

这样则是先执行f1的do something 部分，再执行`f2`，最后执行`cb`。

另外nodeJS提供的`fs.readFile()`等也是单开了一个新的线程。



## 事件 {#事件}

## promise {#promise}

### promise对象 {#promise对象}

`promise`对象是一个异步操作的结果。提供了一些API使得异步执行可以按照同步的流表示出来，避免了层层嵌套的回调函数。保证了回调是以异步的方式进行调用的。

该对象有三种状态：`pending`、`fullfiled`、`rejected`。并且状态的改变只能有一次，并且也不能中途终止`pending`状态。当一个`promise`对象没有`throw err`或者没有显式地`reject`，那么如果完成了任务就是`fullfilled`的。不是说非得要调用resolve使之fullfilled，在链式调用中只有第一个`promise`对象是需要使用resolve方法来触发的。但最好的方式是每次都显示地调用`resolve`或者`reject`来决定链后面是调用哪个方法，实现这个的方式就是每次都在then中重新生成一个新的`promise`对象，决定其状态并返回该对象：

```
var onRejected = console.error.bind(console);
var promise = Promise.resolve();
promise.then(function () {
    var retPromise = new Promise(function (resolve, reject) {
       reject(new Error("this promise is rejected"));
    });
    return retPromise;
}).catch(onRejected);
```

使用promise的步骤如下：

```
function asyncFunc() {
    return new Promise(function(resolve, reject) {
        // some async task
        if (/*异步操作成功*/) {
            resolve(value);
        } else {
            reject(err);
        }
    });
}

asyncFunc().then(function(value) {
    //do something
}, function(err) {
    //do something
});
```



