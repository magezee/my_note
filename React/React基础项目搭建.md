## React基础项目搭建

### 基本内容构建

<u>有`Demo`命名的件仅用于举例，可随意删改</u>

搭建好的模板

```
git clone https://github.com/magezee/reactDemo.git
然后进入项目 npm install 下载依赖所需配置npm包
```

- 使用脚手架构建项目

```
npx create-react-app project_name
```

- 删除src目录下的所有文件，如有必要public目录下的也可删除

- 在src目录下建立自己的index.js，启动项目的main是index.js

**文件管理（默认统一规定）**

- 启动脚本管理

  常规的react项目都会有一个index.js和App.js

  `index.js`用于启动文件，通常只渲染app.js里导出的App组件和根页面

```jsx
import React from 'react'
import {render} from 'react-dom'
import App from './App.js'

render(
    <App />,
    document.querySelector('#root')
)
```

​		`App.js`用于当做组件的根组件，导入并渲染所需子组件（不只是用作渲染，也用于进行数据处理和事件声明）

```jsx
import React,{Component} from 'react'
import { ComponentDemo } from './components'

export default class App extends Component {
    render() {
        return(
            <ComponentDemo/>
        )
    }
}
```

- 组件目录管理

  在`src`目录下新建一个目录`components`用于存储组件，每编写一个新组建，相应的再该目录下再新建一个组件名的目录，将组件文件放置于此

```jsx
// /src/components/ComponentDemo
import React, { Component } from './react'

export default class ComponentDemo extends Component {
    render() {
        return (
            <div>
                A component for export
            </div>
        )
    }
}
```

​		在`components`目录下新建一个index.js文件，目的是用于接收所有导入组件并导出，这样在使用组件时实际上只需要定位components即可

```jsx
// /src/components/index.js
export {default as  ComponentDemo} from './ComponentDemo'
```

- 页面

  /src目录下创建`views`目录

  然后建立各自的网页（使用components里的组件来组成页面），views下面的目录结构和components目录结构类似

- 路由管理

  /src目录下创建`routes`目录和index.js，使用路由时则导入该index.js

- 资源静态

  根目录下创建`public`目录一般资源均放于此（如图像、图标等）

  

----

### webpack配置

做webpack配置：暴露项目的webpack.config.js文件，直接在该文件中进行修改配置（之前会有prod和dev两个环境的相关配置，但是新版run eject之后只存在一个webpack.config.js文件）

但是这样过于麻烦，因此通过`react-app-rewired`来配置webpack.config.js，这种配置方式无需生成更多额外的文件，同时更简单可控

```
yarn add react-app-rewired customize-cra
```

更改项目package.json文件

```json
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
	// 改为
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test",
    "eject": "react-app-rewired eject"
  },
```

在项目根目录创建一个 `config-overrides.js` 用于修改默认配置

```jsx
const { override } = require('customize-cra');		// // 解构
module.exports = override(
	A(),
    B(),
    ...		// 各种配置项一起写在这里
)
```

#### less

使项目能识别less文件以代替css

```
yarn add less less-loader
```

注意：less-loader现在的版本这么操作会有问题（less-loader：5.0.0版本可用）

```jsx
const { override, addLessLoader } = require('customize-cra');
module.exports = override(
	addLessLoader({
		javascriptEnabled: true,
	}),
)
```

#### 装饰器

使项目能使用装饰器

```
yarn add @babel/plugin-proposal-decorators	// 安装修饰符插件
```

```jsx
const { override, addDecoratorsLegacy } = require('customize-cra')		
module.exports = override(
    addDecoratorsLegacy()   // 配置适配器模式方法 这里可以传很多方法
)
```



----

### Redux配置

```jsx
yarn add redux 
```

#### reducers

在项目src目录下创建`reducers文件夹`，用于统一存放reducer文件，创建index.js文件，用于统一导入reducer文件并导出

```jsx
// /src/reducers/indes.js
import {combineReducers} from 'redux'	
import reducerDemo from './reducerDemo'
	
export default combineReducers({	// // redux专门用来提供合并reducers的工具
    reducerDemo,
})
```

一开始进行reducer的编写时，只需写明state和声明一个简单方法导出即可，后续再加功能

```jsx
// /src/reducers/reducerDemo/indes.js
const reducerDemo= {
    demo:'this is a demo string'
}

export default (state = reducerDemo, action) => {
    switch(action.type) {
    	default:
    	return state
    }
}
```

#### store

在项目的src目录下创建`store.js`文件（注意只能存在一个store）

```jsx
import {createStore} from 'redux'
import rootReducer from './reducers'

export default createStore(rootReducer)
```

#### actions

在项目src目录下创建`actions文件夹`，用于统一存放action文件

一般新建一个`actionType.js`文件统一对action的type进行管理并导出，然后另创建具体的action.js文件使用`action创建函数`的方式导出具体action

```jsx
// /src/actions/actionType.js
export default {
    TYPE_DEMO_A: 'TYPE_DEMO_A',
    TYPE_DEMO_B: 'TYPE_DEMO_B' 
}
```

```jsx
//  /src/actions/actionDemo.js
import actionType from './actionType'

export const typeA = () =>{
    return {
        type: actionType.TYPE_DEMO_A
    }
}

export const typeB = () =>{
    return {
        type: actionType.TYPE_DEMO_B
    }
}

```

在reducer里导入对应action

```jsx
// /src/reducers/reducerDemo/index.js
import actionType from '../../actions/actionType'

const reducerDemo= {
    demo:'this is a demo string'
}

export default (state = reducerDemo, action) => {
    switch(action.type) {
		case actionType.TYPE_DEMO_A:
            return (( )=>{
                state.demo ='A was chosed'
                alert(state.demo)
                return state
            })() 
        case actionType.TYPE_DEMO_B:
            return (( )=>{
                state.demo ='B was chosed'
                alert(state.demo)
                return state
            })() 
        default:
            return state
    }
}
```

在要用的组件中dispatch

```jsx
// /src/compoments/ComponentDemo/index.js
import React, { Component } from 'react'
import store from '../../store'
import {typeA, typeB} from '../../actions/actionDemo'
export default class ComponentDemo extends Component {
    render() {
        return (
            <div>
                <button onClick = {() => {
                    store.dispatch(typeA())
                }}>actionA</button>

                <button onClick = {() => {
                    store.dispatch(typeB())
                }}>actionB</button>
            </div>
        )
    }
}
```

#### Provider

使用provider进行快捷组件交流，不需要多层传递数据

使用时需要将其包在要传递组件的最外层，由于`<App/>`为组件渲染根元素，因此任意子组件内都可直接使用provider传递的数据，无需层层相传，必须要要拥有store属性，值为创建的store

```jsx
// /index.js
import React from 'react'
import { render } from 'react-dom'
import App from './App'
import store from './store'
import {Provider} from 'react-redux'

console.log(store.getState())
render(
        
    <Provider store={store}>
        <App />
    </Provider>,   
    
    document.querySelector('#root')
)
```

#### connect

在对应组件内通过connect( )( )自动生成的容器组件（高阶组件），即可通过映射的方式使用redux的方法，而不是每次都要导入store

```jsx
// /src/compoments/ComponentDemo/index.js
import React, { Component } from 'react'
import {typeA, typeB} from '../../actions/actionDemo'
import {connect} from 'react-redux'

class ComponentDemo extends Component {
    render() {
        console.log(this.props)
        return (
            <div>
                <button onClick = {() => {
                   this.props.chooseA()
                }}>actionA</button>

                <button onClick = {() => {
                    this.props.chooseB()
                }}>actionB</button>
            </div>
        )
    }
}

// 映射state到props
const mapStateToProps = (state) => {
    return {
        DomoProps: state
    }
}

// 映射dispatch到props
const mapDispatchToProps = (dispatch, ownProps) => {
    return {
      chooseA: () => { dispatch(typeA()) },
      chooseB: () => { dispatch(typeB()) }
    }
}

export default connect(mapStateToProps,mapDispatchToProps)(ComponentDemo) 
```

#### redux-thunk

使用`redux-thunk`处理异步action

```
yarn add redux-thunk
```

直接在`store.js`文件里导入即可（同时要导入使用applyMiddleware方法）

```jsx
import {createStore, applyMiddleware} from 'redux'
import thunk from 'redux-thunk'
import rootReducer from './reducers'

export default createStore(
    rootReducer,
    applyMiddleware(thunk))
```

一般而言，会把同步action和异步操作分开，同步action用于定义action.type等数据，而异步只引用同步实现异步方法

```jsx
//  /src/actions/actionDemo.js
import actionType from './actionType'

export const typeA = () =>{
    return {
        type: actionType.TYPE_DEMO_A
    }
}

export const typeB = () =>{
    return {
        type: actionType.TYPE_DEMO_B
    }
}


const typeC = () =>{
    return {
        type: actionType.TYPE_DEMO_C
    }
}

// 异步action 使用redux-thunk之后就可以在actionCreator里return一个方法，此方法的参数是dispatch（只要return了方法会自动识别并传入dispatch方法）
export const typeCAsynC = () => {
    return (dispatch) => {
        setTimeout(() =>{
            dispatch(typeC())		// 手动dispatch
        },2000)
    }
}
```

将其他文件实现按钮C的代码效果补上（并不重要可忽略）

```jsx
// /src/actions/actionType.js
export default {
    TYPE_DEMO_A: 'TYPE_DEMO_A',
    TYPE_DEMO_B: 'TYPE_DEMO_B',
    TYPE_DEMO_C: 'TYPE_DEMO_C' 
}
```

```jsx
// /src/reducers/reducerDemo/index.js
import actionType from '../../actions/actionType'

const reducerDemo= {
    demo:'this is a demo string'
}

export default (state = reducerDemo, action) => {
    switch(action.type) {
		case actionType.TYPE_DEMO_A:
            return (( )=>{
                state.demo ='A was chosed'
                alert(state.demo)
                return state
            })() 
        case actionType.TYPE_DEMO_B:
            return (( )=>{
                state.demo ='B was chosed'
                alert(state.demo)
                return state
            })() 
        case actionType.TYPE_DEMO_C:
            return (( )=>{
                state.demo ='C was chosed(delay)'
                alert(state.demo)
                return state
            })()     
        default:
            return state
    }
}
```

```jsx
// /src/compoments/ComponentDemo/index.js
import React, { Component } from 'react'
import {typeA, typeB, typeCAsynC} from '../../actions/actionDemo'
import {connect} from 'react-redux'


class ComponentDemo extends Component {
    render() {
        console.log(this.props)
        return (
            <div>
                <button onClick = {() => {
                   this.props.chooseA()
                }}>actionA</button>

                <button onClick = {() => {
                    this.props.chooseB()
                }}>actionB</button>

                <button onClick = {() => {
                    this.props.chooseC()
                }}>actionC</button>
            </div>
        )
    }

    
}

// 映射state到props
const mapStateToProps = (state) => {
    return {
        DomoProps: state
    }
}

// 映射dispatch到props
const mapDispatchToProps = (dispatch, ownProps) => {
    return {
      chooseA: () => { dispatch(typeA()) },
      chooseB: () => { dispatch(typeB()) },
      chooseC: () => { dispatch(typeCAsynC()) }
    }
}



export default connect(mapStateToProps,mapDispatchToProps)(ComponentDemo) 
```



-----

### 路由配置

```
yarn add react-router-dom
```

只需在项目文件index.js下使用一次Router包住渲染根组件

让路由工作，需要再导入`Route`组件，使用Router组件去调需要使用路由的组件，当访问指定路由时才渲染指定组件

```jsx
// index.js

import React from 'react'
import { render } from 'react-dom'
import App from './App'
import store from './store'
import {Provider} from 'react-redux'
import {BrowserRouter as Router, Route} from 'react-router-dom'

console.log(store.getState())
render(
        
    <Provider store={store}>
        <Router>
            {/* component，path为固定属性不可更改,即路由为'/'时渲染App组件，和不写明path效果一样 */} 
            <Route component={App} path='/'/>  
        </Router>
    </Provider>,   
    
    document.querySelector('#root')
)
```





## 项目构建思想

层级关系：

- index渲染根组件app和关键入口主路由（如首页和404等平级关系）

- app渲染路由

- 路由渲染页面

- 页面渲染组件

  

