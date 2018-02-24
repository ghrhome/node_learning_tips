# webpack3学习笔记

webpack 是前端开发者一个跨不过去的编译工具。不过由于他的快速迭代，让很多同学在学一个版本的时候，下一个新版本就发布了，让人感觉非常蛋疼和无奈：

“我是谁，我在干嘛，我要做什么？”

不过，如果你已经掌握 webpack 比较老的版本，对于新版本的学习而言，应该只需要 0.5 天的工作量就可以完成。因为，其基本理念都是以 JS 为中心，增加的只是其辅助特性。

webpack 到目前为止已经走过了三个大版本，每个版本之间都，增加了非常多可用的特性，但是每个版本的具体配置项都有些差异，很容易让老版本用户形成依赖。现在 webpack 3 已经走到了 3.8 版本。现版本对于 webpack 2.x 的配置项来说，最突出的就是 loader 的写法的改变和内置了 webpack-dev-server 的特性。

所以，本文将带你全面了解一下 webpack 3.x 版本的新特性和一些常用的 webpack 3.x 的配置。

## entry-入口 JS 配置 {#entry-入口-js-配置}

entry 选项主要用来定制 webpack 引用 JS 文件的主入口，它可以配置多个值来定义多个 JS 主文件入口。具体配置项为：

entry可以是三种值\[string \| object \| array\]

* 字符串:如 entry:’./src/index.js’，字符串也可以是函数的返回值，如entry: \(\) =&gt; ‘./demo’。

* 数组形式，如\[react,react-dom\]，可以把数组中的多个文件打包转换为一个chunk；

```
entry: ["./app/entry1", "./app/entry2"],
```

* 对象形式，如果我们需要配置的是多页应用，或者我们要抽离出指定的模块做为公共代码，就需要采用这种形式了，属性名是占位符name的值，属性值可以是上面的字符串和数组，如下:

```
// 值得注意的是入口文件有几个就会生成几个独立的依赖图谱。 
entry:{ 
main:'./src/index.js', second:'./src/index2.js',
vendor: ['react','react-dom'] // 将 react,react-dom 一起打包 
}
```

有时候，开发者还会利用 `require.resolve(route)`来在 entry 中引入具体路径的文件。该方法只是用来作为路径模块的搜索，其返回的是指定文件的绝对路径名。解析机制有点类似，require\(route\)

```
entry: [ require.resolve('react-dev-utils/webpackHotDevClient'), 
require.resolve('./polyfills'), 
//返回 /Users/villainHR/Desktop/file/polyfills/index.js require.resolve('react-error-overlay'), 'src/index.js' ],
```

entry 还可以接受 function 返回的 string,array,object 值：

```
entry: () => './demo'
```

## context 上下文基础目录 {#context-上下文基础目录}

context 用来设置 `entry` 和 loaders 的上下文索引依赖。默认情况下，entry 等参考的是当前目录（CWD）

```
// 手动设置 context: path.resolve(__dirname, "app")
```

通过，entry 设置的相对路径，会优先寻找 `./app` 路径下的文件。

## output-输出 JS 配置 {#output-输出-js-配置}

### 基础配置项 {#基础配置项}

output 里面又很多基本的配置项，基础配置项有：

```
output:{ 
path: path.join(__dirname,'./dist'),
 filename:'js/bundle-[name]-[hash].js', 
 chunkFilename:'js/[name].chunk.js',
  chunkLoadTimeout: 12000, publicPath:'/dist/' }
```

* path: 用来决定输出目录。通常结合 path.join/resolve\(\) 设置绝对路径。

* filename\[alias: name\]: 决定具体输出的文件名，上面 output 选项中，可以用到很多全局的占位符，比如：name、\[hash\] 等。

* chunkFilename: 主要是用来对没在 entry 中定义的文件来设定具体的输出目录和文字的。常常用来按需的异步加载当中，例如：

```
require.ensure(["modules/tips.jsx"], 
function(require) { var a = require("modules/tips.jsx"); // ... }, 'tips');
```

这里，chunkFilename 就可以将 tips.jsx 具体输出为 js/tips.chunk.js。

* publicPath：并不是用来输出打包路径，而是在 runtime 的时候，手动修改 src 引用路径的。常常配合 css/style/html-loader 来修改。例如，会将: 
  `src="picture.jpg" Re-writes ➡ src="/assets/picture.jpg"`
   。相当于，手动修改请求 url 的地址。
* chunkLoadTimeout\[Number\]: 设置 webpack chunk 的请求超时时间。// TODO

上面也提到了，在 output 中会存在一起全局占位符，比如：：name、\[hash\] 等。代表的含义为：

* \[id\]:webpack给块分配的内部chunk id，如果你没有隐藏，你能在打包后的命令行中看到；
* \[hash\]：每次构建过程中，生成的唯一 hash 值;（每次都变）
* \[chunkhash\]: 依据于打包生成文件内容的 hash 值,内容不变，值不变；

### hash 控制 {#hash-控制}

在全局占位符中，有一个特殊的值 \[hash\]。我们可以在 webpack 中，针对 hash 输出做相关的控制。（这里会一并影响到 \[chunkhash\]）

* output.hashDigest: 具体输出 hash 的 encoding，有 ‘hex’, ‘latin1’ or ‘base64’。默认为 hex
* output.hashDigestLength: 输出 hash 具体长度。默认为 20。
* output.hashFunction: 引入文件的具体算法，默认为 md5。
* output.hashSalt: 给 hash 加盐，很少用。

如果，有使用 `ExtractTextWebpackPlugin` 的话，它会通过 `contenthash` 选项，直接覆盖上面 4 个的 hash 选项。

### 自定义库的打包 {#自定义库的打包}

在 webpack 中，如果想要自己写一个包并发布的话，就需要独立打包成为一个 library。这里，在 webpack 中有指定的配置项，和我们平常写的 webpack 打包项目不同。

独立打包一个库，常常会遇到应用外部库的事情，你可以选择大伯啊，也可以选择不打包。这里，除了 output 选项之外，还需要`externals`选项。下面内容主要对 output 里面内容做详解解释，而 externals 会放到后面进行讲解。

externals 主要是用来解决外部库打包问题，基本设置为：

```
externals: {
    key: "value"
  },
```

value: 用来指明 外部库 通过全局（window，global）的方式暴露的模块名。例如：

```
window.jQuery = {
  //xxx
}
```

webapck 会直接处理`window`或者`global`对象进行模块化包裹。否则，则不会对指定模块进行`externals`处理。这点非常关键。

* key: 具体指代，在经过 value 处理过后的外部库，能够在 webpack 通过 require 或者 import 的包名，例如：

```
 externals: {
    $: "jQuery"
  }
  
// index.js

var $ = require("$");
```

在 output 中主要有 4 个关键选项设置值：

* output.library\[String\]: 用来设置通过，CDN 引入时的全局 Name。对于，require/import 引入的没影响（模块引入具体是根据具体代码中的`exports`来决定）
* output.libraryExport: 限定通过模块具体暴露的接口。例如：

```
libraryExport: ["MyModule", "MySubModule"]; // 只能到处前面两个模块
```

最重要的模块输出选项应该是`libraryTarget`，它是用来决定模块以何种规范输出，在全局变量的 Name 是啥。其基本格式为：

```
output.libraryTarget[String]: var | assign | this | window | global | commonjs | commonjs2 | amd | umd
```

上面那些全部是都是可选项值。这里先告诉大家，在现代 JS 的写法中，最后一个`umd`的选项是最常用的。如果你还是想通过 script 标签引入的话，则前面 5 个比较适合给你灵活自定义。当然，`umd`选项也是适合的，不过灵活性差了点。

上面选项可以分为三种类型：

* 全局变量型\[var,assign\]：用来表示模块导出，是直接在全局中定义变量。比如，jQuery 的
  `$`，不需要额外引入处理。
* 对象绑定型\[this,window,global,commonjs\]：用来将导出的模块，绑定到具体的对象中。比如，jQuery 的 window\[’$’\]，this\[’$’\] 这样的引入绑定。
* 模块绑定型\[commonjs2,amd,umd\]: 需要通过，全局 require\(\) 方法引入的模块。

libraryTarget 常常和`library`选项一起结合使用。下面内容，统一`library`设置为`Villainhr`：

```
output.library: 'Villainhr'
```

接下来，我们具体一个一个来细分讲解一下：

**全局变量型**

**var**

```
output.libraryTarget: 'var'// 默认值
```

在 js 脚本实际编译结果为：

```
var Villainhr = _entry_return_;

// In a separate script...

Villainhr.doSomething();

```

通过`var`变量定义关键字来申明一个变量，将导出的模块直接复制过去。

**assign**

```
output.libraryTarget: assign

```

编译结果为：

```
Villainhr = _entry_return_;


// In a separate script...

Villainhr.doSomething();

```

这里，就直接全局绑定，如果前面已经定义过`Villainhr`则会默认覆盖。其实和`var`定义没啥区别。

**对象绑定型**

对象绑定则是直接通过 Object 的方法，将导出的模块直接绑定到具体对象：

**this**

```
output.libraryTarget: "this"
```

编译结果为：

```
this["Villainhr"] = _entry_return_;


// In a separate script...
this.Villainhr.doSomething();
Villainhr.doSomething(); 
// if this is window
```

导出的模块，直接绑定在 this 上，在全局中直接使用 this.xxx 来进行模块调用。

**window**

```
output.libraryTarget: "window"
```

编译结果为：

```
window["Villainhr"] = _entry_return_;


window.Villainhr.doSomething();

// or

Villainhr.doSomething();

```

这里就是通常的导出方案，直接将输出模块绑定到 window 对象上。其实也相当于`var`绑定，直接通过模块名使用。

**global**

```
output.libraryTarget: "global"
```

编译结果为：

```
global["Villainhr"] = _entry_return_;

```

直接通过 global 对象调用，这个通常用在 NodeJS 的环境。

**commonjs**

```
output.libraryTarget: "commonjs"
```

编译结果为：

```
exports["Villainhr"] = _entry_return_;

require("Villainhr").doSomething();

```

在引用调用时，直接通过`require()`方法引入。

**模块绑定型**

模块绑定型有 commonjs2,amd,umd 这三个选项。他们具体的规范就是根据选项值对应的规范来的——`CommonJS`，`AMD`。

**commonjs2**

编译结果为：

```
module.exports = _entry_return_;

```

这和`commonjs`类似，不过遵循的是 commonjs2 的规范，是直接将其赋值到 module.exports 上的。具体使用还是需要`require()`引用：

```
require("Villainhr").doSomething();

```

**amd**

编译结果为:

```
define("Villainhr", [], function() 
{  
// main body
});

```

这里通过 amd 异步加载的规范，来导出具体的文件模块。具体使用是通过`require`方法直接引入使用：

```
require(['Villainhr'], function(Villainhr) {  
// xxx
});

```

**umd**

这个模块标准应该属于最方便的一种，它实际上集合了 commonjs1/2,amd,this 的绑定方式。具体编译结果为：

```
(function webpackUniversalModuleDefinition(root, factory) {
  if(typeof exports === 'object' && typeof module === 'object')
    module.exports = factory();
  else if(typeof define === 'function' && define.amd)
    define([], factory);
  else if(typeof exports === 'object')
    exports["Villainhr"] = factory();
  else
    root["Villainhr"] = factory();
})(this, function() {
  //what this module returns is what your entry chunk returns
});
```

这里其实一共有 4 个绑定，不过它会优先进行绑定选择，具体顺序是：

> commonjs2 &gt; AMD &gt; commonjs &gt; this

也就是 4 选一，如果已经通过 commonjs2 引入的话，则不能在通过其它方式使用。比如，this.Villainhr 这样是访问不到的。

  










































  


