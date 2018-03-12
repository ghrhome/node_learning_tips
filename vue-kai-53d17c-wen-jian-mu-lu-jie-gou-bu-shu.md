# Vue 开发\|文件目录结构部署

基于 vue-cli 脚手架生成项目模板基础上做了些改动，加入了 vue-router ，vuex 等配套设施，本地 dev server 中加入了接口 mock 功能，还增加一个 build server 来预览 build 结果页面，前后端通过 spa 的方式实现分离，并相应做了分离后的联调，部署方案。在这里俺也对整个过程简单做个介绍吧。



**目录结构**

```
   ├── index.html                      入口页面
    ├── build                           构建脚本目录
    │   ├── build-server.js                 运行本地构建服务器，可以访问构建后的页面
    │   ├── build.js                        生产环境构建脚本
    │   ├── dev-client.js                   开发服务器热重载脚本，主要用来实现开发阶段的页面自动刷新
    │   ├── dev-server.js                   运行本地开发服务器
    │   ├── utils.js                        构建相关工具方法
    │   ├── webpack.base.conf.js            wabpack基础配置
    │   ├── webpack.dev.conf.js             wabpack开发环境配置
    │   └── webpack.prod.conf.js            wabpack生产环境配置
    ├── config                          项目配置
    │   ├── dev.env.js                      开发环境变量
    │   ├── index.js                        项目配置文件
    │   ├── prod.env.js                     生产环境变量
    │   └── test.env.js                     测试环境变量
    ├── mock                            mock数据目录
    │   └── hello.js
    ├── package.json                    npm包配置文件，里面定义了项目的npm脚本，依赖包等信息
    ├── src                             项目源码目录    
    │   ├── main.js                         入口js文件
    │   ├── app.vue                         根组件
    │   ├── components                      公共组件目录
    │   │   └── title.vue
    │   ├── assets                          资源目录，这里的资源会被wabpack构建
    │   │   └── images
    │   │       └── logo.png
    │   ├── routes                          前端路由
    │   │   └── index.js
    │   ├── store                           应用级数据（state）
    │   │   └── index.js
    │   └── views                           页面目录
    │       ├── hello.vue
    │       └── notfound.vue
    ├── static                          纯静态资源，不会被wabpack构建。
    └── test                            测试文件目录（unit
&
e2e）
        └── unit                            单元测试
            ├── index.js                        入口脚本
            ├── karma.conf.js                   karma配置文件
            └── specs                           单测case目录
                └── Hello.spec.js

```

  


**快速开始**

```
git clone https://github.com/hanan198501/vue-spa-template.git
cd vue-spa-template
cnpm install
npm run dev

```

  


**命令列表**

```
#开启本地开发服务器，监控项目文件的变化，实时构建并自动刷新浏览器，浏览器访问 http://localhost:8081
npm run dev

#使用生产环境配置构建项目，构建好的文件会输出到 "dist" 目录，
npm run build

#运行构建服务器，可以查看构建的页面
npm run build-server

#运行单元测试
npm run unit

```

  


**前后端分离**

项目基于 spa 方式实现前后端分离，后端将所有 url 都返回到同一个 jsp 页面（由前端提供），此 jsp 页面也是前端的入口页面。然后路由由前端控制（基于vue-router），根据不同的 url 加载相应数据和组件进行渲染。

**接口 mock**

前后端分离后，开发前需要和后端同学定义好接口信息（请求地址，参数，返回信息等），前端通过 mock 的方式，即可开始编码，无需等待后端接口 ready。项目的本地开发服务器是基于 express 搭建的，通过 express 的中间件机制，我们可以很方便的添加接口 mock 功能：

在 build/dev-server.js 中新增接口 mock 处理：

```
// mock api requests
var mockDir = path.resolve(__dirname, '../mock');
fs.readdirSync(mockDir).forEach(function (file) {
  var mock = require(path.resolve(mockDir, file));
  app.use(mock.api, mock.response);
});

```

  


其中，mock 目录下可能有个文件内容如下，描述了一个接口的数据信息：

    module.exports = {

      // 接口地址
      api: '/api/hello',

      // 返回数据
      response: function (req, res) {
        res.send(`

    <
    p
    >
    hello vue!
    <
    /p
    >

        `);
      }
    }


  


**组件化**

整个应用通过 vue 组件的方式搭建起来，通过 vue-router 控制相应组件的展现，组件树结构如下：

```
   app.vue                         根组件（整个应用只有一个）
        ├──view1.vue                    页面级组件，放在 views 目录里面，有子组件时，可以建立子目录
        │   ├──component1.vue               功能组件，公用的放在 components 目录，否则放在 views 子目录
        │   ├──component2.vue
        │   └──component3.vue
        ├──view2.vue
        │   ├──component1.vue
        │   └──component4.vue
        └──view3.vue
            ├──component5.vue
            ……

```

  


**单元测试**

可以为每个组件编写单元测试，放在 test/unit/specs 目录下面, 单元测试用例的目录结构建议和测试的文件保持一致（相对于src），每个测试用例文件名以 .spec.js 结尾。 执行 npm run unit 时会遍历所有的 spec.js 文件，产出测试报告在 test/unit/coverage 目录。

**前后端联调**

前后端分离后，由于服务端和前端的开发环境处于2台不同的机器上，整个联调过程，入口页面需要引用前端机器的静态资源，又要调用后端机器的异步接口。根据入口页面的位置，我们可以使用不同的联调方案：

1. 入口页面在前端机器：

通过在本地 dev-server 中使用[https://github.com/chimurai/http-proxy-middleware](https://link.zhihu.com/?target=https%3A//github.com/chimurai/http-proxy-middleware)中间件把接口请求代理到后端机器，vue-cli 生成的 dev-server 中已经自带了这个功能：

```
// proxy api requests
Object.keys(proxyTable).forEach(function (context) {
  var options = proxyTable[context]
  if (typeof options === 'string') {
    options = { target: options }
  }
  app.use(proxyMiddleware(context, options))
});

```

最好通过启动 dev-server 时传入一个参数来控制是否打开代理功能，这样可以避免开发阶段覆盖掉我们的 mock 配置。

2. 入口页面在后端机器： 后端工程里面的入口 jsp 中引用的 js 文件地址需要指向前端环境中的地址，联调时才能显示最新的修改。主要有2种实现方式： 1） jsp 文件引用一个固定域名（如 debughost）的 js 文件， 后端机器上通过修改此域名的ip指向前端机器，达到引入前端环境 js 的目的。 2） jsp 文件通过获取一个 url 参数（如 debughost）的值，这个值为前端机器的 ip 地址，再动态的插入一个 script 标签引入这个 ip 的前端 js 文件。

举个例子，假设前端机器的 ip 为 172.16.36.90，需要加载前端的js文件地址为：[http://172.16.36.90:8081/main.js](https://link.zhihu.com/?target=http%3A//172.16.36.90%3A8081/main.js)， 那么后端同学的机器中需要在 host 文件加一条记录：

```
172.16.36.90 debughost

```

  


而入口 jsp 页面中则通过以下代码开加载前端js：

```
   var debughost = 'debughost';
    location.search.substr(1).split('
&
').forEach(function (item) {
        var arr = item.split('=');
        var key = arr[0];
        var value = arr[1];
        if (key === 'debughost') {
            debughost = value;
        }
    });
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = 'http://' + debughost + ':8081/main.js?' + (new Date()).getTime();
    document.head.appendChild(script);

```

这样，jsp 页面默认会加载[http://debughost:8081/main.js](https://link.zhihu.com/?target=http%3A//debughost%3A8081/main.js)这个文件。 此外，如果不想用 debughost 这个域名的 js 文件，访问 jsp 时候还可以通过 url 带入 debughost 参数来指定前端 ip 。

**部署方案**分离后前后端代码会存放在2个单独的 git 仓库中，构建过程也是分开的。后端构建时，需要依赖前端的构建结果。具体流程如下：

1. 拉取前端项目代码 2. 构建前端（构建结果放在dist目录） 3. 拉取后端代码 4. 将前端的构建结果（dist目录里的文件）复制到后端工程中 5. 构建后端

此过程可以借助 jenkins 配置，或者，让运维同学配合修改部署脚本。

最终的项目模板会是这样：[annnhan/vue-spa-template](https://link.zhihu.com/?target=https%3A//github.com/annnhan/vue-spa-template)



