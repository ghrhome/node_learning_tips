# URL.createObjectURL和URL.revokeObjectURL {#Htitle}

**一.URL.createObjectURL**

URL.createObjectURL\(\)方法会根据传入的参数创建一个指向该参数对象的URL. 这个URL的生命仅存在于它被创建的这个文档里. 新的对象URL指向执行的File对象或者是Blob对象.

语法: 

```
objectURL = URL.createObjectURL(blob || file);
```

参数:

File对象或者Blob对象

这里大概说下File对象和Blob对象:

File对象,就是一个文件,比如我用input type="file"标签来上传文件,那么里面的每个文件都是一个File对象.

Blob对象,就是二进制数据,比如通过new Blob\(\)创建的对象就是Blob对象.又比如,在XMLHttpRequest里,如果指定responseType为blob,那么得到的返回值也是一个blob对象.

注意点:

每次调用createObjectURL的时候,一个新的URL对象就被创建了.即使你已经为同一个文件创建过一个URL. 如果你不再需要这个对象,要释放它,需要使用URL.revokeObjectURL\(\)方法. 当页面被关闭,浏览器会自动释放它,但是为了最佳性能和内存使用,当确保不再用得到它的时候,就应该释放它.



**二.URL.revokeObjectURL**

URL.revokeObjectURL\(\)方法会释放一个通过URL.createObjectURL\(\)创建的对象URL. 当你要已经用过了这个对象URL,然后要让浏览器知道这个URL已经不再需要指向对应的文件的时候,就需要调用这个方法.

具体的意思就是说,一个对象URL,使用这个url是可以访问到指定的文件的,但是我可能只需要访问一次,一旦已经访问到了,这个对象URL就不再需要了,就被释放掉,被释放掉以后,这个对象URL就不再指向指定的文件了.

比如一张图片,我创建了一个对象URL,然后通过这个对象URL,我页面里加载了这张图.既然已经被加载,并且不需要再次加载这张图,那我就把这个对象URL释放,然后这个URL就不再指向这张图了.

语法:

```
window.URL.revokeObjectURL(objectURL);
```

参数:

objectURL 是一个通过URL.createObjectURL\(\)方法创建的对象URL.

最后,给个综合栗子:

通过ajax获取一张图片,显示在页面里.

html:

```
<body>
    <button id="getPic">获取图片的Blob数据</button>
</body>
```

js:

```
//获取图片Blob数据
      document.getElementById(‘getPic‘).onclick = function(e){
        $.ajax({
          type:‘GET‘,
          url:‘img.png‘,
          resDataType:‘blob‘,
          imgType:‘png‘,
          success:function(resText,resXML){
            var img = document.createElement(‘img‘);
            var objectUrl = window.URL.createObjectURL(resText);
            img.src = objectUrl;
            img.onload = function(){
              window.URL.revokeObjectURL(objectUrl);
            };
            document.body.appendChild(img);
          },
          fail:function(err){
            console.log(err)
          }
        });
        e.preventDefault();
      }
```

指定返回的数据格式为blob二进制数据.

通过返回的图片二进制数据来创建一个对象URL.

当图片加载完成后释放对象URL.

```
var $={};
$.ajax = function(options){
    //1.获取参数
    var type = options.type.toUpperCase() || ‘GET‘;
    var resDataType = options.resDataType || ‘string‘;
    var reqDataType = options.reqDataType || ‘string‘;
    var url = options.url;
    var data = options.data;
    var success = options.success;
    var fail = options.fail;
    var progress = options.progress;
    var imgType = options.imgType || ‘jpg‘;
    
    //2.获取xhr对象
    var xhr = $.getXhr();

    //3.建立连接
    xhr.open(type,url);
    /*指定返回数据的格式需要在发送请求之前*/
    if(resDataType===‘blob‘){
        xhr.responseType = ‘blob‘;
    }
    
    //4.发送请求
    if(type===‘GET‘){
        xhr.send(null)
    }
    else if(type===‘POST‘) {
        if(progress){
            xhr.upload.onprogress = progress;
        }
        if(reqDataType===‘json‘){
            xhr.setRequestHeader(‘Content-Type‘,‘application/json;charset=UTF-8‘);
            data = JSON.stringify(data);  //只能发送字符串格式的json,不能直接发送json
        }
        if(reqDataType===‘string‘){
            xhr.setRequestHeader(‘Content-Type‘,‘application/x-www-form-urlencoded‘);
        }
        xhr.send(data);
    }

    //5.接收数据
    xhr.onreadystatechange = function(){
        if(this.readyState===4 && (this.status>=200 && this.status<300)){
            var res;
            if(resDataType===‘json‘){
                res = JSON.parse(this.responseText);
                success.call(this,res,this.responseXML)
            }
            if(resDataType===‘blob‘){
                res = new Blob([this.response],{type:‘image/‘+imgType});
                success.call(this,res)
            }

        }
    };
};
```

指定响应的格式是二进制数据.

使用xhr.response来获取响应的二进制数据,而不是xhr.responseText. 当定义了xhr.responseType=‘blob‘以后,xhr就没有responseText属性了.

这里虽然使用new Blob\(\),但其实不用它,直接返回xhr.response,一样是正确的.



