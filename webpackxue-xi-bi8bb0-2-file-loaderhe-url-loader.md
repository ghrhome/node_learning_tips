# webpack学习笔记-2-file-loader 和 url-loader

1.前言

    如果我们希望在页面引入图片（包括img的src和background的url）。当我们基于webpack进行开发时，引入图片会遇到一些问题。

    其中一个就是引用路径的问题。拿background样式用url引入背景图来说，我们都知道，webpack最终会将各个模块打包成一个文件，因此我们样式中的url路径是相对入口html页面的，而不是相对于原始css文件所在的路径的。这就会导致图片引入失败。这个问题是用file-loader解决的，file-loader可以解析项目中的url引入（不仅限于css），根据我们的配置，将图片拷贝到相应的路径，再根据我们的配置，修改打包后文件引用路径，使之指向正确的文件。

    另外，如果图片较多，会发很多http请求，会降低页面性能。这个问题可以通过url-loader解决。url-loader会将引入的图片编码，生成dataURl。相当于把图片数据翻译成一串字符。再把这串字符打包到文件中，最终只需要引入这个文件就能访问图片了。当然，如果图片较大，编码会消耗性能。因此url-loader提供了一个limit参数，小于limit字节的文件会被转为DataURl，大于limit的还会使用file-loader进行copy。

    url-loader和file-loader是什么关系呢？简答地说，url-loader封装了file-loader。url-loader不依赖于file-loader，即使用url-loader时，只需要安装url-loader即可，不需要安装file-loader，因为url-loader内置了file-loader。通过上面的介绍，我们可以看到，url-loader工作分两种情况：1.文件大小小于limit参数，url-loader将会把文件转为DataURL；2.文件大小大于limit，url-loader会调用file-loader进行处理，参数也会直接传给file-loader。因此我们只需要安装url-loader即可。

    推荐文档：

    file-loader: https://github.com/webpack-contrib/file-loader

    url-loader: http://www.cnblogs.com/ghost-xyx/p/5812902.html

2.loader中的参数

    上面提到url-loader的参数和file-loader的参数，那么loader的参数是个什么概念呢？loader的参数用来自定义loader处理文件时的工作特性。下面以url-loader为例，介绍一下webpack的loader中的参数。

    首先看下面的例子：

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
                use: ['style-loader', 'css-loader']  
            },  
            {  
                test: /\.jpeg$/,  
                use: [  
                    {  
                        loader: 'url-loader',  
                        options: {  
                            limit: '1024'  
                        }  
                    },  
                ]  
            }  
        ]  
    }  
}  
```

    其中，url-loader的配置中的options属性表示的就是url-loader的参数，还有一种等价的写法

```
{  
    test: /\.jpeg$/,  
    use: 'url-loader?limit=1024  
}  

```

如果有多个参数，就用 ‘

&

’ 连接起来。和http请求中的参数类似。正如前面介绍的，limit这个参数就是告诉url-loader，在文件小于多少个字节时，将文件编码并返回DataURL。此外url-loader还有一些用于file-loader的参数。我们知道，file-loader的作用是将文件复制到其他目录。file-loader提供了一系列参数允许我们自定义诸如输出文件、文件名规则、发布路径等特性的参数。下面介绍一下这些参数。

3.url-loader的参数

    先看下配置好的代码：

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
                use: ['style-loader', 'css-loader']  
            },  
            {  
                test: /\.jpeg$/,  
                use: 'url-loader?limit=1024&name=[path][name].[ext]&outputPath=img/&publicPath=output/',  
            }  
        ]  
    }  
}  
```

这里涉及到了4个参数：limit、name、outputPath、publicPath。其中limit已经说明过。file-loader相关的是name、outputPath和publicPath。下面解释一下这3个参数

    name表示输出的文件名规则，如果不添加这个参数，输出的就是默认值：文件哈希。加上\[path\]表示输出文件的相对路径与当前文件相对路径相同，加上\[name\].\[ext\]则表示输出文件的名字和扩展名与当前相同。加上\[path\]这个参数后，打包后文件中引用文件的路径也会加上这个相对路径。

    outputPath表示输出文件路径前缀。图片经过url-loader打包都会打包到指定的输出文件夹下。但是我们可以指定图片在输出文件夹下的路径。比如outputPath=img/，图片被打包时，就会在输出文件夹下新建（如果没有）一个名为img的文件夹，把图片放到里面。

    publicPath表示打包文件中引用文件的路径前缀，如果你的图片存放在CDN上，那么你上线时可以加上这个参数，值为CDN地址，这样就可以让项目上线后的资源引用路径指向CDN了。

4.安装url-loader

```
npm install --save-dev url-loader  
```

5.demo

https://github.com/KIDFUCKER/webpack-demo.git  


分支：v3-url-loader



















