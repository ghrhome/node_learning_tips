# 用webpack的CommonsChunkPlugin提取公共代码的3种方式

前面我们实现了[多页面分离资源引用,按需引用JS和css](http://blog.csdn.net/github_26672553/article/details/52280422)  
但有一个问题：最后生成的3个js，都有重复代码，我们应该把这部分公共代码单独提取出来。

方式一，传入字符串参数  
new webpack.optimize.CommonsChunkPlugin\(‘common.js’\), // 默认会把所有入口节点的公共代码提取出来,生成一个common.js

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
            chunks:['main','user','common.js']   // 这个模板对应上面那个节点
        }),

        new HtmlWebpackPlugin({
            filename: __dirname+'/build/html/index-build.html',
            template:__dirname+'/src/tpl/index.html',
            inject:'body',
            hash:true,
            chunks:['index','common.js']   // 这个模板对应上面那个节点
        }),

        // css抽取
        new extractTextPlugin("[name].css"),

        // 提供公共代码
        new webpack.optimize.CommonsChunkPlugin('common.js'), // 默认会把所有入口节点的公共代码提取出来,生成一个common.js
    ]
};
```

方式二，有选择的提取公共代码

```
// 提供公共代码
// 默认会把所有入口节点的公共代码提取出来,生成一个common.js
// 只提取main节点和index节点
new webpack.optimize.CommonsChunkPlugin('common.js',['main','index']), 
```

方式三，有选择性的提取（对象方式传参）

推荐

```
new webpack.optimize.CommonsChunkPlugin({
            name:'common', // 注意不要.js后缀
            chunks:['main','user','index']
        }),
```

通过CommonsChunkPlugin，我们把公共代码专门抽取到一个common.js，这样业务代码只在index.js，main.js，user.js











