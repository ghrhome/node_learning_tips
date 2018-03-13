# webpack学习笔记-3-autoprefixer-loader

1.前言

    这篇文章介绍一下，autoprefixer-loader的使用。我们在写css样式时，有些情况下需要加样式前缀以兼容不同的浏览器。手动添加css前缀会写较多的重复代码，降低开发效率。autoprefixer-loader为我们提供了自动添加css前缀的功能。

2.配置autoprefixer-loader

首先安装

```
npm install --save-dev autoprefixer-loader 
```

然后写配置文件

```
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
                test: /\.css$/,  
                use: ['style-loader', 'css-loader', 'autoprefixer-loader']  
            },  
            {  
                test: /\.jpeg$/,  
                // use: 'url-loader?limit=1024&name=[path][name].[ext]&publicPath=output/',  
                use: 'url-loader?limit=1024&name=[path][name].[ext]&outputPath=img/&publicPath=output/',  
            },  
        ]  
    }  
}  
```

然后执行打包命令：

```
npm start 
```

这样就可以了，我么可以在页面中看到，已经自动添加了css前缀了。

3.后记

demo: https://github.com/KIDFUCKER/webpack-demo.git

分支：v4-autoprefixer-loader

