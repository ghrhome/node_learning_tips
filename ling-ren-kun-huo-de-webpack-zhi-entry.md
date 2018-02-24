# 令人困惑的webpack之entry {#articleTitle}

> 使用webpack快一年了，现在1.X版本都过气了，但是感觉自己对它那复杂的配置还是很不熟悉，各种路径，各种loader，各种plugin，又是单页面又是多页面... 在vue-cli出来的时候，都不敢用他的webpack模板，主要就是因为webpack的配置文件看不懂，不敢自己根据需要做修改。现在沉下心来，一点一点的玩弄常用属性和插件，尽力能自如的进行配置。先拿配置中的
>
> `entry`开始。

## 从最简单开始 {#articleHeader0}

最简单的webpack.config.js文件：

```
module.exports = {
    entry: './app.js',
    output: {
        path: './output',
        filename: 'output-file.js'
    }
}
```

这个足够简单，进行webpack之后，会在命令的执行目录下新建output目录（如果需要的话），并将打包app.js和它的依赖，生成output-file.js放在output目录中：

![](/assets/4085088321-58e866e19bba7_articlex.png)

如果不希望这里涉及到的路径和执行webpack命令时的具体路径相关，而是希望相对于配置文件的路径的话，就需要使用`path`模块：

```
var path = require('path')

module.exports = {
    entry: path.resolve(__dirname, './app.js'),
    output: {
        path: path.resolve(__dirname, './output'),
        filename: 'output-file.js'
    }
}

```

## entry的三种形式 {#articleHeader1}

配置文件中`entry`接受三种形式的值：字符串，数组和对象

### 对象entry {#articleHeader2}

对象形式如下：

```
entry: {
    <key>: <value>
    ...
}

```

最先介绍对象形式，是因为这个是最完整的entry配置，其他形式只是它的简化形式而已。对象中的每一对属性对，都代表着一个入口文件，因此多页面配置时，肯定是要用这种形式的entry配置。

**key**

key可以是简单的字符串，比如：'app', 'main', 'entry-1'等。并且对应着`output.filename`配置中的`[name]`变量

```
entry: {
    'app-entry': './app.js'
},
output: {
    path: './output',
    filename: '[name].js'
}
```

上面的配置打包后生成：

![](/assets/3751270214-58e866e1b5858_articlex.png)

key还可以是路径字符串。此时webpack会自动生成路径目录，并将路径的最后作为`[name]`。这个特性在多页面配置下也是很有用的

```
entry: {
    'path/of/entry': './deep-app.js',
    'app': './app.js'
},
output: {
    path: './output',
    filename: '[name].js'
}
```

上面的配置打包后生成：

![](/assets/3492759912-58e866e1f383b_articlex.png)

**value**

value如果是字符串，而且必须是合理的node`require`函数参数字符串。比如**文件路径**：'./app.js'\(`require('./app.js')`\)；比如**安装的npm模块**：'lodash'\(`require('lodash')`\)

```
entry: {
    'my-lodash': 'lodash'
},
output: {
    path: './output',
    filename: '[name].js'
}

```

上面的配置打包后生成：

![](/assets/2287685124-58e866e224630_articlex.png)  
value如果是数组，则数组中元素需要是上面描述的合理字符串值。数组中的文件一般是没有相互依赖关系的，但是又处于某些原因需要将它们打包在一起。比如：

```
entry: {
    vendor: ['jquery', 'lodash']
}

```

### 字符串entry {#articleHeader3}

```
entry: './app.js'
```

等价于下面的对象形式：

```
entry: {
    main: './app.js'
}
```

### 数组entry {#articleHeader4}

```
entry: ['./app.js', 'lodash']
```

等价于下面的对象形式：

```
entry: {
    main: ['./app.js', 'lodash']
}

```

## 应用 {#articleHeader5}

具备了上面的能力，就可以开始配置一个简单的多页面webpack开发环境了。

开始前，要考虑清楚项目目录结构，使用wepback时，一般要有个src源代码目录和一个build的打包目录。

```
|-build
|-src
webpack.config.js
```

### src目录结构 {#articleHeader6}

假设我们有两个页面home和about，两个main.js分别是两个页面的入口文件：

```
|-src
    |-pages
          |-about
                about.html
                main.js
          |-home
                home.html
                main.js
```

### build目录结构 {#articleHeader7}

对于复杂点的webpack项目，先决定打包后的目录结构很重要。webpack就像画笔，打包后的目录就像你打算画的画，要朝着目标去画。

假如我希望“画”是这样的：

```
|-build
      |-assets
             |-js
                 home.bundle.js
                 about.bundle.js
      home.html
      about.html
```

有了这个结构，html中如何引入js文件就清楚了，例如在src/pages/home/home.html中：

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Home</title>
  </head>
  <body>
    Home Page
  </body>
  <script src="assets/js/home.bundle.js"></script>
</html>

```

### webpack配置文件 {#articleHeader8}

接下来，朝着build的结构，写配置文件webpack.config.js：

```
var path = require('path')

module.exports = {
  entry: {
    'assets/js/home': path.resolve(__dirname, './src/pages/home/main.js'),
    'assets/js/about': path.resolve(__dirname, './src/pages/about/main.js')
  },
  output: {
    path: path.resolve(__dirname, './build'),
    filename: '[name].bundle.js'
  }
}
```

### 打包 {#articleHeader9}

在webpack.config.js目录下执行webpack命令，然后手动将两个html文件从src/pages下拷贝到build目录下，这样在build目录下就是一个打包好的多页面结构了。

后面将用各种插件，让webpack打包全自动化，这里只是一个简单的应用例子来了解entry的用法。

## 结语 {#articleHeader10}

`entry`一个人能玩的基本就这么多，一些复杂的配置无非是通过变量的形式给其赋值，完成更灵活的配置。`entry`是webpack的起点，后面所有的文件生成，提取CSS，生成HTML或者是CommonChunk都是在其基础上进行的加工处理。

