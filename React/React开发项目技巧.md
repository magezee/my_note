## React技巧

### 动态修改页面头

react的所有页面代码最终都整合在一个 `index.html` 中，如果想动态更改该html的头元素，有以下方法（以更改title为例）

- 在 `componentDidMount` 中使用原生js进行修改

```jsx
document.title = '标题'
```

- 在路由渲染页面组件时使用 `onEnter`

```jsx
<Route onEnter={()=>{document.title='标题'}} />
```

- 使用第三方包 `react-helmet`

```jsx
import {Helmet} from 'react-helmet';

return (
    <div className="application">
        <Helmet>
            <meta charSet="utf-8" />
            <title>My Title</title>
            <link rel="canonical" href="http://mysite.com/example" />
        </Helmet>
        ...
    </div>
)
```



---

### 重复生成多个组件

react 中，生成重复组件一般使用是通过数组的形式去形成，一个是因为要设置 `key`  的值，另一个原因是不能直接在 jsx 中写循环语句

```jsx
function ComponentTest() {
    const arr = [
        {id:1, data:'a'},
        {id:2, data:'b'} ,
        {id:3, data:'c'},
    ]

    return (
        <div className='content'>
        	{
                arr.map((item) => {
            		return <div key={item.id}>{item.data}</div> 
                })
            }
        </div>
        
    )
}
```



-----

### 操作表单

不应该使用ref获取dom然后直接操作表单，这样显得和JQuery一样，且不符合React的特性

正确的做法是给表单元素设置state值，如获取一个组件中的一个表单元素的value值的做法

```jsx
import React, { Component } from 'react'

export default class ComponentTest extends Component {
	constructor() {
		super();
		this.state = {
			title: '',
		}
	}

	changeTitle = (e) => {
		this.setState({
			title: e.target.value
		})
	}

	render() {
		return (
			<div>
				<input
					type="text"
					value={this.state.title}
					onChange={this.changeTitle}
					placeholder="请输入标题"
				/>
			</div>
		)
	}
}

```



---

### 更改启动端口

在 `package.json` 修改启动文件即可

```
set PORT=3002&&原启动命令
```

```json
"scripts": {
    "start": "set PORT=3002&&react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
},
```



---

### 更改渲染数据

如果有一个UI组件，通过传入的数据进行数据渲染，现在需要对特殊的一些数据做特殊展示，一般有两种做法：

- 直接在UI组件上更改组件解构
- 将数据处理好再传入UI组件，UI组件只负责数据渲染

第二种做法的好处是不会使UI组件和数据产生强关联，但是也要保证UI组件处理数据是灵活的，如果展示数据类型恨死就无法做到了

简单例子：在 jsx 中，react组件也是一种普通的数据格式，可以使用其他数据类型进行存储

```jsx
const data = {
    reactData: (
    	<div>
        	<P>这是一个react数据</P>
        </div>
    )
}

...render() {
    return (
    	<div>
        	{data.reactData}
        </div>
    )
}
```

利用这种思维，可以直接传进一个已经写好样式的react组件，然后UI组件只负责渲染数据

```jsx
// UI组件
import React from 'react'

export default function Form(props) {
  return (
    <div>
      {props.data.map((item) => {
        return <ui>
          <li key={Math.random}><span> {item.title}</span></li>
          <li key={Math.random}>{item.content}</li>
        </ui>
      })}
    </div>
  )
}
```

```jsx
// 传递数据
const data = [
  {
    title: 'TitleA', content: 'ContentA'
  },
  {
    title: 'TitleB', content: 'ContentB'
  }
]

class App extends Component { 
  render() {
    const handleData = (data) => {
      return data.map((item) => ({
        //  // 先将原来对象中的东西解构确保数据完整，然后覆盖更改原数据
        ...item,     
        title: (
          <span style={{color: 'red'}}>
            {item.title}
          </span>
        )
      }))
    }
    return (
      <Form data={handleData(data)} />
    )
  }
}
```



-----

### 上传文件

以上传图片为例，其他文件类型具体做法有类似：

- 找一个图床库（如贴图库），或者在自己项目的后端服务器中写一个接收图片并将图片保存在服务器上的接口
- 将图片信息post到图窗库保存图片接口，这样图片就能保存在网络上，项目就能直接引用该图片链接来显示图片
- 通过redux设置一个图片状态，当重新上传图片时，更改该状态以显示新的图片

```
推荐保存在自己服务器上
后端接口做法参考
https://blog.csdn.net/weixin_33840661/article/details/87975495
```



-----

### 谷歌浏览器插件

**React Developer Tools**

用于快捷查看react页面的dom结构和各元素所传递的props和state值，直接安装插件即可使用

<img src="https://img-blog.csdnimg.cn/2020101904214436.png" style="margin:0"/>

----

**Redux Developer**

用于查看redux中间件的数据传递过程，使用不仅要在浏览器中安装插件，也要在代码中配置，可以用于替代 `redux-logger`

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

