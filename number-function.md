# Number Function

```
 Number.prototype.formatMoney = function(c, d, t){
        var n = this,
            c = isNaN(c = Math.abs(c)) ? 2 : c,
            d = d == undefined ? "." : d,
            t = t == undefined ? "," : t,
            s = n < 0 ? "-" : "",
            i = String(parseInt(n = Math.abs(Number(n) || 0).toFixed(c))),
            j = (j = i.length) > 3 ? j % 3 : 0;
        return s + (j ? i.substr(0, j) + t : "") + i.substr(j).replace(/(\d{3})(?=\d)/g, "$1" + t) + (c ? d + Math.abs(n - i).toFixed(c).slice(2) : "");
    };

  /*  console.log((123456789.12345).formatMoney(2, '.', ','));
    console.log((123456789).formatMoney(2, '.', ','));*/
```

```
 //检查非负浮点数
    function checkNum(num){
        var patt=/^\d+(\.\d+)?$/g;
        return patt.test(num);
    }

    turnover.numberFormat=function(num){
        return parseFloat(num).formatMoney(2, '.', ',');
    };
```



