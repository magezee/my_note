### 基本规则

- 每个文件仅包含一个React组件。

  - 但是，每个文件允许使用多个无状态或纯组件

- 始终使用JSX语法

- `React.createElement`除非从非JSX的文件初始化应用程序，否则不要使用。

  

---

### 类组件与函数组件

创建类组件时，使用关键字`class`而非`React.createClass`

```jsx
// bad
const Listing = React.createClass({
  // ...
  render() {
    return <div>{this.state.hello}</div>;
  }
});

// good
class Listing extends React.Component {
  // ...
  render() {
    return <div>{this.state.hello}</div>;
  }
}
```

如果组件不需要`state`和`ref`，则创造函数式组件（函数式组件不要使用箭头函数声明方式）

```jsx
// bad
class Listing extends React.Component {
  render() {
    return <div>{this.props.hello}</div>;
  }
}

// bad (relying on function name inference is discouraged)
const Listing = ({ hello }) => (
  <div>{hello}</div>
);

// good
function Listing({ hello }) {
  return <div>{hello}</div>;
}
```

---

### 命名

**变量命名**

```jsx
// bad
import reservationCard from './ReservationCard';

// good
import ReservationCard from './ReservationCard';

// bad
const ReservationItem = <ReservationCard />;

// good
const reservationItem = <ReservationCard />;
```

**组件命名**

创建组件名文件夹并使用`index.js`作为组件的文件（方便导入）

```jsx
// bad
import Footer from './Footer/Footer';

// bad
import Footer from './Footer/index';

// good
import Footer from './Footer';
```

**高阶组件命名**

通过高阶组件返回的组件，应该给该组件绑定一个静态`displayName`属性，该属性用于弄清用高阶组件封装了哪个组件，便于日后调试

组件`displayName`可能会被开发人员工具使用或用于错误消息中，并且具有可以清楚表达这种关系的值可以帮助人们了解正在发生的事情

```jsx
// bad
export default function withFoo(WrappedComponent) {
  return function WithFoo(props) {
    return <WrappedComponent {...props} foo />;
  }
}

// good
export default function withFoo(WrappedComponent) {
  function WithFoo(props) {
    return <WrappedComponent {...props} foo />;
  }

  // 短路逻辑，若存在` WrappedComponent.displayName`则返回这个，不然就往下寻找
  const wrappedComponentName = WrappedComponent.displayName
    || WrappedComponent.name
    || 'Component';

  WithFoo.displayName = `withFoo(${wrappedComponentName})`;
  return WithFoo;
}
```

`displayName`:定义调试时的组件name

```jsx
function withHOC(WrapComponent) {
   // 此处未定义名称或者希望动态定义名称
   return class extends React.Component {
     // 定义displayName
     static displayName = `withHOC(${WrapComponent.displayName || WrapComponent.name})`;
     render(){
       console.log("inside HOC")
       return <WrapComponent {...this.props } />;
     }
   }
 }

 App = withHOC(App);
```

如果未定义displayName，那么进行调试的时候，就会显示如下：

```
// react自动定义名称
|---_class2
  |---App
    ...
```

定义displayName后，显示如下：

```
|---withHOC(App)
  |---App
    ...
```

**props命名**

不要使用内置的标签属性去传递props

```jsx
// bad
<MyComponent style="fancy" />

// bad
<MyComponent className="fancy" />

// good
<MyComponent variant="fancy" />
```

---

### 缩进

```jsx
// bad
<Foo superLongParam="bar"
     anotherSuperLongParam="baz" />

// good
<Foo
  superLongParam="bar"
  anotherSuperLongParam="baz"
/>


// 子元素正常缩进
<Foo
  superLongParam="bar"
  anotherSuperLongParam="baz"
>
  <Quux />
</Foo>

// bad
{showButton &&
  <Button />
}

// bad
{
  showButton &&
    <Button />
}

// good
{showButton && (
  <Button />
)}

// good
{showButton && <Button />}
```

---

### 引号

jsx里使用双引号，js里用单引号

```jsx
// bad
<Foo bar='bar' />

// good
<Foo bar="bar" />

// bad
<Foo style={{ left: "20px" }} />

// good
<Foo style={{ left: '20px' }} />
```

---

### 空格

**在自闭标签中始终包含一个空格**

```jsx
// bad
<Foo/>

// very bad
<Foo                 />

// bad
<Foo
 />

// good
<Foo />
```

**在jsx中的大括号内不要加空格**

```jsx
// bad
<Foo bar={ baz } />

// good
<Foo bar={baz} />
```

---

### Props

**使用驼峰命名**

```jsx
// bad
<Foo
  UserName="hello"
  phone_number={12345678}
/>

// good
<Foo
  userName="hello"
  phoneNumber={12345678}
/>
```

**如果一个props属性值明确为true，则忽略ture**

```jsx
// bad
<Foo
  hidden={true}
/>

// good
<Foo
  hidden
/>

// good
<Foo hidden />
```

**一个图像标签应该有alt或者role来注明图像的用处**

不需说明时，alt可以为空，但是alt属性还是要有

且无需在alt时使用`image`、`photo`、`picture`等此造成多余注明，img标签已经表示这是一个图了

```jsx
// bad
<img src="hello.jpg" />

// good
<img src="hello.jpg" alt="Me waving hello" />

// good
<img src="hello.jpg" alt="" />

// good
<img src="hello.jpg" role="presentation" />

// bad
<img src="hello.jpg" alt="Picture of me waving hello" />

// good
<img src="hello.jpg" alt="Me waving hello" />
```

**不要定义快捷键accessKey属性**

```jsx
// bad
<div accessKey="h" />

// good
<div />
```

**避免用数组索引作为组件key值**

```jsx
// bad
{todos.map((todo, index) =>
  <Todo
    {...todo}
    key={index}
  />
)}

// good
{todos.map(todo => (
  <Todo
    {...todo}
    key={todo.id}
  />
))}
```

**为非必要传入的props定义默认值**

```jsx
// bad
function SFC({ foo, bar, children }) {
  return <div>{foo}{bar}{children}</div>;
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};

// good
function SFC({ foo, bar, children }) {
  return <div>{foo}{bar}{children}</div>;
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};
SFC.defaultProps = {
  bar: '',
  children: null,
};
```

**过滤无用的props**

```jsx
// bad
render() {
  const { irrelevantProp, ...relevantProps } = this.props;
  return <WrappedComponent {...this.props} />
}

// good
render() {
  const { irrelevantProp, ...relevantProps } = this.props;
  return <WrappedComponent {...relevantProps} />
}
```

----

### 括弧

如果jsx的标签有多行，则应使用括号包起来

```jsx
// bad
render() {
  return <MyComponent variant="long body" foo="bar">
           <MyChild />
         </MyComponent>;
}

// good
render() {
  return (
    <MyComponent variant="long body" foo="bar">
      <MyChild />
    </MyComponent>
  );
}

// good, when single line
render() {
  const body = <div>hello</div>;
  return <MyComponent>{body}</MyComponent>;
}
```

---

### 标签

**如果组件不需要子组件，则使用自闭标签**

```jsx
// bad
<Foo variant="stuff"></Foo>

// good
<Foo variant="stuff" />
```

**如果组件具有多行属性，则在新行上关闭其标记**

```jsx
// bad
<Foo
  bar="bar"
  baz="baz" />

// good
<Foo
  bar="bar"
  baz="baz"
/>
```

----

### 方法

**不要在事件里声明函数，否则每次调用都会产生一个新函数**

```jsx
function ItemList(props) {
  return (
    <ul>
      {props.items.map((item, index) => (
        <Item
          key={item.key}
          onClick={(event) => { doSomethingWith(event, item.name, index); }}
        />
      ))}
    </ul>
  );
}
```

**在类的构造函数里bind方法**

尽量不要在类中使用箭头函数定义方法，因为这会使它们难以测试和调试，并且可能对性能产生负面影响

```jsx
// bad
class extends React.Component {
  onClickDiv() {
    // do stuff
  }

  render() {
    return <div onClick={this.onClickDiv.bind(this)} />;
  }
}

// very bad
class extends React.Component {
  onClickDiv = () => {
    // do stuff
  }

  render() {
    return <div onClick={this.onClickDiv} />
  }
}

// good
class extends React.Component {
  constructor(props) {
    super(props);

    this.onClickDiv = this.onClickDiv.bind(this);
  }

  onClickDiv() {
    // do stuff
  }

  render() {
    return <div onClick={this.onClickDiv} />;
  }
}
```

**在 render 方法中确保有 return 返回值**

```jsx
// bad
render() {
  (<div />);
}

// good
render() {
  return (<div />);
}
```

