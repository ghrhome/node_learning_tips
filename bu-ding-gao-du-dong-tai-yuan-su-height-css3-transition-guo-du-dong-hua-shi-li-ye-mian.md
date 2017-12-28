# 不定高度动态元素height CSS3 transition过渡动画实例页面

```
.box { width: 400px; padding: 20px; border: 40px solid #a0b3d6; background-color: #eee; overflow: hidden; }
.loading { height: 100%; background: url(loading.gif) no-repeat center; }
.in { width: 100px; margin: 0 auto;  border: 50px solid #beceeb; background-color: #f0f3f9; }
[type=button] { width: 100px; height: 32px; font-size: 100%; }
```

```
<div id="box" class="box">...</div>
<p>
    <input type="button" id="button" value="点击我">
</p>
```

```
// 高度无缝动画方法
var funTransitionHeight = function(element, time) { // time, 数值，可缺省
    if (typeof window.getComputedStyle == "undefined") return;

    var height = window.getComputedStyle(element).height;

    element.style.transition = "none";    // 本行2015-05-20新增，mac Safari下，貌似auto也会触发transition, 故要none下~

    element.style.height = "auto";
    var targetHeight = window.getComputedStyle(element).height;
    element.style.height = height;
    element.offsetWidth = element.offsetWidth;
    if (time) element.style.transition = "height "+ time +"ms";
    element.style.height = targetHeight;
};
```

/\*补充bootstrap collapse写法\*/ dimension==width \| height

```
this.$element
      .removeClass('collapse')
      .addClass('collapsing')[dimension](0)
      .attr('aria-expanded', true)
```

```
this.transitioning = 1

var complete = function () {
      this.$element
        .removeClass('collapsing')
        .addClass('collapse in')[dimension]('')
      this.transitioning = 0
      this.$element
        .trigger('shown.bs.collapse')
}

if (!$.support.transition) return complete.call(this)

var scrollSize = $.camelCase(['scroll', dimension].join('-'))

this.$element
      .one('bsTransitionEnd', $.proxy(complete, this))
      .emulateTransitionEnd(Collapse.TRANSITION_DURATION)[dimension](this.$element[0][scrollSize])
```

# 实验写法

```
<style>
        #animate-test{}
        #animate-test ul{
            visibility: hidden;
        }
        #animate-test .collapse{display: none;visibility: inherit}
        #animate-test .collapse.in{display: block;visibility: inherit}
        #animate-test .collapsing {
            position: relative;
            height: 0;
            overflow: hidden;
            visibility: inherit;
            -webkit-transition-property: height, visibility;
            transition-property: height, visibility;
            -webkit-transition-duration: 0.35s;
            transition-duration: 0.35s;
            -webkit-transition-timing-function: ease;
            transition-timing-function: ease;
        }
    </style>
```

```
 <div id="animate-test">
                        <a class="btn btn-default ys-btn" id="js-show-menu">.....</a>
                        <ul class="animated collapse">
                            <li>1</li>
                            <li>2</li>
                        </ul>
                    </div>
```

```
 $("#js-show-menu").on("click",function(){
                var $elem=$(this).next("ul")
                if($elem.hasClass("collapsing")){
                    return;
                }
                if($elem.hasClass("in")){
                    $elem.removeClass("collapse in");
                    var _height=$elem.css("height");
                    $elem.height(_height);
                     var _targetHeight=0;
                     $elem.addClass("collapsing").height(_targetHeight);

                    $elem.one("transitionend",function(){
                           $elem.removeClass("collapsing").addClass("collapse").height("");

                    });

                }else{
                    $elem.removeClass("collapse in");
                    var _targetHeight=$elem.css("height");
                    var _height=0;

                    $elem.height(_height);

                    $elem.addClass("collapsing").height(_targetHeight);

                    $elem.one("transitionend",function(){
                        $elem.removeClass("collapsing").addClass("collapse in").height("");

                    });
                }




            })
```



