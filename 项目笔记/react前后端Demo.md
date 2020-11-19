## 简介

```
参考仿写项目作者博客：https://juejin.im/post/5c6cda0ae51d457139114898
```

```
在线项目地址：
http://www.gegeda.online:3000/
```

### 技术栈

**前端**

- TypeScript（使 JS 成为强类型语言）
- React（当下最流行的前端框架）
- Axios（处理 HTTP 请求）
- Ant-Design（UI 框架）
- React-Router（处理页面路由）
- Redux（数据状态管理）
- Redux-Saga（处理异步 Action）

**后端**

- Koa2（基于 Node.js 平台的下一代 web 开发框架）
- MongoDB（非关系型数据库）



----

### 初始化

新建一个项目文件夹 List当做前端和后端的总项目目录（也可以完全分开）

初始化前端react框架（前端项目目录为list）

```
npx create-react-app list --typescript
```

初始化后端服务（新建一个server文件夹，后端项目目录为server）

```
npm init -y
tsc --init
```

下载ts所需包

```
npm install typescript -s
npm install ts-node -s
npm install @types/node -s
```



------

## 后端

### 初始化

```markdown
后端的任务：
    设计路由接口供前端请求
    前端请求特定路由，后端拿到前端发送过来的数据
    利用这些数据去执行对应的数据库操作对数据进行增删改查
```

#### 依赖包

下载 koa 包所需的包

【通常@types的包都是放在开发环境的，为了获取api提示，即-save-dev，也就是-s-d，但是放在生产环境也没什么问题】

```
koa本体包
	npm install koa @types/koa -s		
koa路由包	
	npm install koa-router @types/koa-router -s
koa中间件:用于获取提交数据
	npm install koa-bodyparser @types/koa-bodyparser -s
koa中间件:用于处理跨域请求
	npm install  koa2-cors @types/koa2-cors -s
```

下载操作mongoDB数据库所需包

```
npm install mongoose @types/mongoose -s		
```

-----

#### 项目配置

**配置启动命令**

在后端项目的 `/src`  下新建 app.ts 文件作为总入口文件

随便写点东西测试kora服务能否启动

```ts
// /src/app.ts
import Koa from 'koa';
import Router from 'koa-router'

const router = new Router()
const app = new Koa();

router.get('/',async (ctx) => {
    ctx.body = '测试'
})

app.use(router.routes())


app.listen(3000)
```

在 `package.json` 添加启动入口启动文件

```json
  "scripts": {
    "start": "ts-node ./src/app.ts"
  },
```

这样就可以通过 `npm run start` 启动项目了，如果登入 `localhost:3000` 发现有内容，则说明 koa 服务成功生效

但是这样不能热更新，每次修改代码都需要重新启动服务才能看到代码更改后的效果，这样对于开发很不方便

全局下载热更新所需包 `nodemon`，在命令终端输入 `nodemon` 有反应则下载成功

```
npm install -g  nodemon
```

热更新启动项目入口文件

```
nodemon ./src/app.ts
```

显然这样启动项目很累赘，因此直接在 `package.json` 设置快捷启动

在`package.json` 添加配置

```json
{
    "scripts": {
        "start": "ts-node ./src/app.ts",
        "watch": "nodemon"
    },

    "nodemonConfig": {
        "ignore": [
            "node_modules"
        ],
        "watch": [
            "src"
        ],
        "exec": "npm start",
        "ext": "ts"
    },

}
```

相关配置信息，还有很多配置项可以配置，不写置默认即可

```
ignore:
	忽略的文件后缀名或者文件夹
watch:
	监控的文件夹路径或者文件路径
exec:
	当监控到变化时，自动执行的命令
ext：
	监控指定后缀名的文件，用空格间隔
	默认监控的后缀文件：.js, .coffee, .litcoffee, .json
	
[文件路径的书写用相对于 package.json 所在位置的相对路径]
```

此时使用 `npm run watch` 即能实现热更新的的效果

**配置git**

项目上传到 github 的时候默认上传全部文件，如果不指定忽略则会很累赘，在项目根目录中新建 `.gitignore` 文件，告诉 Git 哪些文件不需要添加到版本管理中

```markdown
常用
    以斜杠/开头表示目录
        /mtk/ 			-- 过滤整个文件夹
        /mtk/do.c 		-- 过滤某个具体文件
    以星号*通配多个字符
        *.zip 			-- 过滤所有.zip文件

其他
    以问号?通配单个字符
    以方括号[]包含单个字符的匹配列表
    以叹号!表示不忽略(跟踪)匹配到的文件或目录
```

可以直接将 react 脚手架中生成项目的 `.gitignore` 复制过来即可

```python
# dependencies
/node_modules
/.pnp
.pnp.js

# testing
/coverage

# production
/build

# misc
.DS_Store
.env.local
.env.development.local
.env.test.local
.env.production.local

npm-debug.log*
yarn-debug.log*
yarn-error.log*
```

---



### 数据库

#### 建立数据库模型

**连接数据库**

在 `/src` 下新建一个 `db` 文件夹，`db` 文件夹下新建 `index.ts` 文件夹，用于构建连接数据库的函数

```ts
import mongoose from 'mongoose'
export default (db: string) => {
    const connect = () => {
        mongoose.connect(db, {
            useCreateIndex: true,
            useNewUrlParser: true,
            useUnifiedTopology: true,
            useFindAndModify: false,
        })
        .then(() => {
            return console.log(`Successfully connected to ${db}`)
        })
        .catch((error) => {
            console.log(`Error connecting to database:${error}`)
            return process.exit(1)      // 失败断开进程
        })
    }

    connect()

    mongoose.connection.on('disconnected',connect)      // 当连接断开时，执行connect函数重连 
}
```

**定义数据Scheam和Model**

定义 `todo` 数据，该数据为某个用户制定事项列表

包含的内容为：①内容事项 ②完成状态

```ts
// /src/db/todo.ts
import { Schema, model, Document } from 'mongoose'

// 声明接口继承Document规范对象类型
export interface ITodo extends Document {
  content: string;		// 内容事项
  status: boolean;		// 完成状态
}

const TodoSchema: Schema = new Schema({
  content: String,
  status: {
    type: Boolean,
    default: false,
  },
})

TodoSchema.index({ content: 'text' })	// 建立索引

export model<ITodo>("Todo", TodoSchema)
```

定义 `user` 数据

包含的内容为：①用户账户 ②用户密码 ③该用户创建的todos

```ts
// /src/db/todo.ts
import { Schema, model, Document } from 'mongoose'
import { ITodo } from './todo'

export interface IUser extends Document {
  usr: string;
  psd: string;
  todos: ITodo[];
}

const UserSchema: Schema = new Schema({
  usr: {
    type: String,
    required: true,
    unique: true,
  },
  psd: {
    type: String,
    required: true,
  },
  todos: [
    {
      type: Schema.Types.ObjectId,
      ref: 'Todo',
    },
  ],
})

export model<IUser>('User', UserSchema)
```

这样写 model 和 schemas 挤在一起，如果想分开就专门在 db 文件夹下建一个  `scheams` 文件夹用于统一管理 scheam，建 `models` 文件夹用于统一管理 model

todo

```ts
// /src/db/scheams/todo.ts
import { Document, Schema } from 'mongoose';

export interface ITodo extends Document {
  content: string;
  status: boolean;
}

export const TodoSchema: Schema = new Schema({
  content: String,
  status: {
    type: Boolean,
    default: false,
  },
});

TodoSchema.index({ content: 'text' });
```

```ts
// /src/db/models/todo.ts
import { model } from "mongoose";

import { ITodo, TodoSchema } from "../schemas/todo";

export default model<ITodo>("Todo", TodoSchema);
```

user

```ts
// /src/db/scheams/user.ts
import { Document, Schema } from 'mongoose';
import { ITodo } from './todo';

export interface IUser extends Document {
  usr: string;
  psd: string;
  todos: ITodo[];
}

export const UserSchema: Schema = new Schema({
  usr: {
    type: String,
    required: true,
    unique: true,	// 设置用户名唯一，即无法再注册同名用户
  },
  psd: {
    type: String,
    required: true,
  },
  todos: [
    {
      type: Schema.Types.ObjectId,
      ref: 'Todo',
    },
  ],
});

```

```ts
// /src/db/models/user.ts
import { model } from 'mongoose';

import { UserSchema, IUser } from '../schemas/user';

export default model<IUser>('User', UserSchema);
```

------

#### 设计请求操作

在 `/src` 下新建一个 `services` 目录，用于统一管理对数据库的操作

**todo数据的操作**

包含功能：

- Todo 内容增加
- Todo 记录删除

- Todo 关键字查询

- Tddo 内容修改
- Todo 状态更改

```tsx
// /src/services/todo.ts
import Todo from '../db/models/todo'
import User from '../db/models/user'

export default class TodoService {  
    // 增加todo
    public async addTodo(userId: string, content: string) {
        const todo = new Todo({content})   // 实例化model，将拿到的content作为一条todo数据的内容
        try {
            const res = await todo.save()               // 将该条数据存储进集合
            const user = await User.findById(userId)    // 通过userId查找到指定的user
            user?.todos.push(res.id)                    // 将添加todo数据的主键添加到user数据对象的todos数组中 ?表示user可为null
            await user?.save()                          // 将修改的user数据存储覆盖原数据
            return res
        } catch(error) {
            throw new Error('新增失败')
        }
    }

    // 删除todo
    public async deleteTodo(todoId: string) {
        try {
            return await Todo.findByIdAndDelete(todoId)
        } catch(error) {
            throw new Error('删除失败')
        }
    }
    
    // 获取用户的所有todo
    public async getAllTodos(userId: string) {
        try {
            const res = await User.findById(userId).populate('todos')
            return res?.todos
        } catch(error) {
            throw new Error('获取失败')
        }
    }

    // 更改todo的状态
    public async updateTodoStatus(todoId: string) {
        try {
            const oldRecord = await Todo.findById(todoId)
            const record = await Todo.findByIdAndUpdate(todoId, {
                status: !oldRecord?.status      // 将todo的status状态取反
            })
            return record
        } catch(error) {
            throw new Error('更新状态失败')
        }
    }

    // 更改todo的内容
    public async updataTodoContent(todoId: string, content: string) {
        try {
            return await Todo.findByIdAndUpdate(todoId, {content})
        } catch {
            throw new Error('更新内容失败')
        }
    }

    // 通过关键字搜索todo
    public async searchTodo(userId: string, query: string) {
        try {
            // mongoose 对关键字为中文的查找支持不佳，因此选用正则的方式去查找
            return await User.findById(userId).populate({
                path: 'todos',
                match: { content: {$regex: new RegExp(query), $options: 'i'}}
            })
        } catch(error) {
            console.log(error)
            throw new Error('查询失败')
        }
    }
}
```

**user数据的操作**

包含功能：

- 用户注册
- 用户登入

```ts
// /src/services/user.ts
import User from '../db/models/user'

export default class UserService {
    // 用户注册
    public async addUser(usr: string, psd: string) {
        try {
            const user = new User({
                usr,
                psd,
                todos: []      // 刚注册的用户没有todo事项
            })
            return await user.save()
        } catch(error) {
            if(error.code = 11000){     // 11000错误码表示唯一属性的值冲突
                  throw new Error('用户名已存在')
            }   else {
                throw error
            }
        } 
    }

    // 用户登入
    public async validUser(usr: string, psd: string) {
        try {
            const user = await User.findOne({ usr })
            if(!user) {     // 查询用户
                throw new Error('用户不存在')  
            }
            if(psd === user.psd) {      // 校验密码
                return user
            }
            throw new Error('密码错误')
        } catch(error) {
            throw new Error(error.message)
        }
    }
}
```

----

### 服务器

#### 规范请求

根据不同请求返回不同的返回码，用于提示浏览器请求的状态

常用状态码：

```
200 OK 						请求成功
201 CREATED 				创建成功
202 ACCEPTED 				更新成功
204 NO CONTENT 				删除成功
401 UNAUTHORIZED 			未授权
403 FORBIDDEN 				禁止访问
404 NOT FOUND 				资源不存在
500 INTERNAL SERVER ERROR 	服务器端内部错误
```

在 `/src` 下新建一个 `utils` 目录，用于统一管理请求的相关配置

新建一个 `enum.ts` 文件，用于导出状态码，由于状态码是数值类型，因此可以很方便的使用枚举

```ts
// /src/utils/enum.ts
export enum StatusCode {
    OK = 200,			// 成功
    Created = 201,		// 创建成功
    Accepted = 202,		// 更新成功
    NoContent = 204		// 删除成功
}
```

新建一个 `response.ts` 文件，用于对拿到返回的数据进行再加工

```ts
// /src/utils/response.ts
import { Context } from 'koa'   // 引入Context接口用于规范ctx的类型
import { StatusCode } from './enum'

// 规范返回数据类型
interface IRes {
    ctx: Context;
    statusCode?: number;
    data?: any;
    errorCode?: number;
    msg?: string;
}

const createRes = (params: IRes) => {
    params.ctx.status = params.statusCode! || StatusCode.OK // !表示非空断言，加在可能为空的变量后面，空则为flase
    params.ctx.body = {
        error_code: params.errorCode || 0,
        data: params.data || null,
        msg: params.msg || ''
    }
}

export default createRes
```

#### 设计路由

在 `/src` 下新建一个 `routes` 文件夹用于统一管理路由

使用RESTful 风格接口设计路由的接口

- 根据请求目的，设置对应 HTTP Method
  -  GET 对应读取资源（Read）
  - PUT 对应更新资源（Update）
  - POST 对应创建资源（Created）
  - DELETE 代表删除资源（Delete）

- 动词表示请求方式，名词表示数据源，一般采用复数形式 （如 GET/users/2 获取 id 为 2 的用户）

- 返回相应的 HTTP 状态码

```
设计接口时，应考虑到后端逻辑所需的数据哪些是需要前端请求发送过来的
```

**todo路由**

```ts
// /src/routes/todo.ts
import { Context } from 'koa'
import Router from 'koa-router'

import TodoService from '../services/todo'      // 拿到对应操作数据库的方法
import { StatusCode } from '../utils/enum'      // 拿到设定的返回状态码
import createRes from '../utils/response'       // 拿到设定的加工返回数据方法

const todoService = new TodoService()
const todoRouter = new Router({
    prefix: '/api/todos'     // 设定统一的路由前缀
})    

todoRouter
    // 关键字查找todo
    .get('/search', async (ctx: Context) => {
        const { userId, query } = ctx.query
        try {
            const data = await todoService.searchTodo(userId, query)
            if(data) {
                createRes({ ctx, data })  // 将拿到的返回数据传入再处理方法
            }
        } catch(error) {
            createRes({ctx, errorCode: 1, msg: error.message})    // 自定义errorCode
        }    
    })

    // 根据动态路由获取到不同user的所有todo列表
    .get('/:userId', async (ctx: Context) => {
        const userId = ctx.params.userId    // 获取到附在url中的userId内容 
        try {
            const data = await todoService.getAllTodos(userId)
            if(data) {
                createRes({ ctx, data })
            }
        } catch(error) {
            createRes({ ctx, errorCode: 1, msg: error.message })
        }
    })

    // 更改todo状态
    .put('/status', async (ctx: Context) => {
        const payload = ctx.request.body    // 获取发送过来的请求数据
        const { todoId } = payload
        try {
            const data = await todoService.updateTodoStatus(todoId)
            if(data) {
                createRes({ ctx, statusCode: StatusCode.Accepted })
            } 
        } catch(error) {
            createRes({ ctx, errorCode: 1, msg: error.message })
        }
    })

    // 更改todo内容
    .put('/content', async (ctx: Context) => {
        const payload = ctx.request.body
        const { todoId, content } = payload
        try {
            const data = await todoService.updataTodoContent(todoId, content)
            if(data) {
                createRes({ ctx, statusCode: StatusCode.Accepted })
            }
        } catch(error) {
            createRes({ ctx, errorCode: 1, msg: error.message })
        }
    })

    // 添加todo
    .post('/', async (ctx: Context) => {
        const payload = ctx.request.body
        const { userId, content } = payload
        try {
            const data = await todoService.addTodo(userId, content)
            if(data) {
                createRes({ ctx, statusCode: StatusCode.Created, data })
            }
        } catch(error) {
            createRes({ ctx, errorCode: 1, msg: error.message })
        }
    })

    // 删除todo
    .delete('/:todoId', async (ctx:Context) => {
        const todoId = ctx.params.todoId
        try {
            const data = await todoService.deleteTodo(todoId)
            if(data) {
                createRes({ ctx, statusCode: StatusCode.NoContent })
            }
        } catch(error) {
            createRes({ctx, errorCode:1, msg: error.message})
        }
    })

export default todoRouter 
```

**user路由**

```js
// /src/routes/user.ts
import { Context, Request } from 'koa'
import Router from 'koa-router'

import UserService from '../services/user'
import { StatusCode } from '../utils/enum'
import createRes from '../utils/response'

const userService = new UserService()
const userRouter = new Router({
    prefix: '/api/users'    
})    

userRouter
    // 用户登入
    .post('/login', async (ctx: Context) => {
        const payload = ctx.request.body
        const { username, password } = payload
        try {
            const user = await userService.validUser(username, password)
            createRes({
                ctx,
                data: {
                    userId: user._id,
                    username: user.usr
                }
            })
        } catch(error) {
            createRes({ctx, errorCode: 1, msg: error.message})
        }
    })

    // 用户注册
    .post('/', async (ctx: Context) => {
        const payload = ctx.request.body
        const { username, password } = payload
        try {
            const data = await userService.addUser(username, password)
            if(data) {
                createRes({ ctx, statusCode: StatusCode.Created })
            }
        } catch(error) {
            createRes({ ctx, errorCode: 1, msg: error.message })
        }
    })

export default userRouter
```

#### 总入口

在 `/src` 下新建一个 `config.ts` 用于统一管理可能会变动的服务配置，如连接数据库的地址，和服务启动的端口等，这样项目更加灵活

```ts
const Config = {
    PORT: 4000,     // 服务启动的端口
    MONGODB_URL: 'xxx'  // 数据库地址
}

export default Config
```

可以使用自己电脑当mongoDB的数据库服务器，也可以使用官方提供的免费线上服务器，具体如何申可以看这个博客：

```
https://segmentfault.com/a/1190000021870763?utm_source=tag-newest
```

由于已经在项目配置中配置了 `/src/app.ts` 为入口文件，因此修改该文件内容，记得配置 `koa2-cors` 处理跨域

```ts
import Koa from 'koa'
import bodyParser from 'koa-bodyparser'
import cors from 'koa2-cors'

import Config from './config'
import connectDB from './db'
import todoRouter from './routes/todo'
import userRouter from './routes/user'


const app = new Koa()

connectDB(Config.MONGODB_URL)

app
    .use(cors())
    .use(bodyParser())
    .use(userRouter.routes())
    .use(todoRouter.routes())

app.listen(Config.PORT, () => {
    console.log(`Server ready at htttp://localhost:${Config.PORT}`)
})
```

启动服务 `npm run watch` ，随便访问一个接口，比如`http://localhost:4000/api/todos/status`，有东西就表示没问题



---

## 前端

### 初始化

#### 引入antd

由于本项目主要使用antd作为ui开发框架，因此需要引入

```
npm install antd -s
```

配置antd和antd的按需加载

```
npm install react-app-rewired customize-cra babel-plugin-import -s
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

在项目根目录创建一个 `config-overrides.js` 用于修改默认配置（写后缀名为ts可能会报错）

当修改scripts的对象内容时，需要在`config-overrides.js`进行额外配置，即需要存在这个文件

```jsx
module.exports = function override(config, env) {
  // do stuff with the webpack config...
  return config;
};
```

修改`config-overrides.js`，实现按需导入

 ```jsx
// 不要用 import，可能会报错
const { override, fixBabelImports } = require('customize-cra');

module.exports = override(
    fixBabelImports('import', {
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: 'css',
    }),
);
 ```

修改 `/src/app.ts`，随便写点代码看看 antd 是否能正常使用，启动项目正常能看到 antd 的组件则说明配置成功

```tsx
import React from 'react';
import {Button} from 'antd'

function App() {
    return (
        <div>
        	<Button>Demo</Button>
        </div>
    );
}

export default App;
```

----

#### CSS预处理

可以选择 `less` 或者 `sass`

**Less的配置：**

注意：less-loader现在的版本这么操作会有问题（less-loader：5.0.0版本以前可用）

```
npm install less less-loader -s
```

修改 `config-overrides.js`，引入 less 的配置

```js
const { override, fixBabelImports, addLessLoader } = require('customize-cra');

module.exports = override(
    fixBabelImports('import', {
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: 'css',
    }),
    addLessLoader({
		javascriptEnabled: true,
	})
);
```

**Sass的配置：**

react脚手架生成的项目中已经内置了sass的配置，因此直接下载一个包即可

```
npm install node-sass -s
有时候 npm 下载该包会出错，可以试着用 cnpm 去下载
或者指定版本下载：npm install node-sass@4.14.0 -s（4.14.0版本可以直接下载成功）
```

本项目中选择 Sass 预处理 CSS



----

#### 项目规划

在开始准备前端项目时应有一个大概的初步规划，才能明确接下来该怎么设计页面，然后再慢慢加功能和优化，否则就会无从下手

- 各页面的跳转路由
- 根据页面的功能设计对应的组件

**页面设计**

**首页：**

- 路由：路径为 `/`

- 页面效果：

```
可以使用一些作图软件制作想要的页面的效果
推荐一个在线的免费作图网站：https://www.processon.com/
```

<img src="https://img-blog.csdnimg.cn/20200712180148833.png" style="margin:0">

想要实现的功能：首页负责用户的登入和注册，通过 `切换登入/注册按钮` 来切换渲染不同的提交表单按钮，不同的提交表单按钮会触发不同的请求

如登入按钮会将数据请求到后端的用户登入接口，而注册按钮会将数据请求到后端的用户注册接口

**Todo页：**

- 路由：路径为 `/`

- 页面效果：

<img src="https://img-blog.csdnimg.cn/20200712183317725.png" style="margin:0">

所有的功能都是前端将数据发送到后端进行处理，然后前端拿到后端返回的数据，将该数据渲染到指定位置



-----

#### 配置路由

由于使用到了路由，因此需要安装第三方包

```
npm install react-router-dom @types/react-router-dom -s 
```

在 `/src` 下新建 `views` 目录，且在该目录下建立 `Index` 和 `Todo` 目录，用于存放首页和Todo页的组件

分别在两个目录下新建 `index.tsx` 文件，写一个简易的组件

```tsx
// /src/views/Index/index.tsx(Todo组件同理)
import React, { FC } from 'react'

const Index: FC<any> = () => {
    return (
        <div>首页</div>
    )
}

export default Index
```

修改 `/src/App.tsx` 文件，引入路由

```tsx
import React from 'react';
import {Route, Switch, BrowserRouter} from 'react-router-dom'

import Index from './views//Index'
import Todo from './views/Todo'


function App() {
    return (    
        <React.Fragment>    
			<BrowserRouter>
				<Switch>
					<Route path="/" component={Index} exact={true} />
					<Route path="/todo" component={Todo} />
				</Switch>
			</BrowserRouter>
        </React.Fragment>
    )
}

export default App
```

----

#### 引入redux

使用redux管理项目共享数据，中间件选用redux-saga

```
npm install redux @types/redux -s
npm install react-redux @types/react-redux -s
npm install redux-saga @types/redux-saga -s
```

为了方便查看中间件执行流，在chrom安装插件`Redux DevTools`，同时下载对应代码包

```
npm install redux-devtools-extension -s
```

在 `/src` 下建立 `store` 文件夹用于初始化redux配置

```ts
// /src/store/index.ts
import {applyMiddleware, createStore} from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';
import createSagaMiddleware from 'redux-saga';

const sagaMiddleware = createSagaMiddleware();

const rootReducer = () => {
    return
}

function* rootSaga() {
    return
}

export const store = createStore(
    rootReducer,
    composeWithDevTools(applyMiddleware(sagaMiddleware))
);

sagaMiddleware.run(rootSaga);
```



---

#### 公共文件

一般会在项目中建立一个 `common`  目录用于编写公共的的一些代码，如

- 
- 配置（config）：用于导出公共的配置代码，如请求的地址
- 枚举（enum）：用于导出公共的枚举
- 接口（interface）：用于导出相应的接口

如在后端我们配置的路由地址的前缀均为 `http://localhost:4000/api/`，因此可以直接配置一个base地址，后面在请求中补全后续路由地址即可

```ts
// /src/common/config.ts
let Config = {
    API_URI:"http://localhost:5000/api/",
};
  
export default Config;
```

又由于在后端接口中，声明返回的数据为：`error_code、data、msg`，因此可以根据这些固定的返回信息写明一个接口用于规范犯规数据的类型

```ts
// /src/common/interface.ts
export interface IRes {
    error_code: number;
    data: any;
    msg: string;
}
```



----

#### 配置请求

使用 `axios` 发送请求到后端

```
npm install axios -s
```

一般在项目中建立 `api` 文件夹统一管理请求

```ts
// /src/api/request.ts
import { message } from 'antd';
import axios from 'axios';

import Config from '../common/config';
import {IRes} from '../common/interface'

const request = axios.create({
    baseURL: Config.API_URI,
    headers: {
        'Content-Type': 'application/json; charset=UTF-8'
    }
});

// 使用响应拦截器拦截响应请求并用弹出提示信息
request.interceptors.response.use((response) => {
    const res: IRes = response.data;
    if(res.error_code) {
        message.warn(res.msg);
        throw new Error(res.msg);
    }
    return response.data
});

export default request;
```



----

#### 工具类

一般在项目中新建 `utils` 文件夹用于创建项目工具类

这里为了保存用于的登入状态，使用到了浏览器的 `localStorage` 属性，使用一个工具类将对应功能封装起来

```ts
// /src/utils/index.ts
export class LocalStorage {
    public static get(key: string) {
        return localStorage.getItem(key);
    }
    public static set(key: string, value: string) {
        localStorage.setItem(key, value);
    }
    public static remove(key: string) {
        localStorage.removeItem(key);
    }
}
// 这部分功能可能再加强一下，如加入有效时间设置以及判断等等
```



----

### 页面及组件

配置完成上面内容后，可以简单理一下各个内容之间的联系然后进行代码组合编写

```markdown
渲染页面路线： 
	访问指定地址 → 响应对应路由操作 → 路由组件渲染页面 → 页面组件渲染各个小组件
请求路线路线（请求操作一般写在redux的reducer中）：
	在渲染页面组件前通过调用redux的dispatch方法发送请求获取到初始化页面数据 → 阻塞操作，直到手动调用dispatch发送请求
```

请求一般在页面组件中的 `componentDidMount` 中发送，如果是函数式组件可以使用 `useEffect` 进行模拟该生命周期

注意页面组件和通用组件的区别，如果随意编写，容易将本该页面组件负责的内容写到通用组件，通用组件负责的内容写到页面组件，造成代码混乱冗杂

页面组件和通用组件的区别：

- 通用组件不应该拥有自己的状态值，即所有的数据由页面元素传来的props拿到

- 通用组件不应该使用生命周期函数以及hook，避免周期混乱

**总结：**

- 路由负责渲染页面

- 页面负责获取到组件需要的数据并传给组件进行渲染

- redux负责统一管理请求操作管理公共数据（可以根据具体请求获取的数据是否需要在组件中公共使用决定是否该在redux中控制请求）

  

----

#### 首页

根据项目规划的设计开始编写首页的页面及功能

<img src="https://img-blog.csdnimg.cn/20200712180148833.png" style="margin:0;width:650px">



-----

##### 请求

根据后端的user路由可以进行前端请求编写

```tsx
// /src/api/user.ts
import request from './request';

class UserAPI {
    public static PREFIX = '/users';
    public login(username: string, password: string) {
        return request.post(`${UserAPI.PREFIX}/login`, {
            username,
            password
        });
    }
    public register(username: string, password: string) {
        return request.post(`${UserAPI.PREFIX}`, {
            username,
            password
        });
    }
}

export default UserAPI;
```



-----

##### redux

总体的思路：

1. 外部触发 `dispatch` 函数，将从表单中获取到的登入或注册的信息传给能触发saga监听函数的action参数中
2. action拿到这些信息，通过saga监听函数`takeEvery` 触发saga函数
3. 通过saga去请求后端并拿到返回数据，同时触发影响reducer的action
4. reducer拿到数据信息，保存在redux的state中，用于在组件中使用

```markdown
总体流程：
	dispatch → 触发acion → 触发监听函数 → 触发saga函数 → 触发reducer → 更新state
	实际上就是相当于 dispatch(action) → 中间件对action进行处理 → dispatch(最终action) → 触发reducer → 更新state
```

上面需要值得注意的是，有两种action，一种是为了触发监听函数从而启动saga，一种是为了触发reducer函数，，从这里可以看出saga是如何作为中间件来使用的，如下面这个例子

```ts
// action
const action = (info) => ({
    type: 'SAGA_ACTION',
    payload: info
})

// 外部触发action
dispatch(action(info))		// 将信息传给action


// 监听函数
function* rootSaga() {
    yield takeEvery('SAGA_ACTION',saga)		// 监听到有'SAGA_ACTION'的action被调用，触发saga函数	
}

// saga
function* saga(action) {
    const { payload } = action		// 拿到触发takeEvery函数的action对象并取出info数据
    yield call(fetch(...))		// 将拿到的数据发送给后端操作
    // 可能对返回数据做出一些处理，然后dispatch一个带有最终数据的action触发reducer函数
    yield put({
        type: 'REDUCER_ACTION',
        payload: newData
    })
}

// reducer
const reducer = (initState, action) => {
    switch(action.type) {
        // 从saga那dispatch的'REDUCER_ACION'被reducer判断并执行
        case 'REDUCER_ACION':
            const { payload } = action	// 拿到最终数据
            // 进行一些操作，最终更新state
            return newState
    }
}
```

----

**规范类型**

```ts
// /src/store/user/types.ts
// 规范ActionType，为了避免手打字符串造成输错，一般会定义为一个常量变量来使用
export const REGISTER = 'REGISTER';
export const REGISTER_SUC = 'REGISTER_SUC';
export const LOGIN = 'LOGIN';
export const LOGIN_SUC = 'LOGIN_SUC';
export const LOGOUT = 'LOGOUT';
export const LOGOUT_SUC = 'LOGOUT_SUC';
export const KEEP_LOGIN = 'KEEP_LOGIN';

// 规范action创建函数的传参类型
export interface IAuthState {
    username: string;
    password: string;
}
export interface IUserState {
    userId: string;
    username: string;
    errMsg: string;
}

// 规范action创建函数类型
export interface ILoginAction {
    type: typeof LOGIN;
    payload: IAuthState;
}
export interface ILoginSucAction {
    type: typeof LOGIN_SUC;
    payload: IUserState;
}
export interface ILogoutAction {
    type: typeof LOGOUT;
}
export interface ILogoutSucAction {
    type: typeof LOGOUT_SUC;
}
export interface IRegisterAction {
    type: typeof REGISTER;
    payload: IAuthState;
}
export interface IRegSucAction {
    type: typeof REGISTER_SUC;
    payload: IUserState;
}
export interface IKeepLogin {
    type: typeof KEEP_LOGIN;
    payload: IUserState;
}

// 规范传入reducer的所有action类型
export type UserActionTypes =
    | ILoginAction
    | ILoginSucAction
    | ILogoutAction
    | ILogoutSucAction
    | IKeepLogin
    | IRegisterAction
    | IRegSucAction;
```

**创建action**

```ts
// /src/store/user/action.ts
import {
    IAuthState,
    IUserState,
    LOGIN,
    REGISTER,
    LOGOUT,
    KEEP_LOGIN,
} from './types';

export const login = (authState: IAuthState) => ({
    type: LOGIN,
    payload: authState,
});

export const register = (authState: IAuthState) => ({
    type: REGISTER,
    payload: authState,
});

export const logout = () => ({
    type: LOGOUT
});

export const keepLogin = (userState: IUserState) => ({
    type: KEEP_LOGIN,
    payload: userState,
});
```

**配置reducer**

```ts
// /src/store/user/reducers.ts
import {
    LOGIN_SUC,
    UserActionTypes,
    REGISTER_SUC,
    IUserState,
    KEEP_LOGIN,
    LOGOUT_SUC
}  from './types';

const initialState: IUserState = {
    userId: '',
    username: '',
    errMsg: '',
};

// 此reducer的功能是将用户的登入的信息和登入状态提示添加进state
export default function userReducer(
    state = initialState,
    action: UserActionTypes
) {
    switch (action.type) {
        case REGISTER_SUC:
            // 新建一个空对象，将state和payload的所有内容复制进去，返回一个全新的state
            // 虽然计划是action.payload已经包含所有的state信息，和state略为重复，但是重新传一次state表示和原有的state有联系，显得严谨
            // 后面同名的属性传值会覆盖之前的
            return {
                
                ...state,           
                ...action.payload,
            };
        case LOGIN_SUC:
            return {
                ...state,
                ...action.payload
            };
        case LOGOUT_SUC:
            return {
                ...state,
                userId: '',
                username: '',
                errMsg: '',
            };
        case KEEP_LOGIN:
            return {
                ...state,
                ...action.payload,
            };
        default:
            return state
    }
}
```

**设置saga中间件**

设置user的saga

```ts
// /src/store/user/saga.ts
import { call, put } from 'redux-saga/effects';

import UserAPI from '../../api/user';
import { IRes } from '../../common/interface';
import { LocalStorage } from '../../utils';
import {
  ILoginAction,
  IRegisterAction,
  LOGIN_SUC,
  REGISTER_SUC,
  ILogoutAction,
  LOGOUT_SUC,
} from './types';
import { message } from 'antd';

const userAPI = new UserAPI();

export function* login(action: ILoginAction) {
    const { username, password } = action.payload;

    try { 
        const res: IRes = yield call(userAPI.login, username, password);    // 将登入请求发送到后端
        // 将用户信息存入缓存，使用阻塞方式是为了确保保证后续操作中缓存中已经存在值
        yield call(LocalStorage.set, 'userId', res.data.userId);
        yield call(LocalStorage.set, 'username', res.data.username);
        yield put({
            type: LOGIN_SUC,
            payload: { ...res.data, errMsg: res.msg }
        });
    } catch {}
}

export function* logout(action: ILogoutAction) {
    try {
        yield call(LocalStorage.remove, 'userId');
        yield call(LocalStorage.remove, 'username');
        yield put({
            type: LOGOUT_SUC,
        })
    } catch {}
}

export function* register(action: IRegisterAction) {
    const { username, password } = action.payload;

    try {
        yield call(userAPI.register, username, password);
        yield put({
            type: REGISTER_SUC,
        });
        message.success('注册成功');
    } catch {}
}
```

在src下新建文件 `saga.ts` 用于设置 rootSaga

```ts
// /src/saga.ts
import { takeEvery } from 'redux-saga/effects';

import { login, register, logout } from './store/user/saga';
import { LOGIN, REGISTER, LOGOUT } from './store/user/types';

function* rootSaga() {
    yield takeEvery(LOGIN, login);
    yield takeEvery(LOGOUT, logout);
    yield takeEvery(REGISTER, register);
}

export default rootSaga;
```

同时修改saga的实例配置

```ts
// /src/store/index.ts
import {applyMiddleware, combineReducers, createStore} from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';
import createSagaMiddleware from 'redux-saga';

import rootSaga from '../saga';
import userReducer from './user/reducers';

const sagaMiddleware = createSagaMiddleware();
const rootReducer = combineReducers({
    userReducer,
});

export const store = createStore(
    rootReducer,
    composeWithDevTools(applyMiddleware(sagaMiddleware))
);

// 这里的AppStore实际上是combineReducers中所有state的并类型，相当于{ user: IUserState, xxx:XXX ... }(注意这里是一个对象包含所有类型)
// 这样写的好处是可以解构出对应的state，然后直接提示该state的所有属性
export type AppStore = ReturnType<typeof rootReducer>;
                                  
sagaMiddleware.run(rootSaga);
```

为了让组件可以使用 `redux`，在根组件处配置 `react-redux` 的 `Provider` 并引入写好的 `store`

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import zhCN from 'antd/lib/locale-provider/zh_CN';
import { ConfigProvider } from 'antd';

import App from './App';
import { store } from './store';

ReactDOM.render(
	<Provider store={store}>
		<ConfigProvider locale={zhCN}>
			<App />
		</ConfigProvider>
	</Provider>,
	document.getElementById('root')
);
```



-----

##### 页面

把基础功能先写上

```tsx
// /src/views/Index/index.tsx
import React, { FC, useEffect, useState } from "react";
import { RouteComponentProps } from "react-router"; // TS需要引入传入的props类型
import { connect, ConnectedProps } from "react-redux"; 			// TS需要引入传入的props类型

import { LocalStorage } from "../../utils";
import { AppStore } from "../../store";
import { keepLogin } from "../../store/user/action";
import { compose } from "redux";

// 对redux的映射
const mapState = ({ user }: AppStore) => ({
	user,
});

const mapDispatch = {
	keepLogin,
};

// 组合
const connector = connect(mapState, mapDispatch);
type PropsFromRedux = ConnectedProps<typeof connector>		

const Home: FC<RouteComponentProps & PropsFromRedux> = ({
	history,
	user,
	keepLogin,
}) => {
	
	const [showLogin, setShowLogin] = useState(true);	// 根据状态决定显示登入还是注册按钮
	
	useEffect(() => {
		const userId = LocalStorage.get('userId');
		const username = LocalStorage.get('username');
		if (userId && username) {
			if (!user.userId) {
				keepLogin({ userId, username, errMsg: ''});
			} else {
				history.push('/todo');	// 登入状态下跳转列表页,相当于重定向
			}
		}
	},[user])

	const toggleForm = () => {
		setShowLogin(!showLogin);
		
	};

	return (
		<div>
			<h1>Todo List</h1>
			{/* {showLogin ? <登入组件/> : <注册组件/>} */}
			<p>
				<span>Or&nbsp;&nbsp;</span>
				<span onClick={toggleForm}>
					{showLogin ? '现在注册!' : '已有账号!'}
				</span>
			</p>
		</div>
	);
};

export default connector(Home);

```

**登入组件：**

主要用antd的表单组件收集提交的数据然后传给action创建函数并dispatch触发saga请求后端

```tsx
// /src/components/LoginForm/index.tsx
import { LockOutlined, UserOutlined } from '@ant-design/icons';
import { Button, Form, Input } from 'antd';
import { Store } from 'antd/lib/form/interface';       // antD表单内的数据类型
import React, { FC } from 'react';
import { connect, ConnectedProps } from 'react-redux';

import { login } from '../../store/user/actions';

const mapDispatch = {
    login,  
};

const connector = connect(() => ({}), mapDispatch);
type PropsFromRedux = ConnectedProps<typeof connector>;

interface ILoginForm extends PropsFromRedux {}

const LoginForm: FC<ILoginForm> = ({ login }) => {
    // 添加表单时，通过saga发送请求到后端
    const onFinish = (values: Store) => {
        const { username, password } = values;
        login({
            username,
            password,
        });
    };

    return (
        <Form onFinish={onFinish}>
            <Form.Item
                name="username"
                rules={[{ required: true, message: '请输入用户名!'}]}
            >
                <Input
                    prefix={<UserOutlined />}
                    placeholder="用户名"
                    autoComplete="off"
                />
            </Form.Item>
            <Form.Item
                name="password"
                rules={[{ required: true, message: '请输入密码! '}]}
            >
                <Input prefix={<LockOutlined/>} type="password" placeholder="密码" />
            </Form.Item>
            <Form.Item>
                <Button type="primary" htmlType="submit">
                    登录
                </Button>
            </Form.Item>
        </Form>
    )
}

export default connector(LoginForm);
```

**注册组件**

和登入组件类似

```tsx
// /src/components/RegForm/index.tsx
import { LockOutlined, UserOutlined } from '@ant-design/icons';
import { Button, Form, Input } from 'antd';
import { Store } from 'antd/lib/form/interface';       // antD表单内的数据类型
import React, { FC } from 'react';
import { connect, ConnectedProps } from 'react-redux';

import { register } from '../../store/user/actions';

const mapDispatch = {
    register,  
};

const connector = connect(() => ({}), mapDispatch);
type PropsFromRedux = ConnectedProps<typeof connector>;

interface IRegForm extends PropsFromRedux {}

const RegForm: FC<IRegForm> = ({ register }) => {
    const [form] = Form.useForm();
    const onFinish = (values: Store) => {
        const { username, password } = values;
        register({
            username,
            password,
        });
        form.setFieldsValue({ username:'', password: '' })      // 注册成功后将表单value清空
    };

    return (
        <Form onFinish={onFinish} form={form}>
            <Form.Item
                name="username"
                rules={[{ required: true, message: '请输入用户名!'}]}
            >
                <Input
                    prefix={<UserOutlined />}
                    placeholder="用户名"
                    autoComplete="off"
                />
            </Form.Item>
            <Form.Item
                name="password"
                rules={[{ required: true, message: '请输入密码! '}]}
            >
                <Input prefix={<LockOutlined/>} type="password" placeholder="密码" />
            </Form.Item>
            <Form.Item>
                <Button type="primary" htmlType="submit">
                    注册
                </Button>
            </Form.Item>
        </Form>
    )
}

export default connector(RegForm);
```

更新页面的渲染组件

```tsx
render(
    <div>
	<h1>Todo List</h1>
    {showLogin ? <LoginForm/> : <RegForm/>} 
    <p>
        <span>Or&nbsp;&nbsp;</span>
        <span onClick={toggleForm}>
            {showLogin ? '现在注册!' : '已有账号!'}
        </span>
    </p>
    </div>
)
```



-----

##### 样式

使用sass编写页面样式

```scss
// /src/views/Index/index.module.scss
.wrapper {
    width: 100vw;
    height: 100vh;
    background: #f6f6f6;

    .container {
        position: relative;
        top: 200px;
        width: 300px;
        margin: auto;
        // 屏幕小于700px范围内时，适当将表单上移
        @media screen and (max-width: 700px) {
            top: 150px;
        }
    }
    
    .tip {
        span:nth-child(2) { 
            color: #096dd9;
            cursor: pointer;
            &:hover {
                border-bottom: 1px solid currentColor;
            }
        }
    }
}
```

引入样式，同时适当修改页面标签

```tsx
import styles from './index.module.scss'
...
return (
    <div className={styles.wrapper}>
        <div className={styles.container}>
            <h1>Todo List</h1>
            {showLogin ? <LoginForm/> : <RegForm/>} 
            <p className={styles.tip}>
                <span>Or&nbsp;&nbsp;</span>
                <span onClick={toggleForm}>
                    {showLogin ? '现在注册!' : '已有账号!'}
                </span>
            </p>
        </div>
    </div>
);
```



-----

#### 列表页

<img src="https://img-blog.csdnimg.cn/20200712183317725.png" style="margin:0">

##### 请求

```ts
// /src/api/todo.ts
import request from './request';

class TodoAPI {
    public static PREFIX = '/todos';
    public fetchTodo(userId: string) {
        return request.get(`${TodoAPI.PREFIX}/${userId}`);
    }
    public addTodo(userId: string, content: string) {
        return request.post(`${TodoAPI.PREFIX}`, {
            userId,
            content,
        });
    }
    public searchTodo(userId: string, query: string) {
        return request.get(
            `${TodoAPI.PREFIX}/search?userId=${userId}&query=${query}`
        );
    }
    public deleteTodo(todoId: string) {
        return request.delete(`${TodoAPI.PREFIX}/${todoId}`)
    }
    public updateTodoStatus(todoId: string) {
        return request.put(`${TodoAPI.PREFIX}/status`, {
            todoId,
        });
    }
    public updateTodoContent(todoId: string, content: string) {
        return request.put(`${TodoAPI.PREFIX}/content`, {
            todoId,
            content,
        });
    }
}

export default TodoAPI;
```



----

##### redux

**规范类型**

```ts
// /src/store/todo/types.ts
// Constant
export const FETCH_TODO = 'FETCH_TODO';
export const FETCH_TODO_SUC = 'FETCH_TODO_SUC';
export const ADD_TODO = 'ADD_TODO';
export const ADD_TODO_SUC = 'ADD_TODO_SUC';
export const SEARCH_TODO = 'SEARCH_TODO';
export const SEARCH_TODO_SUC = 'SEARCH_TODO_SUC';
export const DELETE_TODO = 'DELETE_TODO';
export const DELETE_TODO_SUC = 'DELETE_TODO_SUC';
export const UPDATE_TODO_CONTENT = 'UPDATE_TODO_CONTENT';
export const UPDATE_TODO_CONTENT_SUC = 'UPDATE_TODO_CONTENT_SUC';
export const UPDATE_TODO_STATUS = 'UPDATE_TODO_STATUS';
export const UPDATE_TODO_STATUS_SUC = 'UPDATE_TODO_STATUS_SUC';

// State
export interface ITodoState {
    _id: string;
    content: string;
    userId: string;
    status: boolean;
}


// Action 使用接口限制传入reducer的action类型
export interface IFetchAction {
    type: typeof FETCH_TODO;
    payload: { userId: string };
}
export interface IFetchSucAction {
    type: typeof FETCH_TODO_SUC;
    payload: ITodoState[];
}
export interface IAddAction {
    type: typeof ADD_TODO;
    payload: {
        userId: string;
        content: string;
    };
}
export interface IAddSucAction {
    type: typeof ADD_TODO_SUC;
    payload: ITodoState;
}
export interface ISearchAction {
    type: typeof SEARCH_TODO;
    payload: { userId: string; query: string };
}
export interface ISearchSucAction {
    type: typeof SEARCH_TODO_SUC;
    payload: ITodoState[];
}
export interface IDeleteAction {
    type: typeof DELETE_TODO;
    payload: {
        todoId: string;
    };
}
export interface IDeleteSucAction {
    type: typeof DELETE_TODO_SUC;
    payload: {
        todoId: string;
    };
}
export interface IUpdateContentAction {
    type: typeof UPDATE_TODO_CONTENT;
    payload: {
        todoId: string;
        content: string;
    };
}
export interface IUpdateContentSucAction {
    type: typeof UPDATE_TODO_CONTENT_SUC;
    payload: {
        todoId: string;
        content: string;
    };
}
export interface IUpdateStatusAction {
    type: typeof UPDATE_TODO_STATUS;
    payload: {
        todoId: string;
    };
}
export interface IUpdateStatusSucAction {
    type: typeof UPDATE_TODO_STATUS_SUC;
    payload: {
        todoId: string;
    };
}

export type TodoActionTypes =
    | IFetchAction
    | IFetchSucAction
    | IAddAction
    | IAddSucAction
    | IUpdateContentAction
    | IUpdateContentSucAction
    | IUpdateStatusAction
    | IUpdateStatusSucAction
    | ISearchAction
    | ISearchSucAction
    | IDeleteAction
    | IDeleteSucAction;
```

**创建action**

```ts
// /src/store/todo/actions.ts
import {
    ADD_TODO,
    DELETE_TODO,
    FETCH_TODO,
    SEARCH_TODO,
    UPDATE_TODO_CONTENT,
    UPDATE_TODO_STATUS,
} from './types';

export const addTodo = (userId: string, content: string) => ({
    type: ADD_TODO,
    payload: { userId, content },
});

export const fetchTodo = (userId: string) => ({
    type: FETCH_TODO,
    payload: { userId },
});
export const searchTodo = (userId: string, query: string) => ({
    type: SEARCH_TODO,
    payload: { userId, query },
});

export const deleteTodo = (todoId: string) => ({
    type: DELETE_TODO,
    payload: { todoId },
});
export const updateTodoStatus = (todoId: string) => ({
    type: UPDATE_TODO_STATUS,
    payload: { todoId },
});

export const updateTodoContent = (todoId: string, content: string) => ({
    type: UPDATE_TODO_CONTENT,
    payload: { todoId, content },
});

```

**配置reducer**

```ts
// /src/store/todo/reducers.ts
import {
    ADD_TODO_SUC,
    DELETE_TODO_SUC,
    FETCH_TODO_SUC,
    SEARCH_TODO_SUC,
    UPDATE_TODO_CONTENT_SUC,
    UPDATE_TODO_STATUS_SUC,
    ITodoState,
    TodoActionTypes,
} from './types';

const initialState: ITodoState[] = [];

export default function todoReducer(
    state = initialState,
    action: TodoActionTypes
) {
    switch (action.type) {
        case ADD_TODO_SUC:
            console.log(action.payload)
            return [...state, action.payload];
        case FETCH_TODO_SUC:
            return [...action.payload];
        case DELETE_TODO_SUC:
            return state.filter((v) => v._id !== action.payload.todoId);    // 通过生成一个没有指定id的数组来实现删除效果
        case UPDATE_TODO_STATUS_SUC:
            return state.map((v) => 
               v._id === action.payload.todoId ? { ...v, status: !v.status } : v
            );
        case SEARCH_TODO_SUC: 
            return [...action.payload];
        case UPDATE_TODO_CONTENT_SUC:
            return state.map((v) => 
                v._id === action.payload.todoId
                    ? {...v, content: action.payload.content}
                    : v
            );
        default:
            return state;
    }
}
```

将todo的reducer导出给redux实例使用

```ts
// /src/store/index.ts
import {applyMiddleware, combineReducers, createStore} from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';
import createSagaMiddleware from 'redux-saga';

import rootSaga from '../saga';
import userReducer from './user/reducers';
import todoReducer from './todo/reducers';

const sagaMiddleware = createSagaMiddleware();
const rootReducer = combineReducers({
    user: userReducer,
});

export const store = createStore(
    rootReducer,
    composeWithDevTools(applyMiddleware(sagaMiddleware))
);

export type AppStore = ReturnType<typeof rootReducer>;

sagaMiddleware.run(rootSaga);
```

**设置saga中间件**

设置todo的saga

```ts
// // /src/store/todo/saga.ts
import { call, put } from 'redux-saga/effects';
import TodoAPI from '../../api/todo';

import {
    ADD_TODO_SUC,
    DELETE_TODO_SUC,
    FETCH_TODO_SUC,
    IAddAction,
    IDeleteAction,
    IFetchAction,
    ISearchAction,
    IUpdateContentAction,
    IUpdateStatusAction,
    SEARCH_TODO_SUC,
    UPDATE_TODO_CONTENT_SUC,
    UPDATE_TODO_STATUS_SUC,
} from './types';

import { IRes } from '../../common/interface';
import { message } from 'antd';

const todoAPI = new TodoAPI();

export function* fetchTodo(action: IFetchAction) {
    const { userId } = action.payload;

    const res: IRes = yield call(todoAPI.fetchTodo, userId);
    yield put({
        type: FETCH_TODO_SUC,
        payload: res.data,
    });
}

export function* addTodo(action: IAddAction) {
    const { userId, content } = action.payload;

    const res: IRes = yield call(todoAPI.addTodo, userId, content);
    yield put({
        type: ADD_TODO_SUC,
        payload: res.data,
    });
    message.success('新增成功');
}

export function* deleteTodo(action: IDeleteAction) {
    const { todoId } = action.payload;
    yield call(todoAPI.deleteTodo, todoId);
    yield put({
        type: DELETE_TODO_SUC,
        payload: { todoId },
    });
    message.success('删除成功');
}

export function* searchTodo(action: ISearchAction) {
    const { userId, query } = action.payload;

    const res: IRes = yield call(todoAPI.searchTodo, userId, query);
    yield put({
        type: SEARCH_TODO_SUC,
         payload: res.data.todos,
    });
}

export function* updateTodoStatus(action: IUpdateStatusAction) {
    const { todoId } = action.payload;

    yield call(todoAPI.updateTodoStatus, todoId);
    yield put({
        type: UPDATE_TODO_STATUS_SUC,
        payload: { todoId },
    });
}

export function* updateTodoContent(action: IUpdateContentAction) {
    const { todoId, content } = action.payload;

    yield call(todoAPI.updateTodoContent, todoId, content);
    yield put({
        type: UPDATE_TODO_CONTENT_SUC,
        payload: { todoId, content },
    });
    message.success('编辑成功');
}
```

修改rootSaga，加入todo的saga内容

```ts
// /src/saga.ts
import { takeEvery } from 'redux-saga/effects';

import {
    addTodo,
    deleteTodo,
    fetchTodo,
    searchTodo,
    updateTodoContent,
    updateTodoStatus,
} from './store/todo/saga';
import {
    ADD_TODO,
    DELETE_TODO,
    FETCH_TODO,
    SEARCH_TODO,
    UPDATE_TODO_CONTENT,
    UPDATE_TODO_STATUS,
} from './store/todo/types';
import { login, register, logout } from './store/user/saga';
import { LOGIN, REGISTER, LOGOUT } from './store/user/types';

function* rootSaga() {
    yield takeEvery(LOGIN, login);
    yield takeEvery(LOGOUT, logout);
    yield takeEvery(REGISTER, register);
    yield takeEvery(FETCH_TODO, fetchTodo);
    yield takeEvery(SEARCH_TODO, searchTodo);
    yield takeEvery(ADD_TODO, addTodo);
    yield takeEvery(DELETE_TODO, deleteTodo);
    yield takeEvery(UPDATE_TODO_STATUS, updateTodoStatus);
    yield takeEvery(UPDATE_TODO_CONTENT, updateTodoContent);
}

export default rootSaga;
import { takeEvery } from 'redux-saga/effects';

import {
    addTodo,
    deleteTodo,
    fetchTodo,
    searchTodo,
    updateTodoContent,
    updateTodoStatus,
} from './store/todo/saga';
import {
    ADD_TODO,
    DELETE_TODO,
    FETCH_TODO,
    SEARCH_TODO,
    UPDATE_TODO_CONTENT,
    UPDATE_TODO_STATUS,
} from './store/todo/types';
import { login, register, logout } from './store/user/saga';
import { LOGIN, REGISTER, LOGOUT } from './store/user/types';

function* rootSaga() {
    yield takeEvery(LOGIN, login);
    yield takeEvery(LOGOUT, logout);
    yield takeEvery(REGISTER, register);
    yield takeEvery(FETCH_TODO, fetchTodo);
    yield takeEvery(SEARCH_TODO, searchTodo);
    yield takeEvery(ADD_TODO, addTodo);
    yield takeEvery(DELETE_TODO, deleteTodo);
    yield takeEvery(UPDATE_TODO_STATUS, updateTodoStatus);
    yield takeEvery(UPDATE_TODO_CONTENT, updateTodoContent);
}

export default rootSaga;

```



------

##### 页面

为了方便编写代码，建立一个常量枚举用于导出操作类型

```ts
// /src/common/enum.ts
export enum ModalType {
    Edit = 'EDIT',
    Add = 'ADD',
}
```

同理，先编写页面负责的主要逻辑

列表页渲染列表的主要设计思路：

- 从redux的state中拿到信息并存入到自己的state中
- 确认用户登入状态，尚未登入则需要登入
- 渲染所有列表项
- 封装请求函数（react一般先从父组件封装好所有函数，然后传递给子组件使用）

```tsx
// /src/views/Todo/index.tsx
import React, { FC, useEffect, useState } from 'react';
import { connect, ConnectedProps } from 'react-redux';
import { RouteComponentProps } from 'react-router-dom';
import { Button, Empty, Input } from 'antd';

import { ModalType } from '../../common/enum';

import { AppStore } from '../../store';
import {
    addTodo,
    deleteTodo,
    fetchTodo,
    searchTodo,
    updateTodoContent,
    updateTodoStatus,
} from '../../store/todo/actions';
import { keepLogin, logout } from '../../store/user/actions';
import { LocalStorage } from '../../utils';

const mapState = ({ todo, user }: AppStore) => ({
    todo,
    user,
});

const mapDispatch = {
    logout,
    keepLogin,
    addTodo,
    deleteTodo,
    fetchTodo,
    searchTodo,
    updateTodoContent,
    updateTodoStatus,
};

const connector = connect(mapState, mapDispatch);
type PropsFromRedux = ConnectedProps<typeof connector>;

interface ITodoProps extends PropsFromRedux, RouteComponentProps { }
const Search = Input.Search;       // antD中输入表单的搜索样式类型

const Todo: FC<ITodoProps> = ({
    history,
    todo,
    user,
    logout,
    keepLogin,
    deleteTodo,
    updateTodoContent,
    updateTodoStatus,
    fetchTodo,
    addTodo,
    searchTodo,
}) => {
    const [showModal, setShowModal] = useState(false);  // 是否显示弹窗
    // 将redux中的state状态保存在自身的state中
    const [modalTitle, setModalTitle] = useState('');
    const [status, setStatus] = useState(false);
    const [content, setContent] = useState('');
    const [modalType, setModalType] = useState('');
    const [todoId, setTodoId] = useState('');

    // 判断登入状态
    useEffect(() => {
        const userId = LocalStorage.get('userId');
        const username = LocalStorage.get('username');
        if (userId && username) {
            if (user.userId) {
                fetchTodo(user.userId);
            } else {
                keepLogin({ userId, username, errMsg: '' });
            }
        } else {
            history.push('/');
        }
    }, [user]);

    // 更改代办事项的完成状态
    const onToggleStatus = (flag: boolean) => {
        setStatus(flag);
    };

    // 发送不同类型的请求
    const onAdd = (content: string) => {
        addTodo(user.userId, content);
        setStatus(false);
    };
    const onUpdateContent = (todoId: string, content: string) => {
        updateTodoContent(todoId, content);
    };
    const onDelete = (todoId: string) => {
        deleteTodo(todoId);
    };
    const onUpdateStatus = (todoId: string) => {
        updateTodoStatus(todoId);
    };
    const onSearch = (query: string) => {
        searchTodo(user.userId, query);
    };
    const onClose = () => {
        setShowModal(false);
    };
    const onShowModal = (type: ModalType, todoId?: string, content?: string) => {
        setShowModal(true);
        if (type === ModalType.Add) {
            setModalTitle('新增待办事项');
            setContent('');
            setModalType(ModalType.Add);
        }
        if (type === ModalType.Edit) {
            setModalTitle('编辑待办事项');
            setModalType(ModalType.Edit);
            setContent(content!);
            setTodoId(todoId!);
        }
    };

    return (
        <div>
            <div>
                <span>Hello, {user.username}</span>
                <Button type="ghost" size="small" onClick={logout}>
                    退出
                </Button>
            </div>
            <div>
                <Search
                    placeholder="输入要查询的内容"
                    onSearch={(value) => onSearch(value)}
                />
                <Button
                    type="primary"
                    onClick={() => onShowModal(ModalType.Add)}
                >
                    新增
                </Button>
            </div>
            <div>
                {/* 列表组件 */}
            </div>
        </div>
        
    )
}

export default connector(Todo) 
```

**编写列表组件**

列表组件

点击对应按钮，用该列表的id发送请求给后端做处理，并将返回的数据更新redux的state从而更新页面的state，即state存的是当前前操作的单个列表的信息

```tsx
// /src/componnets/TodoItem/index.tsx
import {
    CheckOutlined,
    DeleteOutlined,
    EditOutlined,
    UndoOutlined,
} from '@ant-design/icons';
import React, { FC } from 'react';
import styles from './index.module.scss';
import { ModalType } from '../../common/enum';

interface ITodoItem {
    id: string;
    type: string;
    content: string;
    finished: boolean;
    onShowModal: (type: ModalType, todoId: string, content: string) => void;
    onUpdateStatus: (todoId: string) => void;
    onDelete: (todoId: string) => void;
}

const TodoItem: FC<ITodoItem> = ({
    id,
    content,
    finished,
    onUpdateStatus,
    onDelete,
    onShowModal
}) => (
    <li>
        <div>
            <span>{content}</span>
            <div>
                <EditOutlined
                    onClick={() => onShowModal(ModalType.Edit, id, content)}
                />
                {/* 判断完成状态来渲染不同的互动图标 */}
                {finished 
                        ? 
                    <UndoOutlined onClick={() => onUpdateStatus(id)} />
                        : 
                    <CheckOutlined onClick={() => onUpdateStatus(id)} />
                }
                <DeleteOutlined onClick={() => onDelete(id)} />
            </div>
        </div>
    </li>
)

export default TodoItem;
```

编辑组件

设计思路是把编辑组件做成一个页面的悬浮窗，当有新增和编辑列表时则弹出，根据页面的state渲染内容：

- 新增操作时默认表单内容为空
- 编辑操作时默认表单内容为上一次保存内容

```tsx
// /src/components/FormModal/index.tsx
import { Form, Input, Modal } from 'antd';
import React, { FC, useEffect } from 'react';

import { ModalType } from '../../common/enum';

interface IModalFormProps {
    todoId: string;
    modalType: string;
    visible: boolean;
    title: string;
    content: string;
    onClose: () => void;
    onAdd: (content: string) => void;
    onUpdateContent: (todoId: string, content: string) => void;
}

const ModalForm: FC<IModalFormProps> = ({
    content,
    onClose,
    onAdd,
    onUpdateContent,
    visible,
    title,
    modalType,
    todoId,
}) => {
    const [form] = Form.useForm();
    useEffect(() => {
        form.setFieldsValue({ content })
    },[content])

    const onSubmit = () => {
        if (modalType === ModalType.Add) {
            onAdd(form.getFieldValue('content'));
        }
        if (modalType === ModalType.Edit) {
            onUpdateContent(todoId, form.getFieldValue('content'));
        }
        onClose();
    };

    return(
        <Modal
            title={title}
            visible={visible}
            onOk={onSubmit}
            onCancel={onClose}
            okText="提交"
            cancelText="取消"
            destroyOnClose={true}
            forceRender={true}
        >
            <Form layout="horizontal" form={form}>
                <Form.Item
                    label="内容"
                    name="content"
                    rules={[{ required: true, message: '请输入内容' }]}
                >
                    <Input placeholder="请输入内容" autoComplete="off" />
                </Form.Item>
            </Form>
        </Modal>
    );
}

export default ModalForm;
```

修改页面，将组件渲染

```tsx
// /src/views/Todo/index.tsx
import React, { FC, useEffect, useState } from 'react';
import { connect, ConnectedProps } from 'react-redux';
import { RouteComponentProps, withRouter } from 'react-router-dom';
import { Button, Empty, Input } from 'antd';

import { ModalType } from '../../common/enum';
import ModalForm from '../../components/FormModal';
import TodoItem from '../../components/TodoItem';
import { AppStore } from '../../store';
import {
    addTodo,
    deleteTodo,
    fetchTodo,
    searchTodo,
    updateTodoContent,
    updateTodoStatus,
} from '../../store/todo/actions';
import { keepLogin, logout } from '../../store/user/actions';
import { LocalStorage } from '../../utils';

const mapState = ({ todo, user }: AppStore) => ({
    todo,
    user,
});

const mapDispatch = {
    logout,
    keepLogin,
    addTodo,
    deleteTodo,
    fetchTodo,
    searchTodo,
    updateTodoContent,
    updateTodoStatus,
};

const connector = connect(mapState, mapDispatch);
type PropsFromRedux = ConnectedProps<typeof connector>;

interface ITodoProps extends PropsFromRedux, RouteComponentProps { }
const Search = Input.Search;       // antD中输入表单的搜索样式类型

const Todo: FC<ITodoProps> = ({
    history,
    todo,
    user,
    logout,
    keepLogin,
    deleteTodo,
    updateTodoContent,
    updateTodoStatus,
    fetchTodo,
    addTodo,
    searchTodo,
}) => {
    const [showModal, setShowModal] = useState(false);
    const [modalTitle, setModalTitle] = useState('');
    const [status, setStatus] = useState(false);
    const [content, setContent] = useState('');
    const [modalType, setModalType] = useState('');
    const [todoId, setTodoId] = useState('');

    // 判断登入状态
    useEffect(() => {
        const userId = LocalStorage.get('userId');
        const username = LocalStorage.get('username');
        if (userId && username) {
            if (user.userId) {
                fetchTodo(user.userId);
            } else {
                keepLogin({ userId, username, errMsg: '' });
            }
        } else {
            history.push('/');
        }
    }, [user]);

    // 更改代办事项的完成状态
    const onToggleStatus = (flag: boolean) => {
        setStatus(flag);
    };

    // 发送不同类型的请求
    const onAdd = (content: string) => {
        addTodo(user.userId, content);
        setStatus(false);
    };
    const onUpdateContent = (todoId: string, content: string) => {
        updateTodoContent(todoId, content);
    };
    const onDelete = (todoId: string) => {
        deleteTodo(todoId);
    };
    const onUpdateStatus = (todoId: string) => {
        updateTodoStatus(todoId);
    };
    const onSearch = (query: string) => {
        searchTodo(user.userId, query);
    };
    const onClose = () => {
        setShowModal(false);
    };
    const onShowModal = (type: ModalType, todoId?: string, content?: string) => {
        setShowModal(true);
        if (type === ModalType.Add) {
            setModalTitle('新增待办事项');
            setContent('');
            setModalType(ModalType.Add);
        }
        if (type === ModalType.Edit) {
            setModalTitle('编辑待办事项');
            setModalType(ModalType.Edit);
            setContent(content!);
            setTodoId(todoId!);
        }
    };

    return (
        <div>
            {/* 个人信息 */}
            <div>
                <span>Hello, {user.username}</span>
                <Button type="ghost" size="small" onClick={logout}>
                    退出
                </Button>
            </div>
            {/* 查询和新增事项列表 */}
            <div>
                <Search
                    placeholder="输入要查询的内容"
                    onSearch={(value) => onSearch(value)}
                />
                <Button
                    type="primary"
                    onClick={() => onShowModal(ModalType.Add)}
                >
                    新增
                </Button>
            </div>
            {/* 事项列表内容 */}
            <div>
                {/* 切换状态 */}
                <ul>
                    <li onClick={() => onToggleStatus(false)}>未完成</li>
                    <li onClick={() => onToggleStatus(true)}>已完成</li>
                </ul>
                {/* 对应状态的内容 */}
                <ul>
                    {todo.length? (
                        todo
                            .filter((v) => v.status === status)     /* 渲染状态相同的列表 */
                            .map((v) => (
                                <TodoItem 
                                    key={v._id}
                                    content={v.content}
                                    id={v._id}
                                    type={modalType}
                                    finished={status}
                                    onShowModal={onShowModal}
                                    onDelete={onDelete}
                                    onUpdateStatus={onUpdateStatus}
                                />
                            ))
                    ) : (
                        <Empty />
                    )}
                </ul>
            </div>
            {/* 弹窗 */}
            <ModalForm
                todoId={todoId}
                modalType={modalType}
                content={content}
                visible={showModal}
                title={modalTitle}
                onClose={onClose}
                onAdd={onAdd}
                onUpdateContent={onUpdateContent}
            />
        </div>
        
    )
}

export default connector(Todo) 
```



----

##### 样式

需要设置的样式内容有两个

- 页面样式
- 事项列表组件样式

**组件样式**

```scss
// /src/views/Todo/index.module.scss
.item {
    display: flex;
    min-height: 3rem;
    line-height: 3rem;
    padding: 0.5rem 1rem;
    background-color: #fff;
    border-bottom: 1px solid #ddd;
    justify-content: space-between;
    align-items: center;


    .content {
        width: 300px;
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
        font-weight: bold;
        font-size: 1.2rem;
    }

    @media screen and (max-width: 700px) {
        width: 150px;
        font-size: 1rem;
    }
}

.icon {
    display: inline-block;
    font-size: 1.5rem;
    transition: transform 0.2s ease;
    cursor: pointer;

    &:hover {
        transform: scale(1.2);
    }
    & + .icon {
        margin-left: 1rem;
    }

    @media screen and (max-width: 700px) {
        font-size: 1.2rem;
    }
}
```

把样式增加到组件元素中

```tsx
import styles from './index.module.scss';

...

<li>
    <div className={styles.item}>
        <span className={styles.content}>{content}</span>
        <div>
            <EditOutlined  className={styles.icon} onClick={() => onShowModal(ModalType.Edit, id, content)} />
            {/* 判断完成状态来渲染不同的互动图标 */}
            {finished 
                ? 
                <UndoOutlined  className={styles.icon} onClick={() => onUpdateStatus(id)} />
                : 
                <CheckOutlined  className={styles.icon} onClick={() => onUpdateStatus(id)} />
            }
            <DeleteOutlined  className={styles.icon} onClick={() => onDelete(id)} />
        </div>
    </div>
</li>
```

**页面样式**

```scss
// /src/views/Todo/index.module.scss
.wrapper {
    padding: 5rem 0;
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
}

.user {
    position: absolute;
    right: 20px;
    top: 20px;

    > span {
        margin-right: 10px;
    }
}

.newTodo {
    margin-left: 50px;

    @media screen and (max-width: 700px) {
        margin-left: 20px;
    }
}

.queryBar {
    display: flex;
    margin: 0 auto;
    margin-bottom: 30px;

    @media screen and (max-width: 700px) {
        max-width: 300px;
    }
}

.nav {
    display: flex;
    list-style: none;
    padding: 0;
    margin: 0;
    li {
        cursor: pointer;
        width: 300px;
        display: flex;
        padding: 1rem;

        &.active {
            border-bottom: 2px solid rgba(114, 111, 112, 0.5);
        }
        @media screen and (max-width: 700px) {
            width: 150px;
        }
    }
}

.list {
    width: 600px;

    @media screen and (max-width: 700px) {
        width: 300px;
    }
}

.dot {
    width: 1.5rem;
    height: 1.5rem;
    border-radius: 100%;
    margin-right: 1rem;
}

ul {
    padding: 0;
    margin: 0;
    list-style: none;
}

.noData {
    margin-top: 3rem;
}

.pending {
    background-color: #726f70;
}

.resolved {
    background-color: #f25f66;
}
```

把样式增加到组件元素中

```tsx
<div className={styles.wrapper}>
    {/* 个人信息 */}
    <div className={styles.user}>
        <span>Hello, {user.username}</span>
        <Button type="ghost" size="small" onClick={logout}>
            退出
        </Button>
    </div>
    {/* 查询和新增事项列表 */}
    <div className={styles.queryBar}>
        <Search
            placeholder="输入要查询的内容"
            onSearch={(value) => onSearch(value)}
            />
        <Button
            type="primary"
            onClick={() => onShowModal(ModalType.Add)}
            className={styles.newTodo}
            >
            新增
        </Button>
    </div>
    {/* 事项列表内容 */}
    <div className={styles.main}>
        {/* 切换状态 */}
        <ul className={styles.nav}>
            <li className={status ? '' : styles.active} onClick={() => onToggleStatus(false)}>
                <i className={`${styles.dot} ${styles.pending}`} />
                未完成
            </li>
            <li className={status ? styles.active : '' } onClick={() => onToggleStatus(true)}>
                <i className={`${styles.dot} ${styles.resolved}`} />
                已完成
            </li>
        </ul>
        {/* 对应状态的内容 */}
        <ul className={styles.list}>
            {todo.length? (
                todo
                .filter((v) => v.status === status)     /* 渲染状态相同的列表 */
                .map((v) => (
                    <TodoItem 
                        key={v._id}
                        content={v.content}
                        id={v._id}
                        type={modalType}
                        finished={status}
                        onShowModal={onShowModal}
                        onDelete={onDelete}
                        onUpdateStatus={onUpdateStatus}
                        />
                ))
            ) : (
                <Empty className={styles.noData} />
            )}
        </ul>
    </div>
    {/* 弹窗 */}
    <ModalForm
        todoId={todoId}
        modalType={modalType}
        content={content}
        visible={showModal}
        title={modalTitle}
        onClose={onClose}
        onAdd={onAdd}
        onUpdateContent={onUpdateContent}
        />
</div>
```













