## redux-form

[参考博客](https://segmentfault.com/a/1190000010088546)

### 初始配置

首先要在 `redux` 中进行配置

```js
import { createStore, combineReducers } from 'redux'
import { reducer } from 'redux-form'

const rootReducer = combineReducers({
  // ...其他的reducer
  form: reducer       // redux-form中的key必须声明为form
}) 

const store = createStore(rootReducer)

export default store
```

使用时，和 `react-redux` 中的 `connect` 方法类似，使用 `redux-form` 中的 `reduxForm` 方法将其组件包裹成高阶组件

```js
import React, { Component } from 'react'
import { reduxForm } from 'redux-form'

class App extends Component {
    console.log(this.props)
    render() {
        return (
            <div />
        )
}

export default reduxForm()(App);
```

然后该组件的 `props` 会传入以下属性

<img src="https://img-blog.csdnimg.cn/20210124173551688.png" style="margin:0;width:500px"/>

**配置项**

```js
...
export default reduxForm({
    form: 'demo',
    validate, 
    ...
})(App);
```

```
必要参数:
form									用于命名表单，在store生成此命名的数据节点

可选参数：
onChange							表单触发onChange事件后的回调函数
onSubmit							表单提交配置，可以配置需要提交哪些参数，还有提交时触发的dispatch等
onSubmitSuccess				提交表单成功和失败的回调
onSubmitFail 					提交表单成功和失败的回调
shouldValidate 				是否开启同步验证，一个方法，返回值为布尔，默认开启
validate							同步验证信息，一个方法，返回值为对象
shouldAsyncValidate	 	是否开启异步验证，一个方法，返回值为布尔
asyncValidate					异步验证信息，一个方法，返回值为对象
warn									警告信息，和验证不同的是，警告是内容合法可提交成功，但是有欠缺需要警告
touchOnBlur	 					标识onBlur的触发
touchOnChange 				标识onChange的触发
destroyOnUnmount			在销毁时清除
```



---

### Field组件

```jsx
<Field name component other />
```

组件参数：

- 必须包含 `name` 属性
- 必须包含 `component` 属性，可以是一个组件、无状态组件或者DOM所支持的默认的标签，如 `input、textarea、select`

- 如果添加了 name 和 component 之外的属性：
  - hmtl 标签：其余属性附在标签上
  - react 组件：其余属性传入 props

```jsx
<Field name="testComponent" component='input' type='text' x='x' />
// 生成的标签： <input name="testComponent" type="text" x="x" />

<Field name="testComponent" component={myComponent} type='text' />
// 生成的标签： <input /> 【MyComponent中只有一个<input/>标签】
```

```jsx
import React, { Component } from 'react'
import { Field, reduxForm } from 'redux-form'

const MyComponent = (props) => {
  console.log(props)
  return (
    <div></div>
  )
}

class App extends Component {
  render() {
    console.log(this.props)
    return (
      <Field name="testComponent" component={MyComponent} />
    )
  }
}

export default reduxForm()(App);
```

上面代码中 `MyComponent` 的 props 会传入 `input` 和 `meta` 属性，若传入其他属性则会在 props 中加上

<img src="https://img-blog.csdnimg.cn/2021012418052242.png" style="margin:0;width:250px"/>

如果 <Field> 的 `component` 属性是react组件，则在表单组件上需要引入 props 传入的 `input` 属性

```jsx
const MyComponent = (props) => {
  return (
    <div>
      <input {...props.input} />
    </div>
  )
}
```



---

### 提交数据

使用 <form> 标签将整体提交到 redux 的表单数据包裹起来，然后使用高阶组件后props传来的 `handleSubmit` 方法来提交数据

该方法需要绑定一个函数，提交数据成功时会触发该函数（注意提交失败不会触发），提交的内容会放在该函数的形参中

表单提交内容后

- 提交成功：redux 中的对应表单对象会添加一个属性 `submitSucceeded:true`
- 提交失败：redux 中的对应表单对象会添加一个属性 `submitFailed:true`

```jsx
const handleSubmit = (value) => {
  console.log(value)
}
class App extends Component {
  render() {
    return (
      <form onSubmit={this.props.handleSubmit(handleSubmit)}>
        <Field name="testComponent" component='input' type='check' />
        <button type="submit">提交</button>
      </form>
    )
  }
}

export default reduxForm()(App);
```

需要注意的是，这里需要对 `reduxForm` 方法进行一个额外配置，否则会提交失败，且redux 中的内容并非在提交数据后才会更改，而是操作表单数据时，redux 的内容就会立即发生改变

```jsx
// 相当于将App组件中表单提交的数据会提交到redux对象中的{form:{demo:{...}}}
export default reduxForm({
    form: 'demo'	// form这个key名不能变
})(App);
```

**注意：**这里提交数据主要是因为触发了 `redux-form` 提供的 `handleSubmit` 方法，因此也可以写成下面这种形式

```tsx
const handleSubmit = (value) => {
  console.log(value)
}
class App extends Component {
  render() {
    return (
			<>
        <Field name="testComponent" component='input' type='check' />
        <button onClick={this.props.handleSubmit(handleSubmit)}>提交</button>
      </>
    )
  }
}

export default reduxForm()(App);
```

提交数据内容变化：

```jsx
class App extends Component {
  render() {
    return (
      <div>
        <form onSubmit={this.props.handleSubmit((value) => {console.log(value)})}>
          <Field name="testComponent" component='input' />
          <button type="submit">提交内容</button>
        </form>
        <button onClick={() => {console.log(store.getState())}}>查看redux</button>
      </div>

    )
  }
}

export default reduxForm({form: 'demo'})(App);
```

在表单中输入 `123` 内容提交后，输入内容前，提交内容，输入内容后的打印内容如下

<img src="https://img-blog.csdnimg.cn/20210124203827292.png" style="margin:0"/>

当输入多个数据时

```jsx
<Field name="a" component='input' />
<Field name="b" component='input' />
<Field name="c" component='input' />
```

<img src="https://img-blog.csdnimg.cn/20210124202932868.png" style="margin:0"/>

---

### 数据验证

[参考博客](https://www.cnblogs.com/penghuwan/p/6538987.html)

当数据验证无法通过时，<form> 标签所提交的数据会提交失败，一般通过在 `reduxForm` 中配置 `validate` 来实现

```js
// 返回一个error对象，errors的属性必须和表单的name属性对应，否则没办法在验证错误的时候将错误信息传给指定表单组件
const validate = (values, props) => {			// 可以使用第二个参数接受组件的props
    const errors = {}
    if(!values.userName) {
        errors.userName = '用户名不能为空'
    }
    if(!values.passWord) {
        errors.passWord = '密码不能为空'
    }
    return errors
}

class App extends Component {
    render() {
        return (
            <div>
                 <form onSubmit={this.props.handleSubmit((value) => {console.log(value)})}>
                    <Field name="userName" component='input' type='text' />
                    <Field name="passWord" component='input' type='password' />
                    <Field name="info" component='input' type='text' />
                    <button type="submit">提交内容</button>
                </form>
                <button onClick={() => {console.log(store.getState())}}>查看redux</button>
            </div>
           
        )
    }
}

export default reduxForm({
    form: 'demo',
    validate,
})(App);
```

下面是验证失败和验证成功的redux内容：

<img src="https://img-blog.csdnimg.cn/2021012421514244.png" style="margin:0"/>

react组件的写法：

react 组件可以从 props 中的 `meta` 对象中获取到验证信息

```jsx
const renderField = (props) => {
    const { input, type, label, meta:{touched, error} } = props
    console.log(props)
    return (
        <div>
            <input {...input} placeholder={label} type={type} />
            {/* 如果有验证错误，则显示错误信息，touched在提交表单后为从false变为true，这里的作用是提过一次表单后才显示错误，不要一开始就显示 */}
            {touched && error && <span>{error}</span>}    
        </div>
    )
}


const validate = (values) => {
    const errors = {}
    if(!values.userName) {
        errors.userName = '用户名不能为空'
    }
    if(!values.passWord) {
        errors.passWord = '密码不能为空'
    }
    return errors
}

class App extends Component {
    render() {
        return (
            <div>
                 <form onSubmit={this.props.handleSubmit((value) => {})}>
                    <Field name="userName" component={renderField} type='text' label='Username' /> 
                    <Field name="passWord" component={renderField} type='password' label='Password' />
                    <Field name="info" component={renderField} type='text' label='Info' />
                    <button type="submit">提交内容</button>
                </form>
                <button onClick={() => {console.log(store.getState())}}>查看redux</button>
            </div>
           
        )
    }
}

export default reduxForm({
    form: 'demo',
    validate,
})(App);
```

<img src="https://img-blog.csdnimg.cn/20210124222034381.png" style="margin:0"/>



---

### 其他技巧

**手动更改redux中表单<Field>的值**

dispatch 一个固定的 action创建函数： `change(formname, fieldnam, value)`

注意：value可以为 `'' 或 null` ，但是如果值为 `undefined` 时，就不会正常更改值

```js
import { change } from 'redux-form'

dispatch(change('Demo', 'demo', value))

@reduxForm({form: 'Demo'})
class Component {
  render() {
    return (
    	<Field name='demo' .../>
    )
  }
}
```



----

**redux-form 失效的问题**

redux-from仅会在组件挂载阶段时跟 `<Field>` 建立联系，但是如果在挂载阶段没有该 `Field` 会导致有问题，如下面代码中，在等待阶段会先挂载一个 `<Loading>` 组件，因此就可能导致使用异常

```js
const Component = () => {
  return(
  	{loading 
     	? 
     <Loading /> 
     	: 
		 <div>
     	...
      <Field ...>
     </div>
    }
  )
}
       
export default reduxForm({
    form: 'demo',
})(Component);
```

正确的做法是将使用到 `<Field>` 的组件单独绑定 redux-form，然后在父组件中进行渲染

```js
const Helper = () => {
  return (
  	<Field ...>
  )
}

reduxForm({
    form: 'demo',
})(Helper);

const Component = () => {
  return(
  	{loading 
     	? 
     <Loading /> 
     	: 
		 <Helper />
    }
  )
}
```

**数据销毁的问题**

redux-form 会在绑定的对应组件销毁时清除 form字段中的数据，为了保证不被清除可以关闭默认清除状态

```js
export default reduxForm({
  form: 'demo',
  destroyOnUnmount: false,
})(Component);
```

但是这样也有个隐患，就会导致刷新页面时表单上永远有之前残留的操作数据，为了避免这个情况，可以在组件刷新是重置表单

```js
import { reset } from 'redux-form/es/immutable'

// 这里省略了将 reset通过connect映射到props中
componentWillUnmount () {
		this.props.actions.reset('demo')
	}
```

