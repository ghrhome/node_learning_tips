# webpack学习笔记-4-less-loader

1.背景

    less是一门css的扩展语言，用它我们可以更简洁地写样式。比如，

// main.css

```
#container {  
    width: 200px;  
    height: 100px;  
    margin: auto;  
    border: 1px solid;  
    display: flex;  
    background: url(./1.jpeg);  
}  
#container .button {  
    width: 100px;  
    height: 30px;  
    line-height: 30px;  
    text-align: center;  
    margin: auto;  
    border: 1px solid;  
    cursor: pointer;  
}  
```

用less，我们可以这样写

// main.less

```
#container {  
    width: 200px;  
    height: 100px;  
    margin: auto;  
    border: 1px solid;  
    display: flex;  
    background: url(./1.jpeg);  
    color: #fff;  
    .button {  
        width: 100px;  
        height: 30px;  
        line-height: 30px;  
        text-align: center;  
        margin: auto;  
        border: 1px solid;  
        cursor: pointer;  
    }  
}  
```

可以看到，less的语法更加直观整齐。当然less的语法有很多优点，具体请参考文档：

[less中文文档](http://less.bootcss.com/)



要解析这种类型的文件，就需要用到less-loader，它会在遇到less文件时，将其内容解析成相应的css文件。

2.less-loader的使用：

安装：

```
npm install --save-dev less-loader less  
```

配置less-loader：

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
                test: /\.less$/,  
                use: [  
                    'style-loader',  
                    'css-loader',  
                    'autoprefixer-loader',  
                    'less-loader'  
                ]  
            },  
            {  
                test: /\.jpeg$/,  
                // use: 'url-loader?limit=1024&name=[path][name].[ext]&publicPath=output/',  
                use: 'url-loader?limit=1024&name=[path][name].[ext]&outputPath=img/&publicPath=output/',  
            },  
        ]  
    }  

```

注意要将demo中require\('./main.css'\)改成require\('./main.less'\)

接下来执行webpack打包

```
npm start  
```

3.css模块化

    有时候，我们的样式写在不同的文件中，可能存在一个样式文件引入另一个样式文件的情况。比如上面的样式可以这样写：

// main.less

```
@import 'button.less';  
#container {  
    width: 200px;  
    height: 100px;  
    margin: auto;  
    border: 1px solid;  
    display: flex;  
    background: url(./1.jpeg);  
    color: #fff;  
}  
```

// button.less

```
#container {  
    .button {  
        width: 100px;  
        height: 30px;  
        line-height: 30px;  
        text-align: center;  
        margin: auto;  
        border: 1px solid;  
        cursor: pointer;  
    }  
}  
```

引入样式文件的语句 @import 'button.less';是用css-loader进行解析的。因此我们对less文件的配置

```
{  
    test: /\.less$/,  
    use: [  
        'style-loader',  
        'css-loader',  
        'autoprefixer-loader',  
        'less-loader'  
    ]  
},  
```

是这样执行的：首先，less-loader把less文件里less语法转成css语法，然后用autoprefixer-loader添加浏览器前缀，然后解析引入的文件，最后把计算好的css代码放到一个style标签插入到head标签中。



4.demo

https://github.com/KIDFUCKER/webpack-demo.git

分支 v5-less-loader















  




























