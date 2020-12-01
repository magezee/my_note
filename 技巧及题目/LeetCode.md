### 两数之和

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍

```js
twoSum([2,7,11,15],18)	// [1,2]
twoSum([3,3,3],6)		// [0,1]
```

思路：

- 既然有和关系，就要用 target 和当前值拿到另外一个值，那当然是 `targer - 当前值`
- 判断另外一个值是否也在该数组中，如果在，说明直接拿另外一个值就行
- 为了避免结果为两个完全相同数之和，取到两次相同下标的情况和无用的遍历过程，从当前下标的下一个开始判断

```js
const twoSum = (nums, target) => {
    let res = []
    for(let i=0; i<nums.length; i++) {
        let otherNum = target - nums[i]
        if(nums.includes(otherNum)) {    
            let otherIndex = nums.indexOf(target-nums[i],i+1)
            if(otherIndex != -1) {
                res.push(i)
                res.push(otherIndex) 
                return res
            } 
        }
    }
    return res
};

let arr = [3,4,2]
console.log(twoSum(arr,6))     // [ 1, 2 ]
```



---

### 回文数

思路：

- 数组有反转的 API，因此转成数组来坐
- 数组比较需要转为 String 来比较

```js
const isPalindrome = (str) => {
    let postStr = str.split('')
    let length = Math.floor(str.length / 2)
    let reverStr = [...postStr].reverse()
    postStr.length = length
    reverStr.length = length
    if(postStr.toString() == reverStr.toString() ) {
        return true
    } else return false
};

console.log(isPalindrome('abcddcba'))	// true
```



---

### 罗马数字

<img src="https://img-blog.csdnimg.cn/20201201171833958.png" style="margin:0" >

```js
function romanToInt(str) {
    let sum = 0
    let roman = {
        I: 1,
        V: 5,
        X: 10,
        L: 50,
        C: 100,
        D: 500,
        M: 1000,
        IV: 4,
        IX: 9,
        XL: 40,
        XC: 90,
        CD: 400,
        CM: 900,
    }  
    for(let i=0; i<str.length; i++) {
        if((str[i] + str[i+1]) in roman) {
            sum += roman[(str[i] + str[i+1])]
            i += 1
            continue
        }
        sum += roman[str[i]]
    }
    return sum
};

console.log(romanToInt('MCMXCIV'))		// 1994
```



---

