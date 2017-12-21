## 12.导入excel内容

就是excel上这样的内容

![](https://user-gold-cdn.xitu.io/2017/12/20/160716308aa34b59?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

转成下面的数据

![](https://user-gold-cdn.xitu.io/2017/12/20/160716308ee554b8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![](https://user-gold-cdn.xitu.io/2017/12/20/16071630906c2869?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

目录如下

![](https://user-gold-cdn.xitu.io/2017/12/20/16071630907cb32c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

下面开始写代码，我们利用node.js来写

```
let path = require('path');
//使用ejsexcel读取excel文件  npm install ejsexcel --save
let ejsExcel=require('ejsexcel');
let fs=require('fs');
//读取excel
let exBuf=fs.readFileSync(__dirname+'/resource/userList.xlsx');
let _data=[];
//获取成功后
ejsExcel.getExcelArr(exBuf).then(exlJson=>{
    //获取excel数据
    let workBook=exlJson;
    //获取excel第一张表 sheet1
    let workSheets=workBook[0];
    //导出js的路径
    let newfilepath=path.join(__dirname,"/resource/test.js");
    //遍历第一张表的的每一行数据
    workSheets.forEach((item,index)=>{
        //从第二行开始插入，避免连表头也插入_data里面
        if(index>0){
            //往_data插入单元格个值，item[0]相当于excel中的姓名，item[1]就是excel中的联系电话
            _data.push({
                name:item[0],
                phone:item[1]
            })
        }
    });
    //写入js文件
    fs.writeFileSync(newfilepath, 'let _data='+JSON.stringify(_data)+';export {_data}');
}).catch(error=>{
    //打印获取失败信息
    console.log("读取错误!");
    console.log(error);
});

```

然后命令行执行该js

```
$ node importFile.js

```

然后就发现多了一个test.js文件

![](https://user-gold-cdn.xitu.io/2017/12/20/16071630c7ba133d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

excel的数据就这样导入成js的一个数组了，只要引入这个数组，就可以正常的使用了！

