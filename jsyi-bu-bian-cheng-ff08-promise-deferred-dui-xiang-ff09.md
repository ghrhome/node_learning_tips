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

使用catch来区分成功失败会更好：

```
asyncFunc().then(function(value) {
    //do something
}).catch(function(err) {
    //do something
});
```

`resolve`和`reject`都能接收参数，接收的参数传递给`then`中的回调函数。当`resolve`中的参数是另一个`promise`对象时，表示将该对象的状态传递给在其中调用`resolve`的`promise`对象。

`reject`方法等同于抛出错误。异步操作和`then`中产生的错误都会被`catch`捕获到。Promise 对象的错误具有“冒泡”性质，会沿着链式调用一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个catch语句捕获。  
如果没有使用catch方法指定错误处理的回调函数，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应，即只会沿着链传递。catch返回的也是promise对象。

每次调用`then`方法都返回一个新的`promise`对象。

**注意：由then方法指定的回调函数必须得等主线程的所有同步任务执行完了才会被调用，也就是说Promise规定then中的回调采用异步的方式来调用。**如果解释得仔细点就是，当promise对象的状态变为fufilled时，then指定的回调函数被放入任务队列中，JS主线程会继续执行栈中的剩余部分，并在空闲的时候从任务队列中取出等待执行的回调函数。

### 静态方法Promise.resolve\(\)&Promise.reject\(\) {#静态方法promiseresolvepromisereject}

均返回确定了状态的promise对象，之后可调用then、catch方法。两者都是new Promise的快捷方式，即创建一个确定状态的promise对象，用于将一些非`promise`对象转换为`promise`。

补充下thenable对象：  
只具有then方法的对象，例如jqXHR对象，也就是$.ajax\(\)方法返回的对象。  
thenable对象可使用Promise.resolve\(thenable\)来转换成promise对象

* **promise chain**

即在前一个回调中又返回了一个`promise`对象，后面的`then`方法等待该对象的状态变化而进行调用。链式调用传给then的回调函数所return的值会作为参数传递给下一个then中的回调。

在链式调用中如果throw了一个error或者返回一个rejected状态的promise对象，那么就会绕开之后的then方法，执行catch中的回调，接着再执行catch之后的方法。

注意：

> 每个方法中 return 的值不仅只局限于字符串或者数值类型，也可以是对象或者promise对象等复杂类型return的值会由

> `Promise.resolve(return的返回值);`进行相应的包装处理，因此**不管回调函数中会返回一个什么样的值，最终 then 的结果都是返回一个新创建的promise对象**。其实，:每次调用then或catch都会返回一个新创建的promise对象

需注意的是整个then方法返回了一个新的promise对象，而传递给then的回调函数所return的值将作为参数传递给下一个then中的回调函数。而第一个then中回调函数的参数是传递给resolve\(\)的参数。

### Promise.all和Promise.race {#promiseall和promiserace}

当有多个promise对象需要处理的时候，可以使用静态方法Promise.all，该方法接收一个数组为参数，数组元素为promise对象。最后返回一个新的promise对象，只有当所有的promise对象都为fullfied或其中某个为rejected时，才会调用then或catch方法。then的回调函数接收一个数组作为参数，该数组中的元素是各个promise对象resolve时传递的参数或者是由各自的then的回调所返回的值：

```
var p1 = Promise.resolve(1),
    p2 = Promise.resolve(2),
    p3 = Promise.resolve(3);

Promise.all([p1, p2, p3]).then(function (results) {
    console.log(results);  // [1, 2, 3]
});
```

该部分参考：  
[［ES6］Promise对象Promise.all\(\)方法的使用](http://itbilu.com/javascript/js/41KMSZ9a.html)

> 此外，  
> 传递给 Promise.all 的promise并不是一个个的顺序执行的，而是同时开始、并行执行的。

Promise.race和Promise.all类似，不过前者是当有一个promise对象是fulfilled或reject状态时就会停止，然后调用相应的then或者catch。

同时，在有对象变成fulfilled状态后，就会立即调用then方法，但是并不意味着其他的promise对象不执行了：

> Promise.race 在第一个promise对象变为Fulfilled之后，并不会取消其他promise对象的执行。

### catch 和 then {#catch-和-then}

catch实际上是then的别名，不过是then的第一个参数为null。但表意更明确，同时如果是then中指定的onFulfilled出错了，是不会被同一个then中的onRejected所捕获的，必须在后面再加一链：

```

Promise.resolve(42).then(throwError).then(null, onRejected);
```

即then中的onRejected是被上一个promise对象出错后所调用的。catch则既能捕获promise对象所抛出的错误又能捕获前面的then中出的错。

### promise和deferred {#promise和deferred}

Jquery中的Deferred对象实现了Promise，并且将resolve和reject方法暴露在了构造函数外面，使得改变promise对象的状态更为灵活。  
仍然是状态的改变只有一次，之后的更改会忽略。

deferred.promise\(\)是返回了一个纯粹的promise对象，即不能在外部调用resolve和reject方法。

deferred对象常用的API：

\(1\) 生成Deferred对象

```
var dfd = $.Deferred();
```

\(2\) 状态改变的方法

```
dfd.resolve(arg);  //会调用done、then中的donecallback
dfd.resolveWith(context, arg);  //context是指定了donecallback的this

dfd.reject(arg); //会调用fail、then中的failcallback
dfd.reject(context, arg);
```

\(3\) 完成回调

```
dfd.done(donecallback); //返回一个新的deferred对象
dfd.then(donecallback, [failcallback], [progresscallback]); //返回一个新的promise对象
```

\(4\) 失败回调

```
dfd.fail(failcallback); //返回一个新的deferred对象
dfd.then(donecallback, failcallback, [progresscallback]);
```

此外还有

```
dfd.always(alwayscallback); //不管是resolved还是rejected了都会被调用,返回一个新的deferred对象
```

\(5\) 进度回调

> 当通过调用 notify或notifyWith使延迟对象产生正在执行中的进度通知时，progressCallbacks 就会被调用。

```
dfd.notify(args);  //会触发progresscallback的执行
dfd.progress(progresscallback);  //返回一个新的deferred对象
```

另外还有一个方法：

```
$.when(deferreds)
```

该方法有点类似Promise.all。

-当传入的是一个dfd时，返回它的promise对象。

```
$.when( $.ajax("test.aspx") ).then(function(data, textStatus, jqXHR){
     alert( jqXHR.status ); // alerts 200
});
```

-当传入的是一个普通的JS对象或为空，则会被当成一个resolved的promise对象，donecallback会立即被调用。

-传入多个延迟对象：

```
var d1 = $.Deferred();
var d2 = $.Deferred();

$.when( d1, d2 ).done(function ( v1, v2 ) {
    console.log( v1 ); // "Fish"
    console.log( v2 ); // "Pizza"
});

d1.resolve( "Fish" );
d2.resolve( "Pizza" );
```

以上参考[jQuery.when\(\)](http://www.css88.com/jqapi-1.9/jQuery.when/)

* **与jqXHR的关系**

ajax方法返回jqXHR，该对象继承自`deferred`对象，可以使用`Promise.resolve(jqXHR)`，得到一个`promise`对象，这样就可以调用`promise`的API了 。

这几篇文章讲得比较清楚：  
[JavaScript 异步编程学习笔记](https://github.com/riskers/blog/issues/22)  
[探索Javascript异步编程](http://web.jobbole.com/82291/)  
[JavaScript Promise迷你书（中文版）](http://liubin.org/promises-book/#chapter2-how-to-write-promise)

