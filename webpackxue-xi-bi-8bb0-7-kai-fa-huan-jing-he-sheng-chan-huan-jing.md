# webpack学习笔记-7-开发环境和生产环境

1.背景



    前面的几篇文章介绍了如何使用webpack进行开发，包括webpack的功能，使用webpack打包，webpack的几个常用loader，plugin，使用webpack-dev-server调试。但是开发环境和生产环境是有区别的，开发完成后的项目还需要再进行一些其它处理才能构建到生产环境。比如，在开发阶段我们是不需要对js代码进行压缩处理的，而生产环境就有这种需求。webpack命令提供了使用不同配置文件进行打包过程的参数： --config。这样我们可以写不同的配置文件，开发阶段和生产阶段使用不同的配置文件进行构建就可以了。

    下面介绍一下，js代码压缩插件的使用和如何在开发环境和生产环境打包。

2.UglifyJsPlugin

    webpack内置js代码压缩插件：UglifyJsPlugin，使用时不需要额外安装，配置文件写法：

```
var webpack = require('webpack');  
var ExtractTextPlugin = require('extract-text-webpack-plugin');  
var HtmlPlugin = require('html-webpack-plugin');  
  
var extractCss = new ExtractTextPlugin('[name]-one.css');  
var extractLess = new ExtractTextPlugin('[name]-two.css');  
module.exports = {  
    // 入口文件路径，__dirname是根目录  
    entry: __dirname + '/src/main.js',  
    // 打包生成文件  
    output: {  
        path: __dirname + '/output',  
        filename: 'main.js'  
    },  
  
    module: {  
        rules: [  
            {  
                test: /\.css/,  
                use: extractCss.extract({  
                    fallback: 'style-loader',  
                    use: [  
                        'css-loader',  
                        'autoprefixer-loader'  
                    ]  
                })  
            },  
            {  
                test: /\.less$/,  
                use: extractLess.extract({  
                    fallback: 'style-loader',  
                    use: [  
                        'css-loader',  
                        'autoprefixer-loader',  
                        'less-loader'  
                    ]  
                })  
            },  
            {  
                test: /\.jpeg$/,  
                // use: 'url-loader?limit=1024&name=[path][name].[ext]&publicPath=output/',  
                use: 'url-loader?limit=1024&name=[path][name].[ext]&outputPath=img/&publicPath=./',  
            },  
        ]  
    },  
  
    plugins: [  
        new HtmlPlugin(),  
        new webpack.optimize.UglifyJsPlugin(),  
        extractCss,  
        extractLess  
    ]  
}  
```

3.开发环境和生产环境



    webpack命令可以附带参数：--config，用来指明打包使用的配置文件，若没有这个参数，则默认读取webpack.config.js。作为例子，我们写两个config文件，一个是webpack.dev.config.js，用于开发环境的配置，一个是webpack.prod.config.js。用于生产环境的配置。在我们的例子中，两者的区别就是开发环境打包中没有使用js代码压缩插件而生产环境打包使用了。

// webpack.dev.config.js

```
var ExtractTextPlugin = require('extract-text-webpack-plugin');  
var HtmlPlugin = require('html-webpack-plugin');  
  
var extractCss = new ExtractTextPlugin('[name]-one.css');  
var extractLess = new ExtractTextPlugin('[name]-two.css');  
module.exports = {  
    // 入口文件路径，__dirname是根目录  
    entry: __dirname + '/src/main.js',  
    // 打包生成文件  
    output: {  
        path: __dirname + '/output',  
        filename: 'main.js'  
    },  
  
    module: {  
        rules: [  
            {  
                test: /\.css/,  
                use: extractCss.extract({  
                    fallback: 'style-loader',  
                    use: [  
                        'css-loader',  
                        'autoprefixer-loader'  
                    ]  
                })  
            },  
            {  
                test: /\.less$/,  
                use: extractLess.extract({  
                    fallback: 'style-loader',  
                    use: [  
                        'css-loader',  
                        'autoprefixer-loader',  
                        'less-loader'  
                    ]  
                })  
            },  
            {  
                test: /\.jpeg$/,  
                // use: 'url-loader?limit=1024&name=[path][name].[ext]&publicPath=output/',  
                use: 'url-loader?limit=1024&name=[path][name].[ext]&outputPath=img/&publicPath=./',  
            },  
        ]  
    },  
  
    plugins: [  
        new HtmlPlugin(),  
        extractCss,  
        extractLess  
    ]  
}  
```

















