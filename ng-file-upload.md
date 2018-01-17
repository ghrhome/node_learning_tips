# ng-file-upload

一、ng-file-upload简介

是一款轻量级、跨浏览器的angular上传文件指令

二、特点

（1）支持文件上传进度条、取消、暂停

（2）支持文件拖放和黏贴图像

（3）支持暂停和取消文件上传

（4）支持验证文件的类型 / 大小、图像宽度 / 高度、视频 / 音频持续时间（ng-required）

（5）支持预览显示选择的图像、视频、音频

（6）支持CORS和直接上传文件的二进制数据\( Upload.$http\(\) \)

.....

三、用法

示例：

```
<script src="angular.min.js"></script>
  <!-- shim is needed to support non-HTML5 FormData browsers (IE8-9)--> 
  <script src="ng-file-upload-shim.min.js"></script> 
  <script src="ng-file-upload.min.js"></script> 

  <div ng-app="fileUpload" ng-controller="MyCtrl"> watching model: 
     <div class="button" ngf-select ng-model="file">Upload using model $watch</div>
     <div class="button" ngf-select ng-model="files" ngf-multiple="true" ngf-accept="'image/*" accept="image/*">Upload multiple images using model $watch</div> 
     <div class="button" ngf-select ngf-change="upload($file)">Upload on file change</div> 
     <div class="button" ngf-select ngf-change="upload($files)" ngf-multiple="true" ngf-validate="{size: {min:'10KB', max:'20MB'}}">Upload multiple with size limitation</div> 
     Drop File: 
     <div ngf-drop ng-model="files" class="drop-box" ngf-drag-over-class="dragover" ngf-multiple="true" ngf-allow-dir="true" ngf-accept="'image/*,application/pdf'">Drop Images or PDFs files here</div>
     <div ngf-no-file-drop>File Drag/Drop is not supported for this browser</div>
     Image thumbnail: ![](file || '/thumb.jpg')
     Audio preview: <audio controls ngf-src="file"></audio> 
     Video preview: <video controls ngf-src="file"></video>
  </div>
```

JS：

```
//inject directives and services. 
var app = angular.module('fileUpload', ['ngFileUpload']); 
app.controller('MyCtrl', ['$scope', 'Upload', function ($scope, Upload) { 
    $scope.$watch('file', function (file) { 
        $scope.upload($scope.file); 
    }); 
/* optional: set default directive values */ 
//Upload.setDefaults( {ngf-keep:false ngf-accept:'image/*', ...} );
$scope.upload = function (file) { 
    Upload.upload(
        { 
            url: 'upload/url', 
            fields: {'username': $scope.username}, file: file 
        })
        .progress(function (evt) { 
            var progressPercentage = parseInt(100.0 * evt.loaded / evt.total); 
            console.log('progress: ' + progressPercentage + '% ' + evt.config.file.name); 
        })
        .success(function (data, status, headers, config) { 
            console.log('file ' + config.file.name + 'uploaded. Response: ' + data); 
        })
        .error(function (data, status, headers, config) { 
            console.log('error status: ' + status); 
        })
     }; 
}]);
```

涉及参数：

（1）文件选择：----适用于：

&lt;button&gt;、&lt;div&gt;、&lt;input　type=file&gt;

ngf-select=“true”：默认为true,使得在这个元素上可以选择文件

ng-model="myfiles"：将选择的一个或多个文件与scope模型绑定（通过ngf-multiple和ngf-keep的值可以使得ng-model的值为一个数组或单个文件）

ng-model-rejected="rejfiles"：与不匹配接受通配符的丢弃文件绑定

ng-disabled="selectDisabled"：与一个boolean值绑定，来决定是否禁用文件选择功能

ngf-change="fileSelected\($files, $file, $event, $rejectedFiles\)"：当文件被选择或移除时调用

ngf-multiple="true"：默认为false，为true时表示可以选择多个文件

ngf-capture="'camera'" or "'other'"：允许移动设备使用相机捕获

accept="image/\*"：标准的HTML文件接受的输入属性（依赖于浏览器）

ngf-accept=" 'image/\*' "：用逗号分隔允许的MIME类型来过滤文件

    ngf-validate="{
         size: {min: 10, max: '20MB'},
         width: {min: 100, max:10000}, 
         height: {min: 100, max: 300}, 
         duration: {min: '10s', max: '5m'}, 
         accept: '.jpg'
     }" //  或者 "validate($file)"
    　　　　```
    　　　　`ngf-keep="true" or "false"`：默认为false,保存着以前ng-model的值和追加的新文件
    　　　　`ngf-keep-distinct="true" or "false"`：默认为false，如果ngf-keep设置了的话，则删除重复的选定文件
    　　　　`ngf-reset-on-click="true" or "false"`：默认为true，可以重置模型和点击输入
    　　　　`ngf-reset-model-on-click="true" or "false"`：默认为true，单击时重置模型
    　　　(2)文件删除：----适用于：<button>、<div>
    >注：除了ngf-drop 、ng-model 、 ngf-change之一，其余属性都是可选的

    　　　　`ngf-drop="true" or "false" `：默认为true,使得在该元素上可以删除文件
    　　　　`ng-model="myFiles"`：把被删除的文件与scope模型绑定（通过ngf-multiple和ngf-keep的值可以使得ng-model的值为一个数组或单个文件）
    　　　　`ng-model-rejected="rejFiles"`：与不符合通配合而被删除的文件绑定
    　　　　`ng-disabled="dropDisabled"`：与一个boolean值绑定，来决定是否禁用文件删除功能
    　　　　`ngf-change="fileDropped($files, $file, $event, $rejectedFiles)" `：当文件被删除时调用
    　　　　`ngf-multiple="true" or "false"`：默认为false，为true时表示可以选择多个文件
    　　　　`ngf-accept="'.pdf,.jpg'"`：用逗号分隔允许的MIME类型来过滤文件
    ```javascript
    ngf-validate="{
         size: {min: 10, max: '20MB'},
         width: {min: 100, max:10000}, 
         height: {min: 100, max: 300}, 
         duration: {min: '10s', max: '5m'}, 
         accept: '.jpg'
     }" //  或者 "validate($file)"
    　　　　```
     　　　`ngf-allow-dir="true" or "false"`：默认为true，但只在goole浏览器下可以删除
    ```javascript

    ngf-drag-over-class="{
        accept:'acceptClass', 
        reject:'rejectClass', 
        delay:100
    }" //or "myDragOverClass" or "calcDragOverClass($event)" 
    //拖动css类行为：可以是一个字符串，也可以是一个函数（返回一个类名）或者是一个json对象{accept: 'c1', reject: 'c2', delay:10}，默认是 "dragover".  accept和reject只可以在chrome浏览器并且要通过ngf-accept的检测才可以工作，

`ngf-drop-available="dropSupported"`：设置scope模型的值为真或假（基于文件的拖拽和释放）  
`ngf-stop-propagation="true" or "false"`：默认为false，是否传播拖拽和释放事件  
`ngf-hide-on-drop-not-available="true" or "false"`：默认为false，当文件的拖拽和释放不被支持时隐藏元素

（3）文件预览-----适用于

&lt;img/&gt;、&lt;audio&gt;、&lt;video&gt;

ngf-src="file"：通过设置url，预览被选择的文件

ngf-background="file"：设置背景图片的样式

ngf-no-object-url="true or false"：默认为false，强制生成base64url而不是对象url



