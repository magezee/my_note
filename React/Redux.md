## Redux基础

```
Redux在任何地方都可用，不只是运用于React
redux用于state的管理，管理的是在整个网站各个地方都通用的state，但是不去管理那些只作用于组件自身的state
```

```
npm install redux -save
```

**作用：将想要全局共享的公共数据（类似static数据）通过state进行保存，只能通过action进行修改，修改后影响全局**

----

### 要点

- 项目中所有的state都以一个对象树的形式存储在一个单一的store中
- 只有触发action才能更改state
- 通过编写reducers来实现action更改state的逻辑

```jsx
import { createStore } from 'redux'

// 创建一个store，createStore必须接受一个reducer，reducer为一个方法
let store = createStore(counter)


// 声明一个reducer，接收旧的state和action，根据action来制定修改state的逻辑代码并返回新的state
const state = 0
function counter(state, action) {
    switch(action.type) {
        case '+':
            return state + 1
        case  '-':
            return state - 1
        default:
            return state   // 因为不是任何时候state都需要修改的，因此需要不改变state的出口
    }
}

// action是一个普通的JS对象，普遍约定action内必须使用一个string的type字段来表示将要执行的动作
const add = {type: '-'}
const dec = {type: '+'}

// 改变内部state的唯一方法树dispatch一个action
store.dispatch(add)		// -1
store.dispatch(dec)		// 0


// 创建监听，一般用于更改state时进行渲染更新
store.subscribe()
```



---

### store

```jsx
store = createStore(reducer)

store.getState()	 			// 获取当前state内容
store.dispatch(action)			// 方法更新state
store.subscribe(listener)		// 注册监听器，会返回一个可以解绑监听器的函数，执行该函数则会解绑
```

- **store.getState( )**

  获取所有传入reducer中的state的一个整合对象，这个state对象的值来由定义reducer时传入并可以由dispatch触发reducer更新

```js
function A(state=0, action:any) {
    switch(action.type) {
        case("ADD"):
            state += 1
            return state
        default:
            return state
    }
} 

function B(state={state:0}) {
    return state
} 

let reducer = combineReducers({A, B})	// 最终state中显示的是什么属性名跟这里传进来的名字对应
let store = createStore(reducer)

store.getState()				// { A:0, B:{state:0} }
store.dispatch({type:'ADD'})
store.getState()				// { A:1, B:{state:0} }
```

- **store.dispatch(action)**

  触发state变化的唯一途径，会使用当前`getState()`和传入的`action`以同步方式调用store的reduce函数，返回值会被作为下一个state，成为`getState()`的新返回值，同时变化监听函数（change listener）被触发，当dispatch 时，会触发所有的 reducer 函数，会根据指定的 action 条件触发对应的 reducer

  `dispatch()`方法的的参数必须是一个对象，如果是一个方法则会报错，因此action是一个创建函数，要`store.dispatch(action())`

```js
//从发送action到redux内的state更新这一过程是同步的（即dispatch是同步操作），如果只是简单观察显现可能呈现出异步的效果，这是因为React的setState异步导致的

// 能确保执行完dispatch(A)只会才开始执行dispatch(B),不需要额外加上await
demo = anync () => {
    dispatch(A)
    await request.post(...)	 // 假设这里执行了一些异步操作
    dispatch(B)
}
```

- **store.subscribe(listener)**	

  每当执行`store.dispatch(action)	`时便会自动执行此方法，需要操作当前state时可用`store.getState()`获取当前state

  

-----

### action

```
action在声明reducer时并没有将具体的的action传入，真正将具体action传入reducer是在调用dispatch（action）的时候
```

使用action的两种方法：

- 直接使用一个对象

```jsx
const action = {
	type:''
}
```

- action创建函数，使用一个函数返回一个对象（约定传入参数放进payload属性里）

```jsx
const action = (prams) => {
    return {
        type:''
        payload: {
        	prams	
    	}
    }
}
```



-----

### reducers

**通过触发store的dispatch方法，会触发reducer方法，reducer会拿到dispatch传入的action，再对action内的type属性值进行判断并采用对应方式更改state**

```markdown
这里触发的reducer实际上是 `store = createStore(reducer)` 中的reducer【使用combineReducers方法整合所有reducer】
而该reducer是所有reducer的集合
因此实际上当执行 `store.dispatch(atcion)` 时，会将action传入该集合，相当于触发了所有的reducer，然后执行满足条件的那一块语句
```

reducer函数拥有两个参数：初始state和传入的action，当触发 `dispatch(action)`时，会将该action传递给reducer的第二个参数

**注意：**`switch` 方法中，不能对数据进行操作后直接 `break`，这样相当于将一个 `undefined` 返回给了state，会造成错误，应该在数据操作后将最终的值 `return` 出去

```js
const initData = {
    count: 0
}

const reducer = (data = initData, action) {
    switch(action.type) {
        case 'ADD' :
           	data.count += 1
            return data		// 这里不能使用break
        default:
            return data
    }
}
```

- **设计state结构**

  redux中所有state被保存在一个单一对象中，不同类型的state需要想办法进行区分

```jsx
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
```

- **保证reducer纯净**

  不可在reducer里执行如下操作：

  - 修改传入参数
  - 执行有副作用的参数，如API请求和路由跳转
  - 调用非纯函数，如`Data.now()`或`Math.random()`，这些函数每次调用返回的都是不同结果

  只要传入参数相同，返回计算得到的下一个state就一定相同，没有特殊情况，没有副作用，没有API请求，没有变量修改，单纯执行计算

  reducer的最终目的只是接受一个条件并根据条件修改state，其他不是以修改state为目的的操作应该都在外部操作，如在action中将拿到的数据处理完毕后再将最终结果传递给reducer执行

- **Action**

  redux首次执行时，state为`undefined`，可另设置初始state

```jsx
function todoApp(state = initialState, action) {
  // 这里暂不处理任何 action，仅返回传入的 state
  return state
}
```

<img src="https://img-blog.csdnimg.cn/20200605142623673.png" style="margin:0;width:670px"/>

---

### 组件更新

redux 的更新不会自动引起使用 redux 数据的组件更新，即使是传入组件的 props 或者 state

```jsx
class App extends Component {
	reduxUpdata = () => {
		this.props.dispatch(action())
	}

	render() {
		return (
			<React.Fragment>
				<button onClick={this.reduxUpdata}>控制redux更新</button>
				<div>{this.props.data.count}</div>
			</React.Fragment>
		)
	}
}

function mapToState(state) {
	return {
		data: state.data
	}
}

export default connect(mapToState)(App);
```

如果想让 redux 数据更新同时刷新组件，应该在控制 redux 更新的同时控制组件更新，比较常用的方法就是令组件的一个 state 值和 redux 中的一个state 值联系起来，控制 redux 更新同时手动更改组件 state

```js
class App extends Component {
    constructor() {
        super()
        this.state = {
            count : 0
        }
    }
	reduxUpdata = () => {
		this.props.dispatch(action())
        setState(() => {
            count: this.props.data.count	// 使用setState手动刷新组件
        })
	}

	render() {
		return (
			<React.Fragment>
				<button onClick={this.reduxUpdata}>控制redux更新</button>
				<div>{this.props.data.count}</div>
			</React.Fragment>
		)
	}
}

function mapToState(state) {
	return {
		data: state.data
	}
}

export default connect(mapToState)(App);
```







-----

### 目录管理

**方式一：统一管理reducer和action**

因为每次 dispatch(action) 都会触发所有的 reducer，任意 action 也可以在任意一个组件的 dispatch 中使用，因此为了能方便看到所有的 action 和 reducer，可以统一写在一个文件里，同理 action 的 type 也可以全部写在一个文件里方便导入

**目录结构**

```
src
  |
  |__ actions
  |		|__ index.js
  |		|__ actionTypes.js
  |
  |__ reducers
  |  	|__ index.js
  |
  |__ store.js
```

- **管理reducers**
在项目src目录下创建`reducers文件夹`，用于统一存放reducer文件，创建index.js文件，用于统一导入reducer文件并导出

```jsx
import reducerA from './reducerA'
import reducerB from './reducerB'
import {combineReducers} from 'redux'	// redux专门用来提供合并reducers的工具
export default combineReducers({
    reducerA,	// 相当于 reducerA: rerducerA
    reducerB
})

/*
    不可以直接导出，这样导出的不是一个方法，无法被识别
    
	export default {
    	reducerA,
    	reducerB
	}

*/
```

​		一开始进行reducer的编写时，只需写明state和声明一个简单方法导出即可，后续再加功能

```jsx
const reducerA_state = {
    // ...
}

export default (state = reducerA_state, action) => {
    switch(action.type) {
    	default:
    	return state
    }
}
```

- **管理store**

  在项目的src目录下创建`store.js`文件（注意只能存在一个store）

```jsx
import {createStore} from 'redux'
import rootReducer from './reducers'

export default createStore(rootReducer)
```

- **管理actions**

  在项目src目录下创建`actions文件夹`，用于统一存放action文件，一般新建一个`actionType.js`文件统一对action的type进行管理并导出，然后另创建具体的action.js文件使用`action创建函数`的方式导出具体action

  如：实现一个商品计数器的action

```jsx
// actionType.js
export default {
    CART_AMOUNT_INCREMENT: 'CART_AMOUNT_INCREMENT',
    CART_AMOUNT_DECREMENT: 'CART_AMOUNT_DECREMENT' 
}
```

```jsx
//  /src/actions/cart.js
import actionType from './actionType'
export const increment = (id) =>{
    return {
        type: actionType.CART_AMOUNT_INCREMENT,
        payload: {
            id
        }
    }
}

export const decrement = (id) => {
    return{
        type: actionType.CART_AMOUNT_DECREMENT,
        payload: {
            id
        }
    }
}
```

​		在reducer里导入的是actionType.js，在使用store.dispatch( )方法的组件中导入cart.js

```jsx
//  /src/reducers/cart.js
import actionType from '../actions/actionType'

const initState = [{
    id: 1,
    title: 'Apple',
    price: 8888.66,
    amount: 10
},{
    id: 2,
    title: 'Orange',
    price: 4444.66,
    amount: 12
}]

export default (state = initState, action) => {
    switch(action.type) {
        case actionType.CART_AMOUNT_INCREMENT:
            return state.map(item => {
                if(item.id === action.payload.id) {
                    item.amount += 1
                }
                return item
            })
        default:
            return state
    }
}
```

```jsx
import store from '../../store'
import {increment, decrement} from '../../actions/cart'
// ...
<button onClick = {
  () => {
      this.store.dispatch(decrement(id))
  }  
}/>
<button onClick = {
  () => {
      this.store.dispatch(increment(id))
  }  
}/>
```

-----

**方式二：按功能管理reducer和action**

在 `/src` 下建立 `store` 目录，在该目录下新建一个 `index.js` 文件，用于创建 redux 和引入中间件

在 `store` 目录下根据功能创建各功能目录，在该目录内创建功能涉及到的 action 和 reducer 和 types

这样就能很明确的指定哪些功能有哪些 action 和 会触发哪些 reducer

不按组件划分的原因可能会有很多组件用到了同样的 reducer 和 action 功能，他们很多时候不只是服务于一个组件的，用组件的关系划分可能会存在很多重复

```
具体该如何分开这些功能则按具体项目要求决定，如一个小型网站只有用户和用户操作的功能
则可以分为两块，如 user 和 doing 文件
user文件：负责用户的登入注册登出等这些操作所需要的reducer和action功能
doing文件：负责用户的留言点赞转发等这些操作所需要的reducer和action功能
```

**目录结构**

```
src
 |	
 |__ store
      |
      |__ user
      |    |__ actions.js
      |    |__ reducer.js
      |    |__ types.js
      |
      |__ index.js
```



-----

### recat-redux

```
npm install react-redux -s
```

react-redux是redux对react的官方绑定库，借助react-redux可以很方便的在react中使用redux

react-redux需要配合redux来使用，redux对外暴露了以下方法，除了这些都是react-redux的内容

react-redux 的核心组件只有两个，`Provider` 和 `connect`，Provider 存放 Redux 里 store 的数据到 context 里，通过 connect 从 context 拿数据，通过 props 传递给 connect 所包裹的组件

```jsx
export {
  createStore,
  combineReducers,
  bindActionCreators,
  applyMiddleware,
  compose,
  __DO_NOT_USE__ActionTypes
}
```

**使用步骤：**

1. 通过createStore()方法创建store；

2. 通过Provider组件将store注入到需要使用store的组件中；

3. 通过connect()连接UI组件和容器组件，从而更新state和dispatch(action)

<img src="https://img-blog.csdnimg.cn/20200605143518759.png" style="margin:0;"/>

#### 运用思想

实际项目中，需要权衡是直接使用Redux还是用React-Redux

- React-Redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）

- UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑，如果一个组件既有 UI 又有业务逻辑，则将它拆分成下面的结构：外面是一个容器组件，里面包了一个UI 组件，前者负责与外部的通信，将数据传给后者，由后者渲染出视图

- React-Redux 规定，所有的 UI 组件都由用户提供，容器组件则是由 React-Redux 自动生成，也就是说，用户负责视觉层，状态管理则是全部交给它

**UI组件**

- 只负责 UI 的呈现，不带有任何业务逻辑
- 没有状态（即不使用this.state这个变量）
- 所有数据都由参数（this.props）提供
- 不使用任何 Redux 的 API

 **容器组件**

- 负责管理数据和业务逻辑，不负责 UI 的呈现
- 带有内部状态state
- 使用 Redux 的 API

|                    | **UI组件**                 | **容器组件**                       |
| :----------------- | :------------------------- | ---------------------------------- |
| **作用**           | 描述如何展现（骨架、样式） | 描述如何运行（数据获取、状态更新） |
| **直接使用 Redux** | 否                         | 是                                 |
| **数据来源**       | props                      | 监听 Redux state                   |
| **数据修改**       | 从 props 调用回调函数      | 向 Redux 派发 actions              |
| **调用方式**       | 手动                       | 通常由 React Redux 生成            |



----

#### 组件交流

##### Provider

使用provider进行快捷组件交流，不需要多层传递数据

使用时需要将其包在要传递组件的最外层，由于`<App/>`为组件渲染根元素，因此任意子组件内都可直接使用provider传递的数据，无需层层相传，必须要要拥有store属性，值为创建的store

```jsx
//  /src/index.js

import React from 'react'
import { render } from 'react-dom'
import App from './App'
import store from './store'
import {Provider} from 'react-redux'


render(
    <Provider store={store}>
        <App/>,
    </Provider>,  
    document.querySelector('#root')
)
```

##### connect

通过connect( )( )自动生成的容器组件（高阶组件），经过connect操作后会将dispatch方法传入该组件

使用了connect之后是自动订阅state的变化并进行重新渲染的，不需要再去通过`store.subscribe(listener)`去监听state的变化

```jsx
import {connect} from 'react-redux'

export default VisibleMyComponent = connect()(myComponent)	// 一般合起来写，作用和下面的一样

/* 
	const VisibleMyComponent = connect()(myComponent)
 	export default VisibleMyComponent


	使用装饰器的写法：
	@connect()	// connect()()有两层括号，使用装饰器后会少一个
	class myComponent {...}
	export default myComponent
*/
```

使用connect后会在原来传进组件props的基础上增加内容

```jsx
class componentA {}		// props为传递进来的内容

@connect()
class componentA {}		// props为传递进来的内容和dispatch方法
```

connect( )接受两个参数：

- `mapStateToProps` 
- `mapDispatchToProps`

它们主要是为了让redux使用更加方便而服务的，即使不设置也不影响其功能，前者负责输入逻辑，即将state映射到 UI 组件的参数（props），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action

```jsx
const VisibleMyComponent = connect(
  mapStateToProps,
  mapDispatchToProps
)(myComponent)
```

- **mapStateToProps()**

  它是一个函数，返回一个对象，对象内的属性值（一般是state中的属性）会被加到组件的props中，为了更方便的获取到state值

  当在connect绑定该函数时，对应的函数会自动传进来一个值，这个state为 `store.getState()` 的值

  在组件执行render方法前被执行，因此每次render后的组件状态都与store同步

```jsx
// 若原组件的props内容为{a:1}
// 使用connect高阶组件化并设置mapStateToProps的组件props为{a:1, addPrpps:xxx, dispatch: function}
const mapStateProps = (state, ownProps) => {	// ownProps为传进该组件的的props
    return {
        addProps:state.myState
    }
}
```

-  **mapDispatchToProps()**

  它可以是一个函数，也可以是一个对象，相当于封装了一个传入固定action的dispatch方法并添加到组件的props，为了更方便地使用dispatch方法

  在 ` connect()` 传入该形参，则不会再将 ` dispatch` 方法传入props，取而代之的是封装映射的方法

  mapDispatchToProps()在组件constructor()中被执行，因而只执行一次
  
  为一个函数时：当在connect绑定该函数会传入dispatch方法，然后手动封装一个触发固定action的dispatch函数，可以写死传入形参也可以不写入具体形参，具体使用时再传入

```jsx
const increment = (id) =>{
    return {
        type: actionType.CART_AMOUNT_INCREMENT,
        payload: {
            id
        }
    }
}
const decrement = (id) => {
    return{
        type: actionType.CART_AMOUNT_DECREMENT,
        payload: {
            id
        }
    }
}
// 第一个参数用于接受store.dispatch()方法,第二个参数用于接受组件自身的props
const mapDispatchToProps = (dispatch, ownProps) => {
    return {
      add: (id) => { dispatch(increment(id)) },		
      dec: (id) => { dispatch(decrement(id)) }
    }
}

// 封装后：
// 使用 this.props.dispatch(increment(id)) 等同于使用 this.props.add(id)
// 使用 this.props.dispatch(decrement(id)) 等同于使用 this.props.dec(id)
```

​		为一个对象时，键值内容应为action对象或action创建函数，会自动dispatch该对象中的所有属性（为创建函数时则是执行后的action结果）

```jsx
const increment = (id) => {   
    return {
        type: 'CART_AMOUNT_INCREMENT',       
        payload: {
            id
        }
    }
}
const decrement = (id) => { 
    return {
        type: 'CART_AMOUNT_DECREMENT',
        payload: {
            id
        }
    }
} 

const mapDispatchToProps = {    
	add: increment,
    add: decrement,
}
// this.props.add(id)
```

**一般是在action先写好了mapStateToProps和mapDispatchToProps再导出到组件并传到connect里**

```jsx
// 为了展示方便全写在一个文件里，实际在项目中需要分类各个文件
import React, { Component } from 'react'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import { Provider, connect } from 'react-redux'


// 定义counter组件
class Counter extends Component {
  render() {
    const { value, onIncreaseClick } = this.props	
    return (
      <div>
        <span>{value}</span>
        <button onClick={onIncreaseClick}>自增按钮</button>
      </div>
    )
  }
}


// Action  
const increaseAction = { type: 'increase' }


// Reducer   基于原有state根据action得到新的state
function counter(state = { count: 0 }, action) {
  switch (action.type) {
    case 'increase':
      return { count: state.count + 1 }
    default:
      return state
  }
}


// 根据reducer函数通过createStore()创建store
const store = createStore(counter)


//  将state映射到Counter组件的props
function mapStateToProps(state) {
  return {
    value: state.count
  }
}


//  将action映射到Counter组件的props
function mapDispatchToProps(dispatch) {
  return {
    onIncreaseClick: () => dispatch(increaseAction)
  }
}


//  传入上面两个函数参数，将Counter组件变为App组件
const App = connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter)


ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

**bindActionCreators**

`bindActionCreators` 的作用是将一个或多个 action 和 dispatch 组合起来生成 `mapDispatchToProps` 需要生成的内容

```js
import { bindActionCreators } from 'redux'
bindActionCreators(
	actionCreators,			// 一个action对象或者action创建函数
  dispatch						// 由store或者connect传过来的dispatch方法
)
```

传统写 `mapDispatchToProps`

```js
import {addAction, decAction} from './actions'
const mapDispatchToProps = (dispatch, ownProps) => ({
  add: (count) => {dispatch(addAction(count))},		
  dec: (count) => {dispatch(decAction(count))}
})

// 使用：props.add(10)
```

使用 `bindActionCreators` ,这样写和传统写一样，感受不到该功能好处

```js
const mapDispatchToProps = (dispatch, ownProps) => ({
  add: 	bindActionCreators(
    addAction,
    dispatch
  ),
  dec: bindActionCreators(
    decAction,
    dispatch
  )
})

// 使用：props.add(10)
```

使用整合的方法编写：

```js
const mapDispatchToProps = (dispatch, ownProps) => ({
	actions: bindActionCreators(
    {
      addAction,
      decAction
  	},
    dispatch)
})

// 使用：props.actions.addAction(10)
```

当使用的 action 的数量越多时，越能感受到 `bindActionCreators` 的好处

```js
import * as allActions from './actions'
@connect(
	state => ({
    ...
  }),
  dispatch => ({
    actions: bindActionCreators({...allActions},dispatch)
  })
)
function Component() {
  ...
}
```



----

## Redux中间件

一般使用中间件的原因都是为了处理异步操作（通常是指在action中执行ajax请求）

异步action（因为要保证reducer的纯净，因此不能外调api，所以在action里处理）

运行原理：`actionCreator => 自动dispatch(actionCreator()) => reducer => store => view`

```jsx
// 当使用了connect，进行异步操作时，因为connect会立即dispatch设置的action，但是当异步return action时，就无法在第一时间找到dispatch的action，因此会报错，不能这样写
const action = () => {  
    setTimeout(() => {
        return {
            type: xxx,       
        }
    },2000)
}
```

redux 中的 action 仅支持原始对象，处理有副作用的action，需要使用中间件，中间件可以在发出action，到reducer函数接受action之间，执行具有副作用的操作

**为了能实现能获取到异步返回的最终action数据，需要使用redux的中间件middeware进行处理**



---

### 加载中间件

使用redux提供的 `applyMiddleware` 加载指定中间件

```js
import { applyMiddleware } from 'redux'
```

作用：加载 middleware，将 action 进行多层组合，并且将dispatch和getState方法传入到 action 中，使 action 能通过 dispatch 向下调用新的 action或查看当前的state状态【要配合中间件才能起作用】

```jsx
const lastAction = () =>{
    return {
        type: xxx
    }
}

const beforeAction = () => {
    return (dispatch,getState) => {
        console.log(getState())		// 获取state
        dispatch(lastAction())		// 手动dispatch调用下一个action
    }
}
```

**实现原理：**

applyMiddleware 利用 createStore 和 reducer 创建了一个 store，然后 store 的 getState 方法和 dispatch 方法又分别被直接和间接地赋值给 middlewareAPI 变量

```jsx
export default function applyMiddleware(...middlewares) {
  return createStore => (...args) => {
    // 利用传入的createStore和reducer和创建一个store
    const store = createStore(...args)
    let dispatch = () => {
      throw new Error(
      )
    }
    const middlewareAPI = {
      getState: store.getState,
      dispatch: (...args) => dispatch(...args)
    }
    // 让每个 middleware 带着 middlewareAPI 这个参数分别执行一遍
    const chain = middlewares.map(middleware => middleware(middlewareAPI))
    // 接着 compose 将 chain 中的所有匿名函数，组装成一个新的函数，即新的 dispatch
    dispatch = compose(...chain)(store.dispatch)
    return {
      ...store,
      dispatch
    }
  }
}
```

上面的compose方法可以接受一组函数参数，从右到左来组合多个函数，然后返回一个组合函数

即``compose(funcA, funcB, funcC)``等价于`compose(funcA(funcB(funcC())))`

```jsx
export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }

  if (funcs.length === 1) {
    return funcs[0]
  }

  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```



----

### redux-thunk

`redux-thunk` 是一个处理异步事件的中间件，类似的还有 `redux-promise` ，`redux-saga` 等中间件，使用方法和redux-thunk接近 

```
npm install redux-thunk -s
```

#### 原理

```jsx
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => next => action => {
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }
    return next(action);
  };
}
const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;
export default thunk;
```

redux-thunk 中间件的功能很简单，首先检查参数 action 的类型，如果是函数的话，就执行这个 action ，并把 dispatch, getState, extraArgument 作为参数传递进去，否则就调用 next 让下一个中间件继续处理 action

每个中间件最里层处理 action 参数的函数返回值都会影响 Store 上的 dispatch 函数的返回值，但每个中间件中这个函数返回值可能都不一样。比如上面这个 react-thunk 中间件，返回的可能是一个 action 函数，也有可能返回的是下一个中间件返回的结果。因此，dispatch 函数调用的返回结果通常是不可控的，最好不要依赖于 dispatch 函数的返回值

#### 使用

```jsx
// 直接在store文件里导入即可（同时要导入使用applyMiddleware方法）
import {createStore, applyMiddleware} from 'redux'
import thunk from 'redux-thunk'
import rootReducer from './reducers'

export default createStore(
    rootReducer,
    applyMiddleware(thunk)
)    
```

使用中间件处理前：

<img src="https://img-blog.csdnimg.cn/20200606143355627.png" style="margin:0"/>

使用中间件处理后：

将action传递给中间件，每一个中间件可以处理action并返回新的action，最后一个中间件处理完action后才会将最终的action传递给reducer处理，因此保证了reducer处理的是异步执行完步后的action，不会因为立即执行dispatch而没有及时拿到到action报错

即由于rudux的 action 仅支持原始对象，如果要执行副作用，则需要引入中间件，最终都是由一个 reducer 去处理一个最终 action，只是要拿到这个最终 action，如果是经过副作用处理过得，则需要用中间件处理一下

<img src="https://img-blog.csdnimg.cn/20200606143528458.png" style="margin:0"/>

#### action处理

核心用法只有一个：reducer只针对最后的action作出响应，其他action是为了触发下一个action并将数据传递

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import {createStore, applyMiddleware} from 'redux'
import thunk from 'redux-thunk'

// reducer
const rootReducer = (initState=0, action) => {
    console.log('执行reducer')
    switch(action.type) {
		case('DEMO_TYPE'):
            console.log('执行DEMO_TYPE')
            // {type: "DEMO_TYPE", payload: {data: 2}}  说明数据已经进行处理并传递给最终的reducer
            console.log(action)    
			return initState + action.payload.data
		default: 
			return initState
	}
}

// store
let store =  createStore(
    rootReducer,
    applyMiddleware(thunk)
)    

// action
const action_A = (data) => {
    return(dispatch) => {
        data += 1
        dispatch(action_B(data))
    }
}

const action_B = (data) => {
    return(dispatch) => {
        data += 1
        dispatch(action_C(data))
    }
}

const action_C = (data) => {
    return {
        type: 'DEMO_TYPE',
        payload: {
            data: data
        }
    }
}


const event = () => {
    store.dispatch(action_A(0))     // 传递数据并启动action链
}

ReactDOM.render(
    <div>
        <button onClick={event}>触发dispatch</button>
    </div>,
  document.getElementById('root')
);
// 只打印一遍'执行reducer'和'执行DEMO_TYPE'，说明多次dispatch只有action_C触发了reducer
```

异步的中间件处理

```jsx
// reducer
const reducer = (initState = 0, action) => {
	switch(action.type) {
		case('TYPE_DEMO'):
			console.log('执行reducer')
			return initState + 1
		default: 
			return initState
	}
}

let store = createStore(
  reducer,
  applyMiddleware(thunk)
)    

const action_1 = () => {
  console.log('执行中间件1')
  return {
      type: 'TYPE_DEMO'
  }
}

const action_2 = () => {
  return (dispatch) => {
      setTimeout(() => {
          console.log('执行中间件2')
          dispatch(action_1())
      },2000) 
  }
}

const action_3 = () => {
  return (dispatch) => {
          console.log('执行中间件3')
          dispatch(action_2())
      }
}

export const action_4 = () => {
  return (dispatch) => {
      setTimeout(() => {
          console.log('执行中间件4')
          dispatch(action_3())
      },2000) 
  }
}

const dispatchEvent = () => {
	store.dispatch(action_4())
}

ReactDOM.render(
	<>
		<button onClick={dispatchEvent}>触发dispatch按钮</button>
	</>,
	document.getElementById('root')
);


// 点击按钮执行结果：执行中间件4 → 执行中间件3 → 执行中间件2 → 执行中间件1 → 执行reducer
```

**使用redux-thunk在真实开发中实现数据请求的写法：**

简单核心写法：

```js
const action = (data) => {
    return {
        type: 'TYPE_DEMO',
        payload: {
            data
        }
    }
}

asyncAction = async () => {
    return async (dispatch) => {
		let data = await fetch(url, params)		// 将请求到的数据交给下一个action处理
        dispatch(action(data))					// 这里就用到了中间件的功能
    }
}

dispatch(asyncAction())		// 等于触发了asyncAction后将拿到的data数据付给acion再最终dispatch这个action触发reducer

// 最终执行reducer传入的是 {type: 'TYPE_DEMO',payload: {data} 对象
const reducer = (state = xxx, action) => {
    switch(action.type) {
    	case: 'TYPE_DEMO':
            console.log('对数据进行处理并返回新的state')
    }
}
```

高端写法：可以在请求前后另外dispatch一些操作

比如在请求开始前将一个表示加载中的state设置成true，在请求完成时设置成flase，就实现需要在请求加载中时做的一些操作的效果

```js
getData = async () => {
    return async(dispatch, getState) => {
        try {
            dispatch({type: 'GET_WEATHER_START'})
            const result = await fetch(url, params)
            dispatch(action(data))
            dispatch({type: 'GET_WEATHER_END'})
    	} catch(error) {
        	dispatch({type: 'GET_WEATHER_ERROR', error: err})
    	}     
	} 
}
```



---

### redux-saga

redux-saga比redux-thunk更加强大，不止可以充当中间件的作用，可以实现多种代码效果

```
npm install --save redux-saga
```

用作中间件总体流程和thunk一样，只是处理中间action的方式变成了用saga而非thunk而已

<img src="https://img-blog.csdnimg.cn/20200803102635652.png" style="margin:0"/>

----

#### 基础配置

写上一个saga函数

```js
// src/saga.js
export function* helloSaga() {
  console.log('Hello Sagas!');
}
```

运行 `helloSaga` 之前，必须使用 `applyMiddleware` 将 middleware 连接至 Store，然后使用 `sagaMiddleware.run(helloSaga)` 运行 Saga

```javascript
import { createStore, applyMiddleware } from 'redux'
import createSagaMiddleware from 'redux-saga'
import { helloSaga } from './sagas'
import reducer from 'xxxx'

const sagaMiddleware = createSagaMiddleware()

const store = createStore(
  reducer,	// 这个reducer是用combineReducers综合导出的reducer
  applyMiddleware(sagaMiddleware)	// 声明使用saga中间件
)

sagaMiddleware.run(helloSaga)	// 需要指明要跑的根saga，这个saga一般会写在src下的saga.js文件
```



----

#### 异步调用

- Sagas 被实现为 `Generator` 函数，它会 yield 对象到 `redux-saga middleware`
- 被 yield 的对象都是一类指令，指令可被 middleware 解释执行
- 当 middleware 取得一个 yield 后的 Promise，middleware 会暂停 Saga，直到该 Promise 完成
- 一旦 Promise 被 resolve，middleware 会恢复 Saga 接着执行，直到遇到下一个 yield

```js
import { put, delay } from 'redux-saga/effects'

export function* demoAsync() {
  yield delay(1000)		// 延时函数，单位ms
  yield put({ type: 'DEMO'})
}       
// demoAsync Saga 通过delay(1000)延迟了1秒钟，然后才dispatch一个action
// 相当于await，执行完deley(1000)才会开始执行put({type:'DEMO'})
```

```js
// Generator函数输出结果
const gen = demoAsync()
gen.next() // => { done: false, value: <result of calling delay(1000)> }
gen.next() // => { done: false, value: <result of calling put({type: 'DEMO'})> }	
gen.next() // => { done: true, value: undefined }
```

添加一个saga，使用 `takeEvery` 监听所有的 `DEMO_ASYNC` action，并在 action 被匹配时执行 `demoAsync` 任务

```js
import { delay } from 'redux-saga'
import { put, takeEvery } from 'redux-saga/effects'

export function* demoAsync() {
  yield delay(1000)
  yield put({ type: 'DEMO' })
}

export function* watchDemoAsync() {
  yield takeEvery('DEMO_ASYNC', demoAsync)
}
```

同时启用多个 saga，一般将配置同时启动多个sage的方法命名为 `rootSaga`，作为所有saga的启动器，且 `sagaMiddleware.run(rootSaga)`

```js
import { delay } from 'redux-saga' 
import { put, takeEvery, all } from 'redux-saga/effects'
import { helloSaga } from './sagas'

export function* demoAsync() {
  yield delay(1000)
  yield put({ type: 'DEMO' })
}

export function* watchDemoAsync() {
  yield takeEvery('DEMO_ASYNC', demoAsync)
}

// all 方法 yield 了一个数组，值是调用 helloSaga 和 watchIncrementAsync 两个 Saga 的结果
export default function* rootSaga() {
  yield all([
    helloSaga(),
    watchIncrementAsync()
  ])
}
```

**总体使用思想**

`put(action) = dispatch(action)`，reducer接收到对应的`action,type` 就会对数据进行相应操作，同时使用辅助函数监听对应的`action.type`并调用对应saga函数，最后通过`react-redux`的`connect`回到视图中，完成一次数据驱动视图（MVVM）

可以使用其他effect方法，帮助拿到对应请求数据，再执行`put()`将对应操作所需数据传入（如call）

实际思想和thunk一样，将数据处理完必后将最终的action传递给put函数触发reducer从而操作state



-----

#### 创建Effects

```
使用 redux-saga/effects 包里提供的函数来创建 Effect
【资料参考https://juejin.im/post/5ad83a70f265da503825b2b4】
```

在 `redux-saga` ，Sagas 都用 Generator 函数实现，从 Generator 里 yield 纯 JavaScript 对象表达 Saga 逻辑， 这个对象就是 Effect，因为Effect的存在，方便saga测试异步操作

Effect本质是一个特定的函数，返回的是纯文本对象（解释执行的信息），通过Effect函数，会返回一个字符串，saga-middleware根据这个字符串来执行真正的异步操作，可以具体表现成如下形式：

```
异步操作——>Effect函数——>纯文本对象——>saga-middleware——>执行异步操作
```

因此effect的api方法在使用时要加上yield

```js
function * rootSaga() {
	const resultA = yield put({type: 'TEST'})
	const resultB = put({type: 'TEST'})
	console.log(resultA)	// 触发传入的action
	console.log(resultB)	// 一个包含effect内容的对象，用于给saga解析
}
```

Effect 一般发送给 middleware 的指令以执行的操作：

- 发起一个异步调用（如发一起一个 Ajax 请求）
- 发起其他的 action 从而更新 Store
- 调用其他的 Sagas

```js
yield takeEvery('DEMO_ASYNC', demoAsync)	// 如takeEvery('DEMO_ASYNC', demoAsync) 返回一个对象，称为effect
```

**Effect创建器**

下面介绍的api都是effect创建函数

- 每个函数都会返回一个普通 Javascript 对象，并且不会执行任何其它操作
- 执行是由 middleware 在上述迭代过程中进行的
- middleware 会检查每个 Effect 的描述信息，并进行相应的操作

**pattern 类型**

大部分的 Effect 创建函数的参数都为 `pattern` 类型，它有如下规则：

- 如果以空参数或 `'*'` ，那么将匹配所有发起的 action

- 如果是一个函数，那么将匹配 `pattern(action)` 为 true 的 action

- 如果是一个字符串，那么将匹配 `action.type === pattern` 的 action‘

- 如果它是一个数组，那么数组中的每一项都适用于上述规则 —— 因此它是支持字符串与函数混用的

```js
// 将匹配所有 action
take() 	
take('*')

// 将匹配 entities 字段为真的 action
take(action => action.entities)		
// 如果 pattern 函数上定义了 toString，action.type 将改用 pattern.toString 来测试
// 这个设定在使用 action 创建函数库（如 redux-act 或 redux-actions）时非常有用

// 匹配type === 'INCREMENT_ASYNC' 的action
take('INCREMENT_ASYNC')	

// 一般都是用纯字符串数组
take(['INCREMENT', 'DECREMENT']) 	// 将匹配 INCREMENT 或 DECREMENT 类型的 action
```

```markdown
middleware 提供了一个特殊的 action —— END
	如果发起 END action，则无论哪种 pattern，只要是被 take Effect 阻塞的 Sage 都会被终止
	假如被终止的 Saga 下仍有分叉（forked）任务还在运行，那么它在终止任务前，会先等待其所有子任务完成
```



------

##### put

`put(action)`：使用和功能跟 dispatch   一样，但是一般只在saga函数内部使用，如果在外部想触发reducer还是用 dispatch，可以理解为和thunk中将 dispatch 方法传入返回函数中手动调用一样

```js
// 直接使用 action 对象
put({type:'DEMO_TYPE'})		

// 使用 action 创建函数
actionFun = (params) => {
    return {
        type: prarms
    }
}
put(actionFun('DEMO_TYPE'))
```

通过put可以实现和`react thunk`一样的功能（put中只能传进一个对象，如果像thunk那样传递一个嵌套的调用函数会报错）

```ts
const fun = () => {
	return new Promise((resove) => {
		setTimeout(() => {
			console.log('阻塞执行')
			resove()				// call在promise中遇到resolve前会堵塞
		},1000)
	})
}

function* Demo() {	
	yield call(fun)					// 阻塞操作
	yield put({type: 'ADD'})		// 等待call执行完毕后才dispatch一个最终action给reducer处理，这一步体现了saga的中间件思想
}
```



--------

##### take

`take(pattern)`：参数是pattern类型

take是阻塞的，主要用来监听 `action.type`，只有`dispatch` 或 `put` 对应的 `action.type` ，才会继续往下执行程序，否则会一直堵塞

创建一个 Effect 描述信息，用来命令 middleware 在 Store 上等待指定的 action，Generator 将暂停直到在发起与 `pattern` 匹配的 action 之前，即一般用take来等待用户的输入操作，如请求到信息后，再点击一次按钮，信息才显示

```
take 和 takeEvery 都是监听某个 action, 但是两者的作用却不一致，takeEvery 是每次 action 触发的时候都响应，而 take 则是执行流执行到 take 语句时才响应
takeEvery 只是监听 action, 并执行相对应的处理函数，对何时执行 action 以及如何响应 action 并没有多大的控制权，被调用的任务无法控制何时被调用，并且它们也无法控制何时停止监听，它只能在每次 action 被匹配时一遍又一遍地被调用
take 可以在 generator 函数中决定何时响应一个 action 以及 响应后的后续操作
```

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
import createSagaMiddleware from 'redux-saga'
import { createStore, applyMiddleware, Action } from 'redux'
import { takeEvery, take, all } from 'redux-saga/effects'

// reducer
const reducer = (initState:number = 0, action: Action) => {
	switch(action.type) {
		case('ADD'):
			console.log('执行reducer')
			return initState + 1
		default: 
			return initState
	}
}

// saga
const sagaMiddleware = createSagaMiddleware()

const store = createStore(
	reducer,	
	applyMiddleware(sagaMiddleware)	
)

function* A() {	
	yield take('ADD')
	console.log('执行take')
	
}

function* B() {
	yield takeEvery('ADD', () => {
		console.log('takeEvery')
	})
}

function* C() {
	yield take('DEC')
    console.log('执行DEC')
}

function* D() {
    console.log('开始执行')
}

function* rootSaga() {
	yield all([A(), B(), C(), D()])}

sagaMiddleware.run(rootSaga)

// 触发dispatch事件
const dispatchEvent = () => {
	store.dispatch({type: 'ADD'})
}

ReactDOM.render(
	<>
		<button onClick={dispatchEvent}>触发dispatch按钮</button>
	</>,
	document.getElementById('root')
);

// 页面刷新，打印：开始执行，sagaMiddleware.run(rootSaga) 是启动所有saga的起点
// 第一次点击按钮，打印：执行reducer → 执行take → 执行takeEvery，说明先触发的reducer才触发saga函数
// 多次点击按钮，打印：reducer → 执行takeEvery，说明take的执行次数由take语句个数决定，takeEvery每次都会触发
// 不打印 `执行DEC`，说明不对应的action.type会被take阻塞，不会往下执行
```

使用 `take` 来模拟 `takeEvery`

可以在take的返回值中拿到传入的完整action对象，而takeEvery可以在回调函数的形参中取得

```js
import { takeEvery, take } from 'redux-saga'

function* watchTakeEvery() {
    // 使用回调函数去执行触发操作
    yield* takeEvery('*', function* logger(action) {		
        console.log(action)	
    })
}

function* watchTake() {
    // 不需要回调函数，与take并行
    while(true) {
        const action = yield take('*')
        console.log(action)
    })
}

//  while(true)的作用：一旦到达流程最后一步（logger），通过等待一个新的任意的 action 来启动一个新的迭代（logger 流程）
```

take阻塞的其他用法：

- 触发次数执行

```js
// 在 take 初次的 3 个 TODO_CREATED action 之后， watchFirstThreeTodosCreation Saga 将会使应用显示一条祝贺信息然后中止，这意味着 Generator 会被回收并且相应的监听不会再发生

import { take, put } from 'redux-saga/effects'

function* watchFirstThreeTodosCreation() {
    for (let i = 0; i < 3; i++) {
        const action = yield take('TODO_CREATED')
        }
    yield put({type: 'SHOW_CONGRATULATION'})
}
// 一共有三条 yield take('TODO_CREATED') 语句，因此直到put('TODO_CREATED')三次前，put({type: 'SHOW_CONGRATULATION'})一直处于不执行状态，当执行完三次put('TODO_CREATED')后执行该语句
```

- 阻塞状态

  由于take具有堵塞作用，如果写多个take则有在不同状态执行不同的效果，如登入和注销

```js
function* loginFlow() {
  while (true) {
    yield take('LOGIN')
    // 一系列登入后的操作
    yield take('LOGOUT')
    // 一系列注销后的操作
  }
}
```

---------

##### call 和 fork

这两个函数主要都是用来发起异步操作的（如发送请求），不同的是 call 发起的是阻塞操作，即必须等待该异步操作完全完成才能进行后续的操作，而 fork 是非阻塞的，因此可以使用 fork 来实现多个并发的请求操作（fork相当于生成了一个task——一个在后台运行的进程）

- `call(fn, ...args)`：第一个参数是一个generator函数（也可以是一个返回Promise或任意其它值的普通函数），后面的参数都是依次传入这个函数的形参数值，返回值是promise的resolve结果

  call默认是接收到promise成功标志时解除阻塞，但是如果是普通函数的话，可能达不到阻塞的效果

```js
const fun = () => {
	return new Promise((resove) => {
		setTimeout(() => {
			console.log('阻塞执行')
			resove('promise结果')				// call在promise中遇到resolve前会堵塞
		},1000)
	})
}

function * rootSaga() {
	const result = yield call(fun)			// result内容为：'promise结果'
	console.log('执行完毕')
}
// 执行结果：阻塞执行 → 执行完毕
```

```js
const fun = () => {
	setTimeout(() => {
		console.log('阻塞执行')
	}, 4000);
}

function * rootSaga() {
	yield call(fun)
	console.log('执行完毕')
}
// 执行结果：执行完毕 → 阻塞执行 
```

搭配take使用的例子：

```js
function* loginFlow() {
  while(true) {
    const {user, password} = yield take('LOGIN_REQUEST')	// 拿到触发reducer的`LOGIN_REQUEST`分支的action并解构
    const token = yield call(authorize, user, password)		// 将用户信息传入登入函数authorize，拿到登入信息
    if(token) {
      yield call(Api.storeItem({token}))
      yield take('LOGOUT')
      yield call(Api.clearItem('token'))
    }
  }
}
```

- `fork(fn, ...args)`：参数同理，与call不同，返回的是一个任务标志task，可以和下面api搭配使用、
  - `join(taks)`：阻塞等待直到该fork任务返回的结果
  - `cancel(task)`：取消指定的任务，
  - `cancelled()`：返回一个布尔值，用于判断所在generator函数是否被取消，通常在finally区块中使用该effect来运行取消时的专用代码

```js
// join的效果
const fun = () => {
	return new Promise((resove) => {
		setTimeout(() => {
			console.log('执行完毕')
			resove(1)				
		},1000)
	})
}

function * rootSaga() {
	const task = yield fork(fun)
	let result =  yield join(task)
	console.log(result)
}
// 打印结果：执行完毕 → 1，说明join阻塞了下面打印语句的执行
```

```js
// cancel的效果
function * rootSaga() {
	const taskA = yield fork(fun)
	const taskB = yield fork(fun)
	console.log(taskA.isRunning())		// true
	console.log(taskB.isRunning())		// false
}
```

```js
// cancelled的效果
function* fun (name){
	try {
		yield new Promise((resove) => {
			setTimeout(() => {
				console.log('执行完毕')
				resove(1)				
			},10000)
	})
	} finally {
		if(yield cancelled()) {
			console.log(`${name}任务被取消了`)
		}
	}

}

function * rootSaga() {
	const taskA = yield fork(fun,'A')
	const taskB = yield fork(fun,'B')
	yield cancel(taskB)
	console.log(taskA.isRunning())		// true
	console.log(taskB.isRunning())		// false
}
// 打印：B该任务被取消了，说明取消B任务时会触发cancelled()
```

​		通过 `fork`、`middleare.run` 或 `runSaga` 运行Saga会返回一个任务标志task

| 方法               | 返回值                                                       |
| ------------------ | ------------------------------------------------------------ |
| task.isRunning()   | 若任务还未返回或抛出了一个错误则为 true                      |
| task.isCancelled() | 若任务已被取消则为 true                                      |
| task.result()      | 任务的返回值。若任务仍在运行中则为 `undefined`               |
| task.error()       | 任务抛出的错误。若任务仍在执行中则为 `undefined`             |
| task.done          | 一个 Promise：以任务的返回值 resolve 或 以任务抛出的错误 reject |
| task.cancel()      | 取消任务（如果任务仍在执行中）                               |

```js
const fun = () => {
	return new Promise((resove) => {
		setTimeout(() => {
			resove(100)				
		},1000)
	})
}

function * rootSaga() {
	const task = yield fork(fun)

	// 任务执行中
	console.log(task.isRunning())		// true	
	console.log(task.result())			// undefined	从这里可以看出和join的区别，join是等待完成才拿结果
	console.log(task.done)				// undefined
	
	// 任务结束
	setTimeout(() => {
		console.log(task.isRunning())	// false
		console.log(task.result())		// 100
		console.log(task.done)			// undefined	具体为什么待查清
	},2000)
}
```

```js
const fun = () => {
	return new Promise((resove) => {
		setTimeout(() => {
			console.log('执行完毕')
			resove(1)				
		},10000)
	})
}

function * rootSaga() {
	const taskA = yield fork(fun)
	const taskB = yield fork(fun)
	console.log(taskA.isRunning())			// true
	console.log(taskB.isRunning())			// true
	taskB.cancel()							
	setTimeout(() => {
		console.log(taskA.isRunning())		// ture
		console.log(taskB.isRunning())		// flase
	},2000)
}
// task.cancel()和cancel(task)不同的是由于不是effect函数，因此不用yield才能生效，如果这里直接使用cancel(taskB)，那下面打印的会是true
```

上面代码有一个问题：即使在运行过程中取消了taskB继续执行，但是10s后仍会打印两次`执行完毕`，即有取消了任务操作，但是并没有马上中断后续任务的执行

```js
// 改成Generator函数同理
function* fun() {
	yield new Promise((resove) => {
		setTimeout(() => {
			console.log('执行完毕')
			resove(1)				
		},10000)
	})
}
```

改写fun函数，使用请求的方式，由效果可知，若要令取消任务生效，需要设置为 `Generator` 函数（且无法使用延时函数进行模拟，中断一次请求和取消一次延时任务有点差异，这个待验证）

```js
// 打印了两次请求数据data
const fun = async () => {
    let data = await fetch('www.baidu.com')	
    console.log(data)
}
```

```js
// 打印了一次请求数据data
function* fun(){
    let data = yield fetch('www.baidu.com')
	console.log(data)
}
```

 

-------

##### 辅助函数

- `takeEvery(pattern, saga, ...args)`

`takeEvery` 允许多个 saga 任务同时启动，尽管之前还有一个或多个 实例尚未结束，我们还是可以启动一个新的任务

如：点击一个按钮发送一个异步请求，当请求还没完全结束时，再次点击按钮，则再发送一次全新的请求

```js
import { takeEvery } from 'redux-saga/effects'

function* getDataSaga() {
  try {
      yield put({type: 'OPEN_LOADING'})			// 开启加载 
      const res = yield call(() => fetch('www.baidu.com'))	// 发送请求
      if(res) {
          yield put({type: 'SUCCESS'})			// 成功请求
      }
  } catch(err) {
      yield put({type: 'ERROR'})				// 错误处理
  } finally {
      yield put('CLOSE_LOADING')				// 在结束时关闭加载
  }
}  
            
takeEvery('GET_DATA_REQUEST', getDataSaga)		// 监听 action.type为GET_DATA_REQUEST的action，并运行getDataSaga()
```

- `takeLatest(pattern, saga, ...args)`

  `takeLatest` 只允许一个 `demoAsync` 任务在执行，并且这个任务是最后被启动的那个，如果已经有一个任务在执行的时候启动另一个 `demoAsync` ，那之前的这个任务会被自动取消

  如：点击一个按钮发送一个异步请求，当请求还没完全结束时，再次点击按钮，取消未完成的请求，再发送一次全新的请求

```js
import { takeLatest } from 'redux-saga/effects'
...
takeLatest('DEMO_ASYNC', demoAsync)
```

当触发辅助函数时，会把触发的action对象传入对应的saga函数

```ts
dispatch({type: 'DEMO', payload: '数据'})

function * sagaDemo (action) {
    console.log(action)		// {type: 'DEMO', payload: '数据'}
}

takeEvery('DEMO', sagaDemo)	
```



-------

##### 组合器

用于组合各Effects

- `all([...effects])`：与Promise中的all类似，middleware 并行地运行多个 Effect，并等待它们全部完成，返回一个数组，用于接收执行结果

```js
import { call, race } from `redux-saga/effects`

function* mySaga() {
  const [customers, products] = yield all([
    call(Demo_A),
    call(Demo_B)
  ])
}
```

也可以直接省略all，因为当 yield 后面是一个数组时，数组里面的操作将按照 Promise.all 的执行规则来执行，genertor 会阻塞所有的 effects 被直到所有 effects 执行完成

```js
// 同步执行和按顺序的写法区别
import { call } from 'redux-saga/effects'

function * fetch(payload) {
    return fetch(
        `http://www.baidu.com/payload=${payload}`,
        { method: 'GET' }
    ).then(res => res.json())
}

//同步执行
const [users, products] = yield [
  call(fetch, '/users'),
  call(fetch, '/products')
]

//顺序执行
const users = yield call(fetch, '/users'),
const products = yield call(fetch, '/products')
```

```markdown
当并发运行 Effect 时，middleware 将暂停 Generator，直到以下任一情况发生：
	- 所有Effect都成功完成：返回一个包含所有Effect结果的数组，并恢复Generator
	- 在所有Effect完成之前，有一个Effect被reject：在Generator中抛出reject错误
```

- `race([...effects])`：与Promise中的race类似，如果任意一个effect先完成（无论reject或resolve），将其他effect取消执行，然后继续执行后续代码

```js
// 发起call(fetchUsers)操作，但是如果在这个操作还没有完成之前，Store上先发起了一个CANCEL_FETCH类型的 action,则取消call(fetchUsers)

import { take, call, race } from `redux-saga/effects`
import fetchUsers from './path/to/fetchUsers'

function* fetchUsersSaga {
  const [ response, cancel ] = yield race([
      call(fetchUsers),
      take('CANCEL_FETCH')
  ])
}
```



---

#### 代码设计思路

如实现一个登入的请求并保存返回的登入状态信息总体的思路：

1. 外部触发 `dispatch` 函数，将从表单中获取到的登入的信息传给能触发saga监听函数的action参数中
2. action拿到这些信息，通过saga监听函数`takeEvery` 触发saga函数
3. 通过saga去请求后端并拿到返回数据，同时触发影响reducer的action
4. reducer拿到数据信息，保存在redux的state中，用于在组件中使用

```markdown
总体流程：
	dispatch → 触发acion → 触发监听函数 → 触发saga函数 → 触发reducer → 更新state
	实际上就是相当于 dispatch(action) → 中间件对action进行处理 → dispatch(最终action) → 触发reducer → 更新state
```

上面需要值得注意的是，有两种action，一种是为了触发监听函数从而启动saga，一种是为了触发reducer函数，从这里可以看出saga是如何作为中间件来使用的

```ts
// action
const action = (info) => ({
    type: 'SAGA_ACTION',
    payload: info
})

// 外部触发action
dispatch(action(info))		// 将信息传给action

// 监听函数
function* rootSaga() {
    yield takeEvery('SAGA_ACTION',saga)		// 监听到有'SAGA_ACTION'的action被调用，触发saga函数	
}

// saga
function* saga(action) {
    const { payload } = action		// 拿到触发takeEvery函数的action对象并取出info数据
    yield call(fetch(...))		// 将拿到的数据发送给后端操作
    // 可能对返回数据做出一些处理，然后dispatch一个带有最终数据的action触发reducer函数
    yield put({
        type: 'REDUCER_ACTION',
        payload: newData
    })
}

// reducer
const reducer = (initState, action) => {
    switch(action.type) {
        // 从saga那dispatch的'REDUCER_ACION'被reducer判断并执行
        case 'REDUCER_ACION':
            const { payload } = action	// 拿到最终数据
            // 进行一些操作，最终更新state
            return newState
    }
}
```



----

#### 超时自动取消

利用saga的 `race` 实现超时自动取消功能（axios也能做到但是这里使用saga来实现）

```js
// 整个功能目的：发出一个请求，可以手动取消或者自动超时取消，如果一段时间内没有手动取消任务，则会执行自动超时取消（假设请求的时间很长，超过超时自动取消的时间）
function* controlSaga (action) {
    const task = yield fork(getDataSaga); // 运行getDataSaga函数
    yield race([ // 利用rece谁先来用谁的原则，完美解决 超时自动取消与手动取消的 的问题
        take('CANCEL_REQUEST'), // 手动取消action
        call(delay, 1000) // 延时1秒
    ]);
    yield cancel(task);
}
// call和take任意一个完成都会取消阻塞往下执行cancel，因此可以理解为cancel最多被阻塞
```



---

#### 高阶函数封装

可以写一个高阶函数（返回一个Generator函数）统一封装saga的功能

```js
// controlSaga.js
import { take, fork, race, call, cancel, put } from 'redux-saga/effects';
import { delay } from 'redux-saga';

function controlSaga (fn) {
    /**
     * @param timeOut: 超时时间, 单位 ms, 默认 5000ms
     * @param cancelType: 取消任务的action.type
     * @param showInfo: 打印信息 默认不打印
     */
    return function* (...args) {
        // 这边思考了一下，还是单单传action过去吧，不想传args这个数组过去, 感觉没什么意义
        const task = yield fork(fn, args[args.length - 1]);
        const timeOut = args[0].timeOut || 5000; // 默认5秒
        // 如果真的使用这个controlSaga函数的话，一般都会传取消的type过来, 假如真的不传的话，配合Match.random()也能避免误伤
        const cancelType = args[0].cancelType || `NOT_CANCEL${Math.random()}`;
        const showInfo = args[0].showInfo; // 没什么用，打印信息而已
        const result = yield race({
            timeOut: call(delay, timeOut),
            // 实际业务需求
            handleToCancel: take(cancelType)
        });
        if (showInfo) {
            if (result.timeOut) yield put({type: `超过规定时间${timeOut}ms后自动取消`})
            if (result.handleToCancel) yield put({type: `手动取消，action.type为${cancelType}`})
        }
    
        yield cancel(task);
    }
}

export default controlSaga;
```

```js
import controlSaga from './controlSaga';

function* demoSaga() {
	... 
}
    
function* rootSaga() {
    yield takeLatest('TYPE', controlZaga(demoSaga))
}
```



---

#### thunk和saga

假如有一个场景：用户在登录的时候需要验证用户的 username 和 password 是否符合要求，双方的action写法会有很大不同

**redux-thunk**

```js
// 使用thunk时，一般会把这两个action分成两个文件来写，这里为了方便不进行拆分
import request from 'axios';

// 获取用户数据的逻辑的action
export const loadUserData = (uid) => async (dispatch) => {
    try {
        dispatch({ type: USERDATA_REQUEST });
        let { data } = await request.get(`/users/${uid}`);
        dispatch({ type: USERDATA_SUCCESS, data });
    } catch(error) {
        dispatch({ type: USERDATA_ERROR, error });
    }
}


// 验证登录的逻辑的action
export const login = (user, pass) => async (dispatch) => {
    try {
        dispatch({ type: LOGIN_REQUEST });
        let { data } = await request.post('/login', { user, pass });
        await dispatch(loadUserData(data.uid));		// 在输入登入数据后验证用户信息
        dispatch({ type: LOGIN_SUCCESS, data });
    } catch(error) {
        dispatch({ type: LOGIN_ERROR, error });
    }
}
```

**redux-saga**

```js
import request from 'axios';

export function* loadUserData(uid) {
  try {
    yield put({ type: USERDATA_REQUEST });
    let { data } = yield call(userRequest, `/users/${uid}`);
    yield put({ type: USERDATA_SUCCESS, data });
  } catch(error) {
    yield put({ type: USERDATA_ERROR, error });
  }
}

export function* loginSaga() {
  while(true) {
    const { user, pass } = yield take(LOGIN_REQUEST) 	// 等待Store上指定的action: LOGIN_REQUEST
    try {
      let { data } = yield call(loginRequest, { user, pass }); 	 // 阻塞,请求后台数据
      yield fork(loadUserData, data.uid); 	// 非阻塞执行loadUserData
      yield put({ type: LOGIN_SUCCESS, data }); 	//发起一个action，类似于dispatch
    } catch(error) {
      yield put({ type: LOGIN_ERROR, error });
    }  
  }
}
```



----

### 查看中间件状态

#### redux-logger

redux-logger会在console打印当前触发的action，包括action的上一个状态，当前状态，下一个状态

```
npm install redux-logger -s
```

logger 中间件必须放在所有中间件的最后，不然会出现部分action无法打印日志的情况

```js
import logger from 'redux-logger'
import thunk from 'redux-thunk'

const store = createStore(
    reducer,
    applyMiddleware(thunk, logger)
)
```

一般会进行额外配置

```js
import createLogger from 'redux-logger'
// 调用日志打印方法 collapsed是让action折叠，看着舒服点
const loggerMiddleware = createLogger({collapsed:true})

const store = createStore(
    reducer,
    applyMiddleware(loggerMiddleware)
)
```

<img src="https://img-blog.csdnimg.cn/20201019045649113.png" style="margin:0"/>

---

#### redux-devtools

用于查看redux中间件的数据传递过程，使用不仅要在浏览器中安装插件（Redux Developer），也要在代码中配置，可以用于替代 `redux-logger`

chrom浏览器安装完成后右上角会出现图标，在配置的页面中可以点卡使用

```
npm install redux-devtools-extension -s
```

```js
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(
    reducer,
    composeWithDevTools(applyMiddleware(中间件))
)
```

<img src="https://img-blog.csdnimg.cn/20201019042230942.png" style="margin:0"/>

