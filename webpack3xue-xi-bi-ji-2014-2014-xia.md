### 设置模块索引目录 {#设置模块索引目录}

在 webpack 中，主要提供`alias`和`modules`来进行索引目录的设置。

* alias：设置导入路径的简便写法。如果你有大量的文件依赖路径非常长的话，除了使用
  `modules`设置参考路径之外，还可以根据`alias`来设置路径映射的 key。另外，它还提供了`$`来进行文件的精确匹配，这个看看[文档](https://webpack.js.org/configuration/resolve/#resolve-alias)就行，感觉不常用。例如：

```
alias: {
  Villainhr: path.resolve(__dirname, 'src/Villainhr/')
}

# 如果要获取 src/Villainhr/index.js 的话，可以直接在 import 写入：

import test from 'Villainhr/index';
```

modules: 用来设置模块解析的目录。这样，在进行模块查找的时候，webpack 会优先遍历你定义的目录。如果，你还有自定义模块在，通过普通模块解析是找不到的，这时候可以直接在`modules`中添加 src。

```
modules: [path.resolve(__dirname, "src"), "node_modules"]

```

### package.json 索引解析 {#package.json 索引解析}

和模块搜索最关键的文件是`package.json`、在 node 环境下，如果模块文件中存在 package.json，会根据其`main`字段定义的文件来进行索引。通过 webpack 设定的模块索引，可以更灵活的设置查找规则。其中，主要依赖的是`mainFields`、`mainFiles`、`extensions`这三个字段。

* mainFields\[Array\]: 接受的是数组，用来设置在 package.json 文件中，用来定义具体查找文件的字段。例如：
  `mainFields: ["browser", "module", "main"]`，则会按顺序搜索：`browser`、`module`、`main`这三个字段。
* mainFiles\[Array\]：如果文件不含有 package.json，则直接根据`mainFiles`定义的文件来索引。
* extensions\[Array\]：设置默认文件后缀名。当索引文件时，如果具体路径没带文件名的话，会根据`exntensions`定义的内容来获取。

### loader 简便书写 {#loader 简便书写}

在`modules`指令下，添加具体调用的`loader`一定需要带上后面的`-loader`尾缀。如果你不想带，则会默认报错。不想报错的话，可以使用`moduleExtensions`指令来说明：

```
moduleExtensions: ['-loader']
```

这样，就可以直接在 module 中直接不带`-loader`引用：

```
loader: 'eslint'

```

## externals 定义外部依赖 {#externals 定义外部依赖}

externals 是用来排除某些你并不想打包的库，并且可以通过`import`/`require`在全局环境中调用。这个选项一般是提供给一些开源库或者组件作者使用的。

相当于，你调用了一个库，而最终实际打包的文件里面剔除该库的存在，只是留下了一个引用接口。

```
// 剔除 sw-router 库
externals: {
  Router: 'sw-router'
}

// 在代码中使用
import Router from 'Router';
```

它可以接收如下类型：\(array object function regex\)

* array：用来过滤打包文件中的子模块。

* ```
  // 将 ./math 文件中的 subtract 过滤不打包。
  externals: {
    subtract: ['./math', 'subtract']
  }

  // 忽略如下文件打包：
  import {subtract} from './math';
  ```
* object：通过 key 值，来决定在不同模块规范下，文件暴露的接口名。其中，commonjs 代表 require 模块规则，amd 则代表 define 模块规则，root 则是代表该模块在全局环境，例如 window 访问的对象名。

```
externals : {
  ex_loadsh : {
    commonjs: "lodash", // require('loadsh')
    amd: "lodash",  // define(['loadsh'])
    root: "_" // window._
  }
}

```

通过 externals 定义之后，我们在不同环境访问 loadsh 的方式就变为：

```
// 全局环境
window.ex_loadsh;

// 模块包中
require('ex_loadsh');
```

* function: 前面几个写法都是将需要提出的文件写死，而 function 提供了可以灵活配置的 external list。比如，你想剔除所有 node\_modules 包中的库，而里面又有几个包需要引用的话，可以写为：

```
externals: [
  function(context, request, callback) {
    if (/^yourregex$/.test(request)){ // 剔除具体的包
      return callback(null, 'amd ' + request);  // 通过 amd 定义剔除文件的引用方式
    }
    callback(); 
  }
]
```

不过，更方便的直接使用[webpack-node-externals](https://www.npmjs.com/package/webpack-node-external//webpack.js.org/configuration/resolve/#resolve-alias)来完成。如果项目不是特别复杂，这个配置选项一般用不上。

* regexp: 使用正则选项，相当于就是通过正则的`test`方法，通过路径名的检查，来决定是否剔除。

```
externals: /^(jquery|\$)$/i; //剔除 jquery 和 $ 文件导入

# 剔除如下文件
import $ from 'jquery';
// or
import $ from '$';

```

## target 设定打包环境 {#target 设定打包环境}

> 这里，如果项目并不复杂的话，可以直接忽略该属性设置

因为 webpack 本身都是多环境应用，你可以在 node、web、webworker 甚至其他跨平台环境中：node-webkit、electron-main。其常用选项有三个：

* node：在后台环境中使用
* web：在浏览器主线程环境中使用
* webworker：在 web-worker 环境中使用

具体设置方式为：

```
{
  target: 'node'
}

```

不过，该选项常常用于复杂的打包项目中。比如，一个项目你需要同时输出在 web 环境中和在 webworker 环境中运行的打包脚本文件。这时候，就涉及到多入口文件的 webpack.config.js 设置。这里，可以利用`webpack-merge`来帮助我们完成多入口的合并设置。

```
var baseConfig = {  
  target: 'async-node',
  entry: {
    entry: './entry.js'
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, './dist')
  }
};

let targets = ['web', 'webworker', 'node', 'async-node', 'node-webkit', 'electron-main'].map((target) => {
  let base = webpackMerge(baseConfig, {
    target: target,
    output: {
      path: path.resolve(__dirname, './dist/' + target),
      filename: '[name].' + target + '.js'
    }
  });
  return base;
});

module.exports = targets;
```

在导出的就会同时编译出 web、webworker、node 等目录，里面的文件就是对应环境中使用的包。如果想用原生的可以直接导出，数组形式的配置项：

```
var path = require('path');
var serverConfig = {
  target: 'node',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.node.js'
  }
};

var clientConfig = {
  target: 'web', // <=== can be omitted as default is 'web'
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.js'
  }
};

module.exports = [ serverConfig, clientConfig ];
```

通过，在 webpack 主文件中，具体设置需要使用的配置，即可完成指定环境的编译操作。

## node 提供模拟包 {#node 提供模拟包}

node 指令主要是让 webpack 将在 node 环境运行的包，提供相关的设置，能够直接在打包文件中访问。其策略可以是提供一个桩（空对象），或者直接将现成的包直接打进去。

其基本可选择的值主要是直接参考 node 的模块包名：

```
console
global
process
__filename
__dirname
Buffer
setImmediate
```

不过，主要用到的还是 Buffer 和 global 两个配置项。其打包策略选项有 4 个值：

* true: 直接将该包的 polyfill 直接打进去
* “mock”: 提供空接口。可以访问对应的接口名，但是没有任何作用。
* “empty”: 提供一个空对象。这个连接口名都不能访问
* false: 啥都没有。

上述默认配置值为：

```
node: {
  console: false,
  global: true,
  process: true,
  __filename: "mock",
  __dirname: "mock",
  Buffer: true,
  setImmediate: true
}

```

这样设置了之后，我们可以直接在主文件入口中访问：

```
const Buffer = require('Buffer');

// doSth()
```

如果你想啥都不提供，最好直接将`node`设置为`false`。

```
node:false
```

## devtool 开启 sourceMap 选项 {#devtool 开启 sourceMap 选项}

devtool 主要是给 JS 文件添加对一个的 sourceMap 文件。不过，webpack 可以生成多种 sourceMap 文件，具体的区别是，映射还原度的区别。这里直接参考 webpack 官方文档即可。

![](http://static.zybuluo.com/jimmythr/f88fzeenbludjzz081xpdh5y/image.png "image.png-192.8kB")

其中，最常用的选项就是`source-map`和`cheap-eval-source-map`。（这里实在太多了，各位看着选个合适的就行）如果你想关闭，sourceMap 的话，可以直接使用`hidden-source-map`，或者不传。比如，下面的配置：

```
if (COMPILE) {
    config.devtool = 'hidden-source-map';
    config.output.path = path.join(__dirname,'public');
}

```

devServer 本地 localhost 调试

devServer 是 webpack 提供一个简便的调试服务。它可以根据字段配置，自动起一个 server 来进行调试。如果用过`webpack-dev-server`的同学，应该就很熟悉了。那如果利用 webpack，起一个简单的 server 服务呢？这里，直接给一份配置即可：

```
devServer: {
  contentBase: path.join(__dirname, "dist"), // 设置静态资源访问路径
  compress: true, // 执行压缩
  port: 9000 // 监听的端口
}
```

不过，更常用的是利用 webpack server 的热更新机制。配置列表为：

```
devServer: {
  contentBase: path.join(__dirname, "dist"),
  compress: true,
  port: 9000,
  hot: true,
  hotOnly: true
}
```

接着，你就可以通过`localhost:9000`来访问你编译过后的目录。

剩下 server 配置，大家可以直接参考[webpack devServer](https://webpack.js.org/configuration/dev-server/#devserver-hotonly)。

## 最后说几句 {#最后说几句}

到这里，webpack 3.x 的整个内容差不多已经阐述完了。不过，关于 plugin 的内容，我们这里并没有过多提及，因为，对于一般 webpack 纯使用者来说，直接使用现有成熟的 plugin 即可。还有一个原因是，webpack plugin 内容真的有点多，这里篇幅有限就不写了。后面专门针对其内容详述一篇。

  


  




















