# 轻松掌握深拷贝和浅拷贝

首先，我们必须明确一点，就是JavaScript的变量可以分为以下两种类型：

* 基本类型
  * undefined
  * null
  * number
  * string
  * boolean
  * symbol

> 基本变量是直接按值存放的，存放在栈内存中的简单数据段，可以直接访问。

* 引用类型 Object

> 存放在堆内存中的对象，变量保存的是一个指针，这个指针指向另一个位置。当需要访问引用类型（如对象，数组等）的值时，首先从栈内存中获得该对象的地址指针，然后再从堆内存中取得所需的数据。

  
**一个简单的例子**

```
var a = 2;
var obj1 = {b:2};
var obj2 = obj1;

obj2.b = 3; 

console.log(obj1.b); // 3
console.log(obj2.b); // 3
```

那么问题就来了，有一些场景我们需要将一个对象含的值全部`Copy`给另一个对象，这个时候如果只是简单的赋值操作，只是对指针进行了一个复制，而在堆内存区的值并没有发生改变。所以我们得到以下的结论：

### 什么是深拷贝

> 深拷贝即是在堆内存区拷贝出一个对象来。

深拷贝是开辟一块新的内存地址，将原对象的各个属性逐个复制进去。对拷贝对象和源对象各自的操作互不影响。

### 实现浅拷贝

之前一直有一个错误实现深拷贝的想法，就是**遍历一个对象的k-v对并一一复制给另一个对象**便可以实现**深拷贝**。

**但是是错误的，这个是浅拷贝\(shallowCopy\)**

原因很简单，当`K-V`对里`value`是`Object`的时候，复制过去便仍然是复制引用。比如

```
let obj = {
    a: 1,
    b:{
        c: 2,
        d: 3
    }
}

let obj2 = {}

for(let item of Object.keys(obj)){
    obj2[item] = obj[item]
}

obj2.c = 2; 

obj2.c // 此时obj.c 变成了2
```

另外一点

[Object.assign](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FObject%2Fassign)也是一个`shallowCopy`

```

let obj1 = { a: 0 , b: { c: 0}};
  let obj2 = Object.assign({}, obj1);
  console.log(JSON.stringify(obj2)); // { a: 0, b: { c: 0}}
  
  obj1.a = 1;
  console.log(JSON.stringify(obj1)); // { a: 1, b: { c: 0}}
  console.log(JSON.stringify(obj2)); // { a: 0, b: { c: 0}}
  
  obj2.a = 2;
  console.log(JSON.stringify(obj1)); // { a: 1, b: { c: 0}}
  console.log(JSON.stringify(obj2)); // { a: 2, b: { c: 0}}
  
  obj2.b.c = 3;
  console.log(JSON.stringify(obj1)); // { a: 1, b: { c: 3}}
  console.log(JSON.stringify(obj2)); // { a: 2, b: { c: 3}}

```

### 如何实现深拷贝

* 方法一:
  **JSON对象的parse和stringify**
  \(最简单的\)

```
let obj1 = { a: 0 , b: { c: 0}};

let obj2 = JSON.parse(JSON.stringify(obj1));

obj2.b.c = 3;
console.log(obj2.b.c); // { a: 0 , b: { c: 3}};

console.log(obj1.b.c); // { a: 0 , b: { c: 0}};

```

该方法够处理JSON格式能表示的所有数据类型，但是无法拷贝对象里面的`函数`，`正则表达式`等，而且会丧失所有的`constructor`，也就是说，将是破坏整条`prototype`链。

* 方法二：
  **递归浅拷贝**

刚才提到浅拷贝只能拷贝对象的一层，那么对浅拷贝进行递归便可以实现深拷贝。

```
function deepCopy (oldObj, newObj){
    for(let key in oldObj){
        if(typeof oldObj[key] != 'object'){
            // 是基本类型直接复制
            newObj[key] = oldObj[key];
        }else {
            newObj[key] = oldObj[key].constructor == '[Function: Array]'？[]:{};
            deepCopy(oldObj[key],newObj[key])
        }
    }
}

```













