# webpack学习笔记-5-extract-text-webpack-plugin

1.背景

前面提到过，为了让打包后样式生效，有两种方法，一种是使用style-loader自动将css代码放到生成的style标签中插入到head标签里。另一种就是这篇文章要介绍的，使用extract-text-webpack-plugin插件，将样式文件单独打包，打包输出的文件由配置文件中的output属性指定。然后我们在入口HTML页面写个link标签引入这个打包后的样式文件。

2.插件

关于webpack的插件，在《入门webpack》里有较详细的解释，个人理解插件和loader类似都是文件处理程序，但和loader不同，它不是针对特定类型文件的处理程序，而是在打包的整个过程这个更大的维度上起作用的处理程序。比如，js压缩插件UglifyJsPlugin就是把打包后的js代码进行压缩。还有这篇文章要介绍的插件extract-text-webpack-plugin是把样式文件单独打包。

3.文档：[https://www.npmjs.com/package/extract-text-webpack-plugin](https://www.npmjs.com/package/extract-text-webpack-plugin)

```
文档中提到了将样式文件打包成一个文件和将不同类型的样式文件打包输出成两个样式文件这两个功能。

下面用两个demo演示这两个功能。
```

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

需要注意的是，由于我们是将样式文件单独打包，所以图片的引用是相对于css文件的。因此要修改url-loader的参数，把publicPath改为‘./’。

相应的，index.html文件也要引入打包的样式文件。

```
<!DOCTYPE html>  
<html>  
<head>  
    <title>webpack-demo</title>  
    <script type="text/javascript" src="./output/main.js"></script>  
    <link rel="stylesheet" type="text/css" href="./output/main.css">  
</head>  
<body >  
    <div id="container">  
        <div class="button">  
            点击弹出问候  
        </div>  
    </div>  
</body>  
<script type="text/javascript">  
    var btnElement = document.getElementsByClassName('button')[0];  
    bindButtonElementEvent(btnElement);  
</script>  
</html>  

```

demo1地址：https://github.com/KIDFUCKER/webpack-demo.git

分支：v6-ExtractTextPlugin

5.demo2

假设项目中有css和less两种样式文件，我们希望可以把这两种样式文件分别打包。

// webpack.config.js

```
var ExtractTextPlugin = require('extract-text-webpack-plugin');  
// 生成两个实例，参数是生成文件的相对路径  
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
        extractCss,  
        extractLess  
    ]  
}  
```

demo2地址：https://github.com/KIDFUCKER/webpack-demo.git

分支： v7-ExtractTextPlugin





























