## 1.前言

大家在开发的时候应该知道，有很多常见的实例操作。比如数组去重，关键词高亮，打乱数组等。这些操作，代码一般不会很多，实现的逻辑也不会很难，下面的代码，我解释就不解释太多了，打上注释，相信大家就会懂了。但是，用的地方会比较，如果项目有哪个地方需要用，如果重复写的话，就是代码沉余，开发效率也不用，复用基本就是复制粘贴！这样是一个很不好的习惯，大家可以考虑一下把一些常见的操作封装成函数，调用的时候，直接调用就好！  
源码都放在github上了，大家想以后以后有什么修改或者增加的，欢迎大家来star一下[ec-do](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FchenhuiYj%2Fec-do)。

> 1.下面代码，我放的是es5版本的，如果大家需要看es6版本的，请移步[ec-do2.0.0.js](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2FchenhuiYj%2Fec-do%2Fblob%2Fmaster%2Fsrc%2Fec-do-2.0.0.js)
>
> 2.想看完整代码的，或者部分实例的demo，建议去github看！
>
> 3.下面的代码，都是封装在ecDo这个对象里面，如果里面有this，除了特别说明的，都是指向ecDo

## 2.字符串操作

### 2-1去除字符串空格

```
//去除空格  type 1-所有空格  2-前后空格  3-前空格 4-后空格
//ecDo.trim('  1235asd',1)
//result：1235asd
//这个方法有原生的方案代替，但是考虑到有时候开发PC站需要兼容IE8，所以就还是继续保留
trim: function (str, type) {
    switch (type) {
        case 1:
            return str.replace(/\s+/g, "");
        case 2:
            return str.replace(/(^\s*)|(\s*$)/g, "");
        case 3:
            return str.replace(/(^\s*)/g, "");
        case 4:
            return str.replace(/(\s*$)/g, "");
        default:
            return str;
    }
}
```

### 2-2字母大小写切换

```
/*type
 1:首字母大写
 2：首页母小写
 3：大小写转换
 4：全部大写
 5：全部小写
 * */
//ecDo.changeCase('asdasd',1)
//result：Asdasd
changeCase: function (str, type) {
    function ToggleCase(str) {
        var itemText = ""
        str.split("").forEach(
            function (item) {
                if (/^([a-z]+)/.test(item)) {
                    itemText += item.toUpperCase();
                } else if (/^([A-Z]+)/.test(item)) {
                    itemText += item.toLowerCase();
                } else {
                    itemText += item;
                }
            });
        return itemText;
    }
    switch (type) {
        case 1:
            return str.replace(/\b\w+\b/g, function (word) {
                return word.substring(0, 1).toUpperCase() + word.substring(1).toLowerCase();
            });
        case 2:
            return str.replace(/\b\w+\b/g, function (word) {
                return word.substring(0, 1).toLowerCase() + word.substring(1).toUpperCase();
            });
        case 3:
            return ToggleCase(str);
        case 4:
            return str.toUpperCase();
        case 5:
            return str.toLowerCase();
        default:
            return str;
    }
}
```

### 2-3字符串循环复制

```
//repeatStr(str->字符串, count->次数)
//ecDo.repeatStr('123',3)
//"result：123123123"
repeatStr: function (str, count) {
    var text = '';
    for (var i = 0; i < count; i++) {
        text += str;
    }
    return text;
}
```

### 2-4字符串替换

```
//ecDo.replaceAll('这里是上海，中国第三大城市，广东省省会，简称穗，','上海','广州')
//result："这里是广州，中国第三大城市，广东省省会，简称穗，"
replaceAll: function (str, AFindText, ARepText) {
    raRegExp = new RegExp(AFindText, "g");
    return str.replace(raRegExp, ARepText);
}
```

### 2-5替换\*

```
//字符替换*
//replaceStr(字符串,字符格式, 替换方式,替换的字符（默认*）)
//ecDo.replaceStr('18819322663',[3,5,3],0)
//result：188*****663
//ecDo.replaceStr('asdasdasdaa',[3,5,3],1)
//result：***asdas***
//ecDo.replaceStr('1asd88465asdwqe3',[5],0)
//result：*****8465asdwqe3
//ecDo.replaceStr('1asd88465asdwqe3',[5],1,'+')
//result："1asd88465as+++++"
replaceStr: function (str, regArr, type, ARepText) {
    var regtext = '',
        Reg = null,
        replaceText = ARepText || '*';
    //repeatStr是在上面定义过的（字符串循环复制），大家注意哦
    if (regArr.length === 3 && type === 0) {
        regtext = '(\\w{' + regArr[0] + '})\\w{' + regArr[1] + '}(\\w{' + regArr[2] + '})'
        Reg = new RegExp(regtext);
        var replaceCount = this.repeatStr(replaceText, regArr[1]);
        return str.replace(Reg, '$1' + replaceCount + '$2')
    }
    else if (regArr.length === 3 && type === 1) {
        regtext = '\\w{' + regArr[0] + '}(\\w{' + regArr[1] + '})\\w{' + regArr[2] + '}'
        Reg = new RegExp(regtext);
        var replaceCount1 = this.repeatStr(replaceText, regArr[0]);
        var replaceCount2 = this.repeatStr(replaceText, regArr[2]);
        return str.replace(Reg, replaceCount1 + '$1' + replaceCount2)
    }
    else if (regArr.length === 1 && type === 0) {
        regtext = '(^\\w{' + regArr[0] + '})'
        Reg = new RegExp(regtext);
        var replaceCount = this.repeatStr(replaceText, regArr[0]);
        return str.replace(Reg, replaceCount)
    }
    else if (regArr.length === 1 && type === 1) {
        regtext = '(\\w{' + regArr[0] + '}$)'
        Reg = new RegExp(regtext);
        var replaceCount = this.repeatStr(replaceText, regArr[0]);
        return str.replace(Reg, replaceCount)
    }
}
```

### 2-6检测字符串

```
//检测字符串
//ecDo.checkType('165226226326','phone')
//result：false
//大家可以根据需要扩展
checkType: function (str, type) {
    switch (type) {
        case 'email':
            return /^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$/.test(str);
        case 'phone':
            return /^1[3|4|5|7|8][0-9]{9}$/.test(str);
        case 'tel':
            return /^(0\d{2,3}-\d{7,8})(-\d{1,4})?$/.test(str);
        case 'number':
            return /^[0-9]$/.test(str);
        case 'english':
            return /^[a-zA-Z]+$/.test(str);
        case 'text':
            return /^\w+$/.test(str);
        case 'chinese':
            return /^[\u4E00-\u9FA5]+$/.test(str);
        case 'lower':
            return /^[a-z]+$/.test(str);
        case 'upper':
            return /^[A-Z]+$/.test(str);
        default:
            return true;
    }
}
```

### 2-7 检测密码强度

```
//ecDo.checkPwd('12asdASAD')
//result：3(强度等级为3)
checkPwd: function (str) {
    var nowLv = 0;
    if (str.length < 6) {
        return nowLv
    }
    if (/[0-9]/.test(str)) {
        nowLv++
    }
    if (/[a-z]/.test(str)) {
        nowLv++
    }
    if (/[A-Z]/.test(str)) {
        nowLv++
    }
    if (/[\.|-|_]/.test(str)) {
        nowLv++
    }
    return nowLv;
}
```

### 2-8随机码（toString详解）

```
//count取值范围0-36
//ecDo.randomWord(10)
//result："2584316588472575"
//ecDo.randomWord(14)
//result："9b405070dd00122640c192caab84537"
//ecDo.randomWord(36)
//result："83vhdx10rmjkyb9"
randomWord: function (count) {
    return Math.random().toString(count).substring(2);
}
```

### 2-9查找字符串

可能标题会有点误导，下面我就简单说明一个需求，在字符串`'sad44654blog5a1sd67as9dablog4s5d16zxc4sdweasjkblogwqepaskdkblogahseiuadbhjcibloguyeajzxkcabloguyiwezxc967'`中找出'blog'的出现次数。代码如下

```
//var strTest='sad44654blog5a1sd67as9dablog4s5d16zxc4sdweasjkblogwqepaskdkblogahseiuadbhjcibloguyeajzxkcabloguyiwezxc967'
//ecDo.countStr(strTest,'blog')
//result：6
countStr: function (str, strSplit) {
    return str.split(strSplit).length - 1
}
```

### 2-10 过滤字符串 

```
//过滤字符串(html标签，表情，特殊字符)
//字符串，替换内容（special-特殊字符,html-html标签,emjoy-emjoy表情,word-小写字母，WORD-大写字母，number-数字,chinese-中文），要替换成什么，默认'',保留哪些特殊字符
//如果需要过滤多种字符，type参数使用,分割，如下栗子
//过滤字符串的html标签，大写字母，中文，特殊字符，全部替换成*,但是特殊字符'%'，'?'，除了这两个，其他特殊字符全部清除
//var str='asd    654a大蠢sasdasdASDQWEXZC6d5#%^*^&*^%^&*$\\"\'#@!()*/-())_\'":"{}?<div></div><img src=""/>啊实打实大蠢猪自行车这些课程';
// ecDo.filterStr(str,'html,WORD,chinese,special','*','%?')
//result："asd    654a**sasdasd*********6d5#%^*^&*^%^&*$\"'#@!()*/-())_'":"{}?*****************"
filterStr: function (str, type, restr, spstr) {
    var typeArr = type.split(','), _str = str;
    for (var i = 0, len = typeArr.length; i < len; i++) {
        //是否是过滤特殊符号
        if (typeArr[i] === 'special') {
            var pattern, regText = '$()[]{}?\|^*+./\"\'+';
            //是否有哪些特殊符号需要保留
            if (spstr) {
                var _spstr = spstr.split(""), _regText = "[^0-9A-Za-z\\s";
                for (var j = 0, len1 = _spstr.length; j < len1; j++) {
                    if (regText.indexOf(_spstr[j]) === -1) {
                        _regText += _spstr[j];
                    }
                    else {
                        _regText += '\\' + _spstr[j];
                    }
                }
                _regText += ']'
                pattern = new RegExp(_regText, 'g');
            }
            else {
                pattern = new RegExp("[^0-9A-Za-z\\s]", 'g')
            }
        }
        var _restr = restr || '';
        switch (typeArr[i]) {
            case 'special':
                _str = _str.replace(pattern, _restr);
                break;
            case 'html':
                _str = _str.replace(/<\/?[^>]*>/g, _restr);
                break;
            case 'emjoy':
                _str = _str.replace(/[^\u4e00-\u9fa5|\u0000-\u00ff|\u3002|\uFF1F|\uFF01|\uff0c|\u3001|\uff1b|\uff1a|\u3008-\u300f|\u2018|\u2019|\u201c|\u201d|\uff08|\uff09|\u2014|\u2026|\u2013|\uff0e]/g, _restr);
                break;
            case 'word':
                _str = _str.replace(/[a-z]/g, _restr);
                break;
            case 'WORD':
                _str = _str.replace(/[A-Z]/g, _restr);
                break;
            case 'number':
                _str = _str.replace(/[0-9]/g, _restr);
                break;
            case 'chinese':
                _str = _str.replace(/[\u4E00-\u9FA5]/g, _restr);
                break;
        }
    }
    return _str;
}
```

### 2-11格式化处理字符串

```
//ecDo.formatText('1234asda567asd890')
//result："12,34a,sda,567,asd,890"
//ecDo.formatText('1234asda567asd890',4,' ')
//result："1 234a sda5 67as d890"
//ecDo.formatText('1234asda567asd890',4,'-')
//result："1-234a-sda5-67as-d890"
formatText: function (str, size, delimiter) {
    var _size = size || 3, _delimiter = delimiter || ',';
    var regText = '\\B(?=(\\w{' + _size + '})+(?!\\w))';
    var reg = new RegExp(regText, 'g');
    return str.replace(reg, _delimiter);
}
```

### 2-12找出最长单词

```
//ecDo.longestWord('Find the Longest word in a String')
//result：7
//ecDo.longestWord('Find|the|Longest|word|in|a|String','|')
//result：7
longestWord: function (str, splitType) {
    var _splitType = splitType || /\s+/g,
        _max = 0,_item='';
    var strArr = str.split(_splitType);
    strArr.forEach(function (item) {
        if (_max < item.length) {
            _max = item.length
            _item=item;
        }
    })
    return {el:_item,max:_max};
}
```

### 2-13句中单词首字母大写 

```
/这个我也一直在纠结，英文标题，即使是首字母大写，也未必每一个单词的首字母都是大写的，但是又不知道哪些应该大写，哪些不应该大写
//ecDo.titleCaseUp('this is a title')
//"This Is A Title"
titleCaseUp: function (str, splitType) {
    var _splitType = splitType || /\s+/g;
    var strArr = str.split(_splitType),
        result = "", _this = this
    strArr.forEach(function (item) {
        result += _this.changeCase(item, 1) + ' ';
    })
    return this.trim(result, 4)
}  

```



