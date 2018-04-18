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



