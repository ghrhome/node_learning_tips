# 关于css类名中通配符的使用

不知道在大家在项目中有没有遇到这种情况，类名分别为floor1、floor2、floor3、floor4，然后你突然发现他们需要有一个公共的样式，这时你怎么办呢？

第一种解决方法，再重新添加一个类名，然后分别挂在所需的class里，这种方法可以解决问题，但无疑增加了代码量，而且相信大家都很懒，多麻烦呀，是不？

那么不可以使用 选择器通配符来解决问题 例如：\[class\*="floor"\]｛｝，就是说类名中包含floor的，这样你不用新增选择器名就可以实现对他们的控制了。



其实，其实写这个只是想让和我一样原来没有留意 选择器通配符 的小伙伴们一个启迪，相信那些高手已经不屑于此了，大家具备了这种意识后，相信在项目中会更加得心应手！



# JavaScript获取页面宽度高度大全

网页可见区域宽：document.body.clientWidth

网页可见区域高：document.body.clientHeight

网页可见区域宽：document.body.offsetWidth\(包括边线的宽\)

网页可见区域高：document.body.offsetHeight\(包括边线的宽\)

网页正文全文宽：document.body.scrollWidth

网页正文全文高：document.body.scrollHeight

网页被卷去的高：document.body.scrollTop\(IE7无效\)

网页被卷去的左：document.body.scrollLeft\(IE7无效\)

网页被卷去的高：document.documentElement.scrollTop\(IE7有效\)

网页被卷去的左：document.documentElement.scrollLeft\(IE7有效\)

网页正文部分上：window.screenTop

网页正文部分左：window.screenLeft

屏幕分辨率的高：window.screen.height

屏幕分辨率的宽：window.screen.width

屏幕可用工作区高度：window.screen.availHeight

屏幕可用工作区宽度：window.screen.availWidth

相对于窗口左上角的X：window.event.clientX

相对于窗口左上角的Y：window.event.clientY

相对于整个页面的X：window.event.X

相对于整个页面的Y：window.event.Y





