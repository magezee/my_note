## 编程

### props和state

**使用思想：**

```
state和props主要的区别在于props是不可变的,而state可以根据与用户交互来改变
props只用于传递数据而不用于修改
state用于更新和修改自身数据
当需要对传递进来的props进行修改操作时，应先将其赋值给state，然后对state进行修改
```

**props**

进行数据传递，props是一个对象，渲染组件时传递到组件的值都会储存在此

使用：

```jsx
class A extends Component {
    render() {
        return (
        	<B title = '标题'></B>	{/* 向子组件的props传递数据并储存 */} 
            <C text = '内容'></C>		 
        )
    }
}

// 类组件
class B extends Component {
    render() {
        return (
        	<p>{this.props.title}</p>
            <p>{this.props.name}</p>
        )
    }
}

// 函数式组件
const C = (props) => {
    return (
    	<p>{props.text}</p>		{/* 注意没有this */} 
    )
}
        
B.defaultProps = {		// 设置类组件的默认props属性，函数式组件没有，但是一般不用这个而用state
	name:'默认值'        
}
```

```
props.chilren为HTMLinner
即<div>abc</div>，想要获取到abc，则使用props.chilren
```

**state**

更改自身内部数据，更新

```jsx
class Test extends Component {
    constructor() {
        super()		// 调用父类构造函数，若不加上，则无法正常使用this
        this.state = {
            title: '待办事项列表',
            todos: [{		// 传递数组
                    id: 1,
                    title: '吃饭',
                    Completed: true
                },{
                    id: 2,
                    title: '睡觉',
                    Completed: false
                }
            ],
            article: '<div><i>ArticleText</i></div>'	
        }
    }
    render () {
        return(
            {/* 注意：要传递html元素格式时，可用dangerouslySetInnerHTML传递一个对象属性，其中语法为__html:xxxx */}
        	<div dangerouslySetInnerHTML={{__html:this.state.article}}/>     
            
            {/* 渲染列表时，可以使用map的方式 -- 提供一个方法，原数组中的每一个元素都会执行该方法，并且生成一个新的数组返回（若有接受变量） */
            <div>
                {
					this.state.todos.map(todo => {
						return <div key={todo.id}>{todo.title}</div> 
                    })
                }
            </div>
 

                    }
        )
    }
    
    fun = () => {
        this.setState({
            title: '修改后的 + 'this.state 
        })
    }
    
}
```



---

#### setState机制

在构造函数里定义state，修改state时需要用到setState ( )，不可直接通过`this.state`去修改状态，这样子不会更新组件（在`constructor`函数中可用`this.state`去初始化）

setState()可有两个参数，第一个参数可为对象或者方法，第二个参数可选，为一个回调函数

```jsx
// 对象形式
this.setState({
    state: xxx   
})

// 函数形式：需要return一个对象
//默认传进prevState和props形参 表示了传进来时未被修改的state对象和props
this.setState((preState, props) => {
    ...
    return {
        state: xxx
    }
})
```

**生命周期**

调用setState之后，如果`shouldComponentUpdate`返回true，则会依次调用更新阶段的声明周期函数，所以如果在 `render` 、 `componentWillUpdate` 和 `componentDidUpdate` 中调用了 setState方法，则可能会造成循环调用，最终导致浏览器内存占满后奔溃

**同步与异步**

- `setState`的实现并没有涉及到任何的异步API

- 真正更新组件`state`的是`flushBatchedUpdates`函数，而`setState`不一定会调用这个函数，有可能多次`setState`调用一次这个函数

- `setState`会不会立刻更新`state`取决于调用`setState`时是不是已经处于批量更新事务中
- 组件的生命周期函数和绑定的事件回调函数都是在批量更新事务中执行的

```
为了优化性能，在一个事件函数中，不管setState()被调用多少次，在函数执行结束以后，被归结为一次重新渲染, 这个等到最后一起执行的行为被称为batching
```

即：触发了setState后，可能不会立刻触发更新操作，而是将多个更改后的state排成一个队列，在渲染的时候批量处理，直到下一次`render`调用或`shouldComponentUpdae`返回false时，才会进行state的更新

**可以简单理解为，更新状态发生在一次时间的同步操作后，异步操作前，且异步函数中的状态是立即更新的，整个setState函数的执行机制也是如此**

```jsx
// count = 0
handler = () => {
    this.setState({count: this.state.count +1})
    console.log(this.state.count)   // 0

    setTimeout(() => {
        this.setState({count: this.state.count +1})
        console.log(this.state.count)   // 2
    })
}
```

因此要获得最新的state

- 在setState中使用回调函数
- 在异步函数中执行setState
- 在componentDidUpdate中获取

```jsx
// 使用回调函数获取最新的state
this.setState(
    (prevState, props) => {
        consoloe.log('第一步：' + this.state.isLiked)
        return { isLiked: !prevState.isLiked }
    }, 
    () => {
    	console.log('第二步：' + this.state.isLiked)
	}	
)

console.log('第三步：' + this.state.isLiked)

// 假设state初始状态为false，运行结果为 第三步：false → 第一步：false → 第二步：true
```

**合并**

每次setState实际上都是会执行的，然而执行之后的值没有立马更新state，最终会在异步操作发生前将最终一个state进行统一更新

如果是通过state来设置新的state的话，由于还没有发生更新，每次从state里取的值都是最初的值，因此执行了三遍 `count:this.state.count+1` 结果仍为1

```jsx
// num=0,count=0
handler = () => {
    this.setState(() => {
        console.log('执行了')
        return {num:1, count:this.state.count+1}
    })

    this.setState(() => {
        console.log('执行了')
        return {num:2, count:this.state.count+1}
    })

    this.setState(() => {
        console.log('执行了')
        return {num:3, count:this.state.count+1}
    })     
}

render() {
    console.log('更新了')
    ...
}
    
// 最终结果：打印'执行了'三遍，更新了一遍，表示setState都会执行，但是只会更新一次
//  {num:3, count:1}
```

因此一般这种情况需要用到preState来处理state变化而不是直接取state，此时需要在setState传入函数

```jsx
handler = () => {
    this.setState((preState) => {
        return {num:1, count: preState.count + 1}
    })
    this.setState((preState) => {
        return {num:2, count: preState.count + 1}
    })
    this.setState((preState) => {
        return {num:3, count: preState.count + 1}
    })
}
// {num:3, count:3}
```

**在一个事件中直接使用setState：**

- 和周围代码块是异步的关系

- setState全部执行完毕后，将最终state整合才进行更新state
- 重新渲染在全部state都执行过后

**在一个事件中的异步使用setState：**

- 和周围代码块是同步的关系

- 立即更新state
- 重新渲染在每一次setState执行之后

```jsx
// count初始值为0
handler = () => {
        this.setState({count: this.state.count + 1})
        console.log(this.state.count)	// 0

        this.setState({count: this.state.count + 1})
        console.log(this.state.count)	// 0

        setTimeout(() => {
            this.setState({count: this.state.count + 1})
            console.log(this.state.count)	// 2
        })

        setTimeout(() => {
            this.setState({count: this.state.count + 1})
            console.log(this.state.count)	// 3
            this.setState({count: this.state.count + 1})
            console.log(this.state.count)	// 4
        })

    }

componentDidUpdate() {
    console.log('componentDidUpdate执行')
}

render() {
    console.log('更新了')
}
    

// 执行结果：由setState引起了四次更新
// 可以看出，没有放在异步函数中的setState方法最终会在异步操作前整合更新state从而出现渲染组件
// 而异步函数中的setState方法则是每一次都立即更新（即使在同一个异步函数中有多个setState也是如此），且在完成一次更新后才执行后续代码
```

<img  src="https://img-blog.csdnimg.cn/20200917172027429.png" style="margin:0;width:400px"/>





----

### 事件

**匿名函数事件的声明和绑定**

```jsx
handlEvent = () => {
    console.log('事件启动')
}

<button onClick = {this.handEvent} />
```

**非匿名函数事件的声明和绑定**

这种写法在绑定事件时this的指向会发生改变 因此要加上bind方法

```jsx
handleEvent() {
    console.log('事件启动')
}

 <button onClick={this.handleEvent.bind(this)} />
```

 由于这种写法也会在每次有事件的时候就会执行一次bind，过于累赘，因此一般在只会执行一次的构造函数里就进行绑定，然后再在元素里就无需再次绑定

```jsx
constructor() {
     this.handleEvent = this.handleEvent.bind(this)
}

<button onClick={this.handleEvent} />
```

被绑定的事件函数触发时会返回一个event对象，用形参来接收

```jsx
handleInputChange = (event) => {
    this.setState({
        //event.currentTarget指向当前触发事件的对象
        inputValue: event.currentTarget.value 
    })
}
```

----

**需要用bind的详细原因：**

在JavaScript中，以下两种写法是不等价的：

```jsx
let obj = {
    tmp:'Yes!',
    testLog:function(){
        console.log(this.tmp);
    }
};

obj.testLog();      // 方法一：此时为obj调用testLog函数，其中的this指向obj，所以结果为Yes

let tmpLog = obj.testLog;        // 方法二
tmpLog();       // 此时为window调用tmpLog函数，其中this指向window，但window没有定义tmp，所以结果为undefined
```

bind 方法确保了第二种写法与第一种写法相同

假设：当render里如果出现一个`onClick={this.event}`的实际执行步骤：

```js
let event = this.event
onClick={ event }
```

相当于在全局新建了一个`event`变量指向该组件实例的方法，然后点击时再执行这个方法，因此this指向了window

```js
// 使用bind相当于
let event = this.event.bind(this)
```

```jsx
class Counter extends Component {
  constructor(props) {
    super(props);
    //如果没有constructor里的bind，该this指向window
    this.event = this.event.bind(this);   
  }

  event() {
    // 因为绑定到了组件，才能正常使用this.props调用出组件的props对象
    console.log(this.props.xxx)
  }

  render() {
    return (
      <p>
        <button onClick={this.event} />  
      </p>
    )
  }
}

```

如果不喜欢在构造函数里用bind，那么可以在回调中使用一个箭头函数：

```jsx
event() {
    console.log(this.props.xxx)
}
...
 <button onClick={(e) => this.event(e)} />  
```

这个语法的问题是，每次`buttion`组件渲染时都创建一个不同的回调，在多数情况下，没什么问题

然而，如果这个回调被作为 prop(属性) 传递给下级组件，这些组件可能需要额外的重复渲染

于是在构造函数中进行绑定，以避免这类性能问题

**由于会有那么多问题，因此最直接的解法就是，声明函数使用箭头函数**

由于箭头函数的this不会因为被谁调用而改变，由于指向它的上一级传递而来的this，上一级的this是又new的组件实例传递来的，因此无论谁直接调用该方法，this都只会指向组件实例

```jsx
event = () => {
    console.log(this.props.xxx)
}
...
 <button onClick={this.event} />  
```



----

### 组件交流的技巧

**含参数方法**

一般在声明带参数的方法时，react的思想是将方法通过props传递到子层来实现（这种方式一般用在嵌套三层及以内的组件）

从父组件将一个方法传递到子组件，在子组件中调用该方法，这样在实际调用的方法就不会有形参存在，也不必使用bind去传递形参

父组件：

```jsx
fun = (parms) => {
    console.log(parms)
}
<child fun = {this.fun}/>	{/* 将方法传递给子组件 */}
```

子组件：

```jsx
eventFun = () => {
    this.props.fun(this.state.parms)	// 在子方法里面调父方法
}
<div onClick={this.eventFun}/>
```

```jsx
// 应避免在调用方法时出现形参和使用bind，当然也不是完全不能这样用
evetnFun = (parms) => {
    
}

<div onclick = {this.eventFun.bind(this,parms)}/>
```

也可以直接再返回一个带参函数

```jsx
<div onClick={() => this.fun('xxx')}/>
```



----

### 装饰器

修饰器（Decorator）是一个函数，用来修改类的行为，使用时放在需要修改的类之前，且`@Decorator`后不可添加分号

```jsx
// 定义一个函数，也就是定义一个Decorator，target参数就是传进来的Class。
function testable(target) {
  target.isTestable = true;		// 这里是为类添加了一个静态属性
}

// 在Decorator后面跟着Class(紧写在class上一行)
// 要Decorator后面是Class，默认就已经把Class当成参数隐形传进Decorator了
// @testable MyTestableClass 相当于 testable(MyTestableClass)
@testable
class MyTestableClass {}

console.log(MyTestableClass.isTestable) // true
```

嵌套传参，在外层套函数，只要最后返回的是一个Decorator即可

```jsx
function testable(isTestable) {
  return function(target) {
    target.isTestable = isTestable;
  }
}

// 注意这里，隐形传入了Class，语法类似于testable(true)(MyTestableClass)
@testable(true)
class MyTestableClass {}
MyTestableClass.isTestable // true

@testable(false)
class MyClass {}
MyClass.isTestable // false
```

修改类的原型

```jsx
function testable(target) {
  target.prototype.isTestable = true;
}

@testable
class MyTestableClass {}

let obj = new MyTestableClass();
obj.isTestable // true
```

装饰器不仅可以修饰类，还可以修饰类的属性

```jsx
// 假如修饰类的属性则传入三个参数，对应Object.defineProperty()里三个参数，具体不细说
// target为目标对象，对应为Class的实例
// name为所要修饰的属性名，这里就是修饰器紧跟其后的name属性
// descriptor为该属性的描述对象
// 这里的Decorator作用是使name属性不可写，并返回修改后的descriptor
function readonly(target, name, descriptor){
  descriptor.writable = false;
  return descriptor;
}

class Person {
  @readonly
  property() { return `${this.first} ${this.last}` }
}
```

```jsx
//定义一个Class并在其add上使用了修饰器
class Math {
  @log
  add(a, b) {
    return a + b;
  }
}

//定义一个修饰器
function log(target, name, descriptor) {
  //这里是缓存旧的方法，也就是上面那个add()原始方法
  var oldValue = descriptor.value;

  //这里修改了方法，使其作用变成一个打印函数，最后依旧返回旧的方法
  descriptor.value = function() {
    console.log(`Calling "${name}" with`, arguments);
    return oldValue.apply(null, arguments);
  };

  return descriptor;
}

const math = new Math();
math.add(2, 4);
```

**React里使用装饰器**

```
只有类组件才可以使用装饰器
类组件Class，应用组件的时候React隐性帮你实例化了了，因此看不到new语法 
```

```jsx
//假如有这么一个页面组件，用于显示用户资料的，当从Home组件进去到这个组件时
//希望title从“Home Page”变成“Profile Page”
//注意这里隐形传入了组件，语法类似setTitle('Profile Page')(Profile)
@setTitle('Profile Page')
class Profile extends React.Component {
    //....
}

//开始定义装饰器
//title参数对应上面的“Profile Page”字符串
//WrappedComponent参数对应上面的Profile组件
//然后在组件加载完修改了title，在返回一个新组件，类似高阶组件
const setTitle = (title) => (WrappedComponent) => {
   return class extends React.Component {
      componentDidMount() {
          document.title = title
      }
      render() {
         return <WrappedComponent {...this.props} />
      }
   }
}
```

**Redux应用装饰器**

```jsx
//定义一个组件
class Home extends React.Component {
    //....
}

//以往从状态树取出对应的数据，让后通过props传给组件使用通过react-redux自带的connect()方法
export default connect(state => ({todos: state.todos}))(Home);

//使用装饰器的话就变成这样，好像没那么复杂
@connect(state => ({ todos: state.todos }))
class Home extends React.Component {
    //....
}
```



-----

### 高阶组件

其实就是一个方法接收一个组件，经过加工后返回一个新的组件

```jsx
const withUser = (WrappedCompoment) => {
    const user = 'add user info'	// 增加一个props属性为例
    return (props) => <WrappedCompoment user={user} {...props}/>	// 新增一个props，将组件之前的props的所有属性也一并传递 
}
    
const UserPage = (props) => {
    <div>My name is {props.user} </div>
}

withUser(UserPage)	// Userpage增加了一个user的props属性
```

一般用于内容少但是需要重复使用的组件（相当于一种小模板），如商标

**范例：**

```jsx
// withCopyright.js
import React, {Component} from 'react'

// 在withCopyrigh(YourComponent)后，任意YourComponent组件都会在渲染时执行return的方法 达到了每个组件都能在渲染时渲染<div>&copy; 2020 &nbsp;&emsp;夜夜笳</div>的效果
const withCopyright = (YourComponent) => {
    return class withCopyright extends Component{
        render() {
            return (
                <> 
                    {/* 为了实现props的传递，必须在这里将props的值往下继续传递    ...this.props --获取父组件所有的属性让自己拥有这些属性 */}                   
                    <YourComponent {...this.props}/>    {/* 高阶组件可以劫持低阶的渲染，最终只会显示高阶组件的渲染内容，为了不这样，需要在内部再渲染一次低阶组件  */}
                    <div>&copy; 2020 &nbsp;&emsp;夜夜笳</div>
                </>
            )
        }
    }
}

export default withCopyright
```

```jsx
// Another.js
import React, { Component } from 'react';
import withCopyright from './withCopyright'

class Another extends Component {
    render() {
        return (
            <div>
                Another的信息{this.props.name}
            </div>
        );
    }
}

export default withCopyright(Another)
```

```jsx
// App.js
import React, { Component } from 'react'
import withCopyright from './withCopyright'
import Another from './Another'

class App extends Component {
    render() {
        return (
            <div>
                <Another name="组件"/>         
                这是app组件的渲染信息
            </div>
        );
    }
}

export default withCopyright(App);

```

```jsx
// index.js
import React from 'react'
import {render} from 'react-dom'

import App from './App.js'

render(
    <App />,
    document.querySelector('#root')
)
```

```
由于是export withCopyright(App)，因此在index中渲染的是withCopyright(App)

执行withCopyright(App)方法时，实际上是在渲染class withCopyright，在执行<YourComponent/>时，开始渲染class App里的内容，<Another name="组件"/> ，将props.name向下传递

由于Another组件是执行withCopyright()后export的组件，因此又会重新开始从class withCopyright里进行渲染

因此此时的props.name是传递到了withCopyright里，即this指向class withCopyright，而class Another是拿不到组件的，所以必须使用 <YourComponent {...this.props}/>将父组件withCopyright的props添加到自己身上才能实现props的继续传递

（若Another不经过withCopyright() <YourComponent/>是可以正常传递<Another name="组件"/> 的props.name的）
```

**使用适配器的写法**

```jsx
import React, { Component } from 'react';
import withCopyright from './withCopyright'


@withCopyright   // 装饰器，当存在多个高阶组件时适用，不能加分号
class Another extends Component {
    render() {
        console.log(this.props)
        return (
            <div>
                Another的信息{this.props.name}
            </div>
        );
    }
}

// 这里不用 export default withCopyright(Another) 使用装饰器完成一样的功能
export default Another;

/*  
若存在多个高阶组件 a b c  
    @a@b@c 等同于 a(b(c(Another))) 
*/
```

---

### Render Props

和高阶组件功能类似：处理`逻辑复用`的模式实际就是`将复用逻辑处理成组件形式`

**render props：**组件不自己定义`render函数`，而是通过一个名为`render的props`(所以如果名字为children 也可称为children props) 将外部定义的render函数传入使用

```jsx
// render props
const Test = props => props.render('hello world')
const App = () => (
    <Test render={text => <div>{text}</div>}  />
     {/* 带有渲染属性(Render Props)的组件需要一个返回 React 元素并调用它的函数，而不是实现自己的渲染逻辑 */} 
)

ReactDOM.render((<App />, root)  // 返回<div>hello world</div>
```

```
Render Props，并不是说只能用来复用渲染逻辑，而是表示在这种模式下，组件是通过render()组合起来的
```

---

### Context

提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法

```
在一个典型的 React 应用中，数据是通过 props 属性自上而下（由父及子）进行传递的，但这种做法对于某些类型的属性而言是极其繁琐的（例如：地区偏好，UI 主题），这些属性是应用程序中许多组件都需要的
Context提供了一种在组件之间共享此类值的方式，而不必显式地通过组件树的逐层传递props
```

```jsx
import React, { Component, createContext } from 'react'

// createContext()返回一个对象 里面有Provider和Consumer两个属性 前者用于提供状态 后者用于接收状态
// 解构 相当于 const xxx = createContext('default')     Provider=xxx.Provider  CounterConsumer=xxx.Consumer
const {
    Provider,
    Consumer: CounterConsumer
} = createContext('default')     // React 会往上找到最近的Provider 然后使用它的值 如果没有找到则使用创建时的默认值 注意传递undefined时不会使用默认值


// 这里为了方便理解不把组件分开到各个文件中写 挤在一个js文件里 实际开发中需要将各个组件分开
class CounterProvider extends Component {
    constructor() {
        super()
        this.state = {
            count: 100
        }
    }

    incrementCount = () => {
        this.setState({
            count: this.state.count + 1
        })
    }

    decrementCount = () => {
        this.setState({
            count: this.state.count - 1
        })
    }

    render() {
        return (
            // 要传递值时 需要将Context的组件包在最外层 此时Provider里面的数值便可通过Consumer进行共享
            // Provider有个固定属性value 通过value将信息共享 这个属性的值可以任意 但是为对象元素比较合理
            <Provider value={
                {      
                count: this.state.count,
                onIncrementCount: this.incrementCount,
                onDecrementCount: this.decrementCount
                }
            }>  
                {this.props.children}
            </Provider>
        )
    }
}

class Counters extends Component {
    render() {
        return (
            // 要共享Provider提供数据时必须将Consumer组件包在数据供给对象外层    Consumer组件的children必须是一个function
            <CounterConsumer>   
                {
                    // Consumer默认传进来一个形参arg (arg)=>{...} arg的值为Provider传来的value值
                    // 函数参数解构 直接拿到value里的count值 count = value.count 
                    ({count}) => {
                        return(
                            <span>{count}</span>
                        )
                    }    
                }
            </CounterConsumer>
        
        )
    }
}

class CountBtn extends Component {
    render() {
        return (
            <CounterConsumer>
                {
                    ({onIncrementCount, onDecrementCount}) => {
                        const handler = (this.props.type === 'increment' ? onIncrementCount : onDecrementCount)   // 用type判断采用的方法
                        return(
                            <button onClick={handler}>{this.props.children}</button>    // 注意这里要props.children才能读取到innerHtml里的东西 即 <CountBtn>innerHtml</CountBtn> 
                        )
                    }
                }
            </CounterConsumer>
            
        )
    }
}

export default class Counter_contex extends Component {
    render() {
        return (
            // 注意：在CounterProvider组件中没有去渲染 CountBtn和Counters组件 并非数据传递逻辑上的父子关系（但是是结构上的父子关系才能进行传递）
            // 但是这两个组件却能通过createContext()获取到CounterProvider组件中的数据 即实现了跨组件传递
            <CounterProvider>
                <CountBtn type="decrement">-</CountBtn>
                <Counters></Counters>
                <CountBtn type="increment">+</CountBtn>
            </CounterProvider>
        )
    }
}

/*
    <createContext.Provider value=...>
        <createContext.Consumer>
            <要使用value数据的组件>
        </createContext.Consumer>
    </createContext.Provider>
*/
```



----

### 获取dom

React提供的这个ref属性，表示为对组件真正实例的引用，可以挂载到组件上也可以是dom元素上

- 渲染组件时返回的是组件实例（不能在函数式组件上使用 ref 属性，因为它们没有实例）
- 渲染dom元素时，返回是具体的dom节点

**适合使用 refs 的情况：**

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库

react里面通过ref来获取组件或者dom元素，使用前要调用`createRef()`来创建一个ref

当 ref 被传递给 `render` 中的元素时，对该节点的引用可以在 `ref.current` 中被访问

```jsx
// 点击按钮时更改按钮A文本
import React, { Component ,createRef} from 'react'

export default class DashBoard extends Component {
    constructor() {
		super()
		this.myRef = createRef()
    }
    
    addValue = () => {
        this.myRef.current.innerHTML = '修改后的数据'		// 相当于document.getElementById('A').innerHTML
    }

	render() {
		return (
            <div>
                <button id='A' ref={this.myRef}>修改前文本</button>
                <button id='B' onClick={this.addValue}>更改按钮A文本</button>
            </div>
        )
  	}
}
```



-----

### 使用svg

**引入**

```jsx
<div>
    <svg width={300} height={300}>
        <rect width="100%" height="100%" style={{ fill: 'purple', strokeWidth: 1, stroke: 'rgb(0,0,0)' }} />
    </svg>
</div>
```

**笔画特性**

| **属性**          | **值**                                                       |
| ----------------- | ------------------------------------------------------------ |
| stoke             | 笔画颜色，默认为none                                         |
| stroke-opacity    | 笔画透明度，默认为1.0（完全不透明），值范围：0.0~1.0         |
| stroke-dasharray  | 用一系列数字指定虚线和间隙的长度，如：stroke-dasharray:5,10,5,20 |
| stroke-linecap    | 线头尾的形状：butt（默认）、round、square                    |
| stroke-linejoin   | 图形的棱角或一系列连线的形状：miter（尖的，默认值）、round（圆的）、bevel（平的） |
| stroke-miterlimit | 相交处显示宽度与线宽的最大比例，默认为4                      |

**填充颜色**

| **属性**     | **值**                                                       |
| ------------ | ------------------------------------------------------------ |
| fill         | 指定填充颜色，默认值为 black                                 |
| fill-opacity | 从 0.0 到 1.0 的数字， 0.0 表示完全透明, 1.0(默认值) 表示完全不透明 |
| fill-rule    | 属性值为 nonzero (默认值) 或 evenodd                         |

```jsx
<svg t="1590892819911" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="3659" height="500px" width="500px">
    <path d="M512 608a96 96 0 1 1 0-192 96 96 0 0 1 0 192m0-256c-88.224 0-160 71.776-160 160s71.776 160 160 160 160-71.776 160-160-71.776-160-160-160" fill="#707070" p-id="3660"></path>
</svg> 
```

svg标签的样式是可以直接写在标签内的，写起来像内联样式，但是优先度很低，如果从外部给定一个样式的话，那样式的属性还是根据外部的css来决定

也可以写成style内联样式的属性，此时就是内联样式的优先度，外部文件若想修改则需声明`!important`

```jsx
<path ... fill="#707070"></path>	// 直接在标签内写样式
<path ... style={{fill:'#707070'}}></path>		// 内联写法
```

```less
// 更改大小和填充颜色
svg {
    height: 1em;
    width: 1em;
    
    path {
        fill: red;
    }  
}
```

----

### Hook

Hooks 是react 16.8版本新增的一项特性，可以在不编写class的情况下使用state以及其他的react特性

#### useState

用于引入类组件的state特性

```jsx
import React, {useState, useEffect} from 'react'

export default function Counter() {
    
    // 使用解构接收useState()的值
    const [count, setCount] = useState(0)    // 使用useState创建一个默认值，用解构接收结果，第一个接收默认值，第二个接收更改state的方法
    
    // 监听类似于生命周期中的componentDidMout() 在初次挂载和状态更新时会执行 需要传入一个回调函数
    useEffect(() => {
        document.title = `当前数量为${count}`   // 模板字符
    })

    return (
            <button onClick={() => {setCount(count - 1) }}>-</button>   
            <span>{count}</span>                            
            <button onClick={() => {setCount(count + 1) }}>+</button>
            
        </div>
    )
}

```

**修改 state 的方法**

```tsx
const [count, setCount] = useState(0)

...
// 第一种用法，直接接收一个新的state结果
setCount(count + 1)

// 第二种用法，接收一个方法，该方法返回一个新的state结果
setCount(() => {
   // 其他操作
   return count + 1
})
```

修改一个组件的state值会令组件重新渲染，但需要注意的是，如果对方是一个引用类型，修改引用类型内部的值，实际上组件对state这个引用类型的引用地址是不会发生改变的，因此会判断为state没有发生改变，不会出现渲染组件

```tsx
// 修改obj内部的count时，即使内部已经发生改变，但是state的obj地址没有发生改变，因此不会重新渲染
import React, { useState } from 'react';

const Demo = () => {
    const [obj, setCout] = useState({count:0})	
    const [num, setNum] = useState(0)	// num的值发生变化时会重新渲染

    const changeState = () => {
        return setCout(() => {
            obj.count += 1
            return obj
        })
    }
   
    return(
        <div>
            <div>当前count的值为：{obj.count}</div>
            <button onClick={changeState}>自增按钮</button>
            <button onClick={() => setNum(num + 1)}>更新渲染按钮</button>
        </div>
    )

}
export default Demo
```

假如一个组件有多个状态值的写法是一个个分开多次写

```jsx
function ExampleWithManyStates() {
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
}
```

---

#### useEffect

用于引入类组件的生命周期特性

可以直接在函数组件内处理生命周期事件，它是 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合（组件挂载，更新，销毁）

第一个参数为一个回调函数，它会在组件渲染时执行函数内的内容，如果该函数A返回了一个函数B，则会在组件销毁前执行B函数（如果函数B想声明再返回一个函数C，会报错）

```tsx
// LifecycleDemo.tsx
import React, {useEffect} from 'react'

const LifecycleDemo = () => {
    useEffect(() => {
        console.log('挂载')

        return () => {
            console.log('销毁')
        }

    },)

    return <div>LifecycleDemo组件</div>
}


export default LifecycleDemo
```

```tsx
// App.tsx
import React, {useState} from "react";
import LifecycleDemo from "./funTest";

function App() {
    
    const [isShow, setIsShow] = useState(true)  // 使用一个状态来控制组件的挂载和销毁
    const [count, setCount] = useState(0)       // 使用一个状态来控制组件更新

    const toggle = () => {
        setIsShow(!isShow)
    }

    const changeState = () => {
        setCount(count + 1)
    }

    return (
        <div className="App">
            <button onClick={toggle}>控制组件挂载/销毁开关</button>
            <button onClick={changeState}>控制组件更新开关</button>
            {isShow && <LifecycleDemo />}   {/* 如果isShow为true则渲染该组件的简易写法 */}
        </div>
    );
}

export default App;
```

```
上面代码的效果是：
	在组件渲染时，打印：'挂载'
	在组件销毁时，打印：'销毁'
```

此时会存在一个现象：当组件并非渲染和销毁阶段，而是更新阶段时，则会打印：` 销毁  ` 再打印 `挂载` ，这说明 `useEffect` 在每次更新时会将之前的组件销毁然后重新挂载以达到更新的效果

```markdown
即：在挂载阶段就已经执行了一次函数A，在每次更新阶段执行一次函数B再执行一次函数A，在每次销毁阶段执行一次函数B
	注意：这里的更新指的是所有能触发 LifecycleDemo 组件重新渲染的情况，如上面的代码，如果App的某个state状态发生改变，会导致整个 App 组件重新渲染，因此也会导致 LifecycleDemo 重新渲染（有时候会忽略这点）
```

因此与其将 `useEffect` 看作一个函数来完成3个独立生命周期的工作，不如将它简单地看作是在挂载之后执行副作用的一种方式

---

**阻止每次更新都会执行useEffect**

`useEffect( )` 的第二个参数为可选参数，类型为一个数组，它的作用是比较上一次作用的状态值，如果两次状态值没有发生改变，则不会执行本次的函数，实现了性能优化 

这个值一般指 props 和 state，因为这个两个发生变化会导致组件更新从而触发 useEffect，当然存一个能跨生命周期储存的普通变量也可以

```tsx
const [value, setValue] = useState(xxx);
 
useEffect(() => {
  	...
}, [value]) 	// 仅在value更改时执行
```

这个数组可以传递任意多项，任意一项的数组发生改变时，都会执行 `useEffect`

```tsx
const [value, setValue] = useState(xxx);
 
useEffect(() => {
  	...
}, [value1, value2, value3 ...]) 	// 仅在value更改时执行
```

-----

**绑定事件时需要在更新组件时销毁事件**

这点可以扩展，不只是事件，如计时器这种也是需要清除的

```tsx
import React, { useState, useEffect } from 'react';
const Demo: React.FC = () => {
    const [positions, setPositions] = useState({x:0, y:0})

    useEffect(() => {
        const updataMouse = (e: MouseEvent) => {
            console.log('inner')	
            setPositions({x: e.clientX, y: e.clientY})
        }
        document.addEventListener('click', updataMouse)
        
        // 如果不清除事件，则每次更新state时都会生成新的事件，会指数级增长，大大影响性能
        return () => {
            document.removeEventListener('click', updataMouse)
        }
    })

    return(
        <p>X: {positions.x}, Y：{positions.y}</p>
    )
   
}
export default Demo
```

---

**执行顺序**

```tsx
import React, { useState, useEffect, useRef } from 'react';
const Demo = () => {
    const [count, setCout] = useState(0);

    useEffect(() => {
        console.log('执行useEffect')
        ref.current = count
    })
    
    Promise.resolve().then(() => {
        console.log('执行Promise')
    })
    
    setTimeout(() => {
        console.log('执行setTimeout')
    })

    return(
        <div>
            <button onClick={() => setCout(count + 1) }>自增按钮</button>            
        </div>
    )

}
export default Demo
```

```
打印顺序：执行Promise → 执行useEffect → 执行setTimeout
```

可以看出 `useEffect` 是在组件挂载完毕后才执行的异步函数（即晚于return内组件的渲染），晚于 `Promise` 执行，早于 `setTimeout` 执行

由于是渲染后才执行，因此可以利用这个特性，在渲染更新后还能拿到上一次渲染前的数据，相当于可以实现`setState(preState)方法拿到preSstate的值`

```tsx
import React, { useState, useEffect, useRef } from 'react';
const Demo = () => {
    
    const [count, setCout] = useState(0);
    const ref = useRef<number>()	// 使用useRef储存跨生命周期的数据

    useEffect(() => {
        console.log('执行useEffect')
        ref.current = count
    })
    
    return(
        <div>
            <div>前一个count的值为：{ref.current}</div>
            <button onClick={() => setCout(count + 1) }>自增按钮</button>            
        </div>
    )
}
export default Demo
```

原理：因为组件渲染后才执行，因此渲染  `<div>前一个count的值为：{ref.current}</div>`  时，拿到的 `ref.current` 是未执行 `useEffect` 方法时的数据

----

**仅在挂载和卸载的时候执行**

如果想执行只运行一次的 `effect`（仅在组件挂载和卸载时执行），可以传递一个空数组 `[]` 作为第二个参数，相当于 `effect` 不依赖于 `props` 或 `state` 中的任何值，所以它永远都不需要重复执行

```tsx
const [value, setValue] = useState(xxx);
 
useEffect(() => {
  	...
}, []) 	// 不会在更新组件时执行
```

---

**使用useEffect 获取数据并显示**

在类组件中，一般通过将此代码放在 `componentDidMount` 方法中实现，函数式组件中，可以使用 `useEffect hook` 来实现

```tsx
// 从Reddit网站获取帖子并显示它们的组件
import React, {useEffect, useState} from 'react'

const LifecycleDemo = () => {
    const [posts, setPosts] = useState([]);

    useEffect(() => {
        // 在useEffect使用异步时应该这么写，即声明后立即调用，不能只声明不调用
        (async () => {
            const res = await fetch("https://www.reddit.com/r/reactjs.json")
            const json = await res.json();
            setPosts(json.data.children.map(c => c.data));
        })()
    })

    return (
        <ul>
            {posts.map(post => (
                <li key={post.id}>{post.title}</li>
            ))}
        </ul>
    );
}

export default LifecycleDemo
```

控制获取文章的更新

```tsx
import React, {useEffect, useState} from 'react'

interface IHelloProps {
    subreddit: string
}

// 假设从上级获取到请求的具体的路径
const LifecycleDemo:React.FC<IHelloProps> = ({subreddit}) => {	// 从props中解构subreddit
    const [posts, setPosts] = useState([]);

    useEffect(() => {
        (async () => {
            const res = await fetch(`https://www.reddit.com/r/${subreddit}.json`)
            const json = await res.json();
            setPosts(json.data.children.map(c => c.data));
        })()
    }, [subreddit])		// 每次subreddit发生更改时从新获取文章，否则不会执行effect方法重新获取文章
 
    return (
        <ul>
          {posts.map(post => (
            <li key={post.id}>{post.title}</li>
          ))}
        </ul>
      );
}

export default LifecycleDemo
```

----

#### useRef

**作用：** 

它像一个变量, 类似于 this , 可以存放任何东西，和 `createRef` 一样可用来获取标签的 dom，也可以用来保存跨周期的数据信息

```tsx
// 点击按钮 state 发生变化，然后每次重新渲染 Demo 组件时， 又会执行一遍 `let num = 0`，num 又会被重新赋值为 0，因此 num 会一直为 0，即一般在组件内无法跨周期进行保存
import React, { useState, useEffect } from 'react';
const Demo: React.FC = () => {
    const [numState, setNumState] = useState(0)
    let num = 0
    function change() {
        num += 1
        setNumState(numState + 1)
    }

    console.log(num)		// 始终显示0
    console.log(numState)	// 会持续自增 

    return(
        <div>           
            <button onClick={change}></button>
        </div>
    )
}
export default Demo
```

----

**使用：**

```tsx
//在ts中使用useRef方法需要指定类型，否则会报错
const ref = useRef(null)	// 方法一：指定默认值，编译器自动类型推论指定类型（推荐）
const ref = useRef<any>()	// 方法二：指定具体类型
```

```tsx
// 用useRef创建了couterRef对象，并将其赋给了button的ref属性,这样就能通过访问couterRef.current就可以访问到button对应的DOM对象
import React, {useRef} from 'react'

const eve =(ref:any) => {
    return () => {
        ref.current.disabled =true
    }
    
}

const Demo = () => {
    const buttonRef = useRef(null)	// 注意，如果这里是使用typescript来写的话要传入一个null值，否则类型报错
    
    return (
            <button ref={buttonRef} onClick={eve(buttonRef)}>button</button>
    )
}
export default Demo
```

**与createRef的区别：**

createRef 每次渲染都会返回一个新的引用，而 useRef 每次都会返回相同的引用，由于这个特性，因此修改它的值不会触发重新渲染

即：`useRef.current`  一旦被赋值，在更新渲染时，这个值不会被消除，但是 `createRef.current` 会在更新阶段时销毁并重新绑定值

```tsx
import React, { useState, useRef, createRef } from 'react';
const Demo = () => {
    const [renderIndex, setRenderIndex] = useState(1)
    const refFromUseRef = useRef<any>()
    const refFromCreateRef = createRef<any>()
    
    // 测试每次更新渲染是否会丢失其useRef.current值
    if(!refFromUseRef.current) {
        console.log('useRef更新了')   
    }
    
    // 测试每次更新渲染是否会丢失其createRef.current值
    if(!refFromCreateRef.current) {
        console.log('createRef更新了')
    }
    
    return(
        <>
            <div ref={refFromCreateRef}>测试createRef</div>
            <div ref={refFromUseRef}>测试UseRef</div>
        
            {/* 通过更改state值让组件更新数据重新渲染 */}
            <button onClick={()=> setRenderIndex(renderIndex + 1)}>测试按钮</button>
        </>
    )

}
export default Demo

// 输出结果：
// 首次渲染时会打印 `useRef更新了`和 `createRef更新了` 
// 更新渲染时只会打印 `createRef更新了`
```

`useRef.current` 的值可以被赋予任意的值而不是一定是dom，`createRef.current` 的值仅在通过标签 `ref` 获得，且是只读的，通过设置的标签元素在挂载时传入内容，不可随意更改

```tsx
const refFromUseRef = useRef<any>()
const refFromCreateRef = createRef<any>()

refFromUseRef.current = 'jack'		// 成立
refFromCreateRef.current = 'mary'	// 报错
```

因此 ` useRef` 的特性为：可以存储数据，且不会在更新渲染时丢失这些数据

----

**使用场景：**

**一：**

```tsx
import React, { useState } from 'react';
const Demo = () => {
    const [count, setCout] = useState(0);

    function handleAlertClick() {
        setTimeout(() => {
            alert(count)
        },3000)
    }
    
    return(
        <div>
            <div>当前count的值为：{count}</div>
            <button onClick={() => setCout(count + 1) }>自增按钮</button>
            <button onClick={handleAlertClick}>弹窗按钮</button>
        </div>
    )
}
export default Demo
```

- 代码功能：点击自增按钮实现 `count` 增加，点击弹窗按钮延时3秒显示当前的 `count`

- 存在问题：当点击弹窗按钮后不再次点击自增按钮时，延时后能显示和当前 `count` 一样的数值，但是当点击弹窗按钮后再继续点击自增按钮时，延时后弹窗的数值和当前的 `count` 是不能对应上的

- 原因：React 会重新渲染组件, 每一次渲染都会拿到独立的 count 状态, 并重新渲染一个 handleAlertClick 函数. 每一个 handleAlertClick 里面都有它自己的 count

可以理解为：按钮事件在点击时就已经确定，开始事件时会将当前生命周期（发生更新渲染前）的state和其他数据传入（如果有用到这些数据）

如果不使用 state 而使用一个普通的变量来进行传值，是可以获取到实时的数值的，原因是事件拿到的是当前生命周期的数据，而不触发更新渲染时，延时完执行时也能做出拿到当前的数据值，如下代码所示：

```tsx
import React, { useState } from 'react';
const Demo = () => {
    let count = 0
    function handleAlertClick() {
        setTimeout(() => {
            alert(count)
        },3000)
    }
    
    function changeNum() {
        count += 1
        console.log(count)
    } 

    return(
        <div>
            <button onClick={changeNum}>自增按钮</button>
            <button onClick={handleAlertClick}>弹窗按钮</button>
        </div>
    )
}
export default Demo
```

使用 useRef 解决问题：

```tsx
import React, { useState, useEffect, useRef } from 'react';
const Demo = () => {
    
    const [count, setCout] = useState(0);
    const latestCount = useRef(count)

    useEffect(() => {
        latestCount.current = count
    })

    function handleAlertClick() {
        setTimeout(() => {
            alert(latestCount.current)
        },3000)
    }
    
    return(
        <div>
            <div>当前count的值为：{count}</div>
            <button onClick={() => setCout(count + 1) }>自增按钮</button>
            <button onClick={handleAlertClick}>弹窗按钮</button>
        </div>
    )

}
export default Demo
```

注意的是state如果是一个对象的话，事件使用该对象里的数据，在触发事件时传递的只是这个 state 的引用地址，而非对象内部具体的值，等到需要拿数据的时候才从引用中去取数据，也能达到实时的效果

useRef 每次都会返回同一个引用，所以每一次渲染点击事件拿到的 state 都是一个引用地址，等到需要取数据的时候再从这个引用地址里去取数据，因此它可以达到实时的效果

```
这里能获取到当前count值而非上一个原因是点击事件执行时，异步useEffect函数已完成count的赋值
```

可用引用类型的方式写：功能是一样的，也能获取到实时的数据，原理一样

```tsx
import React, { useState } from 'react';
const Demo = () => {
    const [obj, setCout] = useState({count:0});
    const [num, setNum] = useState(0)

    function handleAlertClick() {
        setTimeout(() => {
            alert(obj.count)
        },3000)
    }
	
    // 点击自增按钮同时更改obj.count 和 num
    function changeState(){
        setCout(() => {
            obj.count += 1
            return obj
        })
        setNum(num + 1)
    }
    
    return(
        <div>
            <div>当前count的值为：{num}</div>
            <button onClick={changeState}>自增按钮</button>
            <button onClick={handleAlertClick}>弹窗按钮</button>
        </div>
    )
}
export default Demo
```

---

**二：**

渲染周期之间共享数据的存储

state不能存储跨渲染周期的组件，因为state的参数每一次保存都会触发组件的重渲染

```tsx
import React, { useState, useEffect, useRef } from 'react';
const Demo = () => {
    const [count, setCount] = useState(0);

    // 把定时器设置成全局变量使用useRef挂载到current上
    const timer = useRef<any>();

    // 首次加载useEffect方法执行一次设置定时器
    useEffect(() => {
      timer.current = setInterval(() => {
        setCount(count => count + 1);
      }, 1000);
    }, []);

    // count每次更新都会执行这个副作用，当count > 5时，清除定时器
    useEffect(() => {
      if (count > 5) {
        clearInterval(timer.current);
      }
    });

    return <h1>count: {count}</h1>;
}
export default Demo
```

----

#### useReducer

引入 Reducer 功能

```tsx
const [state, dispatch] = useReducer(reducer, initialState);
```

```tsx
import React, {useReducer} from 'react'

// reducer
const myReducer = (state, action) => {
  switch(action.type)  {
    case('countUp'):
      return  {
        ...state,
        count: state.count + 1
      }
    default:
      return  state;
  }
}

// 组件
function App() {
  const [state, dispatch] = useReducer(myReducer, { count:   0 });
  return  (
    <div className="App">
      <button onClick={() => dispatch({ type: 'countUp' })}>
        +1
      </button>
      <p>Count: {state.count}</p>
    </div>
  );
}
```

----

#### useContext

和 Context 差不多，还是要使用 ` createContext()` 

```
createContext()返回一个对象，对象里有Provider和Consumer两个属性，前者用于提供状态，后者用于接收状态
可传递一个参数作为默认的Provider值，当向上层组件找不到Provider值时则使用默认值
```

`useContext()`接收的参数为 `createContext()`  返回的对象，该方法的返回值为 Provider 传递的 value 值

```tsx
// App.tsx
import React, { createContext } from 'react';
import Demo from './Demo'

interface ITemeProps  {
    [key: string]: {color: string; background: string;}
}

const themes: ITemeProps = {
    light: {
        color: '#000',
        background: '#eee'
    },
    dark: {
        color: '#fff',
        background: '#222'
    }
}

export const ThemeContext = createContext(themes.light)

const App: React.FC = () => {
    return (
        <div>
            {/* 使用Provider的value传递值，需要将要传递数据的组件包在内 */}
            {/* 子组件使用数据时，只需要useContext()去接受createContext()返回的对象便可拿到传递的value值 */}
            <ThemeContext.Provider value={themes.light} >
                <Demo />
            </ThemeContext.Provider>
        </div>
    )
}

export default App
```

```tsx
// Demo.tsx
import React, { useState, useEffect, useContext} from 'react';
import {ThemeContext} from './App'

const Demo:React.FC = () => {
    const theme = useContext(ThemeContext)
    // 拿到传递的value值
    const style = {
        background: theme.background,
        color: theme.color
    }
    return <div style={style}></div>
}
export default Demo
```



----

#### 自定义Hook

复用逻辑代码

- 是一个函数式组件，使用 `use` 开头命名表示是一个 hook
- 返回值不是 html 标签，而是给其他组件进行使用的数据

```tsx
import { useState, useEffect } from 'react';
const useMousePostion  = () => {
    const [positions, setPositions] = useState({x:0, y:0})

    useEffect(() => {
        const updataMouse = (e: MouseEvent) => {
            console.log('inner')	
            setPositions({x: e.clientX, y: e.clientY})
        }
        document.addEventListener('click', updataMouse)
        return () => {
            document.removeEventListener('click', updataMouse)
        }
    },[])
   
    return positions
}
export default useMousePostion
```

```tsx
import useMousePostion from './useMousePostion'
const App:React.FC = () => {
    const positions = useMousePostion()
    
    return(
    	<p>
        	{positions.x, positions.y}
        </p>
    )
}
```

---

**hook解决的问题**

可用 hook 去代替高阶组件去为组件添加要多次使用的额外功能

高阶组件缺点：代码沉重（如每次需要返回新的无用 html 标签）；代码可读性不高等

▼**例子**

设置一个可复用的请求 loading 功能，在请求开始时设置 loading 状态为 true，

```tsx
import React, { useState, useEffect } from 'react';
import axios from 'axios'

const useURLLoader = (url: string, deps: any[] = []) => {
    const [data, setData] = useState<any>(null)
    const [loading, setLoading] = useState(false)
    useEffect(() => {
        setLoading(true)
        axios.get(url).then(result => {
            setData(result.data)
            setLoading(false)
        })
    }, deps)
    return [data, loading]
}
export default useURLLoader
```

给组件复用：不像高阶函数那样需要给组件套方法导出，直接拿过来用即可

```tsx
import useURLLoader from './useURLLoader'
const App: React.FC = () => {
    const [isRequest, setIsRequest] = useState(false) 
    const [data, loading] = useURLLoader('https://www.baidu.com', [isRequest]) 
    
    return (
        <div>
            <button onclick={() => setIsRequest(!isRequest)}>点击重新请求数据</button>
            {
                loading
                    ?
                <div>获取的数据为{data}</div>
                    :
                <div>Loading...</div>
            }
       	</divdiv>
    )
}

```

----

#### Hook使用规则

- 只在最顶层使用Hook

  即不在条件，循环，函数嵌套中使用hook，确保hook在函数组件的最顶层调用，这样能确保hook在渲染中能按相同的顺序调用

- 只在 React 函数中调用 Hook



---

## 技巧

### props传递组件

当使用map传递组件时，组件不能为空

```jsx
<Button
    key={route.pathname}
    icon={<route.icon />}  //若存在一个route.icon为空时会进行报错，因为这里已经将其组件化了，不能为空
    >
    {route.title}
</Button>
```

----

### children中使用map

在组件中，使用map去遍历children很容易产生错误，因为children可以是任意类型的对象，如果是一个函数，则在再次调用函数时就会进行报错

为了解决这个情况，react内置了一个专门遍历children的静态方法：`React.Children.map` 【React.Children】中还有其他针对children处理的方法

实际场景使用：检测一个组件中传进来的children是否非指定组件，不是则不渲染并警告

```tsx
const Menu: React.FC<any> = (props) => {

    const renderChildren = () => {
        return React.Children.map(children, (child, index) => {
            const childElement = child as React.FunctionComponentElement<MenuItemProps>
                  const { displayName } = childElement.type		// 利用react内置组件属性的type的displName
                  if(displayName === 'MenuItem') {
                      return child
                  } else {
                      console.error('Warning: Menu has a child which is not a MenuItem component')
                  }
        })
    }

    return(
        <ul className={classes} style={style} data-testid='test-menu' >
            <MenuContext.Provider value = {passedContext}>
                {renderChildren()}
            </MenuContext.Provider>
        </ul>
    )
}
```

-----

### Chrome调试方法

需要在对浏览器对脚本的某个变量进行调试查看时，需要在脚本里对其赋值到窗口对象

```
window.parms = parms		// 即可在浏览器的控制台输入parms进行相关查看调试
```

