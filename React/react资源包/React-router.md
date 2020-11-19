## React-router

```jsx
yarn add react-router-dom
```

### 基本使用

#### 配置

只需在项目文件index.js下使用一次Router包住渲染根组件

`Router`组件只用于最外层，提供两种路由方式

- BrowserRouter

  `http://localhost:3000`

- HashRouter

  访问url时会在后面默认带上一个#：` http://localhost:3000/#/`

让路由工作，需要再导入`Route`组件，使用Router组件去调需要使用路由的组件，当访问指定路由时才渲染指定组件

```jsx
import React from 'react'
import {render} from 'react-dom'
import App from './App'

import {BrowserRouter as Router,Route} from 'react-router-dom'
//import {HashRouter as Router} from 'react-router-dom'
render(
    <Router>
        {/* component，path为固定属性不可更改,即路由为'/'时渲染App组件，和不写明path效果一样 */} 
        <Route component={App} path='/'/>  	
    </Router>,
    document.querySelector('#root')
)
```

如下所示，此时可以用`http://localhost:3000/home`去访问对应的路由，显示的内容为app公共的内容加上/home对应组件的内容，不会显示artical和users组件的内容

```jsx
import React, { Component } from 'react';
import {Home, Artical, Users } from './views'
import { Route } from 'react-router-dom';

export default class App extends Component {
    render() {
        return (
            <React.Fragment>
            	<div>App公共内容</div>
            	<Route component={Home} path='/home'/>
            	<Route component={Artical} path='/artical'/>
           	 	<Route component={Users} path='/users'/>
            </React.Fragment>
        );
    }
}
```

当需要向组件中通过props传递数据时，就不可用`component={}`的方式渲染，选用`render`的方式，render需要传递一个方法，该方法的形参`routeProps`包含了路由的history、location等信息，需要一并向下传递

```jsx
<Route path='/home' render{ (routeProps)=>{
	return <Home {...routeProps} x = {1}/>
}/>
```

也可以加入条件判断（如权限认证）

```jsx
<Route path='/home' render{ (routeProps)=>{
	return this.state.isLogin ? <用户页/> : <登入页/>
}/>
```

**路径语法**

- `:paramName` – 匹配一段位于 `/`、`?` 或 `#` 之后的 URL。 命中的部分将被作为一个参数
- `()` – 在它内部的内容被认为是可选的
- `*` – 匹配任意字符（非贪婪的）直到命中下一个字符或者整个 URL 的末尾，并创建一个 `splat` 参数

```js
<Route path="/hello/:name">         // 匹配 /hello/michael 和 /hello/ryan
<Route path="/hello(/:name)">       // 匹配 /hello, /hello/michael 和 /hello/ryan
<Route path="/files/*.*">           // 匹配 /files/hello.jpg 和 /files/path/to/hello.jpg
```



----

#### 模糊/精确匹配

路由默认模糊匹配，如果要定义精确匹配需要在路由组件中添加 `exact={true}` 属性

```jsx
<Route component={xxx} path='/xxx' exact={true}/>
```

模糊匹配的规则是子地址可以匹配父地址

```jsx
<Route component={App} path='/'/>
<Route component={Home} path='/home'/>
<Route component={User} path='/home/users'/>

// 访问/home/user时可以额外匹配到/路由和/home 路由
// 访问/home无法匹配到/home/user路由
```

精确匹配的规则是必须完全满足路由地址才可以进行匹配

```jsx
<Route component={App} path='/'/>
<Route component={Home} path='/home' exact={true}/>
<Route component={User} path='/home/users'/>

// 访问/home/user时可以额外匹配到/路由，由于/home路由设置了精确匹配，因此无法匹配到
```



---

#### link和NavLink

自带的路由跳转超链接，点击跳转到指定路由，to为必须属性

Link本质上是一个`<a herf ="#/home">`，NavLink同理，只是会自动附上一个class，`<a herf ="#/home calss="active">`

```jsx
import { Link } from 'react-router-dom';
```

```jsx
<li><Link to='/home'>跳转到首页</Link></li>
```

-----

#### Redirect

访问匹配地址时重定向访问对应地址，重定向也可以理解为是一个路由

```jsx
import {Redirect} from 'react-router-dom';
```

```jsx
<Redirect to='/home' from='/' />  
// 当访问/时自动跳转访问/home
// from不写时默认表示/
```

当存在冲突的重定向时，以最先声明的优先级最高

```jsx
<Redirect to='/404' from='/test'  />
<Redirect to='/admin' from='/test'  />
// 访问/test时，重定向到/404
```



----

#### Switch

Switch的特点是：遍历寻找第一个满足匹配的路由，寻找到后不再继续匹配

```jsx
import {Switch} from 'react-router-dom'
```

不用switch包含的路由声明，会遍历一遍所有的路由，然后将可以满足匹配条件的路由都进行渲染

```jsx
<React.Fragment>
    <Route component={Home} path='/home'/>
    <Route component={Artical} path='/home/artical'/>
    <Route component={Users} path='/home/artical/users'/>
</React.Fragment>
// 访问/home/artical/users时会将三者的组件都渲染出来
```

Swithch组件内的路由或重定向组件只会执行一个，遍历寻找第一个满足匹配的路由（即：当A和B都满足匹配条件时，且A在B前声明，那只会匹配A不会匹配B）

```jsx
<React.Fragment>
    <Switch>
        <Route component={Home} path='/home'/>
        <Route component={Artical} path='/home/artical'/>
        <Route component={Users} path='/home/artical/users'/>
    </Switch>
</React.Fragment>
// 访问/home/artical/users时，/home路由满足条件，因此只渲染Home组件
```

----

**实现匹配子路由**

方法一：将最子层声明在最上层，根据switch最先的匹配机制，

```jsx
import React, { Component } from 'react';
import {Home, Artical, Users } from './views'
import { Route, Switch} from 'react-router-dom';
export default class App extends Component {
    render() {
        return (
            <React.Fragment>
                <Switch>
                	<Route component={Users} path='/home/artical/users'/>
                	<Route component={Artical} path='/home/artical'/>
                    <Route component={Home} path='/home'/> 
                </Switch>
            </React.Fragment>
        );
    }
}
// 访问'/home/artical'时，第一个路由不匹配，继续向下匹配，匹配成功，不会匹配到‘home’
```

方法二：添加`exact`属性（推荐），精准匹配，当遇到的路由不是完全满足匹配时，则会继续往下寻找

```jsx
import React, { Component } from 'react';
import {Home, Artical, Users } from './views'
import { Route, Switch} from 'react-router-dom';
export default class App extends Component {
    render() {
        return (
            <React.Fragment>
                <Switch>
                    <Route component={Home} path='/home' exact/>
                    <Route component={Artical} path='/home/artical' />
                    <Route component={Users} path='/home/artical/users'/>
                </Switch>
            </React.Fragment>
        );
    }
}
// 访问/home/artical/users，第一个寻找到的路由为'/home'，但由于设置的exact属性，只满足模糊匹配不满足精确匹配，所以会继续往下匹配，当匹配到第二个满足项'/home/artical'满足了模糊匹配，并且没有exact属性项，因此满足条件匹配到'/home/artical'并且不会继续向下匹配到'/home/artical/users'
```

<Redirect>里添加 exact的理解，和<route>同理

```jsx
<Redirect to='/404' from='/test' excat/> 	// 精确访问路由时，只有/test会跳转，/test/testChiid则不会
<Redirect to='/404' from='/test' />			// 模糊访问路由时，/test和/test/testChiid都会发生跳转
```

因此重定向组件一般要写在路由组件下面，不然就会直接执行重定向而找不到路由组件

```jsx
// 用/匹配404因为任意路由都可以模糊匹配到/
<Switch>
    <Route component={Artical} path='/admin/artical'/>
    <Route component={Settings} path='/admin/settings'/>
	<Redirect to='/admin' from='/' exact/>
	<Redirect to='/404' from='/' />
</Switch>
```

----

**配置简单的404路由**

功能是访问/时自动跳转到/home，访问/home的子层（如/home/xxx）时能正常渲染，但是如果访问了没有配置的路由（如/xxx）时会自动跳转到404页面

配置思路：

由于跳转到404页面时，不渲染<App>组件内容，因此不能再<App>组件里去渲染<Notfound>组件，因此需放在顶层路由与<App>同级

```jsx
import React from 'react'
import {render} from 'react-dom'
import { Notfound } from './views'
import App from './App'
import {BrowserRouter as Router, Route,Redirect, Switch} from 'react-router-dom'
render(
    <Router>  
           <Switch>
            <Route component={App} path='/home' />
            <Route component={Notfound} path='/404'/>
            {/* 必须把转home的写在404前面，由于含有一定冲突，即访问'/'时优先选择跳转home而非404 */}
            <Redirect to='/home' from='/' exact />  
            <Redirect to='/404' from='/'/>
            </Switch>        
    </Router>,
    document.querySelector('#root')
)
```

```jsx
import React, { Component } from 'react';
import {Home, Artical, Users } from './views'
import { Route, Link,  Switch} from 'react-router-dom';

export default class App extends Component {
    render() {
        return (
            <React.Fragment>
                <ul>
                    <li><Link to='/home'>跳转到首页</Link></li>
                    <li><Link to='/artical'>跳转到文章</Link></li>
                    <li><Link to='/users'>跳转到用户</Link></li>
                </ul>
                <Switch>
                    <Route component={Home} path='/home'/>
                    <Route component={Artical} path='/artical'/>
                    <Route component={Users} path='/users'/>   
                </Switch>
            </React.Fragment>
        );
    }
}
```

------

#### withRouter

只有被<Route>去渲染的组件才会在props里找到对应的路由API，当想不去使用<Route>也能在props里使用对应API时，需要使用`withRouter()`去构造一个高阶组件

一个父组件去渲染子组件，父组件的路由api无法传递给子组件，需要子组件自己使用 `withRouter()` 去让自己携带

```jsx
import {withRouter} from 'react-router-dom'

export default withRouter(component)	// 直接将返回的高阶组件导出使用
```



-----

### history

```
yarn add history
```

history有三种类型：`browser`，`hash`，`memory`

```jsx
import {createBrowserHistory, createHashHistory, createMemoryHistory} from 'history'
```

如果使用了React router，它会自动为你创建history对象，这样就不需要真正的去跟history打交道

#### Location

history对象中最重要的属性就是`location`，当使用路由进行跳转时，跳转后的页面的props里会传入history和location等对象

- location对象表明了应用当前位于什么位置。它包含了一些从URL中获取到的属性：pathname、search、hash
- 每个location对象中都一个与之相关的唯一的`key`属性，这个`key`可以用于鉴别跟存储特定location的相关数据
- 每个location对象都有一个与之相关的state，用于路由间的数据交流，这些信息不会展示在URL上的方法

**注意：location里带的数据由路由跳转得来，A=>B，B可以拿到A传来的location数据，但B=>B（刷新）B只能拿到B的数据**

```jsx
// 每个location对象都包含了以下属性，这些数据实际上是从上一个路由带来的，如要获取state对象（this.props.location.state）
{ 
    pathname: '/here', 
    search: '?key=value', 
    hash: '#extra-information', 
    state: {model: true}, 
    key: 'abc123'
}
// url = .../here?key=value#extra-information
```

----

#### location跳转

**goBack( )**

返回到上一个页面，同时也会将locations中数组的索引减一

**goForward( )**

前进一个页面，但是这只会发生在有有可以前进的页面的时候，就是当用户在这之前点击过返回按钮，同时也会将locations中数组的索引加一

**go()**

后退、前进指定次数，同时更改localtion索引

```jsx
history.go(-3)	// 后退三次
history.go(3)	// 前进三次
```

-----

#### Push( )

跳转到指定地址并且会在当前location数组之后添加一个新的地址

在这个方法操作之后，location数组中任何位于这个地址之后的地址（因为用户通过后退按钮回到上一个页面会在当前地址之后产生一个地址）都会被清除

```
即location中目前存着四个地址[A,B,C,D]
D地址通过两次后退回到B，然后再push进入E
这样location就变成了[A,B,E],会清掉原来后面的C,D并加入新的E
这样就无法通过再次前进寻找到C和D
```

默认情况下，当点击通过React router生成`<Link>`的的时候，调用的是`history.push`方法

push()方法参数对象state属性可用于数据进行共享，可通过跳转后路由的`this.props.location.state`取得

```jsx
history.push('/new-place')		// 简单写法，默认跳转指定pathname（/new-palce）

history.push({					// 带对象写法，若只有pathname属性则等同于上面
    pathname: '/new-place',
    state: {obj}				// 跳转时将参数同时传递给跳转路由，用于数据共享
})
```

-----

#### Replace( )

`Replace`方法跟`push`方法类似，但是不同的是`Replace`是在当前索引的位置替换地址而不是在当前索引的位置之后添加一个新的地址。位于当前索引位置之后的地址不会被清楚，

```
即location中目前存着四个地址[A,B,C,D]
D地址通过两次后退回到B，然后再push进入E
这样location就变成了[A,E,C,D]
```

一般用于重定向场景

```
如果存在三个页面A、B、C，B重定向到C
操作A→B时会自动跳转到C
当使用push时，在C页面点击后退，会后退到B，然后又会重定向到C
但使用replace时，在C页面点击后退，会后退到A
```

----

#### Listen( )

接收一个函数作为它的参数，这个函数会被添加到history中的用于保存监听函数的数组中

当地址发生改变时（无论是通过在代码中调用history对象的方法还是通过点击浏览器的按钮），history对象会调用它所有的监听函数

这能够编写一些代码用于监听当地址发生改变时，来执行相关的操作

```jsx
const youAreHere = document.getElementById('youAreHere')
history.listen(function (location) {
  youAreHere.textContent = location.pathname
})
```

----

#### createHref( )

接收一个location对象作为参数，然后输出一个URL

```jsx
consot location = {
  pathname: '/one-fish',
  search: '?two=fish',
  hash: '#red-fish-blue-fish'
}
const url = history.createHref(location)
const link = document.createElement('a')
a.href = url	// <a href='/one-fish?two=fish#red-fish-blue-fish'></a>
```

