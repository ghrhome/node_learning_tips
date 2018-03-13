# webpack学习笔记-5-extract-text-webpack-plugin

1.背景

    前面提到过，为了让打包后样式生效，有两种方法，一种是使用style-loader自动将css代码放到生成的style标签中插入到head标签里。另一种就是这篇文章要介绍的，使用extract-text-webpack-plugin插件，将样式文件单独打包，打包输出的文件由配置文件中的output属性指定。然后我们在入口HTML页面写个link标签引入这个打包后的样式文件。

2.插件

    关于webpack的插件，在《入门webpack》里有较详细的解释，个人理解插件和loader类似都是文件处理程序，但和loader不同，它不是针对特定类型文件的处理程序，而是在打包的整个过程这个更大的维度上起作用的处理程序。比如，js压缩插件UglifyJsPlugin就是把打包后的js代码进行压缩。还有这篇文章要介绍的插件extract-text-webpack-plugin是把样式文件单独打包。

3.文档：https://www.npmjs.com/package/extract-text-webpack-plugin

    文档中提到了将样式文件打包成一个文件和将不同类型的样式文件打包输出成两个样式文件这两个功能。

    下面用两个demo演示这两个功能。

4.demo1 将样式文件打包成一个文件

// webpack.config.js

```
var ExtractTextPlugin = require('extract-text-webpack-plugin');  
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
                test: /\.less$/,  
                use: ExtractTextPlugin.extract({  
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
        new ExtractTextPlugin('main.css')  
    ]  
}  

```







































