## 环境配置

```
yarn add antd
```

```jsx
import Button from 'antd/lib/button'	// 引入antd框架的按钮组件
import 'antd/dist/antd.css'		// 引入antd框架的css

render() {
    return (
    	<Button>antd样式按钮</Button>
    )    	
}
```

**这样使用太麻烦，因此需要额外配置**

```
yarn add react-app-rewired customize-cra babel-plugin-import
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

在项目根目录创建一个 `config-overrides.js` 用于修改默认配置（当修改scripts的对象内容时，需要在`config-overrides.js`进行额外配置，即需要存在这个文件）

```jsx
module.exports = function override(config, env) {
  // do stuff with the webpack config...
  return config;
};
```

修改`config-overrides.js`，需要用到`babel-plugin-import`，它是一个用于按需加载组件代码和样式的 babel 插件

 ```jsx
const { override, fixBabelImports } = require('customize-cra');

module.exports = override(
    fixBabelImports('import', {
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: 'css',
    }),
);
 ```

然后就可以按需导入了

```jsx
import { Button } from 'antd'	// 按需引入antd框架的按钮组件和css，无需再导入antd的css

render() {
    return (
    	<Button>antd样式按钮</Button>
    )    	
}
```



-----

### 定制主题

自定义主题需要用到 less 变量覆盖功能，引入 `customize-cra` 中提供的 less 相关的函数 `addLessLoader`来帮助加载 less 样式，同时修改 `config-overrides.js` 文件

```
yarn add less less-loader
```

```jsx
// config-overrides.js
const { override, fixBabelImports } = require('customize-cra');

module.exports = override(
    fixBabelImports('import', {
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: 'css',
    }),
);

// 修改为
const { override, fixBabelImports, addLessLoader } = require('customize-cra');
module.exports = override(
    fixBabelImports('import', {
      libraryName: 'antd',
      libraryDirectory: 'es',
      style: true,
    }),
    addLessLoader({
        javascriptEnabled: true,
        modifyVars: { '@primary-color': '#1DA57A' },
    }),
);
```

利用了 `less-loader`的 `modifyVars` 来进行主题配置，配置后需重启项目（yarn start）

```css
@primary-color: #1890ff; 							// 全局主色
@link-color: #1890ff; 								// 链接色
@success-color: #52c41a; 							// 成功色
@warning-color: #faad14; 							// 警告色
@error-color: #f5222d; 								// 错误色
@font-size-base: 14px; 								// 主字号
@heading-color: rgba(0, 0, 0, 0.85); 				// 标题色
@text-color: rgba(0, 0, 0, 0.65); 					// 主文本色
@text-color-secondary: rgba(0, 0, 0, 0.45); 		// 次文本色
@disabled-color: rgba(0, 0, 0, 0.25); 				// 失效色
@border-radius-base: 4px; 							// 组件/浮层圆角
@border-color-base: #d9d9d9; 						// 边框色
@box-shadow-base: 0 2px 8px rgba(0, 0, 0, 0.15); 	// 浮层阴影
```

**官方提供了另外两套完整的主题配置，只需在使用antd组件的文件中引入即可**

```
import 'antd/dist/antd.dark.less'
import 'antd/dist/antd.compact.less'

// 若无配置less，直接使用~.css也是可以的
```



-----

### 设置语言

目前的默认文案是英文，可以进行其他语言配置

```jsx
import zhCN from 'antd/es/locale/zh_CN'
import { ConfigProvider } from 'antd'
render{
    return(
    // 包在渲染根组件外层
    <ConfigProvider locale ={zhCN}>
    	<App/>
    </ConfigProvider>
    )
   
}
```



----

## 使用

### 通用

#### [Button - 按钮](https://ant.design/components/button-cn/#API)

- icon：设置按钮图标
- htmlType：设置按钮的原生type值
  - submit：提交按钮
  - button：可点击的按钮
  - reset：重置按钮（清除表单数据
- onClick：点击回调
- loading：设置按钮是否处于载入状态



-------

### 布局

#### [Grid - 栅格](https://ant.design/components/grid-cn/)

**基于 Flex 布局**

通过`<Row/>`组件新建一行，通过`<Col>`组件确定该行的一列和列宽占比

栅格系统区域按照 24 等分，一共有24位，如：三个等宽的列（占满该行）可以使用三个`<Col span={8}/>`来创建（24/8=3）

如果一个 `row` 中的 `col` 总和超过 24，那么多余的 `col` 会作为一个整体另起一行排列

```jsx
import { Row, Col } from 'antd';
<Row>
	<Col span={8}>col-8</Col>
	<Col span={8}>col-8</Col>
	<Col span={8}>col-8</Col>
</Row>
```

**Row**

| 成员        | 说明                                                         | 类型                                                         | 默认值  |
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :------ |
| **align**   | 子元素垂直对齐方式                                           | `top` | `middle` | `bottom`                                  | `top`   |
| **gutter**  | 栅格间隔，可以写成像素值或支持响应式的对象写法来设置水平间隔 `{ xs: 8, sm: 16, md: 24}`。或者使用数组形式同时设置 `[水平间距, 垂直间距]`。 | number/object/array                                          | 0       |
| **justify** | 水平排列方式                                                 | `start` | `end` | `center` | `space-around` | `space-between` | `start` |

<img src='https://img-blog.csdnimg.cn/20200505172616394.png' style='float:left; height:350px' />

**Col**

| 成员       | 说明                                                     | 类型             | 默认值 |
| :--------- | :------------------------------------------------------- | :--------------- | :----- |
| **flex**   | flex 布局属性                                            | string \| number | -      |
| **offset** | 栅格左侧的间隔格数，间隔内不可以有栅格                   | number           | 0      |
| **order**  | 栅格顺序，数字越小顺序越先                               | number           | 0      |
| **pull**   | 栅格向左移动格数                                         | number           | 0      |
| **push**   | 栅格向右移动格数                                         | number           | 0      |
| **span**   | 栅格占位格数，为 0 时相当于 `display: none`              | number           | -      |
| **xs**     | `<576px` 响应式栅格，可为栅格数或一个包含其他属性的对象  | number\|object   | -      |
| **sm**     | `≥576px` 响应式栅格，可为栅格数或一个包含其他属性的对象  | number\|object   | -      |
| **md**     | `≥768px` 响应式栅格，可为栅格数或一个包含其他属性的对象  | number\|object   | -      |
| **lg**     | `≥992px` 响应式栅格，可为栅格数或一个包含其他属性的对象  | number\|object   | -      |
| **xl**     | `≥1200px` 响应式栅格，可为栅格数或一个包含其他属性的对象 | number\|object   | -      |
| **xxl**    | `≥1600px` 响应式栅格，可为栅格数或一个包含其他属性的对象 | number\|object   | -      |



----

#### [Layout - 布局](https://ant.design/components/layout-cn/#API)

- `Layout`：布局容器，其下可嵌套 `Header` `Sider` `Content` `Footer` 或 `Layout` 本身，可以放在任何父容器中。
- `Header`：顶部布局，自带默认样式，其下可嵌套任何元素，只能放在 `Layout` 中。
- `Sider`：侧边栏，自带默认样式及基本功能，其下可嵌套任何元素，只能放在 `Layout` 中。
- `Content`：内容部分，自带默认样式，其下可嵌套任何元素，只能放在 `Layout` 中。
- `Footer`：底部布局，自带默认样式，其下可嵌套任何元素，只能放在 `Layout` 中。

```jsx
<Layout>
  <Header>header</Header>
  <Layout>
    <Sider>left sidebar</Sider>
    <Content>main content</Content>
    <Sider>right sidebar</Sider>
  </Layout>
  <Footer>footer</Footer>
</Layout>	
```



-----

### 导航

#### [Menu - 导航菜单](https://ant.design/components/menu-cn/#API)

```jsx
import { Menu } from 'antd';	
const { SubMenu } = Menu;	// SubMenu需要单独引入，等同于import SubMenu from 'antd/lib/menu/SubMenu';
```

```jsx
<Menu>
  <Menu.Item>菜单项</Menu.Item>
  <SubMenu title="子菜单">
    <Menu.Item>子菜单项</Menu.Item>
  </SubMenu>
</Menu>
```

Menu 组件的三个核心部分：**Menu**、**Menu.Item**、**Menu.SubMenu**

- `Menu `为一个用例中最顶层组件

- `SubMenu` 的父组件为 SubMenu 或 Menu

- `Menu.Item `作为叶子 (leaf) 节点，父组件为 SubMenu 或 Menu

Menu.ItemGroup节点做分组提示用，无其他意义

```jsx
<Menu>        
    <SubMenu title='菜单项'>
        <Menu.ItemGroup title='A组'>
			<Menu.Item key="1"> a </Menu.Item>
			<Menu.Item key="2"> b </Menu.Item>
		</Menu.ItemGroup>
		<Menu.ItemGroup title='B组'>
			<Menu.Item key="3"> c </Menu.Item>
		</Menu.ItemGroup>
	</SubMenu>
</Menu>	  
```

<img src='https://img-blog.csdnimg.cn/20200501122743470.png' style='margin:0;padding:10px 0 '>

`SubMenu`和`Menu.Item`都可以当成一个节点，原则上而言，`SubMenu`是`Menu.Item`父节点，即在一个菜单中，若一个节点无子层，则用`Menu.Item`，若有则用`SubMenu`

```jsx
<Menu>
	<SubMenu title='菜单项'>
		<Menu.Item key="1"> a </Menu.Item>
		<SubMenu title='子菜单项'>
			<Menu.Item key="2"> b </Menu.Item>
			<Menu.Item key="3"> c </Menu.Item>
		</SubMenu>
	</SubMenu>
</Menu>	  
```

<img src='https://img-blog.csdnimg.cn/2020050112371336.png' style='margin:0;padding:10px 0 '>

-----

**点击事件**

点击事件`onclike`属性只能绑定在Menu上，Menu通过key属性来确认具体点击了哪个按钮，该方法执行后会回调返回该指定按钮的一个对象，包含`item, key, keyPath, domEvent`属性，可通过事件的形参获取到对应的值（可以通过解构方便获取），

```jsx
onMenuClick = ({key}) => {	// 只想用到返回对象的key属性，函数对象解构
	...	
}
<Menu onclick={this.onMenuClick}></Menu>
```



-----

#### Pagination - 分页

- onChange：指定一个方法，在更改页数的时候执行，该方法的形参接受传回的数据（page, pageSize）,page表示当前的页数，pageSize表示当前页数最大显示行数
- onShowSizeChange：指定一个方法，在更改每页显示最大行数的时候执行
- current：当前页数
- hideOnSinglePage：是否在只有一页时影藏分页器（true or false）
- showQuickJumper：是否显示快速跳转（true or false）

换页切换数据原理：

如果请求到了100行数据，默认每页显示10行，当点击换页时，不会切换数据

实际上100行数据已经全部拿到并且排序在后面了，但如果要进行换页切换显示数据时，必须要绑定换页事件，否则不会自动切换数据，而且绑定的时间必须**重新再请求一次数据**才行，antd会根据当前切换的page和pageSize自动排列显示到指定行数的数据



-----

### 数据录入

#### [From - 表单](https://ant.design/components/form-cn/#API)

基本的表单数据域控制展示，包含布局、初始化、验证、提交，配合 Input, Button, Select 等组件使用

```jsx
import { Form, Input, Button } from 'antd';
<From>
	<Form.Item>
    	<Input/>
        <Button/>
        <Select/>
    </Form.Item>
</From>
```

**Form**

- 布局（Form.Item也拥有labelCol和wrapperCol，会覆盖Form的）：

  - labelCol：label 标签布局，同 组件，设置 `span` `offset` 值，如 `{span: 3, offset: 12}` 或 `sm: {span: 3, offset: 12}`

  - wrapperCol：输入控件设置布局，用法同labelCol


可利用labelCol加wrapperCol占用栅格大于24切换到下一行的特点实现标签和输入控件不在同一行的效果 

```jsx
const layout = {
	labelCol: { span: 24 },
	wrapperCol: { span: 20 },
};
<Form {...layout} />
```

- 事件：

  - onFinish：提交表单且数据验证成功后回调事件，提交表单且数据验证失败后回调事件，该方法传回一个values对象，包含写入表单的信息`function(values)`，表单数据验证由每一个的`<Form.Item/>`组件的rules属性决定

  - onFinishFailed：提交表单且数据验证失败后回调事件，`function({ values, errorFields, outOfDate })`

  - onFieldsChange： 字段更新时触发回调事件，` Function(changedFields, allFields)`

  - onValuesChange：字段值更新时触发回调事件，`Function(changedValues, allValues)`

```jsx
onFinishFailed = ({values}) => {	// 因为错误时传回的对象包括values, errorFields, outOfDate属性，因此将value解构出来
	console.log(values);
};

onFinish = values => {		// 成功则只有一个values对象，无需解构
	console.log(values);
};

<Form onFinishFailed={this.onFinishFailed}  onFinish={this.onFinish}>
	<Form.Item rules={[{ required: true, message: '该字段为必选不可留空!'}]}>
    	<Input defaultValue='请输入用户名'/>
    </Form.Item>
</Form> 
```

- 其他：
  - initialValues：表单默认值，只有初始化以及重置时生效，该属性为一个对象，属性为各个提交框的`name`

```jsx
<Form initialValues={{title:'标题',admin:'作者'}}>
```



------

**Form.Item**

表单字段组件，用于数据双向绑定、校验、布局等

- name：用于确定每个输入框，要让rules规则生效，要有name属性

  被设置了 `name` 属性的 `Form.Item` 包装的控件，表单控件会自动添加 `value`（或 `valuePropName` 指定的其他属性） `onChange`（或 `trigger` 指定的其他属性），数据同步将被 Form 接管

| 设置name属性导致的结果：                                     |
| ------------------------------------------------------------ |
| 不再需要也不应该用 `onChange` 来做数据收集同步（可以使用 Form 的 `onValuesChange`），但还是可以继续监听 `onChange` 事件 |
| 不能用控件的 `value` 或 `defaultValue` 等属性来设置表单域的值，默认值可以用 Form 里的 `initialValues` 来设置。注意 `initialValues` 不能被 `setState` 动态更新，需要用 `setFieldsValue` 来更新 |
| 不应该用 `setState`，可以使用 `form.setFieldsValue` 来动态改变表单值 |

`form` 的用法

```jsx
import { Form } from 'antd';

const Test = () => {
    
    const [form] = Form.useForm();
    
	const onFinish = () => {
    	form.setFieldsValue({ username: '', password: '' });
	};
    
    return (
    	<Form onFinish={onFinish} form={form}>
        	<Form.Item name="username">
                ....
            </Form.Item>
            <Form.Item name="password">
                ....
            </Form.Item>
        </Form>
    );
}
```

`onFinish` 的用法

```jsx
onFinish = values => {		
	console.log(values);
};
<Form  onFinish={this.onFinish}>
	<Form.Item name='admin'>
    	<Input />
    </Form.Item>
    <Form.Item name='title'>
    	<Input />
    </Form.Item>
</Form>
// 当提交框admin提交内容'a'，title提交内容'b'，打印数据为{admin:'a',title:'b'}
// 此方法使用与<Form.Item>组件内只包含一个提交框的情况，包含多个提交框时则无效
```

- label：标签文本

- rules：校验规则，设置字段的校验逻辑，它是一个数组，数组里存放多个对象，每一个对象为一条规则，多个对象规则合并，不满足任意一条规则时都不能通过，每次表单内容发生改变时都会触发一次规则判断

  - message：错误信息，不满足规则时会跳出，不设置时会通过模板自动生成
  - enmu：是否匹配枚举中的值
  - len，max，min：string 类型时为字符串（最长/最小）长度；number 类型时为确定数字（最大/最小值）； array 类型时为数组（最大/最小）长度
- pattern：正则表达式匹配
  - required：是否为必选字段
  - type：类型
  - whitespace：如果字段仅包含空格则校验不通过
  - validator：自定义校验，接收 Promise 作为返回值，`(rule, value) => Promise`，rule为接收的规则对象，value为表单内容更新后的value

```jsx
<Form.Item
	rules={[
		{required: true, message: '不能为空'},
		{min: 4, message:'必须大于4位'},
		{max: 8, message:'必须小于8位'},
        {validator: (rule,value) => {
            if(value !== '敏感词汇'){
                return Promise.resolve()
            }
            else return Promise.reject('含敏感词汇！')
        }}
	]}
/>
```

-----

**通过FormInstance控制表单数据：**

要使用`FormInstance`，需要在`<Form>`组件内绑定ref，通过`refName.current`f获取到对应方法

```jsx
class Edit extends Component {
    constructor() {
        super()
        this.formRef=createRef()
    }
    
    setInputData = () ={
        this.formRef.current.setFieldsValue({
        	editor:'设定的内容'	// 表单通过name寻找到对应的元素，将该元素在表单中的value设置为对内容
    	})
    }
    
   	render() {
        return (
            <Form ref={this.formRef} >
				<Form.Item name='editor'>
                	<Input/>
                </Form.Item>
                <button onClick={this.setInputData}/>
			</Form>
        )
    }

```

| 名称                  | 说明                                                         | 类型                                                         |
| :-------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **getFieldValue**     | 获取对应字段名的值                                           | (name: [NamePath](https://ant.design/components/form-cn/#NamePath)) => any |
| **getFieldsValue**    | 获取一组字段名对应的值，会按照对应结构返回                   | (nameList?: [NamePath](https://ant.design/components/form-cn/#NamePath)[], filterFunc?: (meta: { touched: boolean, validating: boolean }) => boolean) => any |
| **getFieldError**     | 获取对应字段名的错误信息                                     | (name: [NamePath](https://ant.design/components/form-cn/#NamePath)) => string[] |
| **getFieldsError**    | 获取一组字段名对应的错误信息，返回为数组形式                 | (nameList?: [NamePath](https://ant.design/components/form-cn/#NamePath)[]) => FieldError[] |
| **isFieldTouched**    | 检查对应字段是否被用户操作过                                 | (name: [NamePath](https://ant.design/components/form-cn/#NamePath)) => boolean |
| **isFieldsTouched**   | 检查一组字段是否被用户操作过，`allTouched` 为 `true` 时检查是否所有字段都被操作过 | (nameList?: [NamePath](https://ant.design/components/form-cn/#NamePath)[], allTouched?: boolean) => boolean |
| **isFieldValidating** | 检查一组字段是否正在校验                                     | (name: [NamePath](https://ant.design/components/form-cn/#NamePath)) => boolean |
| **resetFields**       | 重置一组字段到 `initialValues`                               | (fields?: [NamePath](https://ant.design/components/form-cn/#NamePath)[]) => void |
| **scrollToField**     | 滚动到对应字段位置                                           | (name: [NamePath](https://ant.design/components/form-cn/#NamePath), options: [[ScrollOptions](https://github.com/stipsan/scroll-into-view-if-needed/blob/ece40bd9143f48caf4b99503425ecb16b0ad8249/src/types.ts#L10)]) => void |
| **setFields**         | 设置一组字段状态                                             | (fields: [FieldData](https://ant.design/components/form-cn/#FieldData)[]) => void |
| **setFieldsValue**    | 设置表单的值                                                 | (values) => void                                             |
| **submit**            | 提交表单，与点击 `submit` 按钮效果相同                       | () => void                                                   |
| **validateFields**    | 触发表单验证                                                 | (nameList?: [NamePath](https://ant.design/components/form-cn/#NamePath)[]) => Promise |



---

**对表单数据域进行交互**

函数式组件：

`useForm` 是` React Hooks` 的实现，只能用于函数组件

```jsx
// 重置表单按钮，resetFields()为重置表单数据方法，更多方法看官网
const [form] = Form.useForm();
const onReset = () => {
    form.resetFields();
};
<Button htmlType="button" onClick={onReset}/>
```

类组件：

可以通过`ref` 获取数据域，也可以通过`useForm`

```jsx
formRef = React.createRef<FormInstance>();
onReset = () => {
	his.formRef.current.resetFields();
};
<Button htmlType="button" onClick={onReset}/>
```



---

#### [Input - 输入框](https://ant.design/components/input-cn/#API)

Input：

- prefix：带有前缀图标的 input
- onChange： 输入框内容变化时的回调
- onPressEnter：按下回车的回调
- disabled：是否禁用
- defaultValue：输入框默认内容
- placeholder：输入提示

```jsx
import { Input } from 'antd'
import { UserOutlined } from '@ant-design/icons'
< Input prefix={<UserOutlined/>} />
```





-----

### 数据展示

#### [Table - 表格](https://ant.design/components/table-cn/#API)

```jsx
import { Table, Tag } from 'antd';
const { Column, ColumnGroup } = Table;
```

**Table常用属性**：

- dataSource：绑定要传入的数据源，数据源为多个对象组成的数组

- rowKey：表格每行数据的唯一key，该属性为一个方法，每行的数据存储在形参record中，返回值为设置的key值

- column:直接在Table里根据column数组指定渲染列数，不用再去使用`<Column>`组件（灵活，推荐）

  column是一个由对象组成的数组，每个对象会解析成一个<Column>组件，对象里的属性会自动解析成该组件的属性，多个对象则解析成多个`<Column>`组件

```jsx
const dataSource = [
  {
    key: '1',
    name: '胡彦斌',
    age: 32,
    address: '西湖区湖底公园1号',
  },
  {
    key: '2',
    name: '胡彦祖',
    age: 42,
    address: '西湖区湖底公园1号',
  },
];

const columns = [
  {
    title: '姓名',
    dataIndex: 'name',
    key: 'name',
  },
  {
    title: '年龄',
    dataIndex: 'age',
    key: 'age',
  },
  {
    title: '住址',
    dataIndex: 'address',
    key: 'address',
  },
];

<Table dataSource={dataSource} columns={columns} rowKey={record => record.id} />;
// <Column key='name dataIndex='name' title='姓名'>
// <Column key='age' dataIndex='age' title='年龄'>`
// <Column key='address' dataIndex='address' title='住址'>`
```

<img src='https://img-blog.csdnimg.cn/20200503131604649.png' style='float:left'/>

-----

**Column常用属性：**

- title：数据对应列
- dataIndex：该列显示数据源的指定属性（数据源内设置的属性必须要和该对象属性名完全一致）
- key：设置每列标志唯一值，确定不会有重复的值就行，如果已经设置了唯一的 `dataIndex`，可以忽略这个属性
- pagination：传入一个对象，设置分页属性，[ctrl + 点击查看分页具体操作](#Pagination)

- render：传入一个方法，return要渲染的组件，该方法回调传回三个参数：`text`、`record`、`index`，text和record表示该行数据的`dataSource`数据源对象，index表示改行数据在数据源中的序列下标（dataSource[index]），注意三者声明顺序不能变更

```jsx
<Column 
    title="actions" 
    dataIndex="actions" 
    key="actions"
    pagination={{
		total:this.state.total,
		hideOnSinglePage: true,
		showQuickJumper: true,
		onChange:this.onPageChange
	}} 
    render={(text, record, index) =>{
		return  <Button>操作</Button>
	}}
></Column> 
```

范例：

```jsx
const data = [{
    key: '1',
    Name: 'John',
    age: 32,
  },
  {
    key: '2',
    Name: 'Jim',
    age: 42,
}]

render () {
    <Table dataSource={data}>
    	<Column title="Name" dataIndex="Name" key="Name" />
        <Column title="Age" dataIndex="age" key="Age" />
	</Table>
}
```

<img src='https://img-blog.csdnimg.cn/20200502165018860.png' style='float:left'/>

**可以用`<ColumnGroup/>`组件对表格进行再次拆分**

```jsx
const data = [{
    key: '1',
    Name: 'John',
    age: 32,
    address: 'New York No. 1 Lake Park'
  },
  {
    key: '2',
    Name: 'Jim',
    age: 42,
    address: 'London No. 1 Lake Park'
}]

render () {
    <Table dataSource={data}>
        <ColumnGroup title="Base">
    		<Column title="Name" dataIndex="Name" key="Name" />
        	<Column title="Age" dataIndex="age" key="Age" />
        </ColumnGroup>   
        <Column title="Address" dataIndex="address" key="Address" />
	</Table>
}
```

<img src='https://img-blog.csdnimg.cn/20200502165706661.png' style='float:left'>

----

#### [Tag - 标签](https://ant.design/components/tag-cn/#API) 

**设置颜色**

```jsx
import { Tag } from 'antd'
<Tag color="magenta">magenta</Tag>
<Tag color="#87d068">magenta</Tag>
```

<img src='https://img-blog.csdnimg.cn/20200503155007219.png' style='float:left'>

------

**设置图标**

```jsx
import { Tag } from 'antd';
import {
  CheckCircleOutlined,
  SyncOutlined,
  CloseCircleOutlined,
  ExclamationCircleOutlined,
  ClockCircleOutlined,
  MinusCircleOutlined,
} from '@ant-design/icons';

ReactDOM.render(
  <>
    <div>
      <h4>Without icon</h4>
      <Tag color="success">success</Tag>
      <Tag color="processing">processing</Tag>
      <Tag color="error">error</Tag>
      <Tag color="warning">warning</Tag>
      <Tag color="default">default</Tag>
    </div>

    <div>
      <h4>With icon</h4>
      <Tag icon={<CheckCircleOutlined />} color="success">
        success
      </Tag>
      <Tag icon={<SyncOutlined spin />} color="processing">
        processing
      </Tag>
      <Tag icon={<CloseCircleOutlined />} color="error">
        error
      </Tag>
      <Tag icon={<ExclamationCircleOutlined />} color="warning">
        warning
      </Tag>
      <Tag icon={<ClockCircleOutlined />} color="default">
        waiting
      </Tag>
      <Tag icon={<MinusCircleOutlined />} color="default">
        stop
      </Tag>
    </div>
  </>,
  mountNode,
);
```

<img src='https://img-blog.csdnimg.cn/20200503155549557.png' style='float:left'>

----

####  [Tooltip - 文字提示](https://ant.design/components/tooltip-cn/#API)

鼠标移入则显示提示，移出消失，气泡浮层不承载复杂文本和操作，可用来代替系统默认的 `title` 提示，提供一个`按钮/文字/操作`的文案解释

使用时直接包在要弹出提示组件的外层就行：

```jsx
import { Tooltip } from 'antd';
<Tooltip title='提示文字' placement='topLeft'> 
	<div><div>
</Tooltip>
```

常用属性：

- title：弹出显示文字
- placement：弹出气泡框相对元素位置

  `top` 、`left` 、`right`、 `bottom`、 `topLeft` 、`topRight` 、`bottomLeft`、 `bottomRight`、 `leftTop` 、`leftBottom` 、`rightTop`、 `rightBottom`







----

### 反馈

#### [message - 全局提示](https://ant.design/components/message-cn/#API)

**种类**

- `message.success()`
- `message.error()`
- `message.info()`
- `message.warning()`
- `message.warn()` 
- `message.loading()`

```jsx
import { message } from 'antd';
message.success('This is a success message')
message.error('This is an error message')
message.warning('This is a warning message')
```

<img src='https://img-blog.csdnimg.cn/20200503114713484.png' style='float:left'>

-----

#### [Modal - 对话框](https://ant.design/components/modal-cn/#API)

```jsx
import { Modal } from 'antd';
```

**函数式方法（定制化功能不如组件式）：**

弹窗样式

默认只有`Modal.confirm()`会出现确定和取消按钮，其他均为只提示框

- `Modal.info()`
- `Modal.success()`
- `Modal.error()`
- `Modal.warning()`
- `Modal.confirm()`

方法参数为一个Object，其常用属性：

- content：内容
- title：标题内容
- icon：自定义图标

- onOk：点击确定的回调方法
- onCancel：点击取消或右上角X的回调方法

函数式方法使用时，点击消息框的确定或取消，消息框会自动立即消失，但是如果要等待异步方法执行完毕再关闭消息框时，需要使用组件式自定义消失

```jsx
Modal.confirm({
	title: '标题',
	content: '内容',
	onOk() {
		console.log('确定按钮回调事件')
	}
})
```

**组件式方式：**

