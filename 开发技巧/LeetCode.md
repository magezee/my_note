### 两数之和

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍

```js
twoSum([2,7,11,15],18)	// [1,2]
twoSum([3,3,3],6)		// [0,1]
```

**思路：**

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

**思路：**

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

**思路：**

- 使用 `key-value` 的数据格式将有规律的规则保存起来

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

### 最长公共前缀

<img src="https://img-blog.csdnimg.cn/2020120209541782.png" style="margin:0" >

**思路：**

- 用正则表达式硬怼

```js
function longestCommonPrefix(strArr) {
    let result = true
    let res = ''
    for(let count=0; count<strArr[0].length; count++) {
        let str = strArr[0].slice(0, count+1)
        let reg = new RegExp(`^${str}`)

        for(let i=0; i<strArr.length; i++) {
            result = result && reg.test(strArr[i])
            if(!result) {
                return res
            }
        }
        res = str
    }
}

console.log(longestCommonPrefix(["flower","flow","flight"]))    // fl
```



---

### 删除排序数组中重复项

<img src="https://img-blog.csdnimg.cn/20201202145655599.png" style="margin:0" >

**思路：**

- 由于是在原数组上进行更改，如果在遍历中删除数组元素，会导致每个数据的下标发生变动，遍历下标就可能无法遍历到每个数据，因此每次删除数据后都需要收到将下标 -1

```js
// 数组是有序排列的情况
function removeDuplicates(arr) {
    for(let i=0; i<arr.length; i++) {
        if(arr[i] === arr[i+1]){    
            arr.splice(i,1)
            i -=1
        }  
    }
    return arr.length
}

removeDuplicates([0,0,1,1,1,2,2,2,3,3,4])	// [0,1,2,3,4]
```

```js
// 数组是无序排列的情况
function removeDuplicates(arr) {
    for(let i=0; i<arr.length; i++) {
        let index = arr.indexOf(arr[i],i+1)
        if(index !== -1) {
            arr.splice(index,1)
            i-=1    
        } 
    }
    console.log(arr)
    return arr.length
}

removeDuplicates([0,0,1,2,1,1,2,2,2,3,3,4])		// [0,1,2,3,4]
```



---

### 移出元素

<img src="https://img-blog.csdnimg.cn/20201202154406162.png" style="margin:0" >

```js
function removeElement(nums, val) {
    while(nums.includes(val)) {
        nums.splice(nums.indexOf(val),1)
    }
    return nums.length
};
removeElement([0,1,2,2,3,0,4,2],2)      // [ 0, 1, 3, 0, 4 ]
```



---

### 进制转换

10进制转任意进制的过程：

- 10进制数字整除该进制，记录每一次整除的余数值，直到整除结果为0
- 将记录的余数反转，就是该进制结果

```js
function sysConvert(scale, num) {
    let remainderArr = []
    while(num !== 0 ) {
        remainderArr.push(num % scale)
        num = Math.floor(num / scale) 
    }
    remainderArr.reverse()      // 余数反转
    return Number(remainderArr.join(''))
}

console.log(sysConvert(16,150))     // 96
```



----

