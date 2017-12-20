# 微信h5支付开发指南

## 前言

H5支付是基于公众号基础开发的一种非微信内浏览器支付方式（需要单独申请支付权限），可以满足在微信外的手机H5页面进行微信支付的需求。同时，由于H5链接传播十分方便、来源不易追踪，商户需要特别注意做好防钓鱼、防刷单的处理，控制风险。

**备注：**本文档重点介绍与前端相关的以及一些准备工作的部分，部分后端接口实现的不重点描述

## 微信支付场景

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d39e023f9b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

微信支付场景

## h5支付场景介绍

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d3a0689ec3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

h5支付场景介绍

## 准备工作

### 申请入口

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d3a038d6d3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

申请入口

### ua确定

为了保证最后的微信h5能成功，请确认需要的ua能把案例的微信支付唤起，其本质是一个url连接，带有唤起微信支付的功能。  
案例url:[wxpay.wxutil.com/mch/pay/h5.…](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttp%3A%2F%2Fwxpay.wxutil.com%2Fmch%2Fpay%2Fh5.v2.php)  
参考的生成地址格式（该页面首先进行安全校验，通过后才会唤起支付，不通过则会提示各种问题）：[wx.tenpay.com/cgi-bin/mmp…](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fwx.tenpay.com%2Fcgi-bin%2Fmmpayweb-bin%2Fcheckmweb%3Fprepay_id%3Dwx201711292155101b1c4a8fce0223911410%26amp%3Bpackage%3D3611216311)

### 商户准备

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d39e47274c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

商户准备

### 用户支付流程

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d3cecbcc64?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

用户支付流程

### 图解支付流程（来源微信官网）

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d3d3e3d1c9?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

下单支付时序图

### 注意事项

1、由商户后台向微信支付发起下单请求（调用统一下单接口）注：交易类型trade\_type=MWEB，[参考下单文档](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fpay.weixin.qq.com%2Fwiki%2Fdoc%2Fapi%2FH5.php%3Fchapter%3D9_20%26amp%3Bindex%3D1)  
2、统一下单接口返回支付相关参数给商户后台，如支付跳转url（参数名“mweb\_url”），商户通过mweb\_url调起微信支付中间页  
3、中间页进行H5权限的校验，安全性检查（此处常见错误请见下文）  
4、如支付成功，商户后台会接收到微信侧的异步通知，后台部分需要同步订单的状态  
5、用户界面需要正确设置支付结果的显示逻辑，建议如上图设计。

## 技术准备

## 常见的技术问题

### 图解常见的技术问题

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d3d5bb00e3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

常见的技术问题

### 1 . 网络环境未能通过安全验证，请稍后再试

ip问题：本质原因是因为生成支付地址与实际支付时ip不一致，除去个人切换网络之外的个人原因，其主要难点存在于获取用户网络ip的部分。  
**参考文档**：[获取ip指引](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fpay.weixin.qq.com%2Fwiki%2Fdoc%2Fapi%2FH5.php%3Fchapter%3D15_5)  
**备注**：文档已经比较详细了，只不过如果你是纯前端资源项目比如spa的，可能需要自己通过某种方式获取用户的ip，那么一种方式可以通过设置nginx实现，比如提到的下面的方式：

```
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Real-Port $remote_port;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

**其他方式：**

你也可以通过请求一个线上比较稳定的获取ip的站点来实现如果以上的方式行不通，参考代码如下，依赖于jsonp方案实现前端跨域请求。

```
// 获取ip的方法
  getIp(){
       return new Promise((resolve, reject) => {  
           request.get('https://freegeoip.net/json/').use(jsonp({
           })).end(
               (err, res)=>{
                   if(res&&res.body){
                       localStorage.setItem('ip',res['body']['ip'])
                   }
                 }
        )
       })
   },
```

### 2 . 商家参数格式有误，请联系商家解决

1. 当前调起H5支付的referer为空导致，一般是因为直接访问页面调起H5支付，请按正常流程进行页面跳转后发起支付，或自行抓包确认referer值是否为空。换句话就是说，需要你从产品支付页生成调节跳转到这个页面，不能直接跳转到该地址。

2. 如果是APP里调起H5支付，需要在webview中手动设置referer，如\(  
   Map extraHeaders = new HashMap\(\);  
   extraHeaders.put\("Referer", "商户申请H5时提交的授权域名"\);//例如[www.baidu.com](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttp%3A%2F%2Fwww.baidu.com)\)\)

### 3. 商家存在未配置的参数，请联系商家解决

1，当前调起H5支付的域名（微信侧从referer中获取）与申请H5支付时提交的授权域名不一致，如需添加或修改授权域名，请登陆商户号对应的商户平台--"产品中心"--"开发配置"自行配置  
2，如果设置了回跳地址redirect\_url，请确认设置的回跳地址的域名与申请H5支付时提交的授权域名是否一致（**需对redirect\_url进行urlencode处理，js中建议使用encodeURIComponent方法**）

### 4. 支付请求已失效，请重新发起支付

一般很少发生这种情况，如果已失效，需要产品定义相关的方案，一般是用户返回上一个页面，重新进行支付流程。

### 5. 请在微信外打开订单，进行支付

h5支付不能再微信端进行

### 6. OS：签名验证失败 或者 安卓：系统繁忙，请稍后再试

基本不会遇到，如果遇到了参考官网的解释：  
1，请确认同一个MWEB\_URL只被一个微信号调起，如果不同微信号调起请重新下单生成新的MWEB\_URL  
2，如MWEB\_URL有添加redirect\_url，请确认参数拼接格式是否有误，是否有对redirect\_url的值做urlencode,可对比以下例子格式：  
[wx.tenpay.com/cgi-bin/mmp…](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fwx.tenpay.com%2Fcgi-bin%2Fmmpayweb-bin%2Fcheckmweb%3Fprepay_id%3Dwx20161110163838f231619da20804912345%26amp%3Bpackage%3D1037687096%26amp%3Bredirect_url%3Dhttps%253A%252F%252Fwww.wechatpay.com.cn)

## 其他

### App-webview中支持支付

* 安卓代码，需要拦截页面的url，如果有对应的协议关键字，就用相关的唤起工具类

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d3d716b029?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

安卓唤起支付代码

参考文档：[csdn:安卓webview唤起微信支付](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttp%3A%2F%2Fm.blog.csdn.net%2Fxiaoxiao_ming%2Farticle%2Fdetails%2F54692378)

* ios 完成配置即可

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d3feae478e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

微信支付配置

  


![](https://user-gold-cdn.xitu.io/2017/12/20/160717d3fa76a7b7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

支付宝支付配置

备注：如果你遇到了其他app打开支付宝支付跳转到了你的app,是因为你的配置文档里写了支付宝的协议，去掉即可。比如下面的配置就会导致这种情况。

![](https://user-gold-cdn.xitu.io/2017/12/20/160717d400f50e05?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

支付宝错误配置

### 获取app中的用户信息

* 前提说明 如果你需要在app中一定要使用微信h5的支付，那么你肯定需要需要获取用户的信息，因为app中已经登录过的不需要进入webivew重新登录。

* 区分ua 因为wap是针对所有ua的，所以针对不同的ua需要单独做处理，就需要区分不同的ua,那么app重需要追加特殊的标识，比如"XHX-v1.1.1",针对ua的转化以及识别，本次方案中采用了ua-parse的模块。

```
let UAParser = require('ua-parser-js')
let parser = new UAParser()
let result = parser.getResult()
// TYPE 枚举： Mobile Safari ; WeChat ; App ; QQ ; ali
let type = result.browser.name
let version = result.browser.version
let os = result.os.name
let isApp=result.ua.indexOf('XHX')>-1
if(isApp){
  type='App'
}else if(result.ua.indexOf('AlipayClient')>-1){
  type="ali" 
}
var info = {
  os, type, version
}
module.exports = info
```

如果你是vue的项目，建议store中存储一份ua相关的数据，便于使用，代码如下（仅供参考）

```
const state = {
    // type 枚举： 'Mobile Safari' ; 'WeChat' ; 'App' ; 'QQ' ; 'ali' ;
    os:localStorage.getItem("os")?localStorage.getItem("os"):'',
    type:localStorage.getItem("type")?localStorage.getItem("type"):'',
    version:localStorage.getItem("version")?localStorage.getItem("version"):'',
    ip:localStorage.getItem("ip")?localStorage.getItem("ip"):'',
}
const getters = {
     getUa:state=>{
         return state
     }
}
const mutations = {
  // 更新用户存储信息,建议存储到cookie一份，用于网关的请求
  updateUa(state, ua) {
      state.os = ua.os
      localStorage.setItem('os', ua.os)
      state.type = ua.type
      localStorage.setItem('type', ua.type)
      state.version = ua.version
      localStorage.setItem('version', ua.version)

  },
  updateIp(state,ip){
    state.ip = ua.ip
    localStorage.setItem('ip', ua.ip)
  }
}
const actions = {  
}
export default {
    state,
    getters,
    mutations,
    actions
} 

```

**定义获取用户信息方法**

因为ios和安卓载入webview的时机不同，我们做了针对性的处理，如果你们不做区分也可以的。另外这里做个常识性的解释：app中有js相关的方法的写法，可以将一些js的方法注入到webview之中，在使用之前需要确定这些方法注入时是在你的方法之前还是之后。

我们目前摸索出来并决定的结果是：ios在前端之后，安卓在前端之前,客户端的方法定义在window.xhxapp域名空间下，前端定义在window域名空间下，具体代码如下：

```
if(ua.type=='App'){
//ios app内
    if(ua.os=="iOS"){
    // 与ios 约定 ，ios加载webview之后调用前端isReady方法
        window.isReady=function(){
        //约定 getUserInfo  返回user的json对象，共四个字段
            if(window.xhxapp&&window.xhxapp.getUserInfo){
            let user=window.xhxapp.getUserInfo();
            store.commit("updateUser",user)
            }
         }
    }
    else{   //安卓app内
            if(window.xhxapp&&window.xhxapp.getUserInfo){
                        let user=window.xhxapp.getUserInfo();
                        user=JSON.parse(user)  //是否加json转换根据app传值类型，不固定
                        store.commit("updateUser",user)
               }
     } 
}else {

}
```

## 参考文档

* [微信h5支付文档](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fpay.weixin.qq.com%2Fwiki%2Fdoc%2Fapi%2FH5.php%3Fchapter%3D15_1)
* [微信h5支付，知乎问答](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fwww.zhihu.com%2Fquestion%2F39182675)
* [常见的问题类型-微信官方](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fpay.weixin.qq.com%2Fwiki%2Fdoc%2Fapi%2FH5.php%3Fchapter%3D15_4)



