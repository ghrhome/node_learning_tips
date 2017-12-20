## 1.前言

`chrome`，对于web开发者而言，都是很熟悉的一个浏览器，无论是从事的是前端，后端或者是测试！简单、快捷和功能强大使它受到了许多开发者的青睐！在浏览器排行上，`chrome`最多人使用，而且占了浏览器份额的半壁江山。可见其欢迎程度。作为一个前端开发者，我也是注重`chrome`的使用技巧。今天给大家分享下，希望能帮到大家，如果大家还有些什么好用的技巧，欢迎在评论补充，让大家交流意见，一起进步！

> 1.今天关于`chrome`的指南，我也是以学习的态度写的，是不完全的，希望大家注意和理解！要想更深入了解`chrome`，要靠大家自主学习！  
> 2.如果今天有什么写错了，写得不好，或者还有什么推荐的，非常希望和欢迎大家评论推荐，大家交流下意见，相互学习，相互进步!  
> 3.给大家提一个就是，chrome调试优化。一般来说，在日常开发上，经常使用的开发者工具面板就是：`element`,`console`,`sources`,`network`,`performance(timeline)`。这五个，我感觉是万金油五兄弟了！除了这五个，其它的面板使用频率可能不多，但是也要熟悉，知道。

## 2.element

![](https://user-gold-cdn.xitu.io/2017/11/6/163107652226c3f65202c75abdda0747?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


这个想必大家会经常用到，特别是切图调整样式的时候，这个我就按照图上的编号简单说一下！  
**`一`**：就是页面上的元素，鼠标放到上面，在页面上会出现**`（二）`**的变化，方便查找元素！  
**`三`**：点击这个箭头，然后就能在页面上快速选择元素！  
**`四`**：浏览进入移动端适配模式，如下图。可以更换各种机型，或者点击‘Edit’添加其他机型或者自定义机型！旁边的‘Online’就是模拟网络的各种情况，‘Online’旁边的图标就是横竖屏切换！这里简单提一下，大家按需使用！  
online

![](https://user-gold-cdn.xitu.io/2017/11/6/f0f6c97e8a9ab29bcab280971caf4561?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**`五`**：被选择元素的所有的涉及样式，页面的样式可以在这里随意的修改！  
**`六`**：被选择元素所有计算后的样式（有些样式会重复，覆盖，在这里就显示覆盖后的样式）以及盒模型！  
**`七`**：被选择元素的事件（这个貌似只要知道就行了，至少我没用过）

### 其它常用功能

**1.颜色取色器**

![](https://user-gold-cdn.xitu.io/2017/11/6/d8e2fd5a244eb7b58a6e0a13f08d82f1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**2.改变颜色显示方式**

![](https://user-gold-cdn.xitu.io/2017/11/6/9bf851dafbdec6be67bc9f55e81e9efe?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**3.元素状态筛选**

注意看页面的‘**招商银行**’

![](https://user-gold-cdn.xitu.io/2017/11/6/971a9044387a618b85295fcaa501afee?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![](https://user-gold-cdn.xitu.io/2017/11/6/f82e784681d4f8cb0535b45fc4def5ca?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**4.html布局调试**

注意看页面的‘**唯品会**’

![](https://user-gold-cdn.xitu.io/2017/11/6/009e7be0ce2aba5a97236688b0b6e86c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

当然，如果大家想改变html的元素，在这里也可以随便改!这个步骤一般是在调试样式的时候会用到！

![](https://user-gold-cdn.xitu.io/2017/11/6/0c7539295864e4f223b6a0a8686c525e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**5.css3动画曲线调试**

![](https://user-gold-cdn.xitu.io/2017/11/6/27c25fe0a1df4f3240a4f9d2232e2494?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

如图所示，没改变曲线一下，上面紫色的球，就会根据曲线进行运动，给开发者提供一个动画的参考，下面也会生成相关曲线的代码。这个也是做出优美的css3动画的重要一步！

## 3.console

![](https://user-gold-cdn.xitu.io/2017/11/6/e3bd76dfb47ac381ec948ae198668194?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


console这个界面，平常开发用得非常多，调试JS代码的时候，经常需要在console输出变量或者节点。个人使用上，除了在控制面板内写jS外，用得最多的就是上图，指向的那个按钮，点击一下就清空面板。这个感觉比`console.clear()`更快。  
下面简单说下我平常使用console的情况。

### console.log

这个应该就是console使用最多的功能。很多情况都是`console.log`输出变量，这个方式比`alert`更加的好用，显示的信息更全面。比如输出一个对象和节点

```
alert({1:1});
console.log({1:1})
alert(document.getElementById(
"attribute-box"
));
console.log(document.getElementById(
"attribute-box"
))

```

**alert**

![](https://user-gold-cdn.xitu.io/2017/11/6/e1a3f6e3cde8d2c1b0757844325662b9?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![](https://user-gold-cdn.xitu.io/2017/11/6/28fbacc8853f911eb042f6f381fe005f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**console**

![](https://user-gold-cdn.xitu.io/2017/11/6/8cf053876d7c1af0d3cd9d6e4e6af9ab?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![](https://user-gold-cdn.xitu.io/2017/11/6/2cc76e1162421a5bd80df48e35aa5111?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

可能很多人会以为`console.log`只接收一个参数，其实不是，比如带样式的输出

![](https://user-gold-cdn.xitu.io/2017/11/6/0836380e35b36d2f3efc1d087636d7f1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

相信很多人都知道用`console.log`。但是`console.info`,`console.warn`,`console.error` , 这个三个大家就不是很常用，下面简单演示下！

```
console.log(
'这是普通信息！'
);
console.info(
'这是普通信息！'
);
console.warn(
'这是警告信息！'
);
console.error(
'这是错误信息！'
);

```

![](https://user-gold-cdn.xitu.io/2017/11/6/5b4fe82948030286d7bf44bb5d6b482b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> PS：这个版本有点奇怪，之前`console.info`这个api在前面是有一个蓝色的标志的，我现在用的这个版本和`console.log`的表现完全一样![](https://user-gold-cdn.xitu.io/2017/11/6/0db0fcb03ea021db7cd9393b0330fcbd?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### console.time和console.timeEnd

`console.time`和`console.timeEnd`是用的第二多的api主要是检测，一段代码的执行时间

```
console.time();

for
(
let
 i=0;i
<
10000;i++){
    
}
console.timeEnd();
```

![](https://user-gold-cdn.xitu.io/2017/11/6/c9ea7f199b7e2537ee968d85dd350363?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### console.table

`console.table`平常使用主要就是更直观的显示对象或者数组

```
let
 arr=[
    {a:1,b:2,c:3},
{a:1,b:4,c:3},
{a:3,b:2,c:3},
{a:4,b:2,c:3},
{a:1,b:5,c:3},
{a:1,b:9,c:3},
{a:1,b:2,c:7}
];
console.table(arr)

```

![](https://user-gold-cdn.xitu.io/2017/11/6/240c54c9c307d70e21e9e17fc9b37c77?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```
let
 arr1=[1,2,3,4,5]
console.table(arr1)
```

![](https://user-gold-cdn.xitu.io/2017/11/6/da75417ab7c84bef0697bcddd1aaae70?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```
let
 obj2={a:1,b:2,c:3}
console.table(obj2)
```

![](https://user-gold-cdn.xitu.io/2017/11/6/1a6a1344fd2177ca98caa1842134cb7c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### console.count

`console.count`用法有点抽象，不知道怎么说，看图吧！看了就懂了！就是在调试代码的时候，判断一个函数的执行次数，使用场景不多不少吧！

![](https://user-gold-cdn.xitu.io/2017/11/6/6202c12587bc9f14c1fe73f1622b576a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### console.assert

`console.assert`接收两个参数，第一个参数为判断条件，第二个参数是提示信息，当条件为false时，提示错误信息！

![](https://user-gold-cdn.xitu.io/2017/11/6/3372fed0f6f4cae4ae1705691757a912?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### console.group和console.groupEnd

`console.group`和`console.groupEnd`也是更直观显示数据，但是使用场景感觉并不是很多。我也是知道，但是在开发上没用过！

```
console.group(
"1"
);
console.log(
"1模块的信息 11111111..."
);
console.log(
"1模块的信息 11111111..."
);
console.log(
"1模块的信息 11111111..."
);
console.groupEnd();
console.group(
"2"
);
console.log(
"2模块的信息 22222222..."
);
console.log(
"2模块的信息 22222222..."
);
console.groupEnd();

```

![](https://user-gold-cdn.xitu.io/2017/11/6/78c5f151251bcacdcbb8010b37cba332?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### $

看到$大家不要以为是jquery，其实是浏览器自带的一些api。这个在调试上就比较方便！  


关于$的api，我知道的有几个，但是我使用过用的就下面两个。其它的没怎么了解，大家有需要可以自行上网查找资料！



**$:返回第一个符合条件的元素，相当于document.querySelector**

![](https://user-gold-cdn.xitu.io/2017/11/6/92b8ffaef8b54d53f7202e3b81a3745f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**$$:返回所有符合条件的元素，相当于document.querySelectorAll**

![](https://user-gold-cdn.xitu.io/2017/11/6/2e0218fd8535086a486464dacdc6a47f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


### 查找和监控事件

`getEventListeners`作用就是查找并获取选定元素的事件。用法如下

![](https://user-gold-cdn.xitu.io/2017/11/6/59452835b7f4d4216d3470ae6e146931?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

`monitorEvents`作用是监控你所选元素关联的所有事件，事件触发时，在控制台打印它们。

![](https://user-gold-cdn.xitu.io/2017/11/6/7941dfd035a7258ad0d026dc1bccf195?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![](https://user-gold-cdn.xitu.io/2017/11/6/25203e4b29bafdab3c64744f501b3de9?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

`getEventListeners`和`monitorEvents`感觉在开发上用得并不多了，至少我没用过。但是感觉会有用，就提及一下！

## 4.network

`network`就是每当有请求发送的时候，都会被这里记录，不管是请求文件，还是ajax请求。使用的场景也很多，下面就是面板，大家看下，就知道怎么用了，很好理解！

![](https://user-gold-cdn.xitu.io/2017/11/6/9b63c0631a0813108ee1ec6e7af7c507?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


点击一个请求，在右边显示请求的资源等各种信息，就是看到下图的情形!

首先是请求的一些信息，需要关注的主要是下面这些！

![](https://user-gold-cdn.xitu.io/2017/11/6/1decf1d249d0686c14a15b5465b67711?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


然后请求返回的信息

![](https://user-gold-cdn.xitu.io/2017/11/6/6d31bec398f4cc62ae59325a359b530f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

以JSON形式返回

![](https://user-gold-cdn.xitu.io/2017/11/6/512123515be20c5b96198e6e6dc783bc?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

cookie和timeing也是请求的信息，但是我平常没怎么关注，在这里就不多说了！关于timing，大家可以参考这篇文章：[chrome浏览器中的Timing详情说明](https://link.juejin.im?target=http%3A%2F%2Fblog.csdn.net%2Fitpinpai%2Farticle%2Fdetails%2F52574385)

## 5.Sources

#### 1.断点调试

这一步就是打开文件，在任意一行的行号，点击就会出现一个断点！  


![](https://user-gold-cdn.xitu.io/2017/11/6/26e40787c29ef90feb727add2c2d9d6f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


#### 2.debugger调试

![](https://user-gold-cdn.xitu.io/2017/11/6/470ba6cb92bdd071512f34ccfa0c88f3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


![](https://user-gold-cdn.xitu.io/2017/11/6/e5c519ba33953d40f524d3092761e2fe?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


大家可能看到，上图红框的按钮，我按照编号简单说下：  
1、停止当前的断点调试  
2、继续执行下一行代码，（1.这个方式不会进入函数，2.这个方式快捷键是f10）  
3、跳入函数中去（这个方式快捷键是f11）  
4、跳出当前的函数  
5、禁用所有的断点，停止任何调试  
6、程序运行时遇到异常时是否中断调试

#### 3.调试中查看值

不说话，看图

![](https://user-gold-cdn.xitu.io/2017/11/6/328b2695ef6459aec69a3439fedc2484?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


至于旁边这些，我平常没关注过，需要的自行上网找资料，我在这里不展开讲了！

![](https://user-gold-cdn.xitu.io/2017/11/6/8467293a2c6f14f2b7bc798f61c9210e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

#### 3.查找和切换文件

这个功能说白了就是一个ctrl+p和enter快捷键的使用

![](https://user-gold-cdn.xitu.io/2017/11/6/c09a9fc86240c4594342876ab7fbe4bf?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

#### 4.格式化代码

这个就更没什么了，就是点击一下![](https://user-gold-cdn.xitu.io/2017/11/6/ee2df3bfc296e0970027bfb815af07b9?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![](https://user-gold-cdn.xitu.io/2017/11/6/ee8f905bdd3fd9ca842c6c1689eba3b7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 6.performance\(timeline\)

这个面板前期可能用得不多，但是后期优化的时候，会经常使用，这个面板的功能也是很多，大家都多花点心思了！

![](https://user-gold-cdn.xitu.io/2017/11/6/697e44b301c8024dd468e074d257c676?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


首先解释下，四个区域

### 一.控制面板（Controls）

拥有开启、停止记录，配置记录内容。模拟网络模式，手机核数等功能！这个没什么，大家看下面板就是清楚了！

### 二.概括（Overview）

对页面表现（行为）的一个概述。  
概括区域由一下三个图形记录组成:

**1.FPS**  
绿色的柱越高， FPS 值也越高，用户看着就越流畅，体验就越好。如果太小，用户看着会有卡顿的感觉

**2.CPU**  
这个面积图\(area chart\)表明了哪种事件在消耗 CPU 资源。

**3.NET**  
某一个时刻页面的表现形式（以某一个时刻的画面展示）！把鼠标移动到FPS，CPU或者NET区域任意的位置，就会展示这个时间节点面的截图。左右移动鼠标，可以重发当时的屏幕录像，利用这个可以用来分析各个动画的各个细节，或者是页面加载的快慢！

### 三.Flame Chart

这个面板，网上的说法是:可视化 CPU 堆栈\(stack\)信息记录。这个区域我没接触过，现在也是云里雾里，大家可以自行上网找资料，教程。我个人感觉一般也用不上，所以暂时没有关注这一块！

### 四.详细信息（Detail）

该面板展示当前所选时间段的更多详细信息！当有具体事件被选择时，该面板展示这个事件的更多详细信息。

![](https://user-gold-cdn.xitu.io/2017/11/6/0cae7f1a7ae67d1be5fe1bac011521d1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> 蓝色\(Loading\)：网络请求和HTML解析  
> 黄色\(Scripting\)：JavaScript编译和执行  
> 紫色\(Rendering\)：样式解析，计算，渲染。  
> 绿色\(Painting\)：重排，重绘  
> 灰色\(other\)：其它资源花费加载的时间  
> 白色\(Idle\)：空闲等待时间

这个图是招瓶颈直观的方式之一！比如上图，看到Scripting占了大部分时间，就是在执行js上，或许有待优化！

## 7.application

在平常开发上，这个面板感觉用到的几率不是很大，但是必须要知道！

### 1.cookie

![](https://user-gold-cdn.xitu.io/2017/11/6/e2efb87470b89229b4f8abb897810815?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


### 2.localstorage和localsession

![](https://user-gold-cdn.xitu.io/2017/11/6/490b34ea442c6716522cd98f3e97e2d2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


![](https://user-gold-cdn.xitu.io/2017/11/6/42b93a32eca6ef8a290439495b6b8ffa?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


### 3.缓存

![](https://user-gold-cdn.xitu.io/2017/11/6/711fa75e69bc7d2ba0948210c8143acb?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


### 4.IndexedDB

![](https://user-gold-cdn.xitu.io/2017/11/6/ed12a3f6e4aacdb6b32be2a7057bd97a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


### 5.Frames

Frames 将页面上的资源按frame类别进行组织显示。顶级的top是一个主文档，在top下面是主文档的Fonts、Images、Scripts、Stylesheets等资源。最后一个就是主文件自身。

这个我个人觉得知道下就可以了，基本很少用到。

![](https://user-gold-cdn.xitu.io/2017/11/6/cfc5db553e5b9ad3d57e317e67863195?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


## 8.插件推荐

关于chorem的插件或者扩展程序，我用的也就几个，下面简单推荐下，按需使用，如果不满足，可以到谷歌应用商店找。

### HostAdmin

管理host的一个工具

![](https://user-gold-cdn.xitu.io/2017/11/6/a7518a027bb3de49de010888aec46ea7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### JSON Editor

json格式化的工具

![](https://user-gold-cdn.xitu.io/2017/11/6/23090b4f77014def58a208235b8d2321?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### QR码发生器

![](https://user-gold-cdn.xitu.io/2017/11/6/baeeda58ed864fd8b774480d180faa01?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

就是把当前页面的url转成二维码，使用场景就是当要使用手机测试的时候，懒得在手机上输入整个网址，直接扫码就可在手机访问！如下图

![](https://user-gold-cdn.xitu.io/2017/11/6/baa4106f0571d91be8f233ef86869636?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### vue-devtools

![](https://user-gold-cdn.xitu.io/2017/11/6/a01e81562e32dc25f762c4702250d242?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

有了这个就能看到下面的扩展，使用vue开发的一个神奇，调试会变得很方便！（用这个插件切勿长时间停留在这个面板，有可能页面会奔溃）

![](https://user-gold-cdn.xitu.io/2017/11/6/0679bb32f188a04c480019591282236f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


### WEB前端助手\(FeHelper\)

![](https://user-gold-cdn.xitu.io/2017/11/6/2a6f192cdd38edbc3f0497309016d674?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### Performance-Analyser

网页性能分析工具。这个有用，但是我用的比较少，可能目前的项目没有那么严格，专业吧！尴尬中。。。

![](https://user-gold-cdn.xitu.io/2017/11/6/bb0cc1ec1a416fac4ad8462ec4ccc053?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![](https://user-gold-cdn.xitu.io/2017/11/6/a5602fca80977db6cd7be83ae61a9b93?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


![](https://user-gold-cdn.xitu.io/2017/11/6/f41a3aa9e3f6101dce8d43fb7da75548?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)  


图片太大，也有点多，我就放这两张了！大家觉得有用，就下载就好

### 划词翻译

英文这个是很多程序员的硬伤，划词翻译这个能很好帮到大家！

![](https://user-gold-cdn.xitu.io/2017/11/6/e6fbaae67bbb0ff5655b40387b6306c5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![](https://user-gold-cdn.xitu.io/2017/11/6/a9bfbec8c748563ca6540fd7c91e96e5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 掘金 Chrome 插件

有这个插件，掘金每天会根据自己的设置，推送的文章和项目！（发稿前的这几天，这个插件改版了，跟图上会有不一样，图就不截了，大家注意下就好）

![](https://user-gold-cdn.xitu.io/2017/11/6/50f640cecacb65ab62c41fb23cf54dc9?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## 9.参考链接

[随笔分类 - Chrome开发者工具系列](https://link.juejin.im?target=http%3A%2F%2Fwww.cnblogs.com%2Fconstantince%2Fcategory%2F712675.html)  
[全新Chrome Devtool Performance使用指南](https://link.juejin.im?target=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F29879682)  
[前端 chrome 浏览器调试总结](https://link.juejin.im?target=https%3A%2F%2Fjuejin.im%2Fentry%2F58452127128fe1006c51e5b6)  
[你所不知道的 Console](https://link.juejin.im?target=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000006721606)  
[chrome使用技巧（看了定不让你失望）](https://link.juejin.im?target=http%3A%2F%2Fwww.cnblogs.com%2Fliyunhua%2Fp%2F4544738.html)

## 10.小结

好了，关于chrome的不完全指南就说到这里了，上面所提及的内容，可能也就chrome的20-40%功能，但是在日常开发上的使用，可能占了80%。而且有时候，开发效率不但是编辑器使用的熟练，代码精通，浏览器使用的顺手也是提高效率不可或缺的一环！熟练使用chrome和写代码是一样的道理，都是要靠自己多练，才能熟悉使用，熟能生巧！  
最后，如果大家觉得我有哪里写错了，写得不好，有其它什么建议，推荐的的工具！非常欢迎大家补充。希望能让大家交流意见，相互学习，一起进步！

  


作者：守候i

  


链接：https://juejin.im/post/59ffad656fb9a0450b65c4c0

  


来源：掘金

  


著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

