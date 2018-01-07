# AngularJS中的http拦截

$http服务允许我们与服务端交互，有时候我们希望在发出请求之前以及收到响应之后做些事情。即http拦截。

$httpProvider包含了一个interceptors的数组。

我们这样创建一个interceptor。

```
app.factory('myInterceptor', ['$log', function($log){
    $log.debug('');
    
    var myInterceptor = {};
    
    return myInterceptor;
}])
```

接着注册interceptor.

```
app.config(['$httpProvider', function($httpProvider){
    $httpProvider.interceptors.push('myInterceptor');
}])
```

以下是$http拦截的一些例子。

拦截器中的异步操作

```
app.factory('myInterceotpr','someAsyncServcie', function($q, someAsyncServcie){
    var requestInterceptor = {
        request: function(config){
            var deferred = %q.defer();
            someAsyncService.doAsyncOperation().then(function(){
                ...
                deferred.resolve(config);
            }, function(){
                ...
                deferred.resolve(config);
            })
            return deferred.promise;
        }
    };
    
    return requestInterceptor;
})
```

以上，是一个请求拦截，做了一个异步操作，根据异步操作的结果来更新config。

当然也有响应拦截。

```
app.factory('myInterceptor',['$q', 'someAsyncService', function($q, someAsyncSercice){
    var responseInterceptor = {
        response: function(response){
            var deferred = $q.defer();
            someAsyncService.doAsyncOperation().then(function(response){
                ...
                deferred.resolve(response);
            }, function(response){
                ...
                deferred.resolve(response);
            })
            return deferred.promise;
        }
    };
    return responseInterceptor;
}])
```

■ Session拦截，请求拦截

服务端有2种类型的验证，一个是基于cookie的，一种是基于token的。对于基于token验证，当用户登录，获取一个来自服务端的token，这个token在每一次请求时发送给服务端。

创建一个有关session的injector:



