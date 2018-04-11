# js图片前端预览之 filereader 和 window.URL.createObjectURL

```
//preview img ： filereader方式
    document.getElementById('imgFile').onchange = function(e){ 5         var ele =  document.getElementById('imgFile').files[0];

        var fr = new FileReader();
        fr.onload = function(ele){

            var pvImg = new Image();
            pvImg.src = ele.target.result;
            pvImg.setAttribute('id','previewImg');

            $('.preview_wrap').html('').append(pvImg);18         }
        fr.readAsDataURL(ele);
    }
```

```
//preview img ： URL.createObjectURL 方式
    document.getElementById('imgFile').onchange = function(e){
        var ele =  document.getElementById('imgFile').files[0];

        var createObjectURL = function(blob){
          return window[window.webkitURL ? 'webkitURL' : 'URL']['createObjectURL'](blob);
        };
        var newimgdata = createObjectURL(ele);

        var pvImg = new Image();
        pvImg.src = newimgdata;
        pvImg.setAttribute('id','previewImg');

        $('.preview_wrap').html('').append(pvImg);
    }
```

以上先贴出用filereader 和 URL.createObjectURL 两种预览方式。

按照前辈们的说法，creatObjectURL可以有更好的性能，或许是浏览器自带接口的原因, 可以处理的更快。 

最近做了一个需要上传图片预览的项目，用的最简单的input file上传，最开始想到的就是用filereader实现前端预览，很简单，见前面的第一段代码，在自己手机上测试，没问题。但在某些奇葩手机上，比如oppo 安卓4.3在我们app的webview内通过打开相册上传发现无法预览图片！但在该手机的微信，浏览器内上传均可以！

看了下代码，如下图，是因为base64编码内缺少了图片格式，手动加上image/jpg图片立即显示，刚开始以为是readAsDataURL的问题，没有编码成功，找了一晚上原因，无解！不知哪根筋搭错突然想到打印出input file的type看看，发现type为空，而file的其他属性均正常。

续百度（google最近公司FQ不稳定，没法用），结果看到有人遇到类似奇怪问题，但是没有解决。

无意间却发现图片预览除了filereader还可以用url.createObjectURL。晚上11点26，先赶最后一班地铁。

=====================

第二天一早，把半夜的灵感实验了下，根据判断input file的图片类型，如果不存在就给文件手动赋值类型。（见下方调试用代码，有点乱）

```

```

结果发现file的type并不是你直接赋值就可以改变的，没有用！



于是开始实践URL.createObjectURL \(见最上面的第二部分代码\)

神奇！发现预览正常！



查看代码发现，createObjectURL得到的是一个http格式的文件，即使input file的文件类型不存在也不会编码失败。

  


