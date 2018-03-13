# ES6这些就够了

刚开始用vue或者react，很多时候我们都会把ES6这个大兄弟加入我们的技术栈中。但是ES6那么多那么多特性，我们需要全部都掌握吗？秉着二八原则，掌握好常用的，有用的这个可以让我们快速起飞。

接下来我们就聊聊ES6那些可爱的新特性吧。

## 1.变量声明const和let

我们都是知道在ES6以前，var关键字声明变量。无论声明在何处，都会被视为声明在函数的最顶部\(不在函数内即在全局作用域的最顶部\)。这就是函数**变量提升**例如:

```
function aa() {
    if(bool) {
        var test = 'hello man'
    } else {
        console.log(test)
    }
  }
```

以上的代码实际上是

```
function aa() {
    var test // 变量提升
    if(bool) {
        test = 'hello man'
    } else {
        //此处访问test 值为undefined
        console.log(test)
    }
    //此处访问test 值为undefined
  }
```

所以不用关心bool是否为true or false。实际上，无论如何test都会被创建声明。

接下来ES6主角登场：  
 我们通常用let和const来声明，let表示**变量**、const表示**常量**。let和const都是块级作用域。怎么理解这个块级作用域？

* 在一个函数内部
* 在一个代码块内部

> 说白了**{}大括号内**的代码块即为let 和 const的作用域

看以下代码：

```
function aa() {
    if(bool) {
       let test = 'hello man'
    } else {
        //test 在此处访问不到
        console.log(test)
    }
  }
```

let的作用域是在它所在当前代码块，但不会被提升到当前函数的最顶部。

再来说说const。

```
const name = 'lux'
name = 'joe' //再次赋值此时会报错
```

说一道面试题

```
var funcs = []
    for (var i = 0; i < 10; i++) {
        funcs.push(function() { console.log(i) })
    }
    funcs.forEach(function(func) {
        func()
    })
```

这样的面试题是大家常见，很多同学一看就知道输出 10 十次

但是如果我们想依次输出0到9呢？两种解决方法。直接上代码。

```
// ES5告诉我们可以利用闭包解决这个问题
    var funcs = []
    for (var i = 0; i < 10; i++) {
        func.push((function(value) {
            return function() {
                console.log(value)
            }
        }(i)))
    }
    // es6
    for (let i = 0; i < 10; i++) {
        func.push(function() {
            console.log(i)
        })
    }
```

达到相同的效果，es6简洁的解决方案是不是更让你心动！！！

## 2.模板字符串

es6模板字符简直是开发者的福音啊，解决了ES5在字符串功能上的痛点。

第一个用途，基本的字符串格式化。将表达式嵌入字符串中进行拼接。用${}来界定。

    //es5 
        var name = 'lux'
        console.log('hello' + name)
        //es6
        const name = 'lux'
        console.log(`hello ${name}`) //hello lux

第二个用途，在ES5时我们通过反斜杠\(\\)来做多行字符串或者字符串一行行拼接。ES6反引号\(\`\`\)直接搞定。

    // es5
        var msg = "Hi \
        man!
        "
        // es6
        const template = `<div>
            <span>hello world</span>
        </div>`

对于字符串es6当然也提供了很多厉害的方法。说几个常用的。

```
// 1.includes：判断是否包含然后直接返回布尔值
    let str = 'hahay'
    console.log(str.includes('y')) // true
    // 2.repeat: 获取字符串重复n次
    let s = 'he'
    console.log(s.repeat(3)) // 'hehehe'
    //如果你带入小数, Math.floor(num) 来处理
```

## 3.函数

**函数默认参数**

在ES5我们给函数定义参数默认值是怎么样？

```
function action(num) {
        num = num || 200
        //当传入num时，num为传入的值
        //当没传入参数时，num即有了默认值200
        return num
    }
```

但细心观察的同学们肯定会发现，num传入为0的时候就是false， 此时num = 200 与我们的实际要的效果明显不一样

ES6为参数提供了默认值。在定义函数时便初始化了这个参数，以便在参数没有被传递进去时使用。

```
function action(num = 200) {
        console.log(num)
    }
    action() //200
    action(300) //300
```

**箭头函数**

ES6很有意思的一部分就是函数的快捷写法。也就是箭头函数。

箭头函数最直观的三个特点。

* 不需要function关键字来创建函数
* 省略return关键字
* 继承当前上下文的 this 关键字

```
//例如：
    [1,2,3].map( x => x + 1 )
    
//等同于：
    [1,2,3].map((function(x){
        return x + 1
    }).bind(this))
```

**说个小细节。**

当你的函数**有且仅有**一个参数的时候，是可以省略掉括号的。当你函数返回有且仅有一个表达式的时候可以省略{}；例如:

var people = name =&gt; 'hello' + name

    //参数name就没有括号

作为参考

```
var people = (name, age) => {
        const fullName = 'h' + name
        return fullName
    } 
    //如果缺少()或者{}就会报错
```

## 4.拓展的对象功能

对象初始化简写

ES5我们对于对象都是以键值对的形式书写，是有可能出现键值对重名的。例如：

```
function people(name, age) {
        return {
            name: name,
            age: age
        };
    }
```

键值对重名，ES6可以简写如下：

```
function people(name, age) {
        return {
            name,
            age
        };
    }
```

ES6 同样改进了为对象字面量方法赋值的语法。ES5为对象添加方法：

```
const people = {
        name: 'lux',
        getName: function() {
            console.log(this.name)
        }
    }
```

ES6通过省略冒号与 function 关键字，将这个语法变得更简洁

```
const people = {
        name: 'lux',
        getName () {
            console.log(this.name)
        }
    }
```

ES6 对象提供了Object.assign\(\)这个方法来实现浅复制。Object.assign\(\)可以把任意多个源对象自身可枚举的属性拷贝给目标对象，然后返回目标对象。第一参数即为目标对象。在实际项目中，我们为了不改变源对象。一般会把目标对象传为{}

```
const obj = Object.assign({}, objA, objB)
```



