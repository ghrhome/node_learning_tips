# webpack学习笔记-1-css-loader & style-loader

1.前言：

上一篇文章介绍了webpack的功能，并且构建了一个最简单的demo演示webpack的打包功能。当然webpack不仅有打包功能，还能对项目中各种文件按照我们的需求进行处理，这就用到了loader，所谓loader就是集成到webpack的文件处理方法，这些loader在webpack打包过程中，可以对指定类型的文件进行相应的处理，比如把less语法转换成浏览器可以识别的css语法，引入特定类型的文件（html）等等。下面将介绍一下一系列常用的loader。本文介绍style-loader和css-loader。

之前说过，webpack可以把以指定入口的一系列相互依赖的模块打包成一个文件，这里的模块指的不只是js，也可以是css。也就是说，当你用CommonJs规范去引用css文件时，webpack会把你引用的css文件也打包到最终的生成文件中里。这样我们如何让样式生效呢？有两种方法：一种是，在引入css时，在最后生成的js文件中进行处理，动态创建style标签，塞到head标签里。这样，html页面引用这个js文件时，就可以让样式生效了。另一种方法是，打包时把css文件拆出来，css相关模块最终打包到一个指定的css文件中，我们手动用link标签去引入这个css文件就可以了。这两种方法都需要配置响应的loader。这篇文章先介绍第一种方法，第二种方法在后面介绍。

2.demo：

上一篇文章的demo中，实现了点击弹出问候的效果，现在我们希望给它加上样式。

1.在src文件中，创建main.css

main.css代码如下：

```
#container {  
    width: 200px;  
    height: 100px;  
    margin: auto;  
    border: 1px solid;  
    display: flex;  
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

2.然后在入口文件./src/main.js中引用之：

```
require('./main.css');  
var greet = require('./greet.js');  
/** 
 * 为按钮绑定弹框问候 
 */  
function bindButtonElementEvent(btnElement) {  
    btnElement.addEventListener('click', function () {  
        greet();  
    });  
}  
  
window.bindButtonElementEvent = bindButtonElementEvent;  
```

3.使用npm安装style-loader和css-loader，这两个loader的作用会在下面介绍

```
npm install --save-dev style-loader css-loader 
```

4.在webpack.config.js里配置loader

这里简要介绍一下loader配置语法，看下面的例子：

// webpack.config.js

```
module.exports = {  
  
    entry: __dirname + '/src/main.js',  
  
    output: {  
        path: __dirname + '/output',  
        filename: 'main.js'  
    },  
  
    module: {  
        loaders: [  
            {  
                test: /\.css$/,  
                loader: 'style-loaer!css-loader'  
            }  
        ]  
    }  
}  
```

上面的配置中，loaders是一个数组，其中的元素是我们使用的所有loader，每个loader对应一个object，object的属性中，test是加载器要匹配的文件后缀正则，loader是使用的加载器，“!”用来分隔不同的加载器。上述loader配置表示，webpack在打包过程中，遇到后缀为css的文件，就会使用style-loader和css-loader去加载这个文件。



上面的loader配置是webpack1的写法，对应的webpack2写法如下（由于有些webpack1写法已经不被webpack2支持，因此建议使用webpack2的写法写配置文件，本文后续将使用webpack2的写法）：

// webpack.config.js

```
module.exports = {  
  
    entry: __dirname + '/src/main.js',  
  
    output: {  
        path: __dirname + '/output',  
        filename: 'main.js'  
    },  
  
    module: {  
        rules: [  
            {  
                test: /\.css$/,  
                use: ['style-loader', 'css-loader']  
            }  
        ]  
    }  
}  
```

那么，css-loader和style-loader是干什么用的呢？



引用《入门webpack》中的原文：css-loader使你能够使用类似@import和url（...）的方法实现require的功能，style-loader将所有的计算后的样式加入页面中，二者组合在一起使你能够把样式表嵌入webpack打包后的js文件中。

因此，我们这样配置后，遇到后缀为.css的文件，webpack先用css-loader加载器去解析这个文件，遇到“@import”等语句就将相应样式文件引入（所以如果没有css-loader，就没法解析这类语句），最后计算完的css，将会使用style-loader生成一个内容为最终解析完的css代码的style标签，放到head标签里。

需要注意的是，loader是有顺序的，webpack肯定是先将所有css模块依赖解析完得到计算结果再创建style标签。因此应该把style-loader放在css-loader的前面（webpack loader的执行顺序是从右到左）。

5.打包

命令行输入：

```
npm start  
```

打包后，刷新页面，可以看到，已经有了样式了。

6.后记：

demo地址：https://github.com/KIDFUCKER/webpack-demo.git

分支： v2-css-loader

* 









