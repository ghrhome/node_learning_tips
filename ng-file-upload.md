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



