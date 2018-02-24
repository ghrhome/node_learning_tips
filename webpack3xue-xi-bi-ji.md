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

有时候，开发者还会利用 `require.resolve(route)`来在 entry 中引入具体路径的文件。该方法只是用来作为路径模块的搜索，其返回的是指定文件的绝对路径名。解析机制有点类似，require\(route\)

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

context 用来设置 `entry` 和 loaders 的上下文索引依赖。默认情况下，entry 等参考的是当前目录（CWD）

```
// 手动设置 context: path.resolve(__dirname, "app")

```

通过，entry 设置的相对路径，会优先寻找 `./app` 路径下的文件。

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

如果，有使用 `ExtractTextWebpackPlugin` 的话，它会通过 `contenthash` 选项，直接覆盖上面 4 个的 hash 选项。

### 自定义库的打包 {#自定义库的打包}



