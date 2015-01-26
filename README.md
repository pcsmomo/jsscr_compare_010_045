# Collections
## reduce(Alias : inject, foldl)
### 리스트로부터의 하나의 결과를 작성한다.
### 사용 가능한 경우, ECMAScript5 네이티브`reduce`에 위임한다.
#### (0.2.0 이름 변경됨) 

    Example)
    var sum = _.reduce([1, 2, 3], 0, function(memo, num){ return memo + num });
    => 6

```javascript
    // Source
    _.reduce = function(obj, memo, iterator, context) {
      if (obj && obj.reduce) return obj.reduce(_.bind(iterator, context), memo);
      _.each(obj, function(value, index, list) {
        memo = iterator.call(context, memo, value, index, list);
      });
      return memo;
    };
```

[reduce 메서드][c]
[c]: https://msdn.microsoft.com/ko-kr/library/ie/ff679975(v=vs.94).aspx


## reduceRight(Alias : foldr)
### reduce와 동일한 기능을 한다. 하지만 리스트의 오른쪽 값부터 적용한다.
#### (0.3.3 추가됨) 
    
    Example)
    var list = [[0, 1], [2, 3], [4, 5]];
    var flat = _.reduceRight(list, [], function(a, b) { return a.concat(b); });
    => [4, 5, 2, 3, 0, 1]
    
```javascript
    // Source
    _.reduceRight = function(obj, memo, iterator, context) {
      if (obj && obj.reduceRight) return obj.reduceRight(_.bind(iterator, context), memo);
      var reversed = _.clone(_.toArray(obj)).reverse();
      _.each(reversed, function(value, index) {
        memo = iterator.call(context, memo, value, index, obj);
      });
      return memo;
    };
```

## sortBy
### iterator가 제공하는 기준에 따라 객체의 값을 정렬한다.

    Example)
    _.sortBy([1, 2, 3, 4, 5, 6], function(num){ return Math.sin(num); });
    => [5, 4, 6, 3, 1, 2]
    
```javascript
    // Source
    _.sortBy = function(obj, iterator, context) {
      return _.pluck(_.map(obj, function(value, index, list) {
        return {
          value : value,
          criteria : iterator.call(context, value, index, list)
        };
      }).sort(function(left, right) {
        var a = left.criteria, b = right.criteria;
        return a < b ? -1 : a > b ? 1 : 0;
      }), 'value');
    };
```

## sortedIndex
### iterator가순서를 유지하기 위해 객체의 삽입되어야하는 최소의 인덱스를 찾아내는 비교함수를 사용한다.

    Example)
    _.sortedIndex([10, 20, 30, 40, 50], 35);
    => 3
    
```javascript
    // Source
    _.sortedIndex = function(array, obj, iterator) {
      iterator = iterator || _.identity;
      var low = 0, high = array.length;
      while (low < high) {
        var mid = (low + high) >> 1;
        iterator(array[mid]) < iterator(obj) ? low = mid + 1 : high = mid;
      }
      return low;
    };
```

## toArray
### 안전하게 배열로 변환하여 모든 것을 iterable 하게 한다.

    Example)
    (function(){ return _.toArray(arguments).slice(0); })(1, 2, 3);
    => [1, 2, 3]
    
```javascript
    // Source
    _.toArray = function(iterable) {
      if (!iterable) return [];         // falsy 값이면 빈객체 반환
      if (_.isArray(iterable)) return iterable;     // 이미 배열이면 그대로 반환
      return _.map(iterable, function(val){ return val; });     // 유사배열객체일 경우 _.map 을 이용하여 배열로 변환하여 반환
    };
```

## size
### 안전하게 객체의 요소 수를 돌려 준다.

변경점 :  

    Example)
    _.size({one : 1, two : 2, three : 3});
    => 3
    
```javascript
    // Source
    _.size = function(obj) {
      return _.toArray(obj).length;
    };
```


# Arrays 
## first(Alias : head)
### 배열의 첫 번째 요소를 반환한다.

변경점 :  n 옵션 추가

    Example)
    _.first([5, 4, 3, 2, 1]);
    => 5
    _.first([5, 4, 3, 2, 1], 3);
    => [5, 4, 3]
    
```javascript
    // Source
    _.first = function(array, n) {
      return n ? Array.prototype.slice.call(array, 0, n) : array[0];
    };
```

## rest(Alias : tail)
### 배열의 첫 번째 항목을 제외한 모든 것을 반환한다.
#### (0.4.5 추가됨) 

    Example)
    _.rest([5, 4, 3, 2, 1]);
    => [4, 3, 2, 1]
    _.rest([5, 4, 3, 2, 1], 3);
    => [2, 1]
    
```javascript
    // Source
    _.rest = function(array, index) {
      return Array.prototype.slice.call(array, _.isUndefined(index) ? 1 : index);
    };
```

## intersect
### 전달 된 모든 배열 사이에서 공통되는 항목을 포함하는 배열을 제공한다.

    Example)
    _.intersect([1, 2, 3], [101, 2, 1, 10], [2, 1]);
    => [1, 2]
    
```javascript
    // Source
    _.intersect = function(array) {
      var rest = _.rest(arguments);
      return _.select(_.uniq(array), function(item) {
        return _.all(rest, function(other) {
          return _.indexOf(other, item) >= 0;
        });
      });
    };
```

## zip
### 단일 배열에 여러 목록을 함께 압축한다. 인텍스를 공유하는 요소가 함께 간다.

    Example)
    _.zip(['moe', 'larry', 'curly'], [30, 40, 50], [true, false, false]);
    => [["moe", 30, true], ["larry", 40, false], ["curly", 50, false]]
    
```javascript
    // Source
    _.zip = function() {
      var args = _.toArray(arguments);
      var length = _.max(_.pluck(args, 'length'));
      var results = new Array(length);
      for (var i=0; i<length; i++) results[i] = _.pluck(args, String(i));
      return results;
    };
```

## indexOf
### 배열 안에서 item이 최초로 출현하는 위치를 반환한다. 배열에 item이 포함되어 있지 않은 경우는 -1을 돌려 준다.

    Example)
    _.indexOf([1, 2, 3], 2);
    => 1
    
```javascript
    // Source
    _.indexOf = function(array, item) {
      if (array.indexOf) return array.indexOf(item);
      for (var i=0, l=array.length; i<l; i++) if (array[i] === item) return i;
      return -1;
    };
```

## lastIndexOf
### 배열 안에서 item이 마지막으로 출현하는 위치를 반환한다.
#### (0.2.0 추가됨) 

    Example)
    _.lastIndexOf([1, 2, 3, 1, 2, 3], 2);
    => 4
    
```javascript
    // Source
    _.lastIndexOf = function(array, item) {
      if (array.lastIndexOf) return array.lastIndexOf(item);
      var i = array.length;
      while (i--) if (array[i] === item) return i;
      return -1;
    };
```







참고자료
https://cdn.rawgit.com/jashkenas/underscore/0.4.5/index.html
http://underscorejs.org/
https://github.com/tipjs/Underscore.js-kr/blob/master/underscore.js
https://msdn.microsoft.com/ko-kr/library/ie/ff679975(v=vs.94).aspx
