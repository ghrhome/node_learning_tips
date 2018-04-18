# angular ngrepeat index

```
<li ng-repeat="thing in things" ng-init="parentIndex = $index">
    {{ $index }}
    <ul>
        <li ng-repeat="value in thing.values">
            {{ value }} 
            {{ $index }} <!-- inner $index -->
            {{ $parent.$index }} <!-- parent $index -->
            {{ parentIndex }} <!-- also parent $index -->
        </li>
    </ul>
</li>
```

```
  $scope.$on("initSwiper",function() {
                        _initSwiper();
                        //页面事件
                        //这里暂时先禁掉 table的 tab键
                        document.onkeydown = function(){
                            if(event.keyCode == 13||event.keyCode == 9) {
                                return false;
                            }
                        };
                        $(".table").find("input").attr("tabIndex","-1");

                    });
```



