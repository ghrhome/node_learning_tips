# CommonsChunkPlugin的一些总结，以及如何分别打包公共代码和第三方库

#### 如何分别打包公共代码和第三方库 {#如何分别打包公共代码和第三方库}

现在假设入口文件里面有

```
entry:{
    main1:'./app/js-uglify/a.js',
    main2:'./app/js-uglify/b.js',
    vendor:['./app/js-uglify/j1','./app/js-uglify/j2']
}

```

a.js和b.js是项目文件，j1和j2是第三方插件

出口文件配置：

```
output: {
    path: BUILD_PATH,
    filename: '[name].bundle.js'
 }

```

增加模块插件：

```
new webpack.optimize.CommonsChunkPlugin({
  name: 'common',
  chunks: ['main1', 'main2'],
  filename: 'common.bundle.js',
  minChunks: 2,
}),
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  chunks: ['common'],
  filename: 'vendor.bundle.js',
  minChunks: Infinity,
})
```

执行-p后，可以在build目录下，生成以下几个文件

> main1.bundle.js -&gt; 仅包含main1.js的代码文件  
> mian2.bundle.js -&gt; 仅包含main2.js的代码文件
>
> common.bundle.js -&gt; 包含main1.js和main2.js公共部分的代码文件（不包含第三方库代码）  
> vendor.bundle.js -&gt; 仅包含第三方库

可以用来抽取第三方类库和框架，这样每一个入口文件无论是否调用它们，都不会将它们重复打包进去















