## **createObjectURL方法制作前端图片预览上传**

HTML

```
<div class="fixed">
    <a href="javascript:;" class="file" id="localImag">
        <input type="file" id="file" multiple="multiple">
        <img src="http://www.jq22.com/img/cs/500x500-1.png" id="preImg">
    </a>
    <p id="img-box"></p>
</div>
```

CSS

```
body {
	margin:0;
}
.file {
	display:block;
	height:120px;
	width:120px;
	position:relative;
	margin:0 auto;
	overflow:hidden;
	text-decoration:none;
	text-indent:0;
	border:none;
	margin-top:20px;
}
.file input {
	height:120px;
	width:120px;
	font-size:100px;
	margin:0;
	padding:0;
	position:absolute;
	left:0;
	top:0;
	opacity:0;
	z-index:999;
}
.file img {
	width:120px;
	height:120px;
	position:absolute;
	left:0;
	top:0;
	border:none;
}
#img-box {
	text-align:center;
	width:100%;
	height:120px;
	margin:0;
	margin-top:20px;
}
#img-box img {
	width:120px;
	height:120px;
	margin-right:10px;
	border:1px solid #eee;
}

```

JS

```
(function() {
    //获取图片路径方法
    function getObjectURL(file) {
        var url = null;
        if (window.createObjectURL != undefined) { // basic
            url = window.createObjectURL(file);
        } else if (window.URL != undefined) { // mozilla(firefox)
            url = window.URL.createObjectURL(file);
        } else if (window.webkitURL != undefined) { // webkit or chrome
            url = window.webkitURL.createObjectURL(file);
        }
        return url;
    }

    function preview(that, options) {
        //接受files数组列表
        var _file = that.files,
            str = "";
        console.log(_file);
        //限制上传图片的最大值
        if (_file.length > options.maxLen) {
            alert("最多上传" + options.maxLen + "张图片！");
            return;
        }

        //循环拼接为字符串
        for (var i = 0; i < _file.length; i++) {
            str += "<img src='" + getObjectURL(_file[i]) + "'/>";
        }

        //将图片动态添加到图片展示区
        document.getElementById(options.showID).innerHTML = str;
    }


    var upload_preview = window.uploadPreview = function(options) {
        var defaults = {
            fileID: options.fileID || "file",
            showID: options.showID || "img-box",
            maxLen: options.maxLen || 3
        }
        //获取页面的input标签
        var file = document.getElementById(defaults.fileID);
        //给input绑定change事件
        file.onchange = function() {
            var that = this;
            preview(that, defaults);
        };
    }
})()
//调用
uploadPreview({
    fileID: "file", //input的id
    showID: "img-box", //预览图片存放的div盒子
    maxLen: 2 //允许上传的最大值
});
```

```
uploadPreview({
    fileID : "file", //input的id
    showID : "img-box",//预览图片存放的div盒子
    maxLen : 2  //允许上传的最大值
});
```



