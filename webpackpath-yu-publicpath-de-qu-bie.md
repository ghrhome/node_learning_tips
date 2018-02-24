# path与publicPath的区别

## 前言 {#前言}

在webpack模块化开发的过程中，发现webpack.config.js配置文件的输出路径总有一个path与publicPath，不解其意。

```

module.exports = {
  output: {
    path: path.resolve("./examples/dist"),
    filename: "app.js",
    publicPath: "What should I put here?"   
  } 
}
```

## 正文 {#正文}

### 官方解释 {#官方解释}

> publicPath:
> The output.path from the view of the Javascript / HTML page
> 从JS/HTML页面来看的输出路径

### 我的理解 {#我的理解}

**output.path**  
储存你所有输出文件的本地文件目录。（绝对路径）

举个例子：  
path.join\(\_\_dirname, “build/”\)  
webpack将会把所有的文件输出到localdisk/path-to-your-project/build/

**output.publicPath**  
你上传所有打包文件的位置（相对于服务器根目录）

举个例子：  
/assets/  
假设你将这个工程部署在服务器`http://server/`  
通过将output.publicPath设置为`/assets/`，这个工程将会在`http://server/assets/`找到webpack资源。  
在这种前提下，所有与webpack相关的路径都会被重写成以`/assets/`开头。

```
src="picture.jpg" Re-writes ➡ src="/assets/picture.jpg"

Accessed by: (http://server/assets/picture.jpg)

src="/img/picture.jpg" Re-writes ➡ src="/assets/img/picture.jpg"

Accessed by: (http://server/assets/img/picture.jpg)

```

**重要**

如果你在用style-loader或者css sourceMap，你就需要设置publicPath。把它设置成服务器地址的绝对路径，比如

`http://server/assets/`，这样资源可以被正确加载。









