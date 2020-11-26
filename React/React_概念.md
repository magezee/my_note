## 概念

### 基本

#### 框架包

通过脚手架下载react，会下载以下三个包：

- react：相关react的一些api，同时也涵盖了babel功能，因此可以不配置babel，只需要引入react即可将jsx语法编译为js对象
- react-dom：把react的虚拟dom转化为真实dom并进行渲染
- react-scripts：包括打包工具webpack，以及把react和react-dom进行关联，打包，编译



-----

#### 特性

三大特性：

- 组件化
- 虚拟DOM
- 单向数据流



-----

#### JSX编译原理

使用JSX，可以极大的简化React元素的创建，JSX抽象化了`React.createElement()`函数的使用

react使用Babel将ES6的语法转化为可被编译器识别的ES5的JS语法，即利用了Babel将JSX语句转化为JS语句

- 使用类这是JSX的语法写组件，但不是合法的JS代码，不能直接运行

```jsx
<div className="app" id="appRoot">
    <h1 className="title">类组件</h1>
    <p>类组件是继承React.Component的</p>
</div>
```

- react在真正渲染的时候会利用Babel把上面的JSX代码编译为下面的方式为运行，下面的代码是合法的JS代码

```jsx
// React.createElement()是用于创建元素的方法 第一个参数为标签名 第二个为标签组件属性，剩下的为子元素
React.createElement('div',{className: 'app',id: 'appRoot'},
	React.createElement('h1',{className: 'title'},'类组件'),
   	React.createElement('p',null,'类组件是继承React.Component的')
)
```

- JS表示一个虚拟DOM的方式

```jsx
const appVDom = {tag: 'div',attrs: {className: 'app',id: 'appRoot'},children: [
    {tag: 'h1',attrs:{className: title},children: ['类组件']},
    {tag: 'p',children: ['类组件是继承React.Component的']}
]}
```



----

### 组件

```
组件不能小写开头，会无法识别
```

- 类组件有state，函数式组件没有，因此类组件又称有状态组件，函数式组件又称无状态组件

- 完全受控组件：组件的数据完全由外部的props传递而得

  非受控组件：只有state数据而不从props接受数据

  半受控组件：即有props又有state

**创建组件方式一：函数**

```jsx
import React from 'react';			// 需要引入才能使用react
import ReactDOM from 'react-dom';	// 需要引入才能使用react

const App = (props) => {
    return(
        <div>
            <h1>Welcome {props.title}!</h1>   {/* 在jsx里插入js代码 */}
        </div>
    )
}


ReactDOM.render(
    <App title = '1996'></App>,    // 注意这里要加逗号  这里也可以使用闭环 <App title = '1996' />
    document.querySelector('#root')
)
```

需要注意的是，函数组件在声明时默认传入props参数，因此可以直接使用参数解构来快捷拿到想要的数据

```jsx
// 假设传入的props为{x:1,y:2}
const App = ({x, y}) => { 
    console.log(props.x, props.y)	// 1  2
	console.log(x, y)   			// 1  2
}
```

**创建组件方式二：使用类，该类必须继承 React.Component 类**

```jsx
class App extends Component {         
    render(){
        return(
            <div className="app" id="appRoot">
                <h1 className="title">类组件</h1>
            </div>
          
        )
    }
} 
```

```
vscode添加组件的快捷方式
输入rcc(类组件) 
输入rfc（函数式组件）
```

**渲染根组件**

渲染return组件只能存在一个根组件， 而且实际上在写入代码的根元素上在外层还会自动新建一个根元素`<div>`

```jsx
return (
	<div className='abc'>
		<TodoHeader/>
		<TodoInput/>
		<TodoList/>
	</div>
)
```

渲染页面结果：

```jsx
<div id="root">
	<div class="abc">
		<TodoHeader/>
		<TodoInput/>
		<TodoList/>
	</div>
</div>
```

若无需使用额外根元素，可使用空（<></>）元素或Fragment元素作为根元素作为根元素，使用Fragment元素时需要导入

```jsx
import React, { Component, Fragment} from 'react'

return (
	<Fragment>		{/* 或者使用 < > */}
		<TodoHeader/>
		<TodoInput/>
		<TodoList/>
    </Fragment>
)
```



---

### 组件生命周期

函数式组件无组件生命周期

- 挂载阶段

```
当组件实例被创建并插入DOM中时，其生命周期调用顺序：
constructor()    
static getDerivedStateFromProps()    --获取state和props 使用该方法时直接return一个static对象属性即可
render()
componentDidMount()
```

- 更新阶段

```
当组件的props或state发生变化时会触发组件更新,其更新的生命周期调用顺序:
static getDerivedStateFromProps()
shouldComponentUpdate()    --确定是否更新 true or flase
render()
getSnapshotBeforeUpdate()
componentDidUpdate()
```

- 卸载阶段

```
当组件从DOM中移除时会调用
componentWillUnmount()
```

其他：错误处理

```
当渲染过程,生命周期,或子组件的构造函数中抛出错误时会调用
static getDerivedStateFromError()
componentDidCatch()
```

<img src="https://img-blog.csdnimg.cn/20200916103039601.png" style="margin:0">

**使用场景**

- getSnapshotBeforeUpdate：

  在React更新DOM元素之前，获得一个快照，它的返回结果将作为 `componentDidUpdate` 的第三个参数，一般用于获取更新前的DOM



-----

**普通变量无法跨生命周期**

下面代码，点击按钮 state 发生变化，然后每次重新渲染 Demo 组件时， 又会执行一遍 `let num = 0`，num 又会被重新赋值为 0，因此 num 会一直为 0

```tsx
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

如果想让 num 也增长，声明应该放在组件外部

```tsx
let num = 0
const Demo: React.FC = () => {
    ...
}
```



-----

### 渲染

**render()**

渲染组件

```jsx
import { render } from 'react-dom'
```

render方法可接受 2 个参数 ：`render([react element], [DOM node])`，表示将指定的组件渲染到指定的HTML文档DOM

```js
render (
    <App/>,
    document.querySelector('#root')		// 将App组件渲染到id=root的节点中	
)
```

-----

**renderToString() 、renderToStaticMarkup()**

将组件输出成HTML字符串 —— 服务器渲染的基础

```jsx
import {renderToString} from 'react-dom/server'
import {renderToStaticMarkup} from 'react-dom/server'
```

服务端：

使用 `renderToString或renderToStaticMarkup` 方法将 `Hello`组件渲染到 `#react-target` 节点内

```jsx
import {renderToString} from 'react-dom/server'
import React from 'react'
import KoaRouter from 'koa-router'
import Hello from '../../react/server'
app.get('/hello', (ctx, next) => {
    ctx.body = `
      <html>
      <head>
        <title>title</title>
        <script src="/socket.io/socket.io.js"></script>
      </head>
      <body>
        <div id="react-target">${renderToString(<Hello />)}</div>
        <script src="/main.js"></script>
      </body>
      </html>
    `
  })
```

浏览器端：

使用 `render` 方法将组件渲染到 `#react-target` 节点内

```dart
render(<Hello />, document.getElementById('react-target'))
```

----

renderToString 和renderToStaticMarkup功能差不多，参数需传入一个组件

- renderToString：渲染组件时会自动带上一个data-reactid属性，该属性是react组件的一个唯一标志id

```jsx
renderToString(<Conpoment>)    
// Conpoment组件渲染了很多html元素，最终html上显示的标签为
<div data-reactid="..."/>
<a data-reactid="..."/>
......
```

- renderToStaticMarkup：不会带上data-reactid属性

----

在服务端渲染上的区别：

- 使用 `renderToStaticMarkup` 渲染出的是不带`data-reactid`的纯 `html` 在前端 `react.js` 加载完成后, 之前服务端渲染的页面会抹掉之前服务端的重新渲染(可能页面会闪一下). 换句话说 前端`react`就根本就不认识之前服务端渲染的内容, `render` 方法会使用 `innerHTML` 的方法重写 `#react-target` 里的内容
- 而使用 `renderToString` 方法渲染的节点会带有 `data-reactid` 属性, 在前端 `react.js` 加载完成后, 前端 `react.js` 会认识之前服务端渲染的内容, 不会重新渲染 `DOM 节点`, 前端 `react.js` 会接管页面, 执行 `componentDidMout` `绑定浏览器事件`等 这些在服务端没完成也不可能执行任务.

-----

注意：

` <div id="react-target">${renderToString(<Hello />)}</div>`标签和渲染组件之间不能带空格

`<div id="react-target">   ${renderToString(<Hello />)}   </div>`

这样会增加空格, 导致前端 `react.js` 不认识之前服务端渲染的内容, 导致重新渲染



----

### 组件key

将相同类型组件存入一个数组时，需要给每个组件一个单独的 key ，React 用这个 key 作为组件的身份来识别具体对哪个组件做增删改各种操作。通常使用这个组件的唯一标识来作为key值

一般而言，在枚举数组map并返回组件时，需要为组件绑定key

```jsx
const post = [{
        id:1,
        title:...
    },{
        id:2,
        title:...
    },
    ...
]

// content = [<Post .../>, <Post .../>, ...]
const content = posts.map((post) =>		
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
)
```

并不能在组件中通过 prop 取到它的值，在 Post 组件中，可以取到 `props.id` 的值，但 `props.key` 的值是拿不到的

其实在同一父组件下，使用多个相同组件时，都需要声明唯一性

```html
<Father>
	<A />
    <A />
    <A />
</Father>
```

理论上在同一个父组件内渲染了多个A组件，也需要为A声明一个唯一值key，但是手动去写组件而非循环遍历的方式返回时，react会自动隐式分配一个“react-id”，因此手动写的组件一般不需额外声明key



---

### 优化更新机制

在React默认更新机制中，默认的是只要一个组件的state发生变化，则该组件的所有子组件都会触发更新机制，如下面代码：Root的state只与A与A_1有关，与A_2和B无关

```jsx
import React, { Component, Fragment } from 'react'

class A extends Component {
    render() {
        console.log('A组件更新了')
        return (
            <Fragment>
                <A_1 data={this.props.data}/>
                <A_2/>
            </Fragment>
        );
    }
}

class A_1 extends Component {
    render() {
        console.log('A_1组件更新了')
        return (
            <div>
                组件A_1
            </div>
        );
    }
}


class A_2 extends Component {
    render() {
        console.log('A_2组件更新了')
        return (
            <div>
                组件A_2
            </div>
        );
    }
}


class B extends Component {
    render() {
        console.log('B组件更新了')
        return (
            <div>
                组件B
            </div>
        );
    }
}


class Root extends Component {
    constructor() {
        super()
        this.state = {
            data: 'xxx'
        }
    }

    eventClick = () => {
        this.setState({
            data: 'xxxx'
        })
    }

    render() {
        return (
            <Fragment>
                <button onClick={this.eventClick}>更改root状态按钮</button>
                <A data={this.state.data}/>
                <B/>
            </Fragment>
        )
    }
}
```

期望更新的状态是Root和A还有A_1触发更新，A_2和B不触发更新，但是实际上Root下的所有组件都会发生更新，即A_2和B的更新是无用的（绿色是期望render路径，红色是无用render）

<img src="https://img-blog.csdnimg.cn/20200916153121431.png" style="margin:0; width:400px">

<img src="https://img-blog.csdnimg.cn/20200916154132882.png#pic_center" style="margin:0; ">

----

**PureComponent**

`PureComponent `会自动识别哪些组件状态有更改并且只更新发生变化的组件，使用时只需把继承Component类换成继承PureComponent即可

```jsx
import React, { PureComponent } from 'react'

class App extends PureComponent {
    
}
```

此时已经可以把A_2和B的render去掉了

<img src="https://img-blog.csdnimg.cn/20200916154512888.png#pic_center" style="margin:0; ">

**Component和PureComponent区别**

继承Component时，只要state有发生改变，，就会触发重新渲染，而不管这个state里的内容是否会和原来一样

```jsx
import React from 'react';

class App extends React.PureComponent {
    constructor() {
        super()
        this.state ={
            str: 'test'
        }
    }
    
    eventClick = () => {
        this.setState({
            str: 'test'
        })
    }
    
    render() {
        console.log('已更新')	// 点击时，只会打印一次'已更新'，如果是继承Component，则点击一次打印一次
        return (
            <div >
                <button onClick={this.eventClick}>test</button>
            </div>
        )
    }
}
```

但是`PureComponent`只能进行浅比较，当层级多（对象一级属性的value为引用类型）时则无法比较成功

```jsx
import React from 'react';

class App extends React.PureComponent {
    constructor() {
        super()
        this.state ={
            obj: {
                num : 1
            }
        }
    }
    
    eventClick = () => {
        this.setState({
            obj: {
                num:1
            }
        })
    }
    
    render() {
        console.log('已更新')	// 无法判断出{num : 1}的内容是不变的，因此此时点击一次会打印一次
        return (
            <div >
                <button onClick={this.eventClick}>test</button>
            </div>
        )
    }
}
```

因此如果想完全做到渲染性能优化还是应该使用`shouldComponentUpdate`重写重新渲染逻辑

---

**shouldComponentUpdate**

常规写法

```jsx
shouldComponentUpdate(nextProps, nextState) {
    return nextState.xxx !== this.state.xxx		// 或者nextProps.xxx !== this.props.xxx	
}
```

这里一般都需要指定到具体的值，因为如果没有设置state默认是`null`，没有传props默认是`{}`，null和{}的判断相等标准有所区别

```js
if({} !== {}){
    console.log('true')      // 打印
}
if(null !== null) {
    console.log('true')      // 不打印
}
```

如下面例子，当在App类中设置了渲染更新标准时，即使不用`PureComponent`，也不会多次执行更新渲染，同时也可以解决更深度的比较

```jsx
import React from 'react';

class Demo extends React.Component {
    constructor() {
        super()
        this.state ={
            obj:{
                str:'xxx'
            }
        }
    }
    
    shouldComponentUpdate(nextPops, nextState) {
        return nextState.obj.str !== this.state.obj.str		// 如果是 nextState.obj !== this.state.obj 则还是会多次渲染
    }

    eventClick = () => {
        this.setState({
            obj:{
                str:'xxx'
            }
        })
    }
    
    render() {
        console.log('已更新')	
        return (
            <div >
                <button onClick={this.eventClick}>test</button>
            </div>
        )
    }
}

export default Demo
```



-----

### 类组件和函数组件

在类组件每一次重新渲染时，会执行一遍 `render()` 内的代码

在函数式组件每一次重新渲染时，会重新执行一遍函数组件内的所有代码 

且组件内声明的语句有一个特色：在不同的生命周期下，声明的同名变量在不同的声明周期下，在 react 看来是不同的变量

每一次更新渲染函数式组件没有实例的说法

---

如下面的例子代码：

类组件和函数式都是同一个作用：更新的时候添加一个点击事件，通过更改鼠标坐标并点击更改state从而发生更新【添加事件函数`addEventListener()` 对于完全相等的事件函数不会多次添加】

类组件的效果：每次更新时点击事件不会发生叠加，即如果点击三次，则再点击时只会打印一条 `Class_inner`

函数式组件的效果：每次更新时点击事件会发生点击，即如果点击三次，再次点击时会不止打印一条 `Fun_inner`，说明此时绑定的事件有多个，多个事件同时生效打印了多次

```jsx
import React, { Component } from 'react'

export default class TestDemo extends Component {
    constructor() {
        super()
        this.state = {
            staus:true,
            positionX:0
        }
    }

    updataMouse = (e) => {
        console.log('Class_inner')	
        this.setState({
            positionX:e.clientX
        })
    }

    componentDidMount() {
        document.addEventListener('click', this.updataMouse)
    }

    componentDidUpdate() {
        document.addEventListener('click', this.updataMouse)
    }

    render() {
        return (
            <div></div>
        )
    }
}
```

```jsx
import React, {useState, useEffect} from 'react'

export default function FunDemo() {
    const [staus, setStaus] = useState(true)
    const [positionX, setPositionX] = useState(0)

	const updataMouse = (e) => {
		console.log('Fun_inner')	
		setPositionX(e.clientX)
	}
    
    useEffect(() => {
        document.addEventListener('click', updataMouse)
    })

    return (
        <div></div>
    )
}
```

这是因为函数式组件的 `updataMouse` 被多次声明的原因，改写代码成下面，发现只有 `updataMouse2` 函数会出现事件叠加的现象，而 `updataMouse1` 事件无论组件更新了多少次都只会打印一条信息

说明了只有函数式组件内的代码在更新渲染时会被重新执行，声明了多次 `updataMouse2` 函数，且不同生命周期内的同名函数实际上在 react 看来是不同的，因此会多次绑定，因此最终就会产生事件叠加的效果

【class组件更新时只会执行render内的方法，因此更新时不会重新声明事件函数】

```tsx
import React, {useState, useEffect} from 'react'
  
const updataMouse1 = (e) => {
    console.log('inner1')	
}

export default function FunDemo() {
    const [staus, setStaus] = useState(true)
    const [positionX, setPositionX] = useState(0)
  
    const updataMouse2 = (e) => {
        console.log('inner2')
        setPositionX(e.clientX)	 
    }

    useEffect(() => {
        document.addEventListener('click', updataMouse1)
        document.addEventListener('click', updataMouse2)
    })

    return (
        <div></div>
    )
}
```

不同声明周期下声明的同名变量会是不同的变量，如果该变量没有被继续引用则会被当垃圾清理，但是如果保持对这些变量的引用（往往都是函数调用），就像使用闭包没有及时断开引用一样，会让这些变量无法被清理，因此就会造成了事件叠加，需要手动地将这些引用及时清理掉

```tsx
import React, {useState, useEffect} from 'react'

export default function FunDemo() {
    const [staus, setStaus] = useState(true)
    const [positionX, setPositionX] = useState(0)

	const updataMouse = (e) => {
		console.log('Fun_inner')	
		setPositionX(e.clientX)
	}
    
    useEffect(() => {
        document.addEventListener('click', updataMouse)
        return () => {
            removeEventListener('click', updataMouse)
        }
    })

    return (
        <div></div>
    )
}
```

---

### 严格模式

React在严格模式下会执行两边render以帮助检查额外副作用

```jsx
ReactDOM.render(
    <React.StrictMode>
        <App />
    </React.StrictMode>,
    document.getElementById('root')
)
```

- 识别具有不安全生命周期的组件
- 关于旧版字符串引用API使用的警告
- 关于不建议使用findDOMNode的警告
- 检测意外的副作用
- 检测遗留上下文API



严格模式无法自动检测副作用，但是会有意地双重调用以下功能（仅用于开发模式，在生产模式下，不会重复调用声明周期）以帮助发现问题

- 类成分`constructor`，`render`和`shouldComponentUpdate`方法
- 类组件静态`getDerivedStateFromProps`方法
- 功能组件主体
- 状态更新器功能（的第一个参数`setState`）
- 函数传递给`useState`，`useMemo`或`useReducer`

```jsx
class App extends React.PureComponent {
    render() {
        console.log('更新')	// 在浏览器上打开时，会发现打印了两次'更新'
        return(
            <div/>
        )
    }
}
```



-----

### MVC和MVVM

**MVC**

- M：Model
- V：View
- C：Controller

React是MVC的架构（Vue是MVVM）（待确定）：

数据更改视图一定会发生变化

视图变化数据一定会发生更改

```
MVC的思想：Controller负责将Model的数据用View显示出来
```

【图中的箭头表示操作或调用的意思：如Controller可对View和Model的内容进行调用操作，而View不会对Controller进行干涉】

<img src="http://www.ruanyifeng.com/blogimg/asset/2015/bg2015020108.png" style="margin:0;height:300px">

**更改状态：**

- 用户可以向 View 发送指令（DOM 事件），再由 View 直接要求 Model 改变状态。

- 用户也可以直接向 Controller 发送指令（改变 URL 触发 hashChange 事件），再由 Controller 发送给 View

- react把业务模式主要放在C里面，内部繁重，适合中大型项目

**MVC模式同时提供了对html，css 和JavaScript的完全控制**

- **Model**

  应用程序中用于处理应用程序数据逻辑的部分 —— 通常模型对象负责在数据库中存取数据

```markdown
Model规定了一个模块的数据类型：
	如一个登入模块包括String类型的UserID和Number类型的Password
Model负责对数据进行获取和存放
	数据从服务器或数据库中获得
数据存放的地方是在Model，而使用数据的地方是在Controller
```

- **View**

  应用程序中处理数据显示的部分 —— 通常视图是依据模型数据创建的

- **Controller**

  应用程序中处理用户交互的部分 —— 通常控制器负责从视图读取数据，控制用户输入，并向模型发送数据

```markdown
Controller是MVC中的数据和视图的协调者
	- Controller里面把Model的数据赋值给View来显示
	- View接收用户输入的数据然后由Controller把这些数据传给Model来保存到本地或者上传到服务器
```

----

**MVVM**

- M：Model
- V：View
- VM：ViewModel



---

### 性能优化

- 渲染列表时加Key

- 自定义事件、DOM事件及时销毁

- 合理使用异步组件

- 减少函数 bind this 的次数

- 合理使用 shouldComponentUpdate、PureComponent 和 memo

- 合理使用 ImmutableJS

- webpack层面优化

- 前端通用是能优化，如图片懒加载

- 使用SSR



----

## 源码解析

### JSX/虚拟DOM

**实现createElement**

将jsx组件转化为可被js识别的js对象

```jsx
const React = {
	createElememt
}

function createElement(tag, attrs, ...childrens) {
    return {
        tag,
        attrs,
        childrens
    }
}

export default React
```

  ```jsx
<div className="app" id="appRoot">
    <h1 className="title">类组件</h1>
    <p>类组件是继承React.Component的</p>
</div>
// 经过babel处理等同于
React.createElement('div',{className: 'app',id: 'appRoot'},
	React.createElement('h1',{className: 'title'},'类组件'),
	React.createElement('p',null,'类组件是继承React.Component的')
)
// 最终返回结果
{tag: 'div',attrs: {className: 'app',id: 'appRoot'},children: [
    {tag: 'h1',attrs:{className: title},children: ['类组件']},
    {tag: 'p',children: ['类组件是继承React.Component的']}
]}
  ```

**实现render**









