# webpack学习笔记-6-webpack-dev-server

1.背景

webpack可以自动化完成从开发环境到生产环境的一系列复杂的操作。但是当我们调试项目时，如果每次更新都要跑一下webpack，就会显得很麻烦。



webpack提供了开发环境调试工具——webpack-dev-server，通过这个工具，可以建立本地服务器，它会监测我们的文件改动，当文件被修改时，它会自动执行打包流程，然后自动刷新页面。你可能会觉得，如果每次改动都重新打包，那岂不是会很慢？webpack并不是真的完整地执行打包过程然后把文件输出到你指定的目录下，而是会把打包完的文件放在内存中。当文件改动时，内存中的打包文件就会更新。这样，我们引入的应该是这个内存中的文件。而不是我们自己定义的目录中的输出文件。怎么引入这个内存中的文件呢？这个内存中的文件是和你的入口html文件同目录的。因此在我们的demo中，把index.html引入的js和css文件目录都要改成相对当前目录。这样，我们在调试的时候，需要改动index.html的引用路径，然后调试完再改回来，然后再执行一下打包（因为调试的时候webpack-dev-server并未改动你的真实文件）。如果你觉得这样比较麻烦，还有另一种方式：复制index.html到你的输出目录，然后改动引入js和css文件为相对当前路径。然后执行webpack-dev-server时指定输出目录（默认是根目录）。这样server会加载index.html，然后调试完准备发布时，只需要将输出目录中的index.html删掉，再执行一遍打包过程就可以了。



如果index.html文件可以自动生成那就更方便了，html-webpack-plugin插件为我们提供了这个功能。使用这个插件可以自动在输出目录生成一个html文件（默认名字index.html，也可通过filename参数自定义）

// webpack.config.js

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

这个自动生成的html文件会自动包含你的打包js和样式文件。这样，我们直接使用webpack-dev-server就可以了。

2.安装

```
npm install webpack-dev-server -g
```

值得注意的是，若你的webpack版本和webpack-dev-server要求的不符，会出现webpack-dev-server安装报错，你可以根据报错内容重新安装指定版本的webpack。比如我的报错提示我需要2.2.0的版本。

```
// 先卸载webpack  
npm uninstall webpack  
// 安装2.2.0版本的webpack  
npm install webpack@2.2.0
```

3.webpack-dev-server

参考文档：[http://www.cnblogs.com/pingfan1990/p/5100147.html](http://www.cnblogs.com/pingfan1990/p/5100147.html)

使用webpack-dev-server有两种调试方法：iframe和inline，这两种方式输入的命令和访问的地址不同

iframe方式：

命令：webpack-dev-server \\[--content-base output/\\]（后面的参数表示入口html文件的目录，如果index.html在根目录，则不需要加后面的那个参数，因为默认就是根目录）

访问链接：[http://localhost:8080/webpack-dev-server/index.html](http://localhost:8080/webpack-dev-server/index.html)

inline方式：

命令：webpack-dev-server --inline

访问链接：[http://localhost:8080/index.html](http://localhost:8080/index.html)

4.demo

地址：[https://github.com/KIDFUCKER/calculator1.git](https://github.com/KIDFUCKER/calculator1.git)

分支： v8-webpack-dev-server

