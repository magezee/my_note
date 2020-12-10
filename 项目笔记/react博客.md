## 搭建基础环境

本来想尝试不用脚手架从0开始搭建一个react项目，但是发现需要配置的webpack内容有点多，于是就使用脚手架了···

```
npx create-react-app react_blog
```

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

```jsx
const { override, addLessLoader } = require('customize-cra');
module.exports = override(
	addLessLoader({
		javascriptEnabled: true,
	}),
)
```

**遇到的坑：**

安装less-loader时，发现6.0.1的版本无法正常使用，安装回5.0.0版本就可以了，目前还没找到最新版的可用的方法（或者可以尝试暴漏webpack在webpack里直接修改）

```
yarn add less-loader@5.0.0
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



---

### 设计路由

先简单设计几个路由页面，后续再补充

<img src="http://m.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpEirjVA6FzCjFIplld0e9i8Ezb.5nlaok3u0nDrzlBRX0Rki.tn4U9L7Kn30mwIobA!!/b&bo=ygGBAcoBgQEDCSw!&rf=viewer_4" style="margin:0">

根据路由划分组件

<img src="http://m.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpBn3X6sb2l0ax0HN.ytyrOlPvMa3lGakfgCd0GBAbZvn.7CYI1QYrpg4D057BAjBqA!!/b&bo=1AC6AdQAugEDCSw!&rf=viewer_4" style="margin:0">

----

#### 实现按需加载页面

在页面文件夹views下建立`index.js`用于导出页面给路由管理，其中为了实现按需加载优化性能需要下载安装`react-loadable`

```
yarn add babel-loader
```

```
React 项目打包时，如果不进行异步组件的处理，那么所有页面所需要的 js 都在同一文件中（bundle.js），整个js文件很大，从而导致首屏加载时间过长，这时应对代码进行分割，按需加载，将js 拆分成若干个chunk.js,用到就加载，react-loadable就可以很好地解决这个问题
```

**懒加载原理：**

即使用的时候再导入

如：下面代码在渲染页面结束还没点击按钮的时候，打印`test.js`，表明了`test.js`文件即使在没有使用的时候，也会进行加载

```jsx
// index.html
<button id='btn'>按钮</button>

// index.js
import {add} form 'test.js'
document.getElementById().onclick() = function() {
	console.log(add(1,2))
}

// test.js
console.log('加载了test.js')
export function add (x, y) {
    return x + y
} 

```

使用懒加载（使用的时候再导入，且导入一次会写入缓存，之后不会再重复导入，因此无需担心每调用一次函数都导入一次）

```jsx
document.getElementById().onclick() = function() {
    // 懒加载
	import('./test')
    	.then(() => {
			console.log(add(1,2))        
    	})
}
```

**react-loadable实现原理**

```jsx
import React, { Component } from 'react';

const Loadble = ({
    loader,				// 要加载的组件，这是传入的是一个Promise方法，为`import('...')`
    loading: Loading    // 组件不能小写开头，可以转一下，使用一个loading组件的原因是防止异步加载大文件时耗时过长空屏很尴尬，因此显示一点什么东西给别人看
}) => {
    return class Loader extends Component {
        state = {
            LoaderComponent: null
        }
        
        componentDidMount() {
            // import('...')
            loader()
                .then((resp)=>{
                    this.setState({
                        LoaderComponent:resp.default
                    })
                })
        }
        render() {
            const {
                LoaderComponent
            } = this.state  // 解构
            
            return (
                // 如果组件尚未加载完成，显示loading组件，加载完成后显示加载组件
                LoaderComponent
                ?
                <LoaderComponent/>
                :
                <Loading/>
            );
        }
    }
        
}
export defalut Loadble
```

然后就可以用它来封装一个组件使其变成高阶组件

```jsx
import Loading from './Loading'

HighComponent =  hLoadble({
    loader: () => import(./Component),
    loading: Loading
})
...
redner( <HighComponent/> )
```

当然如果不想了解其中的原理可以把上面都当废话，直接用就是了，最终页面总管理页内容为：

```jsx
// /src/views/index.js
import {Loading} from '../components'
import Loadable from 'react-loadable'

// 封装成更方便的函数，不用多次写loading了
const loaderFun = (loader, loading = Loading) => {
    return Loadable({
        loader,
        loading
    })
}

const NotFound = loaderFun(() => import('NotFound'))
const AboutBlog = loaderFun(() => import('./AboutBlog'))
const BlogArticle = loaderFun(() => import('BlogArticle'))
const Essay = loaderFun(() => import('Essay'))
const MessageBoard = loaderFun(() => import('MessageBoard'))
const Other = loaderFun(() => import('Other'))


export { 
    NotFound,
    AboutBlog,
    BlogArticle,
    Essay,
    MessageBoard,
    Other
}
```

----

#### 实现路由

创建一个统一管理路由并导出的js文件，先集中在一起，后续再看看要不要分开

```jsx
// /src/routers/index.js
// 统一管理路由并导出
import {
    Home,
    NotFound,
    AboutBlog,
    BlogArticle,
    Essay,
    MessageBoard,
    Other
} from '../views'

export const routers = [
    {
        pathname: '/',
        viewCompoment:Home,
        exact:true
    },{
        pathname: '/404',
        viewCompoment: NotFound
    },{
        pathname: '/aboutBlog',
        viewCompoment: AboutBlog,
        excat:true
    },{
        pathname: '/blogArticle',
        viewCompoment: BlogArticle,
        exact:true
    },{
        pathname: '/essay',
        viewCompoment: Essay,
        exact:true
    },{
        pathname: '/messageBoard',
        viewCompoment: MessageBoard,
        exact:true
    },{
        pathname: '/other',
        viewCompoment: Other,
        exact:true
    }
]

```

使用`react-router-dom`来配置路由

```
yarn add react-router-dom
```

配置index.js和app.js路由

```jsx
// /index.js
import React, { Fragment } from 'react'
import { render } from 'react-dom'
import {BrowserRouter as Router, Route} from 'react-router-dom'

import App from './App'

render(
    <Fragment>
        {/* 访问域名立即渲染App组件 */}
        <Router>    
            <Route path='/' render={(routerProps) =>{
                    return <App {...routerProps}  />     // 传递路由api
            }} /> 
        
        </Router>     
    </Fragment>,
    document.querySelector('#root')
)
```

```jsx
import React,{Component, Fragment} from 'react'
import {Route, Switch, Redirect} from 'react-router-dom'

import {routers} from './routers'

import './App.less'

export default class App extends Component {
    
    render() {
        return (
            <Fragment>
                <Switch>
                    {
                        routers.map(router => {
                            return <Route
                                key = {router.pathname}
                                path = {router.pathname}
                                exact = {router.exact}
                                render = {(routerProps) => {
                                    return <router.viewCompoment {...routerProps} />
                                
                                }}
                            />
                        })
                    }
                    <Redirect to='/404' />      {/* 搜寻不到路由时跳转404 */}
                    
                </Switch>

            </Fragment>
        )
    }
}
```

### 设计主页布局

#### 确定方案

先随便画一下主页

```
不错的在线作图网站（可免费）：https://www.processon.com/
```

**方案一：**

<img src="http://a1.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpFLahNyMclOaukaKtKlIYy2HFN5fiUYfJw7AdgecUhTxkE.b9yJKuC2R8SKgdJeVuA!!/b&ek=1&kp=1&pt=0&bo=EgSVAhIElQIDGTw!&tl=1&vuin=756591143&tm=1589767200&sce=60-2-2&rf=viewer_4" style="margin:0">

**方案二：**

<img src="http://m.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpPrAqeL0qOxVN*L7wmPsNsgr7L8MFnX5LVZZzcb47Zmd2426e8ia1BL0JKUVNTk2Tg!!/b&bo=HQS.Ah0EvgIDCSw!&rf=viewer_4" style="margin:0">

总觉得方案一好像少了点什么，没有将大体定住的感觉，因此先采用方案二看看效果

----

#### 设计区域

**决定公共区域和路由变化区域**

除了主内容区跟随着路由跳转而发生变化外，其他都算是公共区域，即路由发生跳转，渲染页面发生变化时，它也会继续存在

一个简单可行的思路：将公共区域的单独抽成各个组件，然后再每个页面渲染时都渲染一遍这些公共组件

不过这种方法太过于累赘，于是准备将顶，左，右部分合并做成一个公共组件`Common`，然后在`app.js`内进行渲染

未来肯定是要再将这些div换成一个单独的一个组件的，但是目前没想到怎么整于是先用div代替这些组件

```jsx
// /src/components/Common/index.js
import React, { Component, Fragment } from 'react'

export default class Common extends Component {
    render() {
        return (
            <Fragment>

                <div className="top-part">
                	这里是顶边栏
                </div>

                <div className='left-part'>
                    这里是左侧边栏
                </div>

                <div className='right-part'>
                    这里是右侧边栏
                </div>

        </Fragment>
        )
    }
}
```

```jsx
// /App.js
import React,{Component, Fragment} from 'react'
import {Route, Switch, Redirect} from 'react-router-dom'

import {routers} from './routers'
import {Common} from './components'

import './App.less'

export default class App extends Component {
    
    render() {
        return (
            <Fragment>
                <Common/>	
                <Switch>
                    {
                        routers.map(router => {
                            return <Route
                                key = {router.pathname}
                                path = {router.pathname}
                                exact = {router.exact}
                                render = {(routerProps) => {
                                    return <router.viewCompoment {...routerProps} />
                                
                                }}
                            />
                        })
                    }
                    <Redirect to='/404' />      {/* 搜寻不到路由时跳转404 */}
                    
                </Switch>

            </Fragment>
        )
    }
}
```

可以看到由于设计的逻辑是在App里渲染其他路由的页面，因此无论什么页面都一定会渲染app的公有内容，因此实现了公共部分在各个路由中的渲染

<img src="http://m.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpARd82T.WDXVKVGjjviap.lrU0.fTlUblrQ5Cc.zCUGlKwiJp77DWfpnBEYugjSfYg!!/b&bo=fgMBAn4DAQIDCSw!&rf=viewer_4" style="margin:0">



----

#### 设计布局样式

现在这个布局很明显就是奔着flex布局走的，于是采用flex布局进行设计看看

**flex布局思路：**将`app.js`作为渲染，负责渲染顶、左、中、右的内容，那么就将渲染`app.js`所负责渲染的全部内容当成一个容器，而四个部分为四个项目在容器内排布就行

为了实现flex布局，发现之前的`app.js`内容设计有问题，于是重新将代码进行整理，总的思路不变，只是将四个部分拆分成四个组件`TopPart`、`LeftPart`、`MiddlePart`、`RightPart`

```jsx
// /scr/components/TopPart/index.js
import React, { Component } from 'react'

export default class TopPart extends Component {
    render() {
        return (

            <div>
                这里是顶边栏
            </div>
        )
    }
}
```

公共部分都差不多长一个样没什么好说的，将`App.js`之间的路由控制直接放在了`MiddlePart.js`里

```jsx
// /scr/components/Mide/index.js
import React, { Component } from 'react'
import {Route, Switch, Redirect} from 'react-router-dom'

import {routers} from '../../routers'

export default class MiddlePart extends Component {
    render() {
        return (
            <Switch>
                {
                    routers.map(router => {
                        return <Route
                            key = {router.pathname}
                            path = {router.pathname}
                            exact = {router.exact}
                            render = {(routerProps) => {
                                return <router.viewCompoment {...routerProps} />
                            
                            }}
                        />
                    })
                }
                <Redirect to='/404' />      {/* 搜寻不到路由时跳转404 */}
                
            </Switch>
        )
    }
}

```

然后`App.js`的代码就可以很干净，只负责渲染这些部分并负责这些部分的样式设计即可

发现组件无法直接定义className，那就用一个div元素将这些组件封在里面，设计div的排版即可决定这些组件的排版

```jsx
// /App.js
import React,{Component, Fragment} from 'react'

import {TopPart, LeftPart, MiddlePart ,RightPart} from './components'

import './App.less'

export default class App extends Component {
    
    render() {
        return (
            <div className='main-show'>

                <div className='top-part' >
                    <TopPart />
                </div>

                <div className='left-part'>
                    <LeftPart/>
                </div>
                
                <div className='middle-part'>
                    <MiddlePart/>
                </div>
                
                <div className='right-part'>
                    <RightPart/>
                </div>       
                
            </div>
        )
    }
}
```

div不怎么规范，换成语义化标签，稍微规范点...

同时为了更方便的flex布局，首先分为两块，一块是顶部部分，和主体部分，然后再将主体部分分成三块，左、中、右

```jsx
<div className='main-show'>

    <header className='top-part' >
        <TopPart />
    </header>

    <div className='content'>
        <aside className='left-part'>
            <LeftPart/>
        </aside>

        <main className='middle-part'>
            <MiddlePart/>
        </main>

        <aside className='right-part'>
            <RightPart/>
        </aside>               
    </div>
</div>
```

最终的效果和之前展示的是一样的，只是代码优化了不少

**设计布局CSS**

由于css写的少，这次项目应该会记录很多css的坑……要减少对UI框架的依赖

因为要设计flex，所以应该先将`html`，`body`，和顶层渲染`id = root`元素（react脚手架生成的html模板中自带的）的宽高和margin设置如下配置

子元素的100%只能通过父元素对比拿到具体值（且不能跨级到父父元素），为了将自己的组件高度撑满，因此应先该将他的父辈们占满整个浏览器窗口

```css
/* /public/index.less */
html, body, #root {
    margin: 0; 
    width: 100%; 
    height: 100%
}
```

```css
/* /src/App.less */ 
.main-show {
    display: flex;      /* 声明flex布局容器，在竖直方向上将顶部和主内容分成两块 */
    flex-direction: column;     /* 定义主轴为垂直方向 */
    min-height: 150px;
    height: 100% ;
    margin: 100px;

    .top-part {
        flex: 1;       /* 设置占比，在竖直方向上为1:10 */
        border: solid 1px;
    }

    .content {
        display: flex;  /* 再定义一个flex容器，在水平方向上分成三个部分 */
        flex: 10;
        .left-part {
            flex:1;     /* 设置三块的占比 1:3:1 */
            border: solid 1px ;
            
        }
    
        .middle-part {
            flex:3;
            border: solid 1px ;
        }
    
        .right-part {
            flex:1;
            border: solid 1px ;
        }
    }   

}
```

关于flex设置占比的一个坑：

```markdown
flex-grow:
	属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大
flex-shrink:
	flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小
	如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小
	如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，0的不缩小
flex-basis：
	在分配多余空间之前，项目占据的主轴空间，浏览器根据这个属性，计算主轴是否有多余空间
	它的默认值为auto，即项目的本来大小
	可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间

flex：
	flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto，后两个属性可选
	该属性有两个快捷值：
		auto (1 1 auto) 
    	none (0 0 auto)
```

**优先使用flex定义，而非单独的写三个分离的属性，因为浏览器会推算相关值**

如上面的代码如果`flex`属性换成`flex-grow`，即使都是只定义了`flex-grow`其他两个默认，但是得到的结果却完全不同，浏览器可能会自动将两侧的`flex-shrink`默认值从1改为0，从而导致了缩放窗口时比例不符的问题

<img src="http://m.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpJh.DaId4jt5AnffWta8pv9g3EzBWbd8RvKKvgMbJQu78m85VcAREv5mqzUAmLOPfA!!/b&bo=tQK8AbUCvAEDCSw!&rf=viewer_4" style="margin:0">`



加点边距

```css
.top-part {
    flex: 1;        
    border: solid 1px;
    margin:  0 0 20px;
}
.middle-part {
    flex:3;
    border: solid 1px ;
    margin: 0 40px;
}
```

最终展现差不多就是这样：

<img src="http://m.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpMFOSKxHBi3iNMYL81pkRyxsaN1.vsz4oFRj11PfdBfaNs5o10EFBlU3NvLerQPcww!!/b&bo=2QOzAdkDswEDCSw!&rf=viewer_4" style="margin:0">



----

### 设计左侧导航栏

首先要设计一下左侧导航栏要实现的基础功能：

- 渲染几个按钮
- 点击按钮进行相应的页面跳转

#### 实现按钮路由跳转

然后为了能使左侧导航组件能使用路由的api，需要引入`import { withRouter } from 'react-router-dom'`

使用`withRouter`方法将左导航组件转为一个高阶组件，这里直接使用了通配符`@withRouter`的写法，效果等同于` withRouter(LeftPart)`

使用路由的`push()`方法进行路由跳转

【这里想做的效果是——导航栏最上层有：首页、技术分享、随笔、留言区、其他，然后鼠标放在技术分享上，会弹出子导航：关于博客、博客文章，但是目前想不到怎么用纯css实现这个效果，因此先不要技术分享，直接把关于博客和博客文章当成一级导航渲染先……】

```jsx
// /src/components/LeftPart/index.js
import React, { Component } from 'react'
import { withRouter } from 'react-router-dom'

@withRouter
class LeftPart extends Component {
    
    render() {
        return (
            <div >
                <button onClick={function(){this.props.history.push('/')}.bind(this)}>首页</button>
                <button onClick={function(){this.props.history.push('/aboutBlog')}.bind(this)}>关于博客</button>
                <button onClick={function(){this.props.history.push('/blogArticle')}.bind(this)}>博客文章</button>
                <button onClick={function(){this.props.history.push('/essay')}.bind(this)}>随笔</button>
                <button onClick={function(){this.props.history.push('/messageBoard')}.bind(this)}>留言区</button>
                <button onClick={function(){this.props.history.push('/other')}.bind(this)}>其他</button>
                
            </div>
        )}
}
export default LeftPart
```

这样就可以实现路由跳转功能了，但是这样写很蠢，因此要优化代码：

直接使用之前的路由总管理js文件，且因为要控制跳转的只有部分页面，如（404不会在这里设置跳转），因此给之前的路由属性加上一个是否导航`isNav`属性，为`true`时则表示是导航栏需要的，顺便加上一个`title`属性标注路由名字

```jsx
// /src/routers/index.js
// 统一管理路由并导出
import {
    Home,
    NotFound,
    AboutBlog,
    BlogArticle,
    Essay,
    MessageBoard,
    Other
} from '../views'

export  const routers = [
    {   
        title: '首页',
        pathname: '/',
        viewCompoment:Home,     
        exact:true,
        isNav:true
    },{
        title: '404',
        pathname: '/404',
        viewCompoment: NotFound    
    },{
        title: '关于博客',
        pathname: '/aboutBlog',
        viewCompoment: AboutBlog,   
        excat:true,
        isNav:true
    },{
        title: '博客文章',
        pathname: '/blogArticle',
        viewCompoment: BlogArticle,     
        exact:true,
        isNav:true
    },{
        title: '随笔',
        pathname: '/essay',
        viewCompoment: Essay,       
        exact:true,
        isNav:true
    },{
        title: '留言板',
        pathname: '/messageBoard',
        viewCompoment: MessageBoard,    
        exact:true,
        isNav:true
    },{
        title: '其他',
        pathname: '/other',
        viewCompoment: Other,       
        exact:true,
        isNav:true
    }
]
```

优化后代码：

```jsx
// /src/components/LeftPart/index.js
import React, { Component } from 'react'
import { withRouter } from 'react-router-dom'

import {routers}  from '../../routers'

const navigation = routers.filter(router => router.isNav === true)      // 过滤掉不是导航用的路由(404路由)

// 装饰器
@withRouter
class LeftPart extends Component {

    changeRoute = (pathname) =>{
        this.props.history.push(pathname)
    }

    render() {
        return (
            <div >
                {navigation.map(route => {
                    return <button
                            key={route.pathname}
                            onClick={this.changeRoute.bind(this, route.pathname)}    /* 因为要直接往里面传参，因此需要用到bind方法 */
                        >{route.title}</button>
                })}              
            </div>
        )}
}
export default LeftPart

```

最终实现效果：

<img src="http://m.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpKf7iZ4WVHgGD5FpwRier8WEWtLKgHinAA2YCQ4oWDd5E4J0BZaGL2LYjCmdiVqX*g!!/b&bo=7wQbAu8EGwIDCSw!&rf=viewer_4" style="margin:0">



----

#### 实现样式设计

首先用flex将按钮一行显示一个

```jsx
/* /src/components/LeftPart/index.less */
.navigation {
    display: flex;
    flex-direction: column;
    height: 50%;	 /* 导航栏不需要占完全部边框，先用50%试试 */

    button {
        flex:1;
        margin-bottom: 5px;
        
    }
}
```

更改样式结果：

<img src="http://a1.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpAkRQY8yEuHN5ccFhGZ8HdJWJSICGw9Rb*.M3CWFotUf0VbZf27jMXKTPpYuucal7Q!!/b&ek=1&kp=1&pt=0&bo=DAGvAQwBrwEDGTw!&tl=1&vuin=756591143&tm=1589871600&sce=60-2-2&rf=viewer_4" style="margin:0">

**设计导航菜单**

为了能让导航菜单能够灵活复用，可以设计一个导航组件

初步设计想要实现的样式：

<img src="http://m.qpic.cn/psc?/V13Q7pAC4ZWKBC/ZOCeIbt3t.P7YdMG6dQVpK6q.CDyPlx*UsEOPZ6TbzpRYs5FxkWiFRmdMhe4FUstMDSY*ICc4wS3HfnhmdtnQw!!/b&bo=mQPFAJkDxQADCSw!&rf=viewer_4" style="margin:0">

这样设计的话就不应该直接使用`<button/>`标签，使用div包裹一个图标和文本会比较合适

由于不能导入src文件夹以外的资源，因此在src里建一个Public目录存放静态资源……

```jsx

```

---

**设计通用按钮UI组件**

**要实现的功能：**

参考antd选了几个重要的功能实现：

- 拥有一个`icon`属性，如果传递时则传递图标，否则无图标按钮
  - 按钮默认无文本，若按钮不设置文本且无icon属性值，则整个按钮不渲染，icon的值为一个组件
  - 如果设置了icon且无文本，则只渲染icon
  - 如果不设置icon有文本，则只渲染文本
  - 如果设置icon和有文本，两者都渲染
- 拥有一个`size`属性，值为`small`、`middle`、`large`
- 拥有一个`block`属性，开启时宽度会适应父宽度

**要实现的样式：**

- 按钮：默认高固定，宽度随着文本增加而增加，窗口拉伸时总大小无变化；有block属性时，宽会随父元素变化

- 图标：图标上下居中，固定宽高，正方形
- 文本：文本上下、左右居中

**实现思路：**

- 首先因为有不同的样式需求，因此可以编写多个匹配不同类名的css，然后动态更改最外层标签类名切换应用的css样式

- 因为标签需要固定宽高，因此直接用固定值就行（如10px）





```css
.button-style-middle {

    min-width: 60px;
    height: 20px;
    border: solid 1px;
    margin: 6px;
    padding: 4px;
    display: flex;
    justify-content: center;
    
    .button-icon {
        
        width: 20px;
        height: 20px;
        margin-right: 5px;
        flex-grow:0;
        
        img {
            width: 100%;
            height: 100%;
        }
    }

    .button-text {
        flex-grow:0;
        vertical-align:middle;
        font-weight:bold;
        text-align: center;
    }
    
}
```

```jsx
            <button>
                <div className='button-style-middle'>
                    <div className='button-icon'>
                        <img src={icon}/>
                    </div>
                    <div className='button-text'>
                        <span>{children}</span>
                    </div>
                </div>
            </button>
```















