## JS编程

### 包装类

**基本数据类型**

String、Number、Boolean、Null、Undefined

**引用数据类型**

Object

----

JS提供了三个包装类 通过这三个包装类可以将基本数据类型的数据转换为对象

```
String()
	可以将基本数据类型字符串转换为String对象
Number()
	可以将基本数据类型字符串转换为Number对象
Boolean()
	可以将基本数据类型字符串转换为Boolean对象
```

注意：实际开发中不会使用基本数据类型的对象，在使用基本数据类型对象做比较时 会出现很多与预期不符的事情

```javascript
/// 不在开发中使用的原因
var num1 = 3;
var num2 = new Number(3);
console.log(typeof num1);       // number
console.log(typeof num2);       // object 

num2.hello = 'hello';           // 向num2对象中添加属性
console.log(num2.hello);        // hello    如果用num1进行同样操作 则会为undefined

var num3 = new Number(3);
console.log(num2 == num3);      // false

var bool1 = new Boolean(true);
var bool2 = true;
console.log(bool1 == bool2);    // true     自动做了类型转换
console.log(bool1 === bool2);   // false

var obj  = new Boolean(false);
if(obj){    // 因为传的是一个对象 因此无论里面是什么都会执行
	alert('程序运行');
}
```

```javascript
/// 包装类的用处
var x = 123;
x = x.toString();
x.hello = 'hello';      // 这里添加了属性后 因为临时对象销毁了 因此添加的属性也随之销毁
console.log(typeof x);  // string

// 对象可以添加属性和调用方法 而基本数据类型不行
// 当对基本数据类型的值去调用属性和方法时，浏览器会临时使用包装类将其转换为对象，然后再调用对象的属性和方法，调用完之后再将其转换为基本数据类型
```

----

### Class

class 相当于用原型链声明构造函数和实现继承的语法糖

**类里面的共有属性和方法一定要加this使用**

```jsx
const x = 1

class Test {
    constructor() {
        this.x = 2
    }

    foo(){
        console.log(x)
    }
    foo_() {
        console.log(this.x)
    }

}

const test = new Test() 
test.foo_()		// 2
test.foo()		// 1 不用this绑定到类时，会直接寻找全局的变量和方法
test.x = 3
test.foo_()		// 3
```

#### this指向

类的方法内部如果有this，默认（只是默认，存在特殊情况）指向类的实例，但是如果赋值给第三方直接使用则可能会报错

```jsx
class Test {
	constructor() {
        this.x = 1
    }
    
    foo(){
        console.log(this.x)
    }
}
const test = new Test() // 使用new关机键将this指向test实例
test.foo()		// 1

const foo = test.foo
foo()	// 因为赋值再调用，this指向undefined，找不到a，报错（不是指向window），丢失this
```

解决方法：

- 在构造函数中，使用bind方法将指定方法的this指向强行绑定到类的的实例上（constructor方法内的this指向实例）

```jsx
const x = 1
class Test {
	constructor() {
        this.x = 2
        // 在new一个实例的时候，这里的所有this都指向实例，相当于每个实例的foo方法都执行了一遍bind操作
        this.foo=this.foo.bind(this)   
    }
    
    foo(){
        console.log(this.x)
    }
    foo_(){
        console.log(this.x)
    }
}
const test = new Test() 	

const obj =  {x:3}
obj.foo = test.foo
obj.foo_ = test.foo_

obj.foo()		// 2 	this指向仍为实例
obj.foo_()		// 3	this指向为调用的obj对象

test.x = 4
obj.foo = test.foo
obj.foo()		// 4	再一次证明this指向实例
```

- 使用箭头函数，箭头函数不会产生this，只能继承，而且继承的是最近的能产生this的父级，类创建的时候this会指向实例对象，this被类中箭头函数继承，因此箭头函数的this指向实例对象，不会更改

```jsx
const x = 2
class Test {
	constructor() {
        this.x = 1 
    }
    
    foo = () => {
        console.log(this.x)
    }
    foo_(){
        console.log(this.x)
    }
}
const test = new Test() // 使用new关机键将this指向test实例	

const obj =  {x:3}
obj.foo = test.foo
obj.foo_ = test.foo_
obj.foo()			// 1
obj.foo_()			// 3

test.x = 4
obj.foo = test.foo
obj.foo()			// 4
```



---

### 异常处理

**try-catch-finally结构**

三种结构：

```
try-catch
try-finally
try-catch-finally
```

- try：执行可能会抛出异常的代码（启动）

- catch：捕获错误，若没有catch，则`throw`异常时，程序直接中断，不会往下执行
- finally：错误处理结束后的操作，无论有没又throw异常，都会执行（try-finally结构中因为没有catch（往上传递也没有的话）抛出异常后直接中断此时不会执行finally）

```javascript
var InputError = {
    NotNumberError:'must input a Number Value',
    FinityError:'must input a Number is Finity',
    DivdendError:'Divdend must not be 0'
}

function div(a,b){
    if((typeof a) != 'number' || (typeof b) != 'number' ) {
        throw InputError.NotNumberError
    }
    if(!isFinite(a) || !isFinite(b)) {   // 不是Ifinity无穷大
        throw InputError.FinityError
    }
    if(b === 0) {
        throw InputError.DivdendError
    }
    return a/b
}

function newDiv(a,b) {
    try {
        var res = div(a,b)
    }
    catch(e) {
        console.log(e)
    }
    finally {
        console.log('异常处理结束')
    }
    return res
}

console.log(newDiv(3,4))	
// must input a Number Value
// 异常处理结束
// undefined
```

**异常传递**

一旦某个函数抛出异常，异常首先会传递到此函数的调用方，如果调用方有对异常进行捕获处理，异常就不再向上传递，如果调用方没有对抛出的异常进行捕获处理，则此异常会向上传递，知道被捕获处理或者不再有可以捕获处理的调用方。

```javascript
function newDiv(a,b) {
    try {
        var res = div(a,b)
    }
    catch(e) {
        if(e === InputError.DivdendError){
            res = NaN
        }
        if(e === InputError.FinityError){
            res = NaN
        }
        else{
            throw e
        }
    }
    finally {
        console.log('内层异常处理结束')
    }
    return res   
}

...
try {
	var res = newDiv(Infinity,1)
}
catch(e){
	console.log('输入错误')
}
finally {
	console.log('外层异常处理结束')
}
```



---

### 字符串的操作

在底层字符串是以数组形式保存的

```javascript
var str = 'Hello World';
console.log(str[0]);            // 'H' 
console.log(str.charAt(0));     // 'H' 功能和上面一样 一般使用上面的写法
console.log(str.length);        // 11
```

**字符串的方法**

- **concat( )** 

  连接两个或多个字符串，和'+'符号作用一样

```javascript
var str = '你好';
var result = str.concat('Jack');   
console.log(result);    // 你好Jack      
```

- **indexof( )**

  检索一个字符串中是否含有指定内容，若有则返回第一个索引，无则返回-1

  可以指定一个字符串，会检索字符串的第一个字符，然后判断跟在第一个字符后面的字符是否符合指定内容 若符合则返回第一个字符索引

- **lastIndexOf()**

  与indexof()类似，从后往前找

- **search( )**

  与indexof()作用一样

```javascript
var str = 'hello';
var result = str.indexOf('l');
console.log(result);                // 2
console.log(str.indexOf('lo'));     // 3    这里返回的是l的索引
console.log(str.indexOf('elo'));    // -1
console.log(str.indexOf('l',3));    // 3    第二个参数可以指定开始查找的索引 因为指定从3开始查找 所以2的l被忽略了 因此第一个找到的l在第3
```

- **slice( )**

  从字符串中截取指定内容（不会影响原字符串），如果是负数表示倒数第n位

```js
var str = 'abcdefg';
var result = str.slice(0,3);    // 表示截取索引[0,3)的字符串（不包括3）
console.log(result);            // abc
console.log(str.slice(3));      // defg  无结束标志表示3以后的都截取
console.log(str.slice(3,-2));   // de  结束标志位倒数第二个字符串
```

- **substring( )**

  与slice()用法几乎一样，但是不能传递负值

- **substr( )**

  substr(3,2) 从第三个索引字符开始向后截取两个字符

- **split( )**

  将有固定分隔符的字符拆分为一个数组

  如果传递空号 则会将每个字符都拆开

```javascript
var str = 'abc,def,ghi,jkl';
var result = str.split(',');    // 以 , 为分隔符
console.log(result);            // ['abc','def','ghi','jkl']
```

- **toUpperCase( )**

  将字符串全部转换为大写（不影响原字符串）

- **toLowerCase( )**

  将字符串全部转换为小写（不影响原字符串）

-  **padStart( ) 、 padEnd( )**

  将一个字符串的长度规定，如果达不到这个长度，则向前、后补充一个值
  
  注意，存在一个弊端，如果是用来限制位数，注意中文占两位，但是这个函数只是用来限制长度的，因此中文会有问题

```javascript
var csr_id = 123
console.log(csr_id.toString().padStart(10,' '))		// `       123`
console.log(csr_id.toString().padEnd(10,' '))		// `123       `
```

- **replace( )**

  在字符串中用一些字符替换另一些字符

```js
var str="hello WORLD"
var str1=str.replace(/o/ig,"*")
console.log(str1) 	//hell* W*RLD
```

- **match( )**

  返回所有查找的关键字内容的数组

```js
var str="To be or not to be"
var str1=str.match(/to/ig)
console.log(str1)                	// ["To", "to"]
console.log(str.match("Hello"))     // null
```



----

### 正则表达式

正则表达式用于定义一些字符串规则

计算机可以根据正则表达式来检查一个字符串是否符合规则

获取将字符串中符合规则的内容提取出来

#### 语法

**匹配模式**

```
i 		// 忽略大小写  
g 		// 全局匹配（不只匹配单个 匹配所有满足条件的字符）
```

**创建正则表达式对象**

- `var reg = new RegExp("正则表达式","匹配模式")`		特点：灵活

```javascript
var reg = new RegExp("a","i");

let str = 'abc'
var reg1 = new RegExp(str,"i");
```

- 使用字面量创建正则表达式 ` var reg = /正则表达式/匹配模式 （注意不带引号）`     特点：简单但不灵活 不能传递变量

```javascript
var reg = /a/i;    // 类型也是对象
```

-----

**正则表达式的符号**

```
|           --表示或
[]          --同样表示或
[ - ]   	--范围匹配
	[a-z]   	--任意小写字母
	[A-Z]   	--任意大写字母
	[A-z]   	--任意字母
	[0-9]   	---任意数字
[^ ]    	--除了指定字符串之外的都匹配
```

```javascript
reg = /a|b/i;       // 检查字符串是否有a或b 和 /[ab]/i 作用一样
reg = /a[xyz]c/;    // 检查一个字符串中是否含有 axc或ayc或azc
reg = /[^0-9]/;     // 检查一个字符串中是否有数字以外的字符
```

----

**量词** 

设置一个内容出现的次数

```
{n}     --正好出现n次
{n,m}   --出现n-m次
{m,}    --出现m次及以上
+       --至少出现一次 等于{1,}
*       --0个或多个 等于{0,}
?       --0个或1个  等于{0,1}
```

```javascript
reg = /a{3}/          // 匹配aaa
reg = /(ab){3}/       // 匹配ababab
reg = /ab{1,3}c/      // 匹配abc或abbc或abbbc
reg = /ab+c/          // 匹配至少含有一个b的字符串 
reg = /ab*c/		  // 匹配任意b
reg = /ab?c/
```

---

**开头与结尾项**

```
^       --匹配开头
$       --匹配结尾
```

```javascript
reg = /^a/
reg = /a$/
reg = /^a$/    // 匹配单个字符a 匹配不到aa
```

```javascript
/// 例子：手机号规则
// 以1开头                           ^1
// 第二位3-9任意数字                  [0-9]
// 三位以后任意数字9个且以第九位为末     [0-9]{9}$

var phoneReg = /^1[3-9][0-9]{9}$/
```

----

**元字符**

```
.       --匹配任意字符
\.      --匹配.字符
\\      --匹配\字符
\w      --匹配任意字母和数字和下划线_               
\W      --匹配字母数字下划线以外的任意字符
\d      --匹配任意数字
\D      --匹配数字以外的任意字符
\s      --匹配空格
\S      --匹配空格以外任意字符
\b      --匹配单词边界  即一串字符的前后不能含有其他字符(若有空格可含有空格)
\B      --匹配单词边界以外任意字符
```

```javascript
var reg = /\./
var reg = new RegExp("\\.")  // 使用构造函数时 由于参数是字符串 而\是字符串中转义字符 所以需要使用\\来代替\ 这里与上行的正则表达式功能一样
```

```javascript
/// 例子：去除字符串开头和结尾的空格
var str = "      hello   world          ";
str = str.repalce(/^\s*|\s*$/g,"");
```

----

**贪婪匹配和惰性匹配**

贪婪匹配：.*

会截取整个字符串中最后的那位

惰性匹配：.*?

截取到匹配最近的一位

```javascript
// 可以看到*?匹配的是最近的一个"作为结尾
var str = '{"OrdrTp":"01", "PAYMENT":"a",{"OrdrTp":"02","PAYMENT":"b"}'
var result_1 = str.match(/"OrdrTp":".*?"/g);		// [ '"OrdrTp":"01"', '"OrdrTp":"02"' ]
var result_2 = str.match(/"OrdrTp":".*"/g);		    // ['OrdrTp:01, "PAYMENT":"a",{"OrdrTp":"02","PAYMENT":"b"' ]
```

----

**先行/后行断言**

pattern是一个正则表达式

- `(?=pattern)`：正向先行断言

  代表字符串中的一个位置，紧接该位置之后的字符序列能够匹配pattern，先行要匹配项写在`(?=pattern)`左边

```jsx
var srt = 'a regular expression'
var reg = /re(?=gular)/g			// 只匹配到regular的re，匹配不到expression的re
// var reg = /re(?=gular)../g		// 匹配regu
```

- `(?!pattern)`：负向先行断言

  代表字符串中的一个位置，紧接该位置之后的字符序列不能匹配pattern

```jsx
var srt = 'regex represents regular expression'
var reg = /re(?!g)/g		// 匹配不到regex和regular
```

- `(?<=pattern)`：正向后行断言

  代表字符串中的一个位置，紧接该位置之前的字符序列能够匹配pattern，后行要匹配项写在`(?<=pattern)`右边

```jsx
var srt = 'regex represents regular expression'
var reg = /(?<=\w)re/g		// 匹配单词内部的re但是不匹配开头re
```

- `(?<!pattern)`：负向后行断言

  代表字符串中的一个位置，紧接该位置之前的字符序列不能匹配pattern

```jsx
var srt = 'regex represents regular expression'
var reg = /(?<!\w)re/g		// 匹配单词首部的re但是不匹配内部re
```

-----

#### 方法

- **test( )**

  检查一个字符串是否符合正则表达式规则（是否含有该字符串）

  符合返回true，否则返回false

```javascript
var str = 'Abcd';
console.log(reg.test(str));
```

- **split( )**  

  默认全局匹配模式

```javascript
var str = '1a2b3c4d5e';
var result = str.split(/[A-z]/);    // 将任意字母作为分隔符进行拆分
console.log(result);    // ["1","2","3","4","5",""]
```

- **search( )  **

  不能全局匹配

```javascript
var str = 'abc aec afc';            
var result = str.search(/[bef]c/);
console.log(result);    // 1
```

- **match( )**

  根据正则表达式 从一个字符串中将符合条件的内容提取出来

  默认只找第一个符合的字符，但是若将正则表达式设置为全局匹配模式，则匹配所有内容，且可以同时设置i和g模式 /[A-z]/gi

```javascript
var str = '1a2b3c4d5e';
var result = str.match(/[A-z]/g);
console.log(result);    // ['a','b','c','d','e']    数组格式
```

- **replace( )**

  将字符串中指定内容替换为新的内容

  默认只会替换第一个符合匹配的字符，可以用正则表达式设置为全局模式，不会影响原字符串

```javascript
var str = '1a2b3c4d5e';
var result = str.replace(/[a-z]/g,"");  // 将字符串中的字母删除
console.log(result);    // 12345
```



-----

### Math方法

- **Math.ceil(n)**

  向上取整。返回大于等于n的最小整数

```javascript
var num = 1.5
var result = Math.ceil(num)
console.log(result)		// 2
```

- **Math.floor(n)**

  向下取整。返回为n的整数部分

```javascript
var num = 1.5
var result = Math.floor(num)
console.log(result)		// 1
```

- **Math.round(n)**

  四舍五入。返回为n四舍五入后的整数

```javascript
var num = 1.5
var result = Math.round(num)
console.log(result)		// 2
```

- **Math.random()**

  返回一个 0 到 1 之间的随机数（包含0，不包含1）

```javascript
var result = Math.random()
console.log(result)		// 0.8647578968666494
```

​	生成[1,max]的随机整数

```javascript
// 任意一个都可,max为期望最大值
parseInt(Math.random()*max,10)+1;
Math.floor(Math.random()*max)+1;
Math.ceil(Math.random()*max);
```

​	生成[0,max]到任意数的随机数

```javascript
// 任意一个都可,max为期望最大值
parseInt(Math.random()*(max+1),10);
Math.floor(Math.random()*(max+1));
```

​	生成[min,max]的随机数

```javascript
// 任意一个都可,max为期望最大值,min为期望最小值
parseInt(Math.random()*(max-min+1)+min,10);
Math.floor(Math.random()*(max-min+1)+min);
```

​	生成指定位数的随机范围数

```javascript
// 不足位数时用空符号补全
Math.round(Math.random()*max).toString().padEnd(maxLength,' ')
```



----

### 操作数组

```js
// 切记！数组是引用类型，不要直接赋值，应该使用浅/深拷贝
let arr= [1,2,3,4]
let newArr= arr
newArr.length = 2
console.log(arr)	// [ 1, 2 ]
console.log(arr)	// [ 1, 2 ]
```

#### 查询

- **indexOf**

  查找数组中指定元素的索引，从0开始，没有找到时返回-1

```jsx
var arr = ['aa', 'bb', 'cc']
arr.indexOf('bb')	// 1
arr.indexOf('dd')	// -1
```

​		可用于去除数组中的重复数据

```jsx
fun = (arr) => {
    var result = []
	arr.map(item => {
     	if(result.indexOf(item) == -1){		// 如果result数组中没有该数据，则push进该数据
            result.push(item)
        }   
	})    
	return result
}
```

----

- **includes**

  检测数组中有无指定值，返回值为布尔值

```jsx
let site = ['runoob', 'google', 'taobao'];
 
site.includes('runoob'); 
// true 
 
site.includes('baidu'); 
// false
```

​		同理，也可以用于数组去重

```js
fun = (arr) => {
    var result = []
    arr.map(item => {
        if(!result.includes(item)){		
            result.push(item)
        }   
    })    
    return result
}
```



----

#### 更改

- **通过改变length截取数组**

```jsx
var arr = [1,2,3,4]
arr.length = 2 	// [1,2]
```

---

- **fill**

  填充数组，三个参数：`value`， `start`，`end`，`value`即该固定值，`start` 与`end`分别为起始索引与终止索引，这两个参数可以省略，起始索引的默认值是0，终止索引的默认值是数组的长度

```js
const arr = new Array(5).fill(1)    // [ 1, 1, 1, 1, 1 ]
```

-----

- **concat**

  复制原数组，添加指定元素进数组并返回新数组，添加元素为数组时，会将值取出来再添加

```jsx
var array=[1,2,3,4,5];   
var array2=array.concat([6,7]);    //参数为数组
console.log(array);    //[1, 2, 3, 4, 5]
console.log(array2);   //[1, 2, 3, 4, 5, 6, 7]
```

​	  相当于扁平化了一层深度，因此可以用来实现数组扁平化

```js
var array=[1,2,3,4,5];   
var array2=array.concat([6,7,[8,9]]);    

array.push([6,7,[8,9]])
console.log(array2);   // [ 1, 2, 3, 4, 5, 6, 7, [ 8, 9 ] ]
console.log(array)     // [ 1, 2, 3, 4, 5, [ 6, 7, [ 8, 9 ] ] ]
```

-----

- **slice**

  按指定下标切割数组，不会对原数组造成影响

```js
var s = "abcdefg";
s.slice(0,4)    // "abcd"
s.slice(2,4)    // "cd"
s.slice(4)      // "efg"
s.slice(3,-1)   // "def"
s.slice(3,-2)   // "de"
s.slice(-3,-1)  // "ef"
```

------

- **reverse**

  将数组反转，直接作用原数组

```js
let arr = [1,2,3,4]

arr.reverse()	// [4,3,2,1]
```

----

- **push**

  直接在原数组上进行修改，添加指定元素，添加元素为数组时，不会将值取出来

```jsx
var array=[1,2,3,4,5];
array.push([6,7]);    //参数为数组
console.log(array);   //[1, 2, 3, 4, 5, [6, 7]]
```

---

- **shift** 

  把数组的第一个元素从其中删除，并返回第一个元素的值（在原数组上进行更改）

```jsx
var array=[1,2,3,4,5];
var result = array.shift();    
console.log(result);	// 1
console.log(array);   	// [2,3,4,5]
```

---

- **unshift**

  将一个或多个元素添加到数组的开头，并返回新数组的长度

```js
let arr = [1, 2, 3, 4, 5]
let res = arr.unshift(6, 7)
console.log(res)   // 7
console.log(arr)   // [6, 7, 1, 2, 3, 4, 5]  
```

---

- **pop**

  把数组的最后一个元素从其中删除，并返回该元素的值（在原数组上进行更改）

```jsx
var array=[1,2,3,4,5];
var result = array.pop();    
console.log(result);	// 5
console.log(array);   	// [1,2,3,4]
```

----

- **join**

  将数组内容转为字符串，默认逗号分隔，可以自定义分隔符，不会对原数组造成影响

```jsx
var arr = [1,2,3,4]
var res = arr.join('/')	

console.log(arr)        // [ 1, 2, 3, 4 ]
console.log(res)        // '1/2/3/4'
```

----

- **flat(n)**

- 用于数组扁平化，n默认是1，表示扁平化深度

```js
const arr = [1, [2, [3, [4, 5]]], 6];

const res1 = arr.flat()       // [ 1, 2, [ 3, [ 4, 5 ] ], 6 ]
const res2 = arr.flat(2)      // [ 1, 2, 3, [ 4, 5 ], 6 ]
const res3 = arr.flat(3)      // [ 1, 2, 3, 4, 5, 6 ]

// 一般直接用Infinity进行通用的扁平化，无论深度多深都能扁平化完毕
const res = arr.flat(Infinity)	// [ 1, 2, 3, 4, 5, 6 ]
```



----

#### 遍历

- **map**

  遍历数组，对每一个下标内容作出逻辑更改并返回新的数组，map内部需要有return值，这个return值是指将当前遍历的结果返回，进入下一个遍历对象，而不是最终结果

```jsx
var array = [1,2,3]

// 声明item变量指向具体遍历到的下标内容，然后声明一个函数，对具体内容作出具体逻辑更改
var result = array.map(item => {
    if(item == 2) {
        item += 1
    }
   	return item		// 注意一定要有return值 否则返回为[undefined,undefined,undefined]   
})
// result = [1,3,3]
```

​	map还可以用于统一更改数组内数据类型

```js
var array = [0,1,2]
var arr = array.map(Boolean)
console.log(arr)	// true false false
```

----

- **forEach**

  相当于for去遍历数组的语法糖，与map不同的是，一般不会去return返回值，直接影响原数组，同时该方法遍历必须每一个元素都遍历到，不支持 `continue` 或者 `break` 来提前结束循环

```js
let arr = [0,1,2,3]
arr.forEach((item, index, arr) => {
	arr[index] = item + 3
})
console.log(arr)	// [3, 4, 5, 6]
```

​		需要配合下标更改数据，直接更改没有效果

```js
let arr = [0,1,2,3]
arr.forEach(item => {
	item += 1
})
console.log(arr)	// [ 0, 1, 2, 3 ]
```

---

- **filter**

  创建一个新的数组，遍历原数组返回满足指定条件的内容push进新数组（filter() 不会对空数组进行检测）

```jsx
var array = [1,3,5,7]
var result = array.filter(item => item > 3)	// result = [5,7]
```

----

- **every和some**

  用于检测数组内元素是否符合指定条件（通过函数确定条件），不会对空数组进行检测，不会改变原始数组

  every：如果有一个元素不满足则返回false，都满足条件返回true

  some：如果任意一个元素可以满足条件，则返回true

```jsx
var arr = [ 1, 2, 3, 4, 5, 6 ]
 
arr.ever( function( item, index, array ){ 
    return item > 3; 
}) 	// flase

arr.some( function( item, index, array ){ 
    return item > 3; 
}) 	// true
```

----

- **find**

  遍历数组，直到返回 true，并返回结果，如果遍历完后都没有返回 true 则返回 undefined

```js
let array = [1, 2, 3, 4, 5]
let res = array.find(function(item){
    return res
})
console.log(res)  // 1
```

```js
let array = [1, 2, 3, 4, 5]
let res = array.find(function(item){
    return item * 3 == 9
})
console.log(res)  // 3
```

---

- **findIndex**

  和 `find` 方法类似，返回的是满足条件的下标，如果遍历完返回-1

```js
let array = [1, 2, 3, 4, 5]
let res = array.findIndex(function(item){
    return item * 3 == 9
})
console.log(res)  // 2
```

-----

- **for-in**

  循环遍历，拿到的值是数组下标或者对象key值，可以随时中断遍历

```js
let arr = [5,6,7,8]
for(let key in arr) {
    console.log(key)        // 0 1 2 3
    if(key == 2) break
    console.log(arr[key])   // 5 6
}
```

---

- **for-of**

  循环遍历，拿到的值是对应的数据，可以随时中断遍历，和 `for-in` 不同，不可以直接遍历对象，但是可以遍历 `Set` 和 `Map` 结构

```js
let arr = [5,6,7,8]
for(let item of arr) {
    console.log(item)        // 5 6 7 8
}
```





----

#### 通用

- **reduce**

  传入一个方法，遍历数组，并为每一个元素执行该方法（不会更改原数组，最终返回的值为最后一个元素执行该方法的返回值，可选）
  
  reduce 可以做到绝大部分的数组遍历效果

```jsx
arr.reduce(callback,[initialValue])
```

```
回调函数参数：
    previousValue （前一个元素调用回调返回的值，或者是提供的初始值（initialValue））
    currentValue （数组中当前被处理的元素）
    index （当前元素在数组中的索引）
    array （调用 reduce 的数组）
```

​		若不提供初始值，则遍历数组会从index=1开始，并且第一次prev的值为数组索引[0]的值

```jsx
var arr = ['a', 'b', 'c', 'd'];
var demo = arr.reduce(function(prev, cur, index, arr) {
    console.log(prev, cur, index);
    return '执行完毕'
})

// a b 1
// 执行完毕 c 2
// 执行完毕 d 3
```

​		给定初始值，则从[0]开始遍历数组

```jsx
var arr = ['a', 'b', 'c', 'd'];
var demo = arr.reduce(function(prev, cur, index, arr) {
    console.log(prev, cur, index);
    return '执行完毕'
},'初始值')

// 初始值 a 0
// 执行完毕 b 1
// 执行完毕 c 2
// 执行完毕 d 3
```

​		数组求和，求乘积

```jsx
var  arr = [1, 2, 3, 4];
var sum = arr.reduce((x,nextX)=>x+nextX)
var mul = arr.reduce((x,nextX)=>x*nextX)
console.log( sum ); //求和，10
console.log( mul ); //求乘积，24
```

​		计算数组中每个元素出现的次数

```jsx
let names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice'];

let nameNum = names.reduce((pre,cur)=>{
  if(cur in pre){
    pre[cur]++	
  }else{
    pre[cur] = 1 // 若该对象里有该属性，则该属性的属性值自增，如果没有，则创建该属性并且赋予属性值1
  }
  return pre
},{})	// 注意初始值为一个空对象
console.log(nameNum); //{Alice: 2, Bob: 1, Tiff: 1, Bruce: 1}
```

​		数组去重

```jsx
let arr = [1,2,3,4,4,1]
let newArr = arr.reduce((pre,cur)=>{
    if(!pre.includes(cur)){
      return pre.concat(cur)
    }else{
      return pre
    }
},[])
console.log(newArr);// [1, 2, 3, 4]
```

​		将二维数组转化为一维

```jsx
let arr = [[0, 1], [2, 3], [4, 5]]
let newArr = arr.reduce((pre,cur)=>{
    return pre.concat(cur)
},[])
console.log(newArr); // [0, 1, 2, 3, 4, 5]
```

​		将多维数组转化为一维

```jsx
let arr = [[0, 1], [2, 3], [4,[5,6,7]]]
const newArr = function(arr){
   return arr.reduce((pre,cur)=>
        // 如果当前项是数组，则继续调用该方法
		pre.concat(
       		Array.isArray(cur) 
       		? 
			newArr(cur) 
            : 
            cur
  		)
  ,[])	// 用到了递归
}
console.log(newArr(arr)); //[0, 1, 2, 3, 4, 5, 6, 7]
```

​		对象里的属性求和

```jsx
var result = [
    {
        subject: 'math',
        score: 10
    },
    {
        subject: 'chinese',
        score: 20
    },
    {
        subject: 'english',
        score: 30
    }
];

var sum = result.reduce(function(prev, cur) {
    return cur.score + prev;
}, 0);
console.log(sum) //60
```

----

- **splice**

  参数有三种：第一个参数表示参数的开始下标，第二个参数表示删除包括起始点自身往后的n个数据，其余参数表示在删除数据后往删除点插入指定数据，会对原数组造成影响，且返回删除（弹出）的数据
  
  实际上splice可以做到大部分数组的增删改查效果

```js
// 删除功能
let arr = [1,2,3,4]
let res = arr.splice(0,2)
console.log(arr)	// [3,4]
console.log(res)	// [1,2]

// 插入功能，第二个参数必须设置为0
let arr = [1,2,3,4]
let res = arr.splice(1,0,5)
console.log(arr)	// [ 1, 5, 2, 3, 4 ]
console.log(res)	// []

// 替换功能
let arr = [1,2,3,4]
let res = arr.splice(1,1,5,6,7,8)
console.log(arr)	// [ 1, 5, 6, 7, 8, 3, 4 ]
console.log(res)	// [ 2 ]

// 可用于删除多个数据并把后面数据往前紧凑
let arr = [1,2,3,4]
let res = arr.splice(1,2,5)
console.log(arr)	// [ 1, 5, 4 ]
console.log(res)	// [ 2, 3 ]
```



---

### 操作对象

**实例方法**

- **hasOwnProperty( )**

  检查对象中是否包含某个属性，且此属性必须为自身而非从原型链上继承而来

```jsx
var obj = {
    name: 'lucy'
}

Object.prototype.x = 1

console.log(obj.hasOwnProperty('name'))     // true
console.log(obj.x)  // 1
console.log(obj.hasOwnProperty('x'))        // false
```

- **isPrototypeOf( )**

  用于测试一个对象是否存在于另一个对象的原型链上

```js
// 原型链父子关系：Foo → Bar → Baz
function Foo() {}
function Bar() {}
function Baz() {}

Bar.prototype = Object.create(Foo.prototype);
Baz.prototype = Object.create(Bar.prototype);

let baz = new Baz();

Baz.prototype.isPrototypeOf(baz); 		// true
Bar.prototype.isPrototypeOf(baz); 		// true
Foo.prototype.isPrototypeOf(baz); 		// true
Object.prototype.isPrototypeOf(baz); 	// true
console.log(baz.isPrototypeOf(Bar))     // false，构造函数不在原
```



----

**静态方法**

- **Object.keys( )**

```jsx
var obj = {x:1, y:2, z:3}
Object.keys(obj)	// ['x', 'y', 'z']
```

- **Object.values( )**

```jsx
var obj = {x:1, y:2, z:3}
Object.values(obj)	// [1, 2, 3]
```

- **Object.create( )**

  创建一个新对象，使用现有的对象来提供新创建的对象的``__proto__``

```jsx
var a ={
    x:1
}
var b = Object.create(a)

console.log(b.__proto__)        // { x: 1 }
console.log(b.prototype)        // undefined
console.log(b.constructor)      // [Function: Object]	对象的构造函数函数为Object
```

- **Object.getOwnPropertyNames( )**

  获取该对象的所有属性key

```jsx
var obj ={
    x:1,
    y:2
}
console.log(Object.getOwnPropertyNames(obj))	// ['x','y']
```

- **Object.defineProperty( )**

  直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象

  可传入三个参数：定义属性的对象，定义或修改属性的名称，修改的属性描述符

  默认情况下，使用 `Object.defineProperty()` 添加的属性值是不可修改的

```jsx
var obj ={}
Object.defineProperty(obj, 'age', {
  value: 42,
  writable: false	// 表示不可写
})
obj.age = 77	// 报错
console.log(obj.age)	// 42
```

​		描述符配置：

```markdown
configurable  [默认flase]
	当且仅当该属性的 configurable 键值为 true 时，该属性的描述符才能够被改变，同时该属性也能从对应的对象上被删除

enumerable  [默认flase]
	当且仅当该属性的 enumerable 键值为 true 时，该属性才会出现在对象的枚举属性中
	
value  [默认undefined]	
	该属性对应的值

writable  [默认flase]
	当且仅当该属性的 writable 键值为 true 时，value才能被赋值运算符改变
	
get  [默认undefined]	
	属性的 getter 函数，如果没有 getter，则为 undefined
	当访问该属性时，会调用此函数，执行时不传入任何参数，但是会传入 this 对象
	该函数的返回值会被用作属性的值

set  [默认flase]
	属性的 setter 函数，如果没有 setter，则为 undefined
	当属性值被修改时，会调用此函数，该方法接受一个参数（也就是被赋予的新值），会传入赋值时的 this 对象
	
拥有布尔值的键 configurable、enumerable 和 writable 的默认值都是 false
属性值和函数的键 value、get 和 set 字段的默认值为 undefined
```

```jsx
function Archiver() {
    var temperature = null;
    var archive = [];

    Object.defineProperty(this, 'temperature', {
        get: function() {
            console.log('get!');
            return temperature;
        },
        set: function(value) {
            temperature = value;
            archive.push({ val: temperature });
        }
    });

    this.getArchive = function() { return archive; };
}

var arc = new Archiver();
arc.temperature; // 'get!'
arc.temperature = 11;
arc.temperature = 13;
arc.getArchive(); // [{ val: 11 }, { val: 13 }]
```

- **Object.preventExtensions( ) **

  让一个对象变的不可扩展，也就是永远不能再添加新的属性

```jsx
var obj = {
    x:1
}
Object.preventExtensions(obj)
obj.y = 2 	// 报错
```

- **Object.getPrototypeOf()**

  获取对象原型（相当于`__proto__`）

```jsx
var obj = {
    x:1
}

console.logObject.getPrototypeOf(obj))	// {}
```

- **Object.isExtensible( )**

  判断一个对象是否是可扩展的（是否可以在它上面添加新的属性）

```jsx
var obj = {}
console.log(Object.isExtensible(obj))	// true
Object.preventExtensions(obj)
console.log(Object.isExtensible(obj))	// false
```

- **Object.setPrototypeOf( )**

  设置对象原型

```jsx
let obj1 = {}
let obj2 = {x:1}
Object.setPrototypeOf(obj1,obj2)
console.log(obj1.__proto__)		// {x:1}
```

- **for in**

  用于遍历数组或对象

```js
let arr = [3,4,5]
let obj = {
	a: 3,
	b: 4,
	c: 5
}

// 数组时，变量是下标
for(let index in arr ) {
	console.log(index)			// 0 1 2
	console.log(arr[index])		// 3 4 5
}

// 对象时，变量是属性名
for(let key in obj) {
	console.log(key)			// a b c
	console.log(obj[key])		// 3 4 5
}
```



-----

### 更改this指向

- **call ( )**

  更改this指向，第一个参数为需要更改的对象（若指定null则this指向window），后面的参数为需要传进去的形参

```javascript
var a = {
    user:"lucy",
    fn:function(x,y){
        console.log(this.user); 
        console.log(x+y); 
    }
}
var b = a.fn;
b(1,2)
// undefined
// 3
b.call(a,1,2);
// lucy
// 3
```

- **apply ( )**

  与call ( )一样，唯一不同的只是后面的传参需要以数组形式传进去

```javascript
var a = {
    user:"lucy",
    fn:function(x,y){
        console.log(this.user); 
        console.log(x+y); 
    }
}
var b = a.fn;
b.apply(a,[1,2]);
```

- **bind ( )**

  执行时不会执行函数，返回一个修改后的函数

```javascript
var a = {
    user:"lucy",
    fn:function(a,b,c){
        console.log(this.user, a, b, c);
    }
}
var b = a.fn;
b.bind(a);			// 不会打印东西，因为没有执行fn

c = b.bind(a,1);	// 可预先填入固定形参
c()                 // lucy， 1 undefined undefined
c(1,2,3)            // lucy， 1 1 2		最多接收三个形参，第四个则会忽略

d = b.bind(a)
d(1,2,3)            // lucy， 1 2 3
```



----

### 解构

**数组结构：**

一般使用

```jsx
var [a,b] = [1,2]
console.log(a)	// 1
console.log(a)	// b
```

留空来跳过被解构数组中的某些元素

```jsx
var [,,third] = ["1", "2", "3"]
console.log(third)	// "3"
```

通过不定参数模式捕获数组中的所有尾随元素

```jsx
var [head, ...tail] = [1, 2, 3, 4]
console.log(tail);	// [2,3,4]
```

**对象结构：**

一般使用：

```jsx
var robotA = { name: "Bender" }
var { name: nameA } = robotA
console.log(nameA)	// "Bender"
```

当属性名与变量名一致时，可简写

```jsx
 var { foo, bar } = {bar: "ipsum", foo: "lorem"}
 console.log(foo)	// "lorem"
```

如果变量名不一致，不会解构

```js
var { a, b } = {bar: "ipsum", foo: "lorem"}
console.log(a)	// undefined
```

从一个对象中提取变量并赋值给和对象属性名不同的新的变量名

```jsx
var o = {p: 42, q: true};
var {p: foo, q: bar} = o;
console.log(foo); // 42 
console.log(bar); // true
```

从一个对象解构，并分配给一个不同名称的变量 同时分配一个默认值，以防未解构的值是undefined

```jsx
var {a:aa = 10, b:bb = 5} = {a: 3};
console.log(aa); // 3
console.log(bb); // 5
```

**参数解构：**

```js
// 要使用函数解构的参数，其声明时要用对象格式
function demo({a, b, c, d}) {
  console.log(a + b)
  console.log(`a:${a}, b:${b}, c:${c}, d:${d}`)
}

let a = 1
let b = 2
let c = 3

demo({c, b, a})		// 要传入和形参同名的参数，写的顺序无所谓
// 3
// a:1, b:2, c:3, d:undefined



demo({cc: 3, bb: 2, aa: 1})		// 传入不同命的参数时
// NaN
// a:undefined, b:undefined, c:undefined, d:undefined
```

有时为了解决传参错误的问题，可以设定默认值

```js
function demo({a=1, b=2, c=3, d=4}) {
  console.log(a + b)
  console.log(`a:${a}, b:${b}, c:${c}, d:${d}`)
}

let a = 9
let b = 8
let c = 7

demo({c, b, a})
// 17
// a:9, b:8, c:7, d:4


demo({cc: 3, bb: 2, aa: 1})   // 传入不同名参数也不会报错
// 3
// a:1, b:2, c:3, d:4
```

如果用ts写，接口类型的参数也是可以用解构传参的，因为接口就相当于一个对象类型

```ts
interface IDemo {
    a: number;
    b: number;
    c: number;
    d?: number;
}

const demo = (params: IDemo) => {
    console.log(`a:${params.a}, b:${params.b}, c:${params.c}, d:${params.d}`)
}

demo({a:3, b:1, c: 5})      // a:3, b:1, c:5, d:undefined
```



---

### 模板字符

使用``符号，变量用${}

```jsx
var parms = '插入的变量'
var str = `自由在字符串里插入变量${parms}`
```



----

### arguments和rest

**arguments**

在函数调用的时候，浏览器每次都会传递进两个隐式参数：

- 函数的上下文对象this

- 封装实参的对象arguments

基本作用：调用方法时，传入方法的参数都会传进arguments里，无论有没有声明形参，是一个类数组对象

```jsx
function fun(x,y){
    console.log(x+y)			// 4
    console.log(arguments);		// [Arguments] { '0': 1, '1': 3, '2': 4 }
}
fun(1,3,4);
```

使用场景：当构建一个函数，他传入参数以及在函数内是 否用到该参数是可选的

```jsx
function bindThis(f, oTarget) {
    return f.bind(oTarget,arguments)  	// bind()方法除了第一个参数必须外，后面传进参数可选
}
```

arguments详细内容：

```jsx
function fun(){
  console.log(arguments);
}
fun('tom',[1,2,3],{name:'Janny'}); 
```

浏览器上调试：

```json
Arguments(3) ["tom", Array(3), {…}, callee: ƒ, Symbol(Symbol.iterator): ƒ]
0: "tom"
1: (3) [1, 2, 3]
2: {name: "Janny"}
callee: ƒ fun()
length: 3
Symbol(Symbol.iterator): ƒ values()
__proto__: Object
```

arguments还有callee，length和迭代器Symbol这些属性：

- callee指向调用函数

```jsx
function fun(){
  console.log(arguments.callee === fun);	// true
}
fun('tom',[1,2,3],{name:'Janny'});
```

```jsx
//arguments.callee引用函数自身
let sum = function (n) {
    if (1 == n) {
        return 1;
    } else {
        return n + arguments.callee(n - 1);
    }
}
```

- length经常在数组或者类数组中看到，可以看到arguments的原型索引\__proto\__的值为Object，故arguments不是数组，而是一个类数组对象，即可以用arguments[n]来取值

```jsx
function fun(){
  console.log(arguments instanceof Array);	// false
  console.log(Array.isArray(arguments));	// false
}
fun('tom',[1,2,3],{name:'Janny'});
```

----

**rest**

es6 新添的特性，用于取代 arguments ，获取函数的多余参数

和 arguments 不同，rest 参数是一个数组，使用更加方便，不过 arguments 是函数的自带属性，而 rest 必须要声明才能使用

```js
...变量名
```

```js
function add(...value){
    console.log(value);
    let sum=0;
    for(var val of value){
        sum += val    
    }
    return sum
}
let result =  add(2,3,5)	// 打印 [2,3,5]
console.log(result)			// 10
```

arguments 存储的是全部的参数值，而 rest 只读取没有明确定义形参的参数值（因此 rest 参数必须被声明在形参最后）

```js
function demo(value, ...params) {
    console.log(value)          // 1
    console.log(params)         // [2,3,4,5]
    console.log(arguments)      // [Arguments] {'0':1, '1':2, '2':3, '3':4, '4':5}
}

demo(1,2,3,4,5)
```

两种写法

```js
// arguments写法
function sortNumbers(){
    // arguments对象不是数组，只是一个类数组对象，为了使用数组的方法，使用Array.prototype.slice.call先将其转为数组
    return Array.prototype.slice.call(arguments).sort()
}
 
//rest写法
const sortNumbers=(...numbers)=>numbers.sort()
```



----

### 扩展运算符...

当 `...` 不用在函数声明的形参中时，它就是一个扩展运算符

**复制对象和数组**

```js
var obj = {x:1, y:2, z:3}
var obj1 = obj					// 这里只是引用，但不是复制，当obj的内容更改时obj1也会更改
var obj2 = {...obj}				// 复制obj的所有内容，与obj相互独立

var obj3 = {...obj, z:4, a:1}	// 可在复制的同时对原内容进行覆盖修改或添加（常用）

var obj4 = Object.assign({}, obj, {z:4, a:1})	// 等同obj3的操作，将obj的内容放到一个空对象，并对指定属性修改
```

```js
var arr = ['a', 'b', 'c']
var arr2 = [...arr]
console.log(arr2)		// ['a', 'b', 'c']
```

因为这个特性，可以使用扩展运算符代替 `concat()` 来拼接数组

```js
var arr1 = ['a', 'b', 'c']
var arr2 = ['d', 'e', 'f']
arr = [...arr1, ...arr2]
console.log(arr)	// ['a', 'b', 'c', 'd', 'e', 'f']

// 等同于 arr = arr1.concat(arr2)
```

**转换数组**

将数组转换成用逗号分隔，常用于调用函数传参

```js
console.log(1, ...[2, 3, 4], 5)  // 1 2 3 4 5

function add(x, y) {
  return x + y;
}
add(...[4,38]) // 42
```

和普通传参区别

```js
var middle = [3, 4]
var arr = [1, 2, middle, 5, 6]
var arr2 = [1, 2, ...middle, 5, 6]

console.log(arr)	// [1, 2, [3, 4], 5, 6]
console.log(arr)	// [1, 2, 3, 4, 5, 6]
```

可以方便使用参数不能为数组的方法

以往都是使用 `apply(null, arr)` ，因为 apply 要求传入的参数是个数组

```js
var arr = [2, 4, 8, 6, 0]
function max(arr) {
  return Math.max(...arr)
  // return Math.max.apply(null, arr)	等同上面
}
```

**字符串转数组**

```js
var str = "hello"
var chars = [...str]
console.log(chars)	// ['h', 'e',' l',' l', 'o']
```



----

### 深浅拷贝

```
https://juejin.im/post/6844904197595332622
https://segmentfault.com/a/1190000020255831
```

- **浅拷贝**

  创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝

  如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址 如果其中一个对象改变了这个地址，就会影响到另一个对象

- **深拷贝**

  将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象

  修改新对象不会影响原对象


---

#### 区别

**赋值**

把一个对象赋值给一个新的变量时，赋的其实是该对象的在栈中的地址，而不是堆中的数据

也就是两个对象指向的是同一个存储空间，无论哪个对象发生改变，其实都是改变的存储空间的内容，因此，两个对象是联动的

**赋值产生的两个对象，任一一个的任意数据发生变化，都会影响另外一个**

```js
var a = {
    num: 1,
    obj: {
        x: 1
    }
}

var b = a

console.log(b)	// { num: 1, obj: { x: 1 } }

a.num = 2
a.obj.x = 2

console.log(a)  // { num: 2, obj: { x: 2 } }
console.log(b)	// { num: 2, obj: { x: 2 } }
```

**浅拷贝**

重新在堆中创建内存，拷贝前后对象的值类型数据互不影响，但拷贝对象前后的引用类型共用同一块内存，因此浅拷贝对象内的引用类型会互相影响

**浅拷贝产生的两个对象，任意一个的引用类型数据发生变化，会影响另外一个**

```js
var a = {
    num: 1,
    obj: {
        x: 1
    }
}

var b = shallowClone(a)
console.log(b)	// { num: 1, obj: { x: 1 } }

a.num = 2
a.obj.x = 2

console.log(a)  // { num: 2, obj: { x: 2 } }
console.log(b)	// { num: 1, obj: { x: 2 } }
```

**深拷贝**

从堆内存中开辟一个新的区域存放新对象，对对象中的子对象（引用类型）进行递归拷贝,拷贝前后的两个对象互不影响

**深拷贝产生的两个对象，两个对象之间没有联系**

```js
var a = {
    num: 1,
    obj: {
        x: 1
    }
}

var b = deepClone(a)
console.log(b)	// { num: 1, obj: { x: 1 } }

a.num = 2
a.obj.x = 2

console.log(a)  // { num: 2, obj: { x: 2 } }
console.log(b)	// { num: 1, obj: { x: 1 } }
```

---

#### 浅拷贝实现

总体思想是新建一个对象，将目标对象内的所有数据复制进去

- **Object.assign()**

  `Object.assign()` 方法可以把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象

```js
var a = {
    num: 1,
    obj: {
        x: 1
    }
}

var b = Object.assign({}, a)
console.log(b)	// { num: 1, obj: { x: 1 } }

a.num = 2
a.obj.x = 2

console.log(a)  // { num: 2, obj: { x: 2 } }
console.log(b)	// { num: 1, obj: { x: 2 } }
```

- **展开运算符...**

  是`Object.assign()`的语法糖

```js
var a = {
    num: 1,
    obj: {
        x: 1
    }
}

var b = {...a}
console.log(b)	// { num: 1, obj: { x: 1 } }

a.num = 2
a.obj.x = 2

console.log(a)  // { num: 2, obj: { x: 2 } }
console.log(b)	// { num: 1, obj: { x: 2 } }
```

- **遍历对象**

  创建一个新的对象，遍历需要克隆的对象，将需要克隆对象的属性以此添加到新对象上

```js
function shallowClone(target) {
    let cloneTarget = {}
    for(const key in target) {
        cloneTarget[key] = target[key]
    }
    return cloneTarget
}
```

---

#### 深拷贝实现

总体思想还是创建一个新的对象，但是每一次碰到引用类型时，都需要创建一个新对象

- **解析为字符串再解析为对象**

  使用`JSON.stringify()`将对象处理成字符串，再通`JSON.parse`将将字符串处理成对象，这样相当于声明新的完全不一样的对象

  此方法的缺陷是经过`JSON.parse`处理后，正则变成空对象，函数变为null，因此无法处理正则和函数（可实现数组或对象的深拷贝，但是无法处理循环引用），

```js
var a = {
    num: 1,
    obj: {
        x: 1
    }
}
var b = JSON.parse(JSON.stringify(a))
console.log(b)	// { num: 1, obj: { x: 1 } }

a.num = 2
a.obj.x = 2

console.log(a)  // { num: 2, obj: { x: 2 } }
console.log(b)	// { num: 1, obj: { x: 1 } }
```

- **使用现成包**

  lodash包含了很多JS的现成方法

```js
var _ = require('lodash')

var a = {
    num: 1,
    obj: {
        x: 1
    }
}
var b = _.cloneDeep(a)	// 开箱即用
console.log(b)	// { num: 1, obj: { x: 1 } }

a.num = 2
a.obj.x = 2

console.log(a)  // { num: 2, obj: { x: 2 } }
console.log(b)	// { num: 1, obj: { x: 1 } }
```

- **亲自手写递归**

  遍历对象、由于不知道要拷贝的对象的层深度，因此可以用递归来解决问题

  - 如果是值类型，无需继续拷贝，直接返回

  - 如果是引用类型，创建一个新的对象，遍历需要克隆的对象，将需要克隆对象的属性执行深拷贝后一次添加到新对象上（即如果有更深层的对象就递归到直至属性为值类型）

**基础版本：**

只考虑普通object的情况

```js
function deepClone(target) {
    if(typeof target === 'object') {
        let cloneTarget = {}
        for(const key in target) {
            cloneTarget[key] = deepClone(target[key])
        }
        return cloneTarget
    } else {
        return target
    }
}
```

**考虑数组：**

只需在最开始的时候判断建立的是对象还是数组

```js
function deepClone(target) {
    if(typeof target === 'object') {
        let cloneTarget = Array.isArray(target) ? [] : {}
        for(const key in target) {
            cloneTarget[key] = deepClone(target[key])
        }
        return cloneTarget
    } else {
        return target
    }
}
```

**解决循环引用：**

当存在循环引用时（对象的属性间接或直接引用了自身的情况），递归会进入死循环导致栈溢出

```js
const target = { }
target.target = target
console.log(target)		// {target: [Circular]}	 Circular表示循环引用

deepClone(target)	// 死循环
```

可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系
当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题

这个存储空间，可以使用`key-value`形式的数据，因此可以选用`Map`或`WeaKMap`

- 检查map中有无克隆过的对象
- 有则直接返回，没有则将当前对象作为`key`，克隆对象作为`value`进行存储

```js
function deepClone(target, map = new WeakMap()) {
    if(typeof target === 'object') {
		let cloneTarget = Array.isArray(target) ? [] : {}
		if(map.get(target)) {
			return map.get(target)
		}
		map.set(target, cloneTarget)	// 重点
        for(const key in target) {
            cloneTarget[key] = deepClone(target[key], map)	// 将map往下传递
        }
        return cloneTarget
    } else {
        return target
    }
}
```

使用`WeakMap`而不选择`Map`的原因是，用`WeakMap`处理循环引用时，因为是弱引用，内存可以被GC释放，当拷贝的对象非常庞大时，使用`Map`会对内存造成非常大的额外消耗，而且需要手动清除`Map`的属性才能释放这块内存，而`WeakMap`则可以巧妙化解

**考虑其他数据类型**

上面只考虑了object和array两种数据类型，实际上所有的引用类型还有很多，需要特殊处理

判断是否为引用类型时，还需要考虑`function` 和 `null` 两种特殊的数据类型

```js
function isObject(target) {
    consst type = typeof target
    return target !== null && (type === 'object' || type === 'function')
}
```

获取数据类型：

```markdown
可以使用toString来获取准确的引用类型
	每一个引用类型都有toString方法，默认情况下，toString方法被每个Object对象继承
	如果此方法未被重写，则返回 "[object, type]"，其中type是对象的类型
	大部分引用类型（如Array、Data、RegExp）都重写了toString方法，可以直接调用Object原型上未被覆盖的toString方法，并使用cll来改变this的指向
```

```js
function getType(target) {
	return Object.prototype.toString.call(target)
}
```

<img src="https://img-blog.csdnimg.cn/20200914165539803.png" style="margin:0"/>

在上面集合中，可以简单分为两类：`可继续遍历的类型` 和 `不可遍历的类型`，需要为它们做不同的拷贝

可继续遍历的类型：

如——`object`、`arrary`、`Map`、`Set`等（目前只考虑这四种），它们的内存都还可以存储其他数据类型的数据，需要继续进行递归，可以通过`constructor`来获取它们的初始化数据，如`{}` 或 `[]`

```js
function getInit(target) {
    const Ctor = target.constructor
    return new Ctor()
}
```

改写后的深拷贝程序：

```js
// 定义可以继续遍历的类型
const mapTag    = '[object Map]'
const setTag	= '[object Set]'
const arrayTag	= '[object Array]'					
const object    = '[object Object]'

// 定义不可以继续遍历的类型
const boolTag   = '[object Boolean]'
const dateTag   = '[object Date]'
const errorTag  = '[object Error]'
const numberTag = '[object Numer]'
const regexpTag = '[object RegExp]'
const stringTag = '[object String]'
const symbolTag = '[object Symbol]'

```



---

### 防抖和节流

#### 防抖

触发高频事件后n秒内函数只会执行一次，如果n秒内高频事件再次被触发，则重新计算时间

使用场景：

- 登录、发短信等按钮避免用户点击太快，以致于发送了多次请求，需要防抖

- 调整浏览器窗口大小时，resize 次数过于频繁，造成计算过多，此时需要一次到位，就用到了防抖

- 文本编辑器实时保存，当无任何更改操作一秒后进行保存
- search搜索联想，用户在不断输入值时不进行联想，用防抖来节约请求资源，

**代码思路：每次触发事件时都取消之前的延时调用方法**

- **使用闭包保存一个标记，因为一直有对象引用该函数，因此闭包的标志不会因为函数执行后便销毁，得以保存**

- **用户每触发一次防抖函数，将计时清零，取消之前的计时器，重新计时**
- **延时结束后执行指定事件**

```jsx
function debounce(fn) {
      let timer = null; 		 // 通过闭包保存一个标记
      return function () {		// return函数，使传入参数的时候不会立即执行函数
        clearTimeout(timer);  // 每当用户输入的时候把前一个 setTimeout clear 掉
        timer = setTimeout(() => { 	// 然后又创建一个新的 setTimeout, 这样就能保证输入字符后的 interval 间隔内如果还有字符输入的话，就不会执行 fn 函数
          fn.apply(this, arguments);	// setTimeout中所执行函数中的this，永远指向window，所以需要改变指向，记得传入隐式参数arguments
        }, 500);
      };
    }

function sayHi() {
      console.log('防抖成功');
    }

var inp = document.getElementById('inp');
inp.addEventListener('input', debounce(sayHi)); 
```

---

#### 节流

高频事件触发，但在n秒内只会执行一次，所以节流会稀释函数的执行频率

使用场景：

- scroll 事件，每隔一秒计算一次位置信息等

- 浏览器播放事件，每个一秒计算一次进度信息等

- input 框实时搜索并发送请求展示下拉列表，每隔一秒发送一次请求 (也可做防抖)

**代码思路：每次触发事件时都判断当前是否有等待执行的延时函数**

- **使用闭包保存一个标记，因为一直有对象引用该函数，因此闭包的标志不会因为函数执行后便销毁，得以保存**

```jsx
function throttle(fn) {
      let timer = null; // 通过闭包保存一个标记
      return function () {
        if (timer) return; // 在函数开头判断标记是否为true，为true则return
        setTimeout(() => { // 将外部传入的函数的执行放在setTimeout中
          fn.apply(this, arguments);
          // 最后在setTimeout执行完毕后再把标记设置null表示可以执行下一次循环了
          timer = null;
        }, 500);
      };
    }

function sayHi(e) {
	console.log(e.target.innerWidth, e.target.innerHeight);
    }
window.addEventListener('resize', throttle(sayHi));
```
