# handlebars-----由于if的条件不足引出的helper

```
<!DOCTYPE html>
<html>
  <head>
    <META http-equiv=Content-Type content="text/html; charset=utf-8">
    <title>由于if功力不足引出的Helper - by 杨元</title>
  </head>
  <body>
    <h1>由于if功力不足引出的Helper</h1>
    <!--基础html框架-->
    <table>
      <thead>
        <tr>
          <th>姓名</th>
          <th>性别</th>
          <th>年龄</th>
        </tr>
      </thead>
      <tbody id="tableList">

      </tbody>
    </table>

    <!--插件引用-->
    <script type="text/javascript" src="script/jquery.js"></script>
    <script type="text/javascript" src="script/handlebars-1.0.0.beta.6.js"></script>

    <!--Handlebars.js模版-->
    <!--Handlebars.js模版放在script标签中，保留了html原有层次结构,模版中要写一些操作语句-->
    <!--id可以用来唯一确定一个模版,type是模版固定的写法-->
    <script id="table-template" type="text/x-handlebars-template">
      {{#each student}}
        {{#if name}}
          {{#compare age 20}}
            <tr>
              <td>{{name}}</td>
              <td>{{sex}}</td>
              <td>{{age}}</td>
            </tr>
          {{else}}
            <tr>
              <td>?</td>
              <td>?</td>
              <td>?</td>
            </tr>
          {{/compare}}
        {{/if}}
      {{/each}}
    </script>

    <!--进行数据处理、html构造-->
    <script type="text/javascript">
      $(document).ready(function() {
        //模拟的json对象
        var data = {
                    "student": [
                        {
                            "name": "张三",
                            "sex": "0",
                            "age": 23
                        },
                        {
                            "sex": "0",
                            "age": 22
                        },
                        {
                            "name": "妞妞",
                            "sex": "1",
                            "age": 18
                        }
                    ]
                };

        //注册一个Handlebars模版，通过id找到某一个模版，获取模版的html框架
        //$("#table-template").html()是jquery的语法，不懂的童鞋请恶补。。。
        var myTemplate = Handlebars.compile($("#table-template").html());

        //注册一个比较大小的Helper,判断v1是否大于v2
        Handlebars.registerHelper("compare",function(v1,v2,options){
          if(v1>v2){
            //满足添加继续执行
            return options.fn(this);
          }else{
            //不满足条件执行{{else}}部分
            return options.inverse(this);
          }
        });

        //将json对象用刚刚注册的Handlebars模版封装，得到最终的html，插入到基础table中。
        $('#tableList').html(myTemplate(data));
      });
    </script>
  </body>
</html>
```

> ```
> 注：Handlebars.registerHelper用来定义Helper，它有两个参数，第一个参数是Helper名称，第二个参数是一个回调函数，
> 用来执行核心业务逻辑。本例中的函数，有三个参数，其中前两个参数是需要比较的两个数，第三个参数是固定的，就叫options，
> 如果加了该参数，就说明这个Helper是一个Block，块级别的Helper，有一定的语法结构，调用的时候加#号，就像if那样。
> ```
>
> ```
> 关于options的使用，小菜所了解的就是这种用法，return
> options.fn(this);表示满足条件继续执行，也就是执行{{#compare
> }}和{{else}}之间的代码；return
> options.inverse(this);表示不满足条件，也就是执行{{else}}和{{/compare}}之间的代码。
> ```
>
>  



