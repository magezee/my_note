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

