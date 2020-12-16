## loadsh

Lodash 是一个一致性、模块化、高性能的 JavaScript 实用工具库，它提供了大量的工具函数用来加强 JavaScrip t的语言功能，每个函数都是精心优化的，可以放心使用而不用关心性能、稳定性问题

官网：[https://www.lodashjs.com](https://www.lodashjs.com/)

**主要用于：**

- 遍历 array、object 和 string
- 对值进行操作和检测
- 创建符合功能的函数

**安装：**

```
npm install lodash -save
```

```js
const _ = require('lodash')
```



---

### 数组

**_.chunk(array, [size=1])**

将数组拆分成多个 `size` 长度的区块，并将这些区块组成一个新数组，如果`array` 无法被分割成全部等长的区块，那么最后剩余的元素将组成一个区块

```js
_.chunk(['a', 'b', 'c', 'd'], 2);		// [['a', 'b'], ['c', 'd']]
_.chunk(['a', 'b', 'c', 'd'], 3);		// [['a', 'b', 'c'], ['d']]
```

---

**_.compact(array)**

创建一个新数组，包含原数组中所有的非假值元素，`false`, `null`,`0`, `""`, `undefined`, 和 `NaN` 都是被认为是假值

```js
_.compact([0, 1, false, 2, '', 3]);		// [1, 2, 3]
```

---

**_.difference(array, [values])**

创建新数组，过滤掉原数组中含有条件数组中重复的值

```js
_.difference([3, 2, 1], [4, 2]);		// [3, 1]
```

---

**_.drop(array, [n=1])**

创建新数组，去除原数组的前n个元素

```js
_.drop([1, 2, 3]);		// [2, 3]
_.drop([1, 2, 3], 2);	// [3]
_.drop([1, 2, 3], 5);	// []
_.drop([1, 2, 3], 0);	// [1, 2, 3]
```

---

**_.dropRight(array, [n=1])**

创建新数组，去除原数组尾部的n个元素

```js
_.dropRight([1, 2, 3]);			// [1, 2]
_.dropRight([1, 2, 3], 2);		// [1]
_.dropRight([1, 2, 3], 5);		// []
_.dropRight([1, 2, 3], 0);		// [1, 2, 3]
```

---

**_.pull(array, [values])**

移除数组中所有和给定值相等的值

```js
var array = [1, 2, 3, 1, 2, 3, '1'];
 _.pull(array, 1, 3);
console.log(array);     // [2, 2, '1']
```

---

**_.pull(array, values)**

和 `._pull` 类似，接收的条件是一个数组

```js
var array = [1, 2, 3, 1, 2, 3];
 _.pullAll(array, [2, 3]);
console.log(array);		// [1, 1]
```

----

**_.remove(array, [predicate])**

移除数组中 `predicate`（断言）返回为真值的所有元素，并返回移除元素组成的数组。`predicate` 会传入3个参数：` (value, index, array)`

```js
var array = [1, 2, 3, 4];
var evens = _.remove(array, function(n) {
  return n % 2 == 0;
});
console.log(array);		// [1, 3]
console.log(evens);		// [2, 4]
```

---

**_.without(array, [values])**

创建一个新数组，过滤满足条件的值

```js
_.without([2, 1, 2, 3], 1, 2);		// [3]
```

---

**_.flattenDepth(array, [depth=1])**

根据 `depth` 递归减少 `array` 的嵌套层级

```js
var array = [1, [2, [3, [4]], 5]];
 _.flattenDepth(array, 1);		// [1, 2, [3, [4]], 5]
 _.flattenDepth(array, 2);		// [1, 2, 3, [4], 5]
```

---

**_.flattenDeep(array)**

将 `array`递归为一维数组

```js
_.flattenDeep([1, [2, [3, [4]], 5]]);	// [1, 2, 3, 4, 5]
```

