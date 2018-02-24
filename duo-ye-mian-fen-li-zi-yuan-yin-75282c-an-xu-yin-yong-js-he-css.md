# 多页面分离资源引用,按需引用JS和css

1.新建./src/tpl/index.html:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <scritp src="http://cdn.bootcss.com/jquery/1.12.4/jquery.js"></scritp>
</head>
<body>
    <h1>这是首页</h1>
</body>
</html>
```

2.修改webpack配置文件:

```
 plugins:[
        new HtmlWebpackPlugin({
            filename: __dirname+'/build/html/login-build.html',
            template:__dirname+'/src/tpl/login.html',
            inject:'body',
            hash:true
        }),

        new HtmlWebpackPlugin({
            filename: __dirname+'/build/html/index-build.html',
            template:__dirname+'/src/tpl/index.html',
            inject:'body',
            hash:true
        }),

        // 拆分插件
        new webpack.optimize.CommonsChunkPlugin({
            name:'user', // 上面入口定义的节点组
            filename:'build-user.js' //最后生成的文件名
        }),

        // css抽取
        new extractTextPlugin("[name].css"),
    ]
```

这样执行webpack命令后，生成的2个目标文件（index-build.html，login-build.html），有什么问题呢？  
2个文件都引入了所有的js和css。

**下面我们要做到按需引用，不同的html模板引入仅它自己需要的css和js**

1.为index.html新建一个index.css:

```
h1{color: green}
```

为index.html新建index.js，并在其中引入index.css:

```
alert('这是首页');

require('./css/index.css');
```

2.编辑webpack.config.js，为不同的html模板指定不同的配置节点

```
var HtmlWebpackPlugin = require('html-webpack-plugin');
var webpack = require('webpack');

var extractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
    // entry是入口文件，可以多个，代表要编译那些js
    //entry:['./src/main.js','./src/login.js','./src/reg.js'],

    entry:
    {
        'main':'./src/main.js',
        'user':['./src/login.js','./src/reg.js'],
        'index':['./src/index.js']
    },

    externals:{
        'jquery':'jQuery'
    },

    module:{
        loaders:[
            // {test:/\.css$/,loader:'style-loader!css-loader'},
            {test:/\.css$/,loader:extractTextPlugin.extract('style','css')}
        ],
    },

    output:{
        path: __dirname+'/build/js', // 输出到那个目录下（__dirname当前项目目录）
        filename:'[name].js' //最终打包生产的文件名
    },

    plugins:[
        new HtmlWebpackPlugin({
            filename: __dirname+'/build/html/login-build.html',
            template:__dirname+'/src/tpl/login.html',
            inject:'body',
            hash:true,
            chunks:['main','user']   // 这个模板对应上面那个节点
        }),

        new HtmlWebpackPlugin({
            filename: __dirname+'/build/html/index-build.html',
            template:__dirname+'/src/tpl/index.html',
            inject:'body',
            hash:true,
            chunks:['index']   // 这个模板对应上面那个节点
        }),

        // css抽取
        new extractTextPlugin("[name].css"),
    ]
};
```

3.执行webpack之后

![](/assets/20160822210719754)

查看index-build.html中只引入了index.css和index.js











