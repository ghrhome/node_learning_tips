# SVG-classList 低版本浏览器不支持情况改进

```
/*/* 兼容IE9 ---------------------------------------- classList start ---------------------------------------- */

// IE下各种类型的图形注册之后才能点击有效
var arrays = [SVGPolygonElement, SVGEllipseElement, SVGPathElement, SVGRectElement, SVGCircleElement, SVGGElement ];

$.each(arrays,function(i,e){
    if (!("classList" in e.prototype)){
        Object.defineProperty(e.prototype, 'classList', {
            get: function() {
                var self = this;
                function update(fn) {
                    return function(value) {
                        var classes = self.className.baseVal.split(/\s+/g),
                            index = classes.indexOf(value);

                        fn(classes, index, value);
                        self.className.baseVal = classes.join(" ");
                    }
                }

                return {
                    add: update(function(classes, index, value) {
                        if (!~index) classes.push(value);
                    }),

                    remove: update(function(classes, index) {
                        if (~index) classes.splice(index, 1);
                    }),

                    toggle: update(function(classes, index, value) {
                        if (~index)
                            classes.splice(index, 1);
                        else
                            classes.push(value);
                    }),

                    contains: function(value) {
                        var className = self.className;
                        return !!~+className.baseVal.split(/\s+/g).indexOf(value);
                    },

                    item: function(i) {
                        return self.className.baseVal.split(/\s+/g)[i] || null;
                    }
                };
            }
        });
    }
});


/*if (!("classList" in SVGElement.prototype)){
    Object.defineProperty(SVGElement.prototype, 'classList', {
        get: function() {
            var self = this;
            function update(fn) {
                return function(value) {
                    var classes = self.className.baseVal.split(/\s+/g),
                        index = classes.indexOf(value);

                    fn(classes, index, value);
                    self.className.baseVal = classes.join(" ");
                }
            }

            return {
                add: update(function(classes, index, value) {
                    if (!~index) classes.push(value);
                }),

                remove: update(function(classes, index) {
                    if (~index) classes.splice(index, 1);
                }),

                toggle: update(function(classes, index, value) {
                    if (~index)
                        classes.splice(index, 1);
                    else
                        classes.push(value);
                }),

                contains: function(value) {
                    var className = self.className;
                    return !!~+className.baseVal.split(/\s+/g).indexOf(value);
                },

                item: function(i) {
                    return self.className.baseVal.split(/\s+/g)[i] || null;
                }
            };
        }
    });
}*/
```



