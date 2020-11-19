## TypeScript

**优势：**

- 程序更容易理解（如在函数传入参数的时候限制传入类型）
- 效率更高
- 更少的错误
- 更好的包容性

### 安装

```
npm install -g typescript
```

```
现在不需要额外下载：npm install -g typings 
现在ts安装依赖包使用(@types/xxx)来安装而不用特地使用typings來安裝

如：npm install --save @types/core-decorators
就能把core-decorator声明d.ts文件下载，就不用安装typings
```

可在cmd输入`tsc` 来查看是否安装成功，安装成功则有反应

可通过`tsc +ts文件名`将ts文件编译成js文件

要在vscode的`run code`中直接运行ts文件，需要全局安装`ts-node`

```
npm install -g  ts-node
```



----

### 引入

使用ts写程序时，一般使用`import`来引入包而非`require`

原因是`import`引入的包会带上`@types/xxx`的声明文件，而`require`则不会

或者这样引用

```js
import xxx = require("xxx");
```



----

### 配置

建一个项目文件夹，如project，cd进该目录执行命令初始化依赖配置

```
npm init -y
tsc --init
```

安装必备包

```
npm install --save lodash @types/lodash
npm install --save @types/node
npm install --save-dev typescript 
```

通常更喜欢安装下面三个

```
npm install --save typescript
npm install --save ts-node
npm install --save @types/node
```

配置 `tsconfig.json`，实际上直接用初始化的就能用，很多配置信息都是默认配好的，如果发现功能没有达到再特地去配

```markdown
需要告诉 tsc 如下几件事情
	项目的根目录在哪？ （以 tsconfig.json 所在的目录为根目录，即项目根目录 )
	输入在哪？ （项目的哪些ts 文件是需要关心的，标准库/第三方库去哪里找）
	输出在哪？（如果不是前端项目，只需要每一个 ts 输出对应的 js 文件即可）
```

具体配置信息

```
https://www.jianshu.com/p/0383bbd61a6b
```

一个配置示例

```ts
{
    // tsconfig 所在的根目录, 则是一个project
    "compilerOptions": {
        "module": "commonjs", // 模块系统
        "target": "es2015",   // 生成目标, 一般选择ES6，因为不是客户端环境，没必要还编译成  ES5
        
        // 一组严苛的编译选项
        "noImplicitAny": false,
        "strictNullChecks": true,
        "strict": true,
        "alwaysStrict": true,
        "sourceMap": false,
        "noImplicitReturns": true,
        "noImplicitThis": true,
        "pretty": true,
        
        "listFiles": true,  // 包含了哪些库，这个必要的时候还是很有用的
        "listEmittedFiles": true, 
        "lib": [            // 要那些 lib，按需选择即可
            "es2016"
        ],
        // "noUnusedLocals": true,
        // "noUnusedParameters": true,
        // "noFallthroughCasesInSwitch": true,
        // 指定库的搜索路径，这个比较有用，一般会指定 @types，还可以按需添加
        "typeRoots": [
            "./node_modules/@types"
        ]
        // 库搜索路径下, 仅使用哪些库, 一般没啥用
        // "types": [
            
        // ]
    },
    // file include会算出一个交集, 指明哪些是项目的 ts 文件
    "include": [
        "./**/*"
    ],
    // 排除项目下面不符合要求的文件，这个按需设定即可，可以放心排除乱七八糟的文件
    "exclude": [
        "node_modules",
        "**/*.spec.ts",
        "*.js"
    ]
}
```

创建入口文件，新建文件夹 src 并在里面新建main.ts 文件

```tsx
// 先随便写点东西
function main() {
  
}
 
main();
```

随便写点代码测试配置是否生效

```tsx
// /src/utils.ts
import * as path from 'path';                     // 测试能否正常使用 Node 的内置模块
 
 
/**
 * 一个正常的class
 * 
 * 不得不说, TS 使用起来真是舒服,各种该有的东西都替你考虑到了
 * 很舒心
 */
export class NodeModuleTester {
  public static readonly STATIC_VAR = 'STATIC';   // 测试static变量
 
  constructor(                                    // 测试构造方法
    private readonly f1: string,
    private readonly f2: number) {
 
  }
 
  public static testPath() {                      // 测试静态方法
    const curdir = './';
 
    console.log(path.resolve(curdir));
  }
 
}
```

改写 main.ts ，直接执行 main.ts  文件，能成功打印则说明配置成功

```tsx
import {NodeModuleTester} from './src/utils';
 
function main() {
  const tester = new NodeModuleTester("s1", 1);
  console.log(NodeModuleTester.STATIC_VAR);
  NodeModuleTester.testPath();
}
 
main()
```

在 `package.json` 中增加命令启动和声明启动的入口文件 main

```json
"scripts": {
    "start": "ts-node ./src/main"
},
```

这样就能用 `npm run start` 来启动整个项目了



----

### 数据类型

原始类型：

- Boolean
- Null
- Undefined
- Number
- BigInt
- String
- Symbol

引用类型：

- Object

---

#### 声明类型的方式

使用`:`声明数据类型

变量值为 null 和 undefined 可以匹配任意类型（相当于这些其他类型的共同子类）

```
但是在真实的项目中，拥有tsconfig.json文件，该文件配置会更加严格的检验数据类型，此时如果将undefined赋值给int是不被允许的
```

any 类型可以匹配任意的数据类型，可以修改为任意数据类型，将一个 any 类型的变量传递任何数据类型结果都是 any 类型

**类型别名：**使用 `type` 给类型起别名

**联合类型：**使用  `|` 将类型规则隔开，表示该变量的值可为这些类型

**交叉类型：** 使用 `&` 将类型规则隔开，表示该变量的值必须满足所有的数据类型（一般用于对象）

```jsx
interface BaseButtonProps {
    className?: string;
    disabled?: boolean;
}

type NativeButtonProps = BaseButtonProps & React.ButtonHTMLAttributes<HTMLElement>
type AnchorButtonProps = BaseButtonProps & React.AnchorHTMLAttributes<HTMLElement>
export type ButtonProps = NativeButtonProps & AnchorButtonProps
```

**类型推论：**当声明变量并赋值没有指明类型时，会自动根据第一次赋值类型给上对应数据类型，后面再更改为其他数据类型时也会报错

```typescript
let isDone: boolean = false;

function (prams:any) {
    ...
}
    
let unite: number | string | boolean = 'string';
```

#### 数组

```typescript
let arrOfNumber: number[] = [1, 2, 3, 4];
```

**元组：若想按顺序规定数组内的元素类型时，使用元组**

但是不能写出长度

```typescript
let user: [string, number] = ['string', 1];
let user_: [string, number] = ['string', 1, 'string'];		// 报错
```

---

#### 函数

 `?` 可以表示函数的可选属性，但是非可选的参数不能排在可选参数后声明，当给函数参数设定默认值的时候也可表示可选

```typescript
function add(x: number, y: number = 10, z?: number) {
    if (typeof z === 'number') {
        return x + y + z 
    }
    else {
        return x + y
    }
}
```

声明返回值类型

声明为any或void时可以无返回值，如果不声明则使用第一次声明函数时的返回值作为规则

```typescript
function add(): number {
	...
}
```

---

#### !和?

**!用法**

用在变量前表示取反

用在变量后时，使null和undefined类型可以赋值给其他类型并通过编译，表示告诉编译器这里有值

```ts
let y:number

y = null		// 无法通过编译
y = undefined	// 无法通过编译

y = null!
y = undefined!
```

```ts
// 由于x是可选的，因此parma.x的类型为number | undefined，无法传递给number类型的y，因此需要用x!
interface IDemo {
    x?: number
}

let y:number

const demo = (parma: IDemo) => {
    y = parma.x!
    return y
}
```

如果存在空情况的判断并赋具体值时，可以不用`!`，但是如果要想令y存在等于`undefined`的情况还是需要用`!`

```ts
interface IDemo {
    x?: number
}

let y:number

const demo = (parma: IDemo) => {
    y = parma.x || 1	// 如果为undefined，返回y=1，如果不为undefined，则返回parma.x的值
    return y
}
```

 **?用法**

除了表示可选参数外

当使用A对象属性`A.B`时，如果无法确定A是否为空，则需要用`A?.B`，表示当A有值的时候才去访问B属性，没有值的时候就不去访问，如果不使用`?`则会报错

```ts
// 由于函数参数可选，因此parma无法确定是否拥有，所以无法正常使用parma.x，使用parma?.x向编译器假设此时parma不为空且为IDemo类型，同时parma?.x无法保证非空，因此使用parma?.x!来保证了整体通过编译
interface IDemo {
    x: number
}

let y:number

const demo = (parma?: IDemo) => {
    y = parma?.x!
    console.log(parma?.x)	// 只是单纯调用属性时就无需!	
    return y
}
    
// 如果使用y = parma!.x!是会报错的，因为当parma为空时，是不拥有x属性的，会报找不到x的错误
```

但是?用法只能读操作而不能写操作，对一个可能为空的属性赋值是不会被编译通过的，此时还需用用到类型断言

```ts
interface IDemo {
    x: number
}

// 编译报错，不能赋值给可选属性
const demo = (parma?: IDemo) => {
    parma?.x = 1	
}
    
// 使用类型断言  
const demo = (parma?: IDemo) => {
    let _parma = parma as IDemo
    _parma.x = 1
}
```





---

### 类

```typescript
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    say() {
        console.log(`my name is ${this.name}`);
    }
}

const lily = new Animal('lily');
lily.say();
```

#### 继承

和其他语言的继承差不多，能使用父类方法，也能重写父类的属性方法

```typescript
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    say() {
        console.log(`my name is ${this.name}`);
    }
}


class Cat extends Animal {
    eat() {
        console.log(`I am eating`);
    }    
}

const susie = new Cat('susie');
susie.say();
susie.eat();
```

重写方法：注意的是如果重写了父类方法，需要使用父类方法时，需要在构造函数中调用super方法，并在后续中用super去执行父类方法

```typescript
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    say() {
        console.log(`my name is ${this.name}`);
    }
}


class Cat extends Animal {
    constructor(name) {
        super(name);    // 如果父类构造函数需传入参数，则constructor和super也应该传入参数
    }
    say() {
        console.log(`I am saying`);
    }    
    animalSay() {
        super.say();
    }

}

const susi = new Cat('susie');
susi.say();          // I am saying
susi.animalSay();   // my name is susie
```

---

#### 公开与私有

- **public：**类成员可以被父子实例和子类访问到
- **private：**类成员不能被父子实例与子类访问到
- **protected：**类成员可以被子类访问到但是不能被父子实例访问到

```typescript
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    public say() {
        console.log(`my name is ${this.name}`);
    }
    private run() {
        console.log('I am runing');
    }
}

const cat = new Animal('cat');
cat.say()
cat.run()   // 报错
```

---

#### 静态

```typescript
class Animal {
    static say() {
        console.log(`hello`);
    }

}

Animal.say()
```

静态方法内只能使用类的静态成员，不能使用实例成员

```typescript
class Cat {
    age: number = 12;
    static food = 'fish';
    run() {
        this.age = 13;
    }

    static say() {
        this.age = 14;  // 报错，不能使用实例成员
        this.food = 'can';
    }
}
```

---

### 接口

- 对对象的形状（shape）进行描述
- 对类（class）进行抽象
- 鸭子类型（模糊推断判断，更关注对象能否使用而非对象的类型本身）
- 接口内的方法不能实现，即只能拥有函数声明而不能拥有函数体
- 一个类只能继承一个类，但可接收多个接口并实现

#### 规定对象

由于接口实例必须实现接口的全部内容，因此可以方便定义对象属性类型

```typescript
interface Person {
    name: string;
    age: number;
}

let mary: Person = {
    name: 'mary',
    age: 12
}
```

实现接口属性不能多也不能少，如果有些属性是非必须实现的，应该使用 `?` 表示该属性为可选属性

```typescript
interface Person {
    name: string;
    age?: number;
}

let mary: Person = {
    name: 'mary',
}
```

只读属性：用 `readonly` 声明，只能在实现接口的时候赋值，后面更改赋值会报错

```typescript
interface Person {
    readonly name: string;
    age?: number;
}

let mary: Person = {
    name: 'mary',
}

mary.name = 'jack';		// 报错 
```

----

#### 配合类

方便灵活，规定约束了具体哪些类中必须实现的哪些功能

```typescript
interface Radio {
    switchRadio():void;
}

interface Battery {
    checkBatteryStatus();
}

class Car implements Radio {
    switchRadio() {
        console.log('实现Car的switchRadio');
    }
}

class Cellphone implements Radio, Battery {
    switchRadio() {
        console.log('实现Cellphone的switchRadio');
    }
    checkBatteryStatus() {
        console.log('实现Cellphone的checkBatteryStatus');
    }
}
```



----

### 枚举

枚举本质上是用一串有顺序的 int 数值去依次表示枚举里的内容

```typescript
enum Direction {
    Up,
    Down,
    Left,
    Right
}
console.log(Direction.Up)       // 0
console.log(Direction.Down)     // 1
console.log(Direction.Left)     // 2
console.log(Direction.Right)    // 3

console.log(Direction[0])    // Up
console.log(Direction[1])    // Down
console.log(Direction[2])    // Left
console.log(Direction[3])    // Right
```

当给具体某个属性赋值时，后面的属性会依据这个赋值数字往下递增赋值

```typescript
enum Direction {
    Up,
    Down = 6,
    Left,
    Right
}
console.log(Direction.Up)       // 0
console.log(Direction.Down)     // 6
console.log(Direction.Left)     // 7
console.log(Direction.Right)    // 8
```

**枚举实现**

```typescript
enum Direction {
    Up,
    Down,
    Left,
    Right
}

// 等同于下面的js代码
var Direction;
(function (Direction) {
    // 将Direction对象新增一个属性up，并赋值0，然后再新增一个属性0 并赋值up
    Direction[Direction["Up"] = 0] = "Up";		
    Direction[Direction["Down"] = 1] = "Down";
    Direction[Direction["Left"] = 2] = "Left";
    Direction[Direction["Right"] = 3] = "Right";
})(Direction || (Direction = {}));

/* 
	最终Direction对象的结果:
     { 
          '0': 'Up',
          '1': 'Down',
          '2': 'Left',
          '3': 'Right',
          Up: 0,
          Down: 1,
          Left: 2,
          Right: 3 
      }
*/
```

#### 常量枚举

常量枚举仅可在属性、索引访问表达式、导入声明的右侧、导出分配或类型查询中使用

可提升枚举性能

```typescript
enum Direction {
    Up = 'Up',
    Down = 'Down',
    Left = 'Left',
    Right = 'Right'
}
var value = 'Up';
if(value === Direction.Up) {
    console.log('do Up')
}

// 转换

var Direction;
(function (Direction) {
    Direction["Up"] = "Up";
    Direction["Down"] = "Down";
    Direction["Left"] = "Left";
    Direction["Right"] = "Right";
})(Direction || (Direction = {}));

var value = 'Up';
if (value === Direction.Up) {
    console.log('do Up');
}

```

```typescript
const enum Direction {
    Up = 'Up',
    Down = 'Down',
    Left = 'Left',
    Right = 'Right'
}
var value = 'Up';
if(value === Direction.Up) {
    console.log('do Up')
}

// 转换

var value = 'Up';
if (value === "Up" /* Up */) {
    console.log('do Up');
}

```



---

### 泛型

在使用函数或类时，声明时不指定属性的类型，使用的时候再声明属性类型

```typescript
function echo<T>(arg: T): T {		// 变量名是任意的，但是普遍使用 T 来表示泛型
    return arg;
} 
```

根据在实际调用的时候传入的值来判断返回类型

```typescript
const result = echo(true);		// 此时该函数的返回值为 boolean 类型
const result  = echo('str');	// 此时该函数的返回值为 String 类型
```

如果不使用泛型，则想让一个函数能匹配任意类型的数据，则会返回 any 类型

```typescript
function echo(arg) {
    return arg;
} 
const result1 = echo(true);		// 函数返回值类型为 any
const result2 = echo('str');	// 函数返回值类型为 any
```

这样的坏处是对返回值没有数据类型的约束，可以随便更改其类型

如果使用泛型，由于已经限制了类型，因此更改返回数据的类型会报错

```typescript
function echo(arg) {
    return arg;
} 
var result = echo(true);		
result = 'srt';		// 不会报错	
```

---

#### 泛型元组

```typescript
function swap<T, U>(tuple: [T, U]): [U, T] {
    return [tuple[1], tuple[0]]
} 

const result = swap([true, 'srt'])		// result 的类型为 [String, Boolean]
```

---

#### 约束泛型

限制一部分传入泛型的类型

```typescript
function echoWithArr<T>(arg: T): T  {
    console.log(arg.length);	// 由于不是所有的类型都具有 length 属性，因此那么写会报错
    return arg;
} 
```

简易改写：

即限制了传入即返回值必须为一个数组类型，数组里的元素是泛型可任意

```typescript
function echoWithArr<T>(arg: T[]): T[]  {
    console.log(arg.length)
    return arg
} 
```

但是这样写很不方便，因此使用约束泛型的写法，由于继承接口，接口内的成员必须被实现，因此实际上限制了传入参数 arg 必须要拥有 length 且数据类型为 number 才可被传入

这是接口的鸭子类型特性的一种应用

```typescript
interface IwithLength {
    length: number
}

function echoWithLength<T extends IwithLength>(arg: T): T {
    console.log(arg.length)
    return arg
}
```

---

#### 类、接口配合泛型

**类**

```typescript
class Queue<T> {
    private data = [];
    push(item: T) {
        return this.data.push(item);
    }
    pop(): T {
        return this.data.pop()
    }
}

// 在实例化的时候声明类的泛型所指数据类型
const queue1 = new Queue<number>()
const queue2 = new Queue<boolean>()

queue1.push(3)
queue1.push(true)   // 报错
```

**接口**

```typescript
interface keyPair<T, U> {
    key: T;
    value: U;
}

let kp1: keyPair<number, string> = {
    key: 123,
    value: 'str'
}

let kp2: keyPair<boolean, number> = {
    key: true,
    value: 123
}
```

规范函数

```typescript
interface IPlus<T> {
    (a: T, b: T): T
}
function plus(a: number, b: number): number {
    return a + b;
}
function connect(a: string, b: string): string {
    return a + b;
}

const funPlus: IPlus<number> = plus;
const funConnect: IPlus<string> = connect;

// 如果不用泛型接口，想要规范的给变量赋予函数时，应该这样写，使用泛型接口可以省了很多代码
const funPlus: (a: string, b: string) => string = plus;
```

----

#### 枚举技巧

**强制定义类型**

比如函数：一般而言，会根据实际在使用中传递的数据编译器会推断出泛型的类型，但是有些情况则不能推测出来导致报错

如一个由参数来判断类型的函数，当参数设为可选且在调用时不传递参数时，则编译器会判断为这个类型是未知的（并不是判断为any）

```typescript
function test<T>(parms?:T):T {
    return parms
}

function demo(parms:number) { }

const num = test()	// num的类型为unknown

demo(num)	// 报错，unknown类型不匹配number类型
```

这个时候，可以在调用函数的时候用 `<>` 声明此次调用函数泛型T所指的具体属性

```typescript
function test<T>(parms?:T):T {
    return parms
}

function demo(parms:number) { }

const num = test<number>()

demo(num) 	// 不会报错，虽然值为undefined，但是是属于number类型的undefined值
```

-----

**默认类型**

当编译器无法根据条件推测出泛型的具体类型时，会指定为 `unknown`，如果不想要这种情况，可以手动地在泛型定义时指定一个默认类型，这样编译器如果无法判断类型时，不会为 `unknow` 而会是指定的默认类型

```typescript
function test<T = number>(parms?:T):T {
    return parms
}

function demo(parms:number) { }

const num = test()

demo(num) 
```

---

#### typeof

在基本用法中 `typeof` 会返回一个该变量的数据类型，但是用在类型中时，表示该变量的类型

```ts
interface IDemo {
    x: number,
    y: string,
}

const obj: IDemo =  {
    x: 1,
    y: '2',
}

let demo1 = typeof obj     // object
type demo2 = typeof obj    // IDemo
type demo3 = obj 		   // 不能直接那么写，因为编译器无法将变量识别为类型，需要用typeof告诉编译器这是取该变量的类型	
```

常用于类型泛型中

```ts
type Demo<T> = number & T;

interface ITest {
    x: string;
} 

function test(parmas: ITest) {
    return parmas
}

type Result1 = Demo<test>                       // 值无法当做类型
type Result2 = Demo<typeof test>                // number & ((parmas: ITest) => ITest)
type Result3 = Demo<ReturnType<typeof test>>    // number & Itest
```





---

### 操作类型

#### 类型别名

使用 `type` 声明要注明的类型别名

```typescript
type PlusType = (x: number, y: number) => number;
function sum(x: number, y: number): number {
    return x + y;
}

const sum_: PlusType = sum;
// 等同于
const sum_: (x: number, y: number) => number = sum;
```

联合别名

```tsx
type NameResolver = () => string;
type NameOrResolver = string | NameResolver

// 判断传入的类型是一个返回值为string的方法还是一个字符串
function getName(n: NameOrResolver): string {
    if(typeof n === 'string') {
        return n
    }else {
        return n()
    }
}
```

---

#### 类型断言

当编译器不清楚数据的类型时，会默认采取类型推论的策略给对应代码添加上数据类型，如果自己本身比编译器更清楚代码应该拥有的数据类型，则使用类型断言去覆盖编译器的类型推论，即编译器不清楚数据类型时，会直接采用自定义的类型断言，而非去推论类型

```tsx
// 由于类型是string或者number，因此编译器无法确定input是否会有length属性，number 则无
function getLength(input: string | number): number {
    const str = input
    if(str.length) {	// 这里会报错
        return str.length	
    }else {
        const number = input
        return number.toString().length		// 这里不会，因为str也可以有toString()方法
    }
}
```

使用类型断言，关键字 `as` 且数据类型是类（大写开头）

```tsx
function getLength(input: string | number): number {
    const str = input as String		// 先将不确定的input断言为string类型进行操作，防止后面的代码报错
    if(str.length) {
        return str.length
    }else {
        const number = input as Number
        return number.toString().length
    }
}
```

断言的快捷写法：在变量前加上 `<类型>`  ，注意这里的类型又是小写了

```tsx
function getLength(input: string | number): number {
    if((<string>input).length) {
        return (<string>input).length
    }else {
        return (<number>input).toString().length
    }
}
```

类型断言并非类型转换，它不能推断为非法的类型

```typescript
function getLength(input: string | number): boolean {
    return (<boolean>input)		// 报错，
}
```

----

#### 预定义条件类型

TS提供了几种内置的预定义的条件类型

- **Exclude<T, U>**

   用于从类型T中去除在U类型中的成员

```ts
type Demo = Exclude<A|B|C|D, A|C,E>		// Demo获取的类型为 B|D
```

- **Extract<T, U>** 

  用于从类型TU中的交集

```ts
type Demo = Extract<A|B|C|D, A|C,E>		// Demo获取的类型为 A|C
```

- **NonNullable<T>** 

  用于从类型T中去除undefined和null类型

```ts
type Demo = Extract<A|B|undefined|null>	// Demo获取的类型为 A|B
```

- **ReturnType<T>** 

  获取函数类型的返回类型

```ts
const test = () => {
    return 1
}
type Demo = ReturnType<typeof test>			// Demo获取的类型为 number，注意要加上typeof，否则会将test识别为类型
```

- **InstanceType<T>** 

  获取构造函数的实例类型

```ts
class A {
    x=0
    y=0
}
type Demo = Instance<typeof A> 			// Demo获取的类型为 A	
```





----

### 命名空间

```
内部模块称作命名空间，外部模块则简称为模块
```

#### 模块和命名空间

**命名空间**

- 在代码量较大的情况下，为了避免各种变量命名相冲突，可将相似功能的函数、类、接口等放置到命名空间内
- 不同文件下同一个命名空间内的变量不能同名，但是这不代表可以直接使用另外一个文件的代码
- 本质上是闭包，用来隔离作用域

```typescript
// namespace中可以定义任意多的变量，这些变量只能在shape下可见，如果要在全局内可见的话就要使用export关键字，将其导出
namespace Shape {
    const pi = Math.PI
    export function cricle(r: number) {
        return pi * r ** 2
    }
}
```

- 一个文件下的内容，如果想要另外一个文件能访问到这部分代码，应该使用 `export` 导出到外部空间（无论是否同一个命名空间）

如果AB文件同属于一个命名空间，当 B 想要引用 A 方数据的时候，应该使用 `///<reference path='A.ts' />`

```typescript
// A.ts
namespace Demo {
    export interface A {
        foo() 
    }
}
```

```typescript
// B.ts
///<reference path="A.ts"/>
namespace Demo {
    class B implements A  {
        foo() {
            console.log('实现A接口的foo方法')
        }
    }
    const b = new B()
    b.foo()     // 实现A接口的foo方法
    
}
```

如果AB文件不属于同一个命名空间，不仅要使用 `///<reference path='A.ts' />` 引入文件，还要注明在导出的对象前加上命名空间去使用

```typescript
// A.ts
namespace Demo_A {
    export interface A {
        foo() 
    }
}
```

```typescript
// B.ts
///<reference path="A.ts"/>
namespace Demo_B {
    class B implements Demo_A.A  {
        foo() {
            console.log('实现A接口的foo方法')
        }
    }
    const b = new B()
    b.foo()     // 实现A接口的foo方法
}
```



---

**模块**

- 模块在其自身的作用域里执行，而不是在全局作用域里，意味着定义在一个模块里的变量，函数，类等只有引入模块以后才能使用
- 只要在文件中使用了 import 和 export 语法，就被编译器视为一个模块
- 一个完整功能的封装，对外提供的是一个具有完整功能的功能包
- 一个模块里可能会有多个命名空间
- 导出声明：任何声明都能通过添加 export 关键字来导出（如：变量，函数，类，类型别名，或接口）

```
为了支持CommonJS和AMD的exports, TypeScript提供了export =语法
export = 语法定义一个模块的导出对象(这里对象是指：类，接口，命名空间，函数或枚举)
若使用export = 导出一个模块，则必须使用TypeScript的特定语法import module = require("module")来导入此模块
```

```typescript
class Demo {
	...
}
export = Demo;
```

```typescript
import demo = require("./Demo");
```

- 模块的一个特点是不同的模块永远也不会在相同的作用域内使用相同的名字
- 不应该对模块使用命名空间，使用命名空间是为了提供逻辑分组和避免命名冲突，模块文件本身已经是一个逻辑分组，并且它的名字是由导入这个模块的代码指定，所以没有必要为导出的对象增加额外的模块层



----

### 声明文件

**使用声明文件的原因**

TypeScript 作为 JavaScript 的超集，在开发过程中不可避免要引用其他第三方的 JavaScript 的库

虽然通过直接引用可以调用库的类和方法，但是却无法使用TypeScript 诸如类型检查等特性功能

为了解决这个问题，需要将这些库里的函数和方法体去掉后只保留导出类型声明，而产生了一个描述 JavaScript 库和模块信息的声明文件，通过引用这个声明文件，就可以借用 TypeScript 的各种特性来使用库文件了



- 声明文件只能声明不能实现

```
declare var 声明全局变量
declare function 声明全局方法
declare class 声明全局类
declare enum 声明全局枚举类型
declare namespace 声明全局对象（含有子属性）
interface 和 type 声明全局类型
```



通常第三方库都会自己设置好了声明文件，只需直接下载就好，只需在下载的指定包名前加上 `@types/` 即可

```
npm install @types/moduleName --save-dev
```

----

