## Jest

```
https://jestjs.io/
```

使用脚手架创建项目时，react 已经内置了 jest，使用命令运行 jest

```
npx jest +测试脚本名
不需要指明项目路径
如 
/src/components/Button/jest.test.js
执行
npx jest jest.test.js
```

在命令后添加 `--watch` 时，会每修改一次项目脚本就自动执行一次测试

```
npx jest jest.test.js --watch
```

---

### React Testing Library

也是 react 脚手架默认安装的一个测试依赖，它与 Jest 结合起来用能很简单的编写组件的关系并测试

如果 react 低于3.0.0版本要手动安装

```
yarn add @testing-library/react
```

**测试文件命名：**

有三种命名方式，项目会将该文件默认视为测试文件（ts、jsx、tsx文件同理）

- 在 `__tests__` 文件夹下的 `.js` 文件
- `.test.js` 文件

- `.spec.js` 文件

**启动**

直接启动项目下的所有测试文件（自动热更新）

```
npm run test
```

----

### testing-library/jest-dom

如果 react 低于3.0.0版本要手动安装

```
yarn add @testing-library/jest-dom
```

它需要在项目的 src 目录下新建一个 `setupTests.ts` 文件并写入以下内容（脚手架自动弄好了）才能生效

```tsx
import '@testing-library/jest-dom/extend-expect';
```

它用于测试组件 dom 的状态，为 `jest` 的 `except` 方法返回值增加更多作用于 dom 的匹配器，如： ` expect().toBeInTheDocument()`

----

#### render

用于渲染一个组件

**RenderResult：**一个类型，用于表示渲染组件类型

```tsx
import {render, RenderResult} from '@testing-library/react'
const myComponent = (props) => {
    return (
    	<div data-testid='testId' className='testClass'>
        	<p>测试组件</p>
        </div>
    )
}

let wrapper:RenderResult,divElement:HTMLElement		// 组件是RenderResult类型，元素标签是HTMLElement类型
test('demo', () => {
	wrapper = render(myComponent({testProps:'test'}))
    divElement = wrapper.getByTestId('testId')		// 通过id获取到组件中的div元素
    expect(divElement).toHaveClass('itestClass')	// 断言该div元素是否含有指定类名
})
```

---

#### fireEvent

用于模拟启动事件

```tsx
import {render, RenderResult, fireEvent} from '@testing-library/react'

interface typeProps {
    testEvent?: () => void;		// 规定函数的数据类型
}

const testProps: typeProps = {
    testEvent: jest.fn()		// 构造mock函数用于当做props传递给组件
}

const myComponent = (props:typeProps) => {
    return (
    	<div onClick={props.testEvent}>
        	<p>测试组件</p>
        </div>
    )
}

let wrapper:RenderResult,divElement:HTMLElement		
test('demo', () => {
	wrapper = render(myComponent(testProps))
    divElement = wrapper.getByTestId('testId')			// 通过id获取到组件中的div元素
    fireEvent.click(divElement)   						// 模拟用户点击该div元素
    expect(testProps.testEvent).toHaveBeenCalledWith()	// 断言点击后 testEvent 事件是否被调用
})

```

---

#### cleanup

清楚储存的内容

不同case之间会自动cleanup

```tsx
import {render, RenderResult, cleanup} from '@testing-library/react'

let wrapper:RenderResult
describe('testDemo',() => {
    beforeEach(() => {
        wrapper = render(myComponent(testProps))
    })
    it('case 1', () => {   
        cleanup()	// 清除beforeEach中的wrapper，不然下面再次声明时会出问题
        const wrapper = render(myComponent(testProps))
    })  

})
```







----

**在 react 中测试组件时，一般会配合 `React Testing Library` 、 `testing-library/jest-dom` 使用**

以下的使用是指三者配合后的情况



----

### 测试与断言

使用 `test()` 声明一个测试用例，第一个参数为该用例的描述名称，第二个参数为一个回调函数，用于声明用例执行的方法

`toBe` 要求完全相同，`toEqual` 仅值相同

```js
// 测试等数值
test('test common matcher', () => {
    expect(2 + 2).toBe(4)
    expect(2 + 2).not.toBe(5)
})

// 测试真假
test('test to be true or false', () => {
    expect(1).toBeTruthy()
    expect(0).toBeFalsy()
})

// 测试大于小于
test('test number', () => {
    expect(4).toBeGreaterThan(3)
    expect(4).toBeLessThan(5)
})

// 测试对象
test('test object', ()=> {
    expect({name: 'lucy'}).toEqual({name: 'lucy'})  // 使用toBe时是不等的
})
```

----

### 用例分类

使用 `describe()` 将测试用例进行管理分类，相当于把多个有一定关联但情况不同的 `test()` 合并在了一起



-----

### 使用

```tsx
import React from 'react'
import {render} from '@testing-library/react'
import Button from './button'

test('test case',() => {
    const warpper = render(<Button>ButtonChildren</Button>)
    const element = warpper.queryByText('ButtonChildren')
    expect(element).toBeTruthy()
})
```



----

### Mock测试

`Jest` 中的三个与 `Mock` 函数相关的API，分别是`jest.fn()`、`jest.spyOn()`、`jest.mock()`

使用它们创建Mock函数能够更好的测试项目中一些逻辑较复杂的代码，例如测试函数的嵌套调用，回调函数的调用等

**Mock函数**

在项目中，一个模块的方法内常常会去调用另外一个模块的方法，在单元测试中，可能并不需要关心内部调用的方法的执行过程和结果，只想知道它是否被正确调用即可，甚至会指定该函数的返回值，此时，使用Mock函数是十分有必要

Mock函数提供的以下三种特性

- 捕获函数调用情况
- 设置函数返回值
- 改变函数的内部实现

---

`jest.fn()`是创建Mock函数最简单的方式，如果没有定义函数内部的实现，`jest.fn()`会返回`undefined`作为返回值

```js
test('测试jest.fn()调用', () => {
    let mockFn = jest.fn();
    let result = mockFn(1, 2, 3);
    expect(result).toBeUndefined();					// 断言mockFn的执行后返回undefined 
    expect(mockFn).toBeCalled();					// 断言mockFn被调用
    expect(mockFn).toBeCalledTimes(1); 				// 断言mockFn被调用了1次
    expect(mockFn).toHaveBeenCalledWith(1, 2, 3);	// 断言mockFn传入的参数为1, 2, 3
})
```

`jest.fn()`所创建的Mock函数还可以设置返回值，定义内部实现或返回`Promise`对象

```js
test('测试jest.fn()返回固定值', () => {
    let mockFn = jest.fn().mockReturnValue('default');
    expect(mockFn()).toBe('default');		// 断言mockFn执行后返回值为default
})

test('测试jest.fn()内部实现', () => {
    let mockFn = jest.fn((num1, num2) => {
        return num1 * num2;
    })
    expect(mockFn(10, 10)).toBe(100);		// 断言mockFn执行后返回100
})

test('测试jest.fn()返回Promise', async () => {
    let mockFn = jest.fn().mockResolvedValue('default');
    let result = await mockFn();
    expect(result).toBe('default');			// 断言mockFn通过await关键字执行后返回值为default
    expect(Object.prototype.toString.call(mockFn())).toBe("[object Promise]");	// 断言mockFn调用后返回的是Promise对象
})
```

实际使用场景：

在`fetch.js`中封装了一个`fetchPostsList`方法，该方法请求了`JSONPlaceholder`提供的接口，并通过传入的回调函数返回处理过的返回值。如果想测试该接口能够被正常请求，只需要捕获到传入的回调函数能够被正常的调用即可

```js
// fetch.js
import axios from 'axios';

export default {
    async fetchPostsList(callback) {
        return axios.get('https://jsonplaceholder.typicode.com/posts').then(res => {
            return callback(res.data);
        })
    }
}
```

```js
import fetch from '../src/fetch.js'

test('fetchPostsList中的回调函数应该能够被调用', async () => {
    expect.assertions(1);
    let mockFn = jest.fn();
    await fetch.fetchPostsList(mockFn);
    expect(mockFn).toBeCalled();	 // 断言mockFn被调用
})
```

---

 **jest.mock()**

`fetch.js`文件夹中封装的请求方法可能在其他模块被调用的时候，并不需要进行实际的请求（请求方法已经通过单侧或需要该方法返回非真实数据）此时，使用`jest.mock(）`去mock整个模块是十分有必要的

`src/fetch.js` 的同级目录下创建一个 `src/events.js`

```js
// events.js
import fetch from './fetch';

export default {
  async getPostList() {
    return fetch.fetchPostsList(data => {
      console.log('fetchPostsList be called!');		// 这个方法使用mock时不会被执行
      // do something
    });
  }
}
```

使用`jest.mock('../src/fetch.js')`去mock整个`fetch.js`模块

```js
// functions.test.js
import events from '../src/events';
import fetch from '../src/fetch';

jest.mock('../src/fetch.js');

test('mock 整个 fetch.js模块', async () => {
    expect.assertions(2);
    await events.getPostList();
    expect(fetch.fetchPostsList).toHaveBeenCalled();
    expect(fetch.fetchPostsList).toHaveBeenCalledTimes(1);
});
```

---

**jest.spyOn()**

`jest.spyOn()`方法同样创建一个mock函数，但是该mock函数不仅能够捕获函数的调用情况，还可以正常的执行被spy的函数

实际上，`jest.spyOn()`是`jest.fn()`的语法糖，它创建了一个和被spy的函数具有相同内部代码的mock函数

使用`jest.mock()`，模块内的方法在执行测试时是不会被jest所实际执行的（如打印信息），此时需要使用 `jest.sypOn()`

```js
import events from '../src/events';
import fetch from '../src/fetch';

test('使用jest.spyOn()监控fetch.fetchPostsList被正常调用', async() => {
    expect.assertions(2);
    const spyFn = jest.spyOn(fetch, 'fetchPostsList');
    await events.getPostList();
    expect(spyFn).toHaveBeenCalled();
    expect(spyFn).toHaveBeenCalledTimes(1);
})
```

---

实际项目的单元测试中，

`jest.fn()`常被用来进行某些有回调函数的测试

`jest.mock()`可以mock整个模块中的方法，当某个模块已经被单元测试100%覆盖时，使用`jest.mock()`去mock该模块，节约测试时间和测试的冗余度是十分必要

当需要测试某些必须被完整执行的方法时，常常需要使用`jest.spyOn()`



------

## API

### Dom相关

- **render**

  获取到一个组件dom

```tsx
const warpper = render(<Button>ButtonChildren</Button>)
```

---

- **queryByText**

  判断一个组件的chirdren是否为指定内容，如果是则返回该html元素，否则返回null

```tsx
const warpper = render(<Button>ButtonChildren</Button>)
const element = warpper.queryByText('ButtonChildren')
expect(element.tagName).toEqual('BUTTON')	// tagName都是大写的
```

- **getByText**

  搜索指定元素下具体指定文本的所有元素，返回该元素

```tsx
const warpper = render(<Button>ButtonChildren</Button>)
const element = warpper.getByText('ButtonChildren')
```

- **toBeInTheDocument**

  指定元素是否在元素是否在document内

```tsx
const warpper = render(<Button>ButtonChildren</Button>)
const element = warpper.getByText('ButtonChildren')
expect(element).toBeInTheDocument(<Button>)
```

- **getByTestId**

  获取指定 id 的标签

  注意，这里检查的 id 值是标签 `data-testid` 的属性值而非 id

```tsx
const warpper = render(<div data-testid='test-id' />)
const componentElement = warpper.getByTestId('test-id')
```

- **container**

  将 TSX 解析成 html 代码返回 `HTMLElement` 类型，这样就能很方便的使用 `HTMLElement` 类型上的方法 

```tsx
const generateMenu = (props) => {
    return(
        <Menu {...props}>
            <MenuItem index={0} >
                active
            </MenuItem>
        </Menu>
    )
}

test('test',() => {
	const wrapper = render(gengerateMenu(props))
    wrapper.container.getElementsByClassName('xxx')
})
```

- **toHaveClass**

  断言指定元素是否拥有指定类名 

```tsx
const Demo = () => {
    return(
    	<div id='demoId' className='demoClass1 demoClass2'>Demo</div>
    )
}
test('test',() => {
	const wrapper = render(<Demo/>)
    divElement = wrapper.getByText('demo')	// 获取组件里的div标签
    expect(divElement).toHaveClass('demoClass1 demoClass2')		// 检查该标签是否拥有指定类名
})
```

- **totoHaveBeenCalled**

  断言指定函数是否被调用

- **toHaveBeenCalledWith**

  断言指定函数是否传进指定参数调用



----

### 通用

- **beforeEach**

  减少重复的代码，在多种情况下时，每种情况都会执行一次 beforeEach 里的代码，这样就不用再每种情况下都写重复代码了

```js
// case 1/2/3 都会执行打印操作
describe('test',() => {
    beforeEach(() => {
            console.log('...')
        })
    it('case 1',() => {})
    it('case 2', () => {})
    it('case 3', () => {})
})
```

