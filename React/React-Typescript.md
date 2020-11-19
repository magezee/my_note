## React配合TS

```
官方配置ts文档：https://zh-hans.reactjs.org/docs/static-type-checking.html#typescript
```

**使用：**

一样使用脚手架，但是要在后面注明 `--typescript` 安装 typescript 支持

```
npx create-react-app projectName --typescript
注意：react最近更新中把命令改写了
npx create-react-app projectName --template typescript
```

**npx的作用：**

- 避免安装全局模块，只使用一次的东西不需要全局安装

```
如上面的create-react-app安装完使用创建项目完毕后会自动删除
```

- 调用项目内部安装的模块

```
如本来调用一个项目里内部的mocha模块需要
node_modules/.bin/mocha
使用npx后只需
npx mocha
```

**vscode设置：**

- tsx文件默认空格为两格，修改为四格在文件→首选项→设置中查找`editor.detectIndentation` 并设为 `false`

**下载第三方包：**

下载了指定包后，为了规范类型，还要专门再下一个类型规范包（有些包可能包里就自带类型规范，有些也可能没有类型规范）

类型规范统一用 `@types` 前缀表示

```
npm install classnames -s
npm install @types/classnames -s
```

- 使用sass

```
yarn add node-sass
```



----

### TS组件写法

文件后缀名不是  `.js` 而是 `.tsx`

```tsx
import React from 'react'

interface IHelloProps {
    message: string
}

// 规定了传入props的message属性的类型
const Hello = (props: IHelloProps) => {
    return <div>{props.message}</div>
}
export default Hello
```

react 提供了一个接口 `React.FunctionComponent` ，它可接受一个泛型，默认为 `{}` ，用于声明有类型约束的函数式组件，规范传入 props 的类型

函数组件设置该接口时，函数组件会自动添加上一些静态API，如 `defaultProps` 等

传入组件的 props 一般都是一个对象，因此要规范类型时使用实现接口的方式去限制这个 props 属性 的数据类型

【当一个参数为一个接口类型时，任意实现该接口的东西都可传入成为该参数】

```tsx
import React from 'react'

interface IHelloProps {
    message?: string
}

const Hello:React.FunctionComponent<IHelloProps> = (props) => {
    return <div>{props.message}</div>
}

// 规定默认值时，即可不传入message，这样接口的message属性要设置为可选属性
Hello.defaultProps = {
    message: 'defalut message'
}

export default Hello

```

`React.FunctionComponent` 官方别名为 `React.FC` 因此可以直接简写为别名使用

```tsx
const Hello:React.FC<IHelloProps> = (props) => {...}
```

如果传入的其中一个props属性为一个对象，想要规范该对象内的属性，可以这么写

```tsx
interface IShowResult {
    message: string
	status: string
}

// 规定了传入props的data对象，即data的属性message和status要满足指定类型
const Show: React.FC<{data: IShowResult}> = ({data}) => {	// 解构data
    ...
}
```



---

### 规范类型

#### React数据类型

React 提供好了很多写好的属性类型，如 button 标签的 `React.ButtonHTMLAttributes`  接口已经写好一个 button 标签默认拥有的全部属性和它们的数据类型，如 `formEncType` 属性等，因此一般要实现一个 button 组件的全部功能，只需要规范好自定义的属性再添加上默认属性即可，这样就可以将这些默认属性传入到函数式组件的 props

```tsx
// 自定义的类型
interface BaseButtonProps {
    className?: string;
    disabled?: boolean;
    size?: ButtonSize;
    btnType?: ButtonType;
    children: React.ReactNode;
    href?: string
}

// 使用react内置提供的按钮属性接口拿到按钮的全部属性并合并自定义属性
type NativeButtonProps = BaseButtonProps & React.ButtonHTMLAttributes<HTMLElement>
// 将合并属性全部设置为可选
type ButtonProps = Partial<NativeButtonProps & AnchorButtonProps> 

const Button: React.FC<ButtonProps> = (props) => {
    const {
        btnType,
        className,  // 为了将用户自定义的class也加上
        disabled,
        size,
        children,
        href,
        ...restProps    // restProps 接收剩余的属性
    } = props

    return (
        <button
            className={classes}
            disabled={disabled}
            {...restProps}	{/* 传进去默认类型 */}
            >
            {children}
        </button>
    )
}
```

这样就能在外部去直接使用这些属性

```tsx
<Button onClick={...} />
```







---

#### 可选类型

react 提供了一个 `Partical` 声明类型别名，它的作用是将传进去的类型都声明为可选类型

```tsx
// className 和 disabled 属性变成可选属性
interface BaseButtonProps {
    className: string;
    disabled: boolean;
}

type ButtonProps = Partial<BaseButtonProps> 
```









---

### 样式系统文件结构

在根目录下创建一个styles文件夹，用于保存全局的配置

mixins 保存的是直接就使用的属性

functions 保存的是在特定情况下需要根据情况计算才能使用的属性

<img src="https://img-blog.csdnimg.cn/20200613171656639.png" />