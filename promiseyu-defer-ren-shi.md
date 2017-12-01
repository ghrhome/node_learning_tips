# Promise与Defer认识

1、deffer对象：jquery的回掉函数解决方案；含义是延迟到未来某个点再执行；

2、$.ajax链式写法：

```
$.ajax("test.php")
    .done(function() { console.log("success"); })   
    .fail(function() { console.log("fail"); })
```

3、指定同一操作的多个回调函数：

```
$.ajax("test.php")
    .done(function() { console.log("success"); })   
    .fail(function() { console.log("fail"); })
    .done(function() { console.log("success two"); })
```

4、为多个操作指定回调函数：若其中一个执行失败则执行fail回调；

```
$.when($.ajax("test.php"), $.ajax("test2.php"))
   .done(function() { console.log("success"); })   
   .fail(function() { console.log("fail"); })
```

5、普通操作的回调函数接口：

```
var dtd = $.Deferred(); // 新建一个deferred对象
var wait = function (dtd) {
    var tasks = function() {
        alert("执行完毕！");
        dtd.resole(); // 改变deferred对象的执行状态从”未完成“改为“已完成”，从而触发done()方法；
        // dtd.reject(); // 从“未完成”改为“已失败”，触发fail()方法；
    }
    setTimeout(tasks, ,5000);
    return dtd;
}
$.when(wait(dtd))
    .done(function() { console.log("success"); })   
    .fail(function() { console.log("fail"); })
 
    // dtd.resolve() 可以立即改变状态，5s后再触发多一次
```

6、用promise优化 全局defer对象，防止执行状态被外部改变：

```
var wait = function () {
    var dtd = $.Deferred(); // 新建一个deferred对象
    var tasks = function() {
        alert("执行完毕！");
        dtd.resole(); // 改变deferred对象的执行状态从”未完成“改为“已完成”，从而触发done()方法；
    }
    setTimeout(tasks, ,5000);
    return dtd.promise(); // 返回promise对象
};
$.when(wait())
    .done(function() { console.log("success"); })   
    .fail(function() { console.log("fail"); })
```

7、另一种防止执行状态被外部改变的方法：用deferred的构建函数：

```
$.Deferred(wait) // 接受一个函数名作为参数，注意是函数名
   .done(function() { console.log("success"); })   
   .fail(function() { console.log("fail"); })
```

8、最后一种防止执行状态被外部改变的方法：在wait对象上部署deferred接口：

```

var dtd = $.Defferred();
var wait = function(dtd) {
    var tasks = function() {
        alert("执行完毕！");
        dtd.resole(); // 改变deferred对象的执行状态从”未完成“改为“已完成”，从而触发done()方法；
    }
    setTimeout(tasks, ,5000);
 
}
dtd.promise(wait);
wait.done(function() { console.log("success"); })        
    .fail(function() { console.log("fail"); })
 
wait(dtd);
```

**结：deferred对象的方法：**

（1） $.Deferred\(\) 生成一个deferred对象。

（2） deferred.done\(\) 指定操作成功时的回调函数

（3） deferred.fail\(\) 指定操作失败时的回调函数

（4） deferred.promise\(\) 没有参数时，返回一个新的deferred对象，该对象的运行状态无法被改变；接受参数时，作用为在参数对象上部署deferred接口。

（5） deferred.resolve\(\) 手动改变deferred对象的运行状态为"已完成"，从而立即触发done\(\)方法。

（6）deferred.reject\(\) 这个方法与deferred.resolve\(\)正好相反，调用后将deferred对象的运行状态变为"已失败"，从而立即触发fail\(\)方法。

（7） $.when\(\) 为多个操作指定回调函数。

（8）deferred.then\(\)有时为了省事，可以把done\(\)和fail\(\)合在一起写，这就是then\(\)方法。

$.when\($.ajax\( "/main.php" \)\).then\(successFunc, failureFunc \);

如果then\(\)有两个参数，那么第一个参数是done\(\)方法的回调函数，第二个参数是fail\(\)方法的回调方法。

如果then\(\)只有一个参数，那么等同于done\(\)。

（9）deferred.always\(\)这个方法也是用来指定回调函数的，它的作用是，不管调用的是deferred.resolve\(\)还是deferred.reject\(\)，最后总是执行。

$.ajax\( "test.html" \).always\( function\(\) { alert\("已执行！"\);} \);

