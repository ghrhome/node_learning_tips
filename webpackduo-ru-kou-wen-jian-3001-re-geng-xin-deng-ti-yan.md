# Webpack多入口文件、热更新等体验

Webpack现今流行的前端打包工具，今儿本人也来分享下自己学习体验。

## 一、html-webpack-plugin

实现html模板文件的解析与生成

* 在plugins加入HtmlWebpackPlugin的配置，如果是多个入口文件，则需要对应加入多个HtmlWebpackPlugin功能。

```
var HtmlWebpackPlugin = require('html-webpack-plugin');
entry:{
    ma: './src/ma', /** .:必须要,表示运行时的根目录，否则找不到文件，且不报错 */
    mb: './src/mb'
},
output: {
    path: './dist',
    filename: '[name].js'
},
plugins: [
    new HtmlWebpackPlugin({ //可以模板，直接引用files对象，是webpack中state对象
        title: '模板A',
        chunks: ['ma']
    }),
    new HtmlWebpackPlugin({
        filename: 'mb.html',
        title: '模板B',
        chunks: ['mb']
    })
]
```

如上代码所示：

* 两个入口文件，ma与mb，所以配置了两个HtmlWebpackPlugin实例
* HtmlWebpackPlugin实例配置项：
  * title：模板title
  * filename：输出的html文件名称
  * chunks：包含的文件，可以entry和其他模块chunk的模块，插件导入到 模板时 没有排序，但都是。
  * excludeChunks：被排除的模块
  * chunksSortMode：添加到页面时模块的排序 none\|default\|function
  * template：模板文件路径所在位置
  * templateContent：一个函数，使用编程语言创建模板
  * inject：js插入位置：body, head
  * 模板可以访问的配置项
    * files：为webpack的stats项，可以在模板文件中使用或者
    * webpackConfig：webpackConfig的配置项
    * options：在模板文件中可以获取的webpack配置项。
* HtmlWebpackPlugin的事件使用：

| 事件名称 | 时机 | 同步/异步 |
| :--- | :--- | :--- |
| html-webapck-plugin-before-html-generation | 生成htmlPluginData之前触发 | async |
| html-webpack-plugin-before-html-processing | htmlPluginData插入到html模板之前触发 | async |
| html-webpack-plugin-alert-asset-tags | 验证资源，以及为资源做标记时触发 | async |
| html-webpack-plugin-after-html-processing | htmlPluginData插入到html模板之后触发 | async |
| html-webpack-plugin-after-emit | 生成html目标文件后触发 | async |
| html-webpack-plugin-alert-chunks | 验证资源块信息 | sync |

[![](https://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

```
var
 compile =
 webpack(config);
compile.plugin(
'compilation', 
function
( compilation, callbak) {
    console.log(
'compilation'
);
    compilation.plugin(
'html-webpack-plugin-before-html-processing', 
function
 (htmlPluginData, callbak) {
        console.log(htmlPluginData)
        callbak()
    })
})
```

[![](https://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

注意：

* 必须要监测compile的compilation事件
* 然后在回调compilation事件时，对compilation参数进行plugin的事件的注册 。

## 二、webpack.optimize.CommonsChunkPlugin

抽取公共模块为一个独立的文件，一是指定的多个模块打成一个包；二是在指定的chunks中抽取公共模块

| 参数名称 | 说明 |
| :--- | :--- |
| name | 可以是字符串，或者是数组，如果指定为entry中一个名称，则只产生此vendor，也可以是一个入口文件列表 |
| filename | 输出文件名 |
| minChunks | 单独文件最小引用数，如设置3，表示同一个模块只有被3个以外的页面引用时才打包 |
| children | 返回，把第三方的vendor包，分解到业务包中 |
| chunks | 数组，从指定的源模块提供共用vendor包 |

1. vendor打成一个包：

```
entry:{
        vendor: ['./src/vendor-jquery', 'bootstrap']
    },
    plugins: [
        new webpack.optimize.CommonsChunkPlugin({
            name: 'vendor'
        })
    ]

```

2. CommonsChunkPlugin正确的引入方式

![](https://9p23sg-ch3302.files.1drv.com/y3mjgANdsNvV7blpZUyooaJMxdELr937ptGJpapyuiE6J4HfX9oErx8MCJXZi4Mc_SJngf6ExoaEY55yDdISrOCfgFkP7dj1HRg49TXt7jq3N9Y7GjHV72iov0dAN5FSZllFwCXuZj9dH8jni3ZNzHHB6-oBcfnpVtNOZkjou-OSJY?width=664&height=699&cropmode=none)

3. 用manifest实现js库的增量更新

如果输出文件名包含hash值，需要引入以下两个插件：

* HashedModuleIdsPlugin：算hash值
* 利用CommonsChunkPlugin配置，他是manifest配置模块所有的依赖抽象，如果mainfest不更新，则html会找不到js文件

```
new webpack.optimize.CommonsChunkPlugin({
  name: 'manifest',
  chunks: ['vendor']
})
```

## 三、webpack 样式打包

这其中就包含对css文件、静态资源以及css所包含的资源文件等处理。

* css-loader：解析css代码
* style-loader：css代码写入到js文件中
* 配置代码如下：

```
loaders:[
    {
        test:/\.css$/, /*不能加引号*/
        loader: 'style-loader!css-loader'
    }
]

```

注意：test后面不能加引号，因这个是正则式

### 1. 样式文件单独存在，但不能处理静态资源

extract-text-webpack-plugin：抽取样式为单独的文件

* 参数配置说明
* ExtractTextPlugin.extract\(arg1,arg2,arg3\)
  * arg1: 可选参数，传入一个loader，当css没有被抽取的时候可以使用该loader
  * arg2：必填参数，用于编译解析css文件的loader
  * arg3：额外选，暂只可传publicPath，表示当前loader的路径
* ExtactTextPlugin在Plugins中构造时，至少需要传入一个文件名参数

filename文件名，可以指定一个固定的，也可用\[name\].\[id\].\[contenthash\]来指定文件名，\[name\]：与entry中的chunk名称一致，\[id\]：将entry的chunk的id一致；\[contenthash\]：根据内容生成hash值

| 参数名称 | 说明 |
| :--- | :--- |
| id | 可先参数，插件实例的惟一标识，缺省会自动生成 |
| filename | 文件名，可以指定一个固定的，也可用\[name\].\[id\].\[contenthash\]来指定文件名，\[name\]：与entry中的chunk名称一致，\[id\]：将entry的chunk的id一致；\[contenthash\]：根据内容生成hash值 |
| options | allchunks：是否将所有额外的chunk都压缩一个文件；disable：禁止使用此插件 |

代码如下\(webpack2.x\)：

```
var ExtractTextPlugin = require('extract-text-webpack-plugin');
module:{
    loaders:[
        {
            test:/\.css$/,
            loader: ExtractTextPlugin.extract({
                fallback: 'style-loader',
                use: 'css-loader'
            })
        }
    ]
},
plugins: [
    new ExtractTextPlugin('[name].css')
]
```

### 2. file-loader实现css中图片或web字体文件打包

```
var ExtractTextPlugin = require('extract-text-webpack-plugin');
module:{
    loaders:[ 
        {
            test:/\.css$/,
            loader: ExtractTextPlugin.extract({
                fallback: 'style-loader',
                use: ['css-loader','postcss-loader']
            })
        },
        {
            test: /\.(eot|svg|ttf|woff|woff2)$/,
            loader: 'file-loader?name=fonts/[name].[ext]'
        }
    ]       
}
```

参数说明：

| 参数名称 | 说明 |
| :--- | :--- |
| name | 配置输出文件名，例如：name=\[name\].\[hash\].\[ext\] |
|  | name子节点配置说明 |
| \[ext\] | 扩展名 |
| \[name\] | 文件名 |
| \[path\] | 相对于上下文的路径 |
| \[hash\] | hash值 |
|  | 输出配置参数 |
| publicPath | 公共资源路径（也可以说是静态资源，就是不参与打包的编译过程的资源） |
| outputPath | 输出资源路径（也可以说是静态资源，就是不参与打包的编译过程的资源） |

publicPath和outputPath使用示例代码:

```
use: "file-loader?name=[name].[ext]
&
publicPath=assets/foo/
&
outputPath=app/images/"
```

### 3. postcss实现浏览器兼容

代码如下\(webpack2.x\):

```
var autoprefixer = require('autoprefixer');
module:{
    loaders:[
        {
            test:/\.css$/,
            loader: ExtractTextPlugin.extract({
                use: ['css-loader','postcss-loader']
            })
        }
    ]       
},
plugins: [
    new webpack.LoaderOptionsPlugin({
        options:{
            postcss:[autoprefixer()]  
        }
    })
]
```

* webpack2.x不支持自定义配置节点，需要用webpack.LoaderOptionsPlugin加入自定义的插件配置节点。
* autoprefixer：一个postcss的插件，用于css3的兼容前端处理
* * browsers：配置浏览器的版本，如：browsers:\['last 2 versions'\]

## 四、热更新

* 安装 webpack-dev-server
* 热更新的概念

利用websocket实现，websocket-server识别到html、css和js的改变，就向websocket-client发送一个消息，websocket-client判断如果是html和css就操作dom，实现局部刷新，如果是js就整体刷新。

* 配置：

```
var config = require('./webpack.base.conf');
var webpack = require("webpack");
var WebpackDevServer = require('webpack-dev-server');
var compile = webpack(config);
compile.plugin('compilation', function( compilation, callbak) {
    compilation.plugin('html-webpack-plugin-before-html-processing', function (htmlPluginData, callbak) {
        callbak()
    })
})
var isProc = true;
if(isProc){
    compile.run(function(err,state){
        console.log(err);
    })
}else{
    var server = new WebpackDevServer(compile,{
        contentBase: './build',
        hot: true,
        inline: true, /*无效*/
        historyApiFallback: true
    });
    server.listen(8080);
}
```

说明：

* hot：启动热更新
* inline：是不会自动刷新网页的，因为此参数只能在cli环境下用
* 在cli下实现页面自动刷新

```
webapck-dev-server --hot --inline --config=配置文件
```

无--inline时，只能在iframe模式下自动刷新：`http://localhost:8080/webpack-dev-server/index.html`  
有--inline时，可以直接访问[`http://localhost:8080/index.html`](http://localhost:8080/index.html)进行自动刷新

## 五、cross-env

实现环境变量的定义

```
"prod": "cross-env NODE_ENV=prod node ./build/dev-server.js",
"dev": "cross-env NODE_ENV=dev node ./build/dev-server.js"

```

## 六、示例代码结构说明

前五节说了这么多，也许让你听得云里雾里的。没有代码来说明程序是多么枯燥啊（声明：此代码还包含后一章节的单元测试和e2e测试的配置）。代码结构图以及源码[下载地址](http://files.cnblogs.com/files/cqhaibin/webpack-demo.rar)：



* build：打包配置文件
  * dev-server.js：打包运行入口
  * webpack.base.conf.js：打包plugins节点的配置
  * webpack.core.conf.js：打包module以及entry的基本配置
* src：源码目录
* test：测试目录
  * e2e：点到点测试
  * unit：单元测试
  * mocks：mockjs模拟数据



