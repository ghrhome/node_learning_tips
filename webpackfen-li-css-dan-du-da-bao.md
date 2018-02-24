# webpack分离css单独打包

## CHANGELOG {#blogTitle0}

**2018-02-08 14:46:06**

> 刚看了下，网上查了 webpack单独打包css，找到的文章，本文比较靠前，但是由于写的比较混乱，因此重新整理一下内容，更通俗易懂一点。

**2018-02-01 14:45:23**

> 由于这篇文章只写了如何把CSS打包成一个CSS文件，没有讲解如何打包成多个CSS文件，经简友提点，这里添加上了 打包成多个CSS文件的方法。

**2016-05-17 11:55**

> 刚学习webpack，记录一下webpack如何单独打包css

---

## 零、介绍 {#blogTitle1}

> 以下是个人项目中总结出来的一些基本知识，记录在这里，加深自己的印象，也让大家能够更快速方便的了解webpack，并且使用它。能力所限，有错误或者问题，请帮忙指出。

webpack 把所有的资源都当成了一个模块, CSS,Image, JS 字体文件 都是资源, 都可以打包到一个 bundle.js 文件中.

![](https://ws3.sinaimg.cn/large/006tNc79gy1fo91iuba5mj31kw0tn0tp.jpg)

webpack基本的使用很简单，但是要方方面面都讲解的话内容很多，因此这边主要讲解一下 如何使用webpack单独打包css。

至于打包出来，怎么加hash值，怎么替换html中的引用路径，怎么上传到CND可以使用gulp来实现。【有兴趣后面在写一篇文章】

## 一、extract-text-webpack-plugin 用法 {#blogTitle2}

单独打包css，在webpack需要使用一个插件，`extract-text-webpack-plugin`

#### 1. 安装extract-text-webpack-plugin {#安装extract-text-webpack-plugin}

```
// use npm 
npm install extract-text-webpack-plugin --save-dev

// or use yarn 
yarn add extract-text-webpack-plugin

```

#### 2. 配置 {#配置}

加载器里面写好插件的配置（使用什么加载器），在webpack的 plugins 里面设置抽离出来的CSS文件名叫什么。

```
var Ex = require('extract-text-webpack-plugin');
// ...省略
module: {
  loaders: [{
    test: /\.less/,
    loader: Ex.extract('style-loader', 'css-loader','less-loader')  // 单独打包出CSS，这里配置注意下
  }]
},
plugins: [
  new Ex("【name】.css")
]
```

> 稍微详细点，可以参考这个
>
> [《extract-text-webpack-plugin 的使用及安装》](https://www.cnblogs.com/dyx-wx/p/6529447.html)

下面放两个实际使用例子，方便大家理解

## 二、单页面应用，把JS里面的CSS单独打包 {#blogTitle3}

> 打包一个文件，只需要常规的在入口的js文件引用 css文件即可， 打包成多个CSS文件，可以设置多个CSS入口，让webpack用 loader去打包。 和分割单独打包js文件一样。下面有两个例子。[【例子来源】](https://github.com/wangcongyi/test/blob/master/postcss-webpack.html)

```

// webpack 1.x 配置   【早期使用的配置，那时候是1.x】
/*   webpack.config.js   */
var precss = require('precss');
var cssnext = require('cssnext');
var autoprefixer = require('autoprefixer');
var cssnano = require('cssnano');
var Ex = require('extract-text-webpack-plugin');
module.exports = {
    entry: './index.js',
    output: {
      filename: 'index.js'
    },
    module: {
      loaders: [{
        test: /\.less/,
        loader: Ex.extract('style-loader', 'css-loader','less-loader')  // 单独打包出CSS，这里配置注意下
      }]
    },
    plugins: [
      new Ex("【name】.css")
    ]
  }
```

## 三、webpack如何打包多个CSS文件 {#blogTitle4}

#### 2. 配置文件添加对应配置 {#配置文件添加对应配置}

下面直接提供一个完成的多入口CSS打包配置

```
// webpack 3.x 的配置
var path = require('path')
var glob = require('globby')  
var webpack = require('webpack')
var ExtractTextPlugin = require('extract-text-webpack-plugin')

// CSS入口配置
var CSS_PATH = {
  css: {
    pattern: ['./src/**/[^_]*.less', '!./src/old/**/*.less'],
    src: path.join(__dirname, 'src'),
    dst: path.resolve(__dirname, 'static/build/webpack'),
  }
}

// 遍历除所有需要打包的CSS文件路径
function getCSSEntries(config) {
  var fileList = glob.sync(config.pattern)
  return fileList.reduce(function (previous, current) {
    var filePath = path.parse(path.relative(config.src, current))
    var withoutSuffix = path.join(filePath.dir, filePath.name)
    previous[withoutSuffix] = path.resolve(__dirname, current)
    return previous
  }, {})
}

module.exports = [
  {
    devtool: 'cheap-module-eval-source-map',
    context: path.resolve(__dirname),
    entry: getCSSEntries(CSS_PATH.css),
    output: {
      path: CSS_PATH.css.dst,
      filename: '[name].css'
    },
    module: {
      rules: [
        {
          test: /\.less$/,
          use: ExtractTextPlugin.extract({
            use: ['css-loader', 'postcss-loader', 'less-loader']
          })
        }
      ]
    },
    resolve: {
      extensions: ['.less']
    },
    plugins: [
      new ExtractTextPlugin('[name].css'),
    ]
  },
// 如果还需要打包js，则可以在这里增加一个单独打包js的处理【根据自己需求来】
// {
//    entry:{},
//    output:{},
//    ... 省略
// }
]
```

可能有同学还在使用webpack1.x,所以这里在贴一下webpack1.x 的简单配置

```
// webpack 1.x 版本
// ...其他配置和webpack3.x一样
module: {
    loaders: [
        {
            test: /\.less$/,
            loader: ExtractTextPlugin.extract("style-loader","css-loader","postcss-loader","less-loader")
        },
    ]
}
plugins: [
  new ExtractTextPlugin('[name].css'),
]
// ...其他配置和webpack3.x一样

```











