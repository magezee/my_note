### 初始化

```
npm init -y
tsc --init
```

#### 依赖包

**TS**

```
npm install typescript -s
npm install ts-node -s
npm install @types/node -s
```

**Koa基础包**

```
koa本体包
	npm install koa @types/koa -s		
koa路由包	
	npm install koa-router @types/koa-router -s
koa中间件:用于获取提交数据
	npm install koa-bodyparser @types/koa-bodyparser -s
koa处理ejs文件包
	npm install koa-views @types/koa-views ejs -s
```

**MongoDB**

```
npm install mongoose @types/mongoose -s
```

---

#### 配合启动项

**热更新**

```
npm install -g nodemon
```

修改`package.json`文件

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

---

在src目录下新建`app.ts`文件，随便写点东西并启动项目`npm run watch`

```tsx
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

如果正常启动则配置成功

#### 配置git

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

### 基础配置与校验

`app.ts`是项目的入口文件，在这里配置项目使用到中间件

#### 配置中间件

修改`app.ts`

```ts
import Koa, { Context } from 'koa';
import views from 'koa-views';
import bodyParser from 'koa-bodyparser';
// 使用import方式引入路由时会和view的声明文件存在不明冲突，因此用require方式引入
let router = require('koa-router')();	

const app = new Koa();

router.get('/',async (ctx:Context) => {
    await ctx.render('index', {
        title: '测试ejs文件是否生效'
    }) 
});

router.post('/done',async (ctx:Context) => {
	ctx.body = ctx.request.body;
});

app
  // view中间件需要在所有路由前配置
  .use(views('src/views', {    
      extension: 'ejs'                  
    })
  )
  .use(bodyParser())
  .use(router.routes());

app.listen(3000);
```

在`src`目录下新建`views`文件夹并新建`index.ejs`文件写入

```ejs
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <%= title %>
    <form action="/done" method="POST">
        <input type="text" name="test">
        <button type="submit" >提交</button>
    </form>
</body>
</html>
```

如果可以正常使用则中间件配置无问题

---

#### 配置数据库

在`/src`下新建`db`目录，新建`index.ts`文件写入数据库连接通用函数

```ts
import mongoose from 'mongoose';

export default (db: string) => {
    const connect = () => {
        mongoose.connect(db, {
            useCreateIndex: true,
            useNewUrlParser: true,
            useUnifiedTopology: true,
            useFindAndModify: false,
        })
        .then(() => {
            console.log(`Successfully connected to ${db}`)
            return 
        })
        .catch((error) => {
            console.log(`Error connecting to database:${error}`)
            return process.exit(1)  // 失败断开进程
        })
    }

    connect()

    mongoose.connection.on('disconnected',connect)      // 当连接断开时，执行connect函数重连 
}
```

在`src`下新建项目公共配置文件（如连接的数据库地址，项目端口号等）`config.ts`

```ts
const Config = {
    PORT: xxx,	// 自定义端口号
    MONGODB_URL: xxx  // 自己的数据库地址，如果有其他配置则写成对象也行
}

export default Config
```

修改`app.ts`，随便写个Scheam和model测试数据库是否正常运行，如果执行相应步奏发现数据库内添加数据，则表示数据库能正常连接工作

```ts
import Koa, { Context } from 'koa';
import views from 'koa-views';
import bodyParser from 'koa-bodyparser'
let router = require('koa-router')()
import {Schema, model, Document} from 'mongoose';

import connectDB from './db';
import Config from './config';

const app = new Koa();
connectDB(Config.MONGODB_URL)

// 测试数据库
interface People extends Document {
    name: string,
    sex: string
}

const PeopleScheam: Schema = new Schema({
    name: String,
    sex: String
})

model<People>('People', PeopleScheam);


// 路由
router.get('/',async (ctx:Context) => {
    await ctx.render('index', {
        title: '测试ejs文件是否生效'
    }) 
});

router.post('/done',async (ctx:Context) => {
	const people = new People({
		name: 'jack',
		sex: 'girl'
	})
	await people.save()
	ctx.body = ctx.request.body
});

// 中间件
app
  .use(views('src/views', {    
      extension: 'ejs'                  
    })
  )
  .use(bodyParser())
  .use(router.routes())

app.listen(Config.PORT, () => {
	console.log(`Start the service at http://localhost:${Config.PORT}!`)
});
```



---

### 设计思路

将`app.ts`用于测试的内容除去，不用入口文件去负责具体的路由操作

```ts
import Koa from 'koa';
import views from 'koa-views';
import bodyParser from 'koa-bodyparser'
let router = require('koa-router')()

import connectDB from './db';
import Config from './config';


const app = new Koa();
connectDB(Config.MONGODB_URL)


app
  .use(views('src/views', {    
      extension: 'ejs'                  
    })
  )
  .use(bodyParser())
  .use(router.routes())

app.listen(Config.PORT, () => {
	console.log(`Start the service at http://localhost:${Config.PORT}!`)
});
```

---

#### 设计路由

在`src`目录下新建`routes`文件夹，用于存放所有的路由配置

此博客要实现的路由及功能：

- 首页：`/` ——用于加载所有文章列表
- 注册：`/sign` —— 用于注册用户
- 登入： `/login` ——用于控制登入逻辑
- 登出：`/logout` ——用于控制登出逻辑
- 编写：`/edit` ——用于编写增加文章
- 编辑：`/modify/:articleID` ——用于修改已经存在的文章
- 删除：`/delete:/articleID` ——用于删除已经存在的文章
- 浏览：`/articles/:articleID`——用于查看已经存在的文章
- 个人：`/user` —— 用于查看自己编写的文章

----

#### 设计数据表

根据功能，可以分成两张表——用户信息和文章信息

用户信息包括

- 用户名：authorName
- 用户密码：authorPassword
- 用户文章：article

文章信息包括

- 文章标题：articleTitle
- 文章作者：articleAuthor
- 文章内容：articleContent
- 发布时间：articleTime
- 浏览量：articleClick

用户信息要和文章信息联系起来，则可用`用户的用户文章联结全部的文章信息`

在`db`目录下新建`schemas`目录，建立`article.ts`文件写入，这里使用了`dayjs`包处理时间，需要下载`dayjs`包，结果是一个`string`类型

```js
import dayjs from 'dayjs';

import {Schema, Document} from 'mongoose';
import { IAurthor } from './author';


export interface IArticle extends Document{
    articleTitle: string;
    articleAuthor: IAurthor;
    articleContent: string;
    articleTime: Date;
    articleClick: number;
}

const ArticleSchema: Schema = new Schema({
    articleTitle: {type: String, required: true},
    articleAuthor: String,
    articleContent: String,
    articleTime: {type: String, default: dayjs().format('YYYY/M/D')},
    articleClick: {type: Number, default: 0}
});

export default ArticleSchema;
```

建立`author.ts`写入

```ts
import {Schema, Document} from 'mongoose';
import {IArticle} from './article';

export interface IAurthor extends Document{
    authorName: string;
    authorPassword: string;
    articles: IArticle[];

}

const AurthorSchema: Schema = new Schema({
    authorName: {type: String, required: true, unique: true},
    authorPassword: {type: String, required: true},
    articles: [
        {type: Schema.Types.ObjectId, ref: 'Article'}
    ]
});

export default AurthorSchema;
```

制定完成表结构Schema后，在`db`目录下建立`model`文件夹用于创建model并导出

```ts
// src/de/model/article.ts
import { model } from 'mongoose';
import ArticleSchema, { IArticle } from '../schemas/article';

export default model<IArticle>('Article', ArticleSchema);
```

```ts
// src/de/model/author.ts
import { model } from 'mongoose';
import  AurthorSchema ,{ IAurthor } from '../schemas/author';

export default model<IAurthor>('Aurthor', AurthorSchema);
```

---

#### 规范返回内容

```
可能不需要，待删除
```

可做一个简易的返回类型规范，涵盖内容应有

- 上下文
- 返回状态码
- 请求到的目标数据
- 错误码
- 返回信息

可做一个工具函数，用于统一创建返回报文，在`src`下新建`utils`目录，新建`response.ts`文件

```ts
import { Context } from 'koa';

// 枚举状态码
export enum StatusCode {
    OK = 200,			// 成功
    Created = 201,		// 创建成功
    Accepted = 202,		// 更新成功
    NoContent = 204		// 删除成功
};

// 规范返回数据
interface IRes {
    ctx: Context;
    statusCode?: number;
    data?: any;
    errorCode?: number;
    msg?: string;
}

const creatrRes = (params: IRes) => {
    params.ctx.status = params.statusCode || StatusCode.OK;
    params.ctx.body = {
       error_code: params.errorCode || 0,
       data: params.data || null,
       msg: params.msg || ''
    }
};

export default creatrRes;
```

-----

#### 公共样式

使用 github 上的 `normalize.css` 来规范样式，它规定了不同浏览器的不同的默认样式属性，让不同的浏览器在渲染网页元素的时候形式更统一

在`src`下新建一个`public`的文件夹，并在此下面新建`style`文件夹并把`normalize.css` 放入此文件夹下

为了引入静态资源，需要用到`koa-static`中间件

```
npm install -s koa-static @types/koa-static
```

修改`app.ts`的中间件设置代码

```ts
app
  .use(views('src/views', {    
      extension: 'ejs'                  
    })
  )
  .use(koaStatic('src/public'))	// 注意不要写成/src/public
  .use(bodyParser())
  .use(indexRouter.routes())
```

其他css部分

```css
/* 
    通用
*/
a{
    text-decoration: none;      
    color: #000;
}

ul,li{
    list-style: none;       
    padding: 0;    
}

.float:after{
    content: '';       
    display: block;     
    clear: both;        
}

.floatfix{
    *zoom: 1;      
}

body{
    background: #f0f0f0;
    padding: 30px;
}

footer{
    position: absolute;
    bottom: 20px;       
    display: block;
    width: 100%;
}



/* 
    登录页面
*/
.login,.login body{
    height: 100%;
    width: 100%;
}

.login body{
    background-image: url('../images/login.jpg');
    background-size: 100% 100%;
    background-repeat: no-repeat;
}

.form-group input{
    height: 24px;
}

footer p{
    text-align: center;     
    line-height: 1.5em;     
    margin: 0;     
}

.form-group,.text-group{
    padding: 0 20 10px;     
}

.form-group{
    height: 40px;
}

.form-group label{
    width: 43px;
    text-align: right;
    display: inline-block;      
}

.form-group input{
    border: 1px solid #c0c0c0;     
    border-radius: 3px;     
    padding: 5px;
    margin-left: 20px;
}

.text-group textarea{
    height: 820px;
}

.text-group label{
    vertical-align: top;        
}

.login .form-group p{
    margin: 0;
    font-size: 12px;
    color: #d20505;
    line-height: 20px;
}

.login form{
    width: 320px;
    position: absolute;     
    top: 50%;
    left: 50%;
    margin-left: -160px;
    margin-top: -125px;
}

.login form input{
    width: 300px;
    background: #fff;
    margin: 0;
}

.login form input[type='submit']{
    width: 90px;
    display: block;
    margin: 5px auto;
    height: 40px;
    background: #183d8e;
    color: #fff;
    cursor: pointer;       
} 

.login footer{
    position: absolute;
    bottom: 20px;       
    display: block;
    width: 100%;
}

/* 
    主页面
*/

header{
    height: 135px;
    background-color: #000;
    background-position: center;
    margin-bottom: 25px;
}

header h1{
    margin: 0;
    text-align: center;
    color: #fff;
    font-size: 30px;
    line-height: 35px;
    padding: 50px 0;
    word-spacing: 0.5em;
}

.main{
    padding: 0 15px;
    width: 1170px;
    margin: 0 auto;
    margin-bottom: 25px;
}

aside{
    width: 180px;
    padding: 9px;
    border: 1px solid #c0c0c0;
    float: left;
    min-height: 450px;
    height: 100%;
}

aside ul li{
    list-style: none;
    height: 30px;
    text-align: center;
}

aside ul li a{
    font-size: 16px;
    line-height: 30px;
}

aside ul li a:hover{
    color: #183d8e;
}

.main-aside-avatar{
    margin-bottom: 10px;
}

.main-aside-avatar img{
    width: 100%;
}

.main-articles{
    margin-left: 220px;
}

.main-articles-item{
    height: 80px;
    border: 1px solid #c0c0c0;
    padding: 10px;
    margin-bottom: 10px;
}

.main-articles-item:last-child{
    margin-bottom: 0;
}

.main-articles-item h2{
    margin: 0;
    font-weight: normal;
    font-size: 24px;
    line-height: 40px;
}

.main-articles-items-des span{
    padding-right: 8px;
    color: #333;
    font-size: 14px;
}

.main-articles ul{
    padding-right: 65px;
}


@keyframes move{        /* 声明一个move动画 创建动画是通过逐步改变从一个CSS样式设定到另一个 注：动画移动的效果可以让元素和其他元素重叠*/
    form{
        transform: translate(0px, 0);   /* 声明xy坐标移动量 从原来位置向右移动了 10px xy正直表示向右/下 移动 */
    }
    to{
        transform: translate(10px, 0);
    }
}

.main-articles-item:hover{
    animation: move 1s;       /* 定义绑定的动画 和播放时间 */
    animation-fill-mode: forwards;  /* animation-fill-mode 属性规定动画在播放之前或之后，其动画效果是否可见 即播完动画后除非鼠标移出否则就定格样式 */
}

span.modify{
    float: right;
}

span.modify a:hover{
    color: #183d8e;
}

span.delete{
    float: right;
    margin-right: 20px;
}

span.delete a:hover{
    color: #183d8e;
}

.page{
    text-align: center;
    margin-top: 15px;
}

.page span{
    display: inline-block;
    background: #fff;
    width: 24px;
    margin: 0 5px;
    line-height: 24px;
    height: 24px;
}

.page span a{
    display: block;
}

.page span .active a{
    background: #183d8e;
    color: #fff;
}

.page span:hover a{
    background: #183d8e;
    color: #fff;
}

/* 
    文章内容页
*/

section.articles{
    border: 1px solid #c0c0c0;
    min-height: 468px;
    padding: 0 15px;
}

.main-articles-title{
    border-bottom: 1px solid #c0c0c0;
}

.main-articles-title h2{
    text-align: center;
}

.main-articles-title p{
    font-size: 14px;
    text-align: center;
}

.main-articles-title p span{
    padding: 0 10px;
}

.main-articles-content p{
    text-indent: 2em;       /* 文本首行缩进 */
    font-size: 16px;
    line-height: 1.8em;
}


/* 
    写文章页
*/

.edit label, .editp label{
    width: 10%;
    text-align: right;
    display: inline-block;
}

.edit input, .edit textarea{
    border: 1px solid #c0c0c0;
    border-radius: 3px;
    width: 85%;
    padding: 6px 5px;
    margin-left: 20px;
}

.edit p{
    text-align: center;
}

.edit input[type="submit"]{
    width: 90px;
    height: 40px;
    background: #183dbe;
    color: #fff;
    cursor: pointer;
}

.form-group input{
    height: 24px;
}

.text-group textarea{
    height: 350px;
}

.text-group label{
    vertical-align: top;
}
```



---

### 首页

#### 页面

设计页面布局

<img src ="https://img-blog.csdnimg.cn/20200924171034854.png" style="margin:0;width:700px"/>

设置具体博客列表具有元素

<img src ="https://img-blog.csdnimg.cn/20200924172658595.png" style="margin:0;width:700px"/>

`src/views/ejs`代码如下

```ejs
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>咯咯哒</title>
        <meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1">
        <meta name="description" content="咕咕咕">
        <meta name="keywords" content="咯咯哒">
        <link rel="stylesheet" href="/style/main.css">
        <link rel="stylesheet" href="/style/normalize.css">
    </head>
    <body>
        <header>
            <h1>头部标题</h1>
        </header>
        <section class="main floatfix">
            <aside>
                <section class="main-aside-avatar">
                </section>
                <ul>
                    <li><a href="/">博客首页</a></li>
                    <li><a href="/edit">撰写博客</a></li>
                    <li><a href="/sign">注册</a></li>
                </ul>
            </aside>

            <section class="main-articles">
                <ul>
                    <% for(let i=0; i<articles.length; i++){ %>
                        <li class="main-articles-item">
                            <h2><a href="/articles/<%= articles[i]._id %>"><%= articles[i].articleTitle %></a></h2>
                            <section class="main-articles-item-des">
                                <p>
                                    <span>作者：<%= articles[i].articlesAuthor %></span>
                                    <span>发布时间：<%= articles[i].articleTime %></span>
                                    <span>浏览量：<%= articles[i].articleClick %></span>
                                    <span class="modeify"><a href="/modefy/<%= articles[i]._id  %>">编辑</a></span>
                                    <span class="delete"><a href="/delete/<%= articles[i]._id  %>">删除</a></span>
                                </p>
                            </section>
                        </li>
                    <% } %>
                </ul>
                
                <!-- 如果总文章数大于每页最大显示数则显示分页栏 -->
                <% if(pageNum > 1){ %>
                    <section class="page">
                    <% for(var i = 1; i <= pageNum; i++){ %>
                        <span <% if(i==page){ %> class="active" <% } %> >
                        <a href="/?page=<%= i %>"> <%= i %> </a>
                        </span>
                    <% } %>
                    </section>
                <% } %> 

            </section>
        </section>
    </body>
</html>
```

由设计布局可知，头部标题，导航，还有HTML中的`<head>`部分，都是可以提取出来当公共部分重复利用且后期修改较大的代码，因此这里将这三部分的代码提取出来

新建目录`/src/views/public`，在`public`目录下存放公共部分的页面元素

```ejs
<!-- /src/views/public/header.ejs -->
<header>
    <h1>头部标题</h1>
</header>
```

```ejs
<!-- /src/views/public/aside.ejs -->
<aside>
    <section class="main-aside-avatar">
    </section>
    <ul>
        <li><a href="/">博客首页</a></li>
        <li><a href="/edit">撰写博客</a></li>
        <li><a href="/sign">注册</a></li>
    </ul>
</aside>
```

```ejs
<!-- /src/views/public/head.ejs -->
<head>
    <meta charset="UTF-8">
    <title>咯咯哒</title>
    <meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1">
    <meta name="description" content="咕咕咕">
    <meta name="keywords" content="咯咯哒">
    <link rel="stylesheet" href="style/main.css">
    <link rel="stylesheet" href="style/normalize.css">
</head>
```

然后就可以重复使用这些公共部分的代码，如改写首页`/src/views/index.ejs`代码，使用`<%- include('xxx') %>`直接导入即可

```ejs
<!DOCTYPE html>
<html lang="en">
    <%- include('./public/head.ejs') %>
    <body>
        <%- include('./public/header.ejs') %>
        <section class="main floatfix">
            <%- include('./public/aside.ejs') %>
            <section class="main-articles">
                <ul>
                    <% for(let i=0; i<articles.length; i++){ %>
                        <li class="main-articles-item">
                            <h2><a href="/articles/<%= articles[i]._id %>"><%= articles[i].articleTitle %></a></h2>
                            <section class="main-articles-item-des">
                                <p>
                                    <span>作者：<%= articles[i].articlesAuthor %></span>
                                    <span>发布时间：<%= articles[i].articleTime %></span>
                                    <span>浏览量：<%= articles[i].articleClick %></span>
                                    <span class="modeify"><a href="/modefy/<%= articles[i]._id  %>">编辑</a></span>
                                    <span class="delete"><a href="/delete/<%= articles[i]._id  %>">删除</a></span>
                                </p>
                            </section>
                        </li>
                    <% } %>
                </ul>
                
                <!-- 如果总文章数大于每页最大显示数则显示分页栏 -->
                <% if(pageNum > 1){ %>
                    <section class="page">
                    <% for(var i = 1; i <= pageNum; i++){ %>
                        <span <% if(i==page){ %> class="active" <% } %> >
                        <a href="/?page=<%= i %>"> <%= i %> </a>
                        </span>
                    <% } %>
                    </section>
                <% } %> 

            </section>
        </section>
    </body>
</html>
```



----

#### 路由

首页路由的主要功能：

- 从数据库里拿到数据
- 将数据传递给ejs文件并渲染出来

```ts
import { Context } from 'koa';
let indexRouter =  require('koa-router')();

import Article from '../db/model/article';

async function getArticles(ctx: Context) {
    let page = ctx.query.page || 1;     // 根据url上的传参来判断显示第几页数据
    let articlesMaxNum = 8;    // 设置每页显示最大数据条数
    let start = (page -1) * articlesMaxNum;
    try {
        const articles = await Article.find().skip(start).limit(articlesMaxNum).sort({articleTime: 1});    // 根据页数拿到指定范围的文章,按照发布时间正序排列
        let articlesCount = await Article.find().count();                                                  // 拿到Artic表内总数据条数
        let pageNum = Math.ceil(articlesCount / articlesMaxNum);                                           // 根据文章总数除于每页最大数得到当前总共文章页数
        console.log(articlesCount)
        return {articles: articles, articlesCount: articlesCount, page: page, pageNum: pageNum};
    } catch(error) {
        throw new Error('搜索文章失败');
    }
}

indexRouter.get('/', async (ctx: Context) => {
    try {
        const {articles, articlesCount, page, pageNum} =  await getArticles(ctx);
        if(articles) {
            await ctx.render('index',{
                articles: articles,
                articlesCount: articlesCount,
                page: page,
                pageNum: pageNum
            })
        } 
    } catch(error) {
        throw new Error('首页路由加载失败');
    }
})

export default indexRouter;
```



---

### 注册

#### 页面

采用大体布局的方法，即更换路由只变换`<section class="main-articles">`内的内容，从而可以写出`/src/views/sign.ejs`的页面元素

```ejs
<!DOCTYPE html>
<html lang="zh--cn" class="login">
    <%- include("./public/head.ejs") %>
    <body>
        <%- include('./public/header.ejs') %>
        <section class="main floatfix">
            <%- include('./public/aside.ejs') %>
            <section class="main-articles">
                <form action="/sign" method="POST">
                    <div class="form-group">
                        <input type="text" name="name" placeholder="用户名">
                    </div>

                    <div class="form-group">
                        <input type="password" name="password" placeholder="密码">
                    </div>

                    <div class="form-group">
                        <input type="password" name="confirm" placeholder="确认密码">
                        <!-- 根据message提示用户注册的状态，如果不传入则隐藏 -->
                        <% if(message){ %>
                            <p><%= message %></p>
                        <% } %> 
                    </div>

                    <div>
                        <input type="submit" value="注册">
                    </div>
                </form>
            </section>
        </section>
    </body>
</html>
```

-----

#### 路由

注册路由主要做的事情：

- 渲染注册表单
- 根据表单提交的注册信息，完成后续注册步奏
- 根据注册情况返回不同的信息

```ts
// src/routes/sign.ts
import { Context } from 'koa';
let signRouter =  require('koa-router')();

import Author from '../db/model/author';

signRouter
    .get('/sign', async (ctx: Context) => {
        await ctx.render('sign',{
                message: ''
        });

    })
    .post('/sign', async (ctx: Context) => {
        const {name, password, confirm} = ctx.request.body;
        let message = '';
        if(password === confirm && password.length >= 6) {
            let author = new Author({
                authorName: name,
                authorPassword: password
            })
            try {
                let user= await Author.find({authorName:name});
                // 取回的值是一个数组类型，空数组会通过判断，为了让空数组不通过取长度判断
                if(user.length) {
                    message = '该用户名已经存在！';
                } else {
                    await author.save();
                    message = '注册成功！';
                }   
            } catch(error) {
                console.log(`sign is error: ${error}`);
            }
        } 
        if(password === confirm && password.length < 6) {
            message = '密码必须大于6位！';
        }
        if(password !== confirm) {
            message = '两次密码输入不一致！';
        }
        await ctx.render('sign',{
            message: message
        });
    })
 
    
export default signRouter;

```



----

### 登入与注销

为了保存用户的登入状态，这里使用`session`来处理，相应地要下载koa的包

```
npm install koa-session @types/koa-session -s
```

然后再`app.ts`里配置一下中间件

```ts
import koaSession from 'koa-session';

const sessionConfig = {
    key: 'koa:sess',    
    maxAge: 600000,    
    overwrite:true,    
    httpOnly:true,      
    signed:true,       
    rolling:false,    
    renew:true        
}
app.keys = ['some secret hurr'];	// 如果设置了signed:true,则需要配置key属性
...
app
    .use(views('src/views', {    
        extension: 'ejs'                  
        })
    )
    .use(koaSession(sessionConfig, app))	// 配置session处理中间件
    .use(koaStatic('src/public'))
    .use(bodyParser())
    .use(indexRouter.routes()) 
    .use(signRouter.routes())
```

#### 页面

由于登入也是一个表单进行登入，因此页面差不多和注册页一样，拿来稍微改改就行

```ejs
<!-- /src/views/login.ejs -->
<!DOCTYPE html>
<html lang="zh--cn" class="login">
    <%- include("./public/head.ejs") %>
    <body>
        <%- include('./public/header.ejs') %>
        <section class="main floatfix">
            <%- include('./public/aside.ejs') %>
            <section class="main-articles">
                <form action="/login" method="POST">
                    <div class="form-group">
                        <input type="text" name="name" placeholder="用户名">
                    </div>
                    <div class="form-group">
                        <input type="password" name="password" placeholder="密码">
                        <% if(message){ %>
                            <p><%= message %></p>
                        <% } %> 
                    </div>
                    <div>
                        <input type="submit" value="登入">
                    </div>
                </form>
            </section>
        </section>
    </body>
</html>
```

#### 登入路由

登入路由做的主要事情：

- 渲染注册表单
- 对比登入信息，如果登入成功则将用户信息存入session
- 根据登入情况返回不同的信息

```ts
// src/routes/login.ts
import { Context}  from 'koa';
let loginRouter =  require('koa-router')();
import {Session}  from 'koa-session'

import Author from '../db/model/author';


loginRouter
    .get('/login', async (ctx: Context) => {
        await ctx.render('login',{
                message: '',
                user: ctx.session?.username
        }) 
    })
    .post('/login', async (ctx:Context) => {
        const {name, password} = ctx.request.body;
        let message = '';
        let userSession = ctx.session as Session;    // 这里由于koa定义session的类型为Session | null 因此要使用类型断言才能赋值
        userSession.username = '';
        try {
            let user = await Author.findOne({authorName:name});
            if(user?.authorPassword === password) {
                userSession.username = name;
                ctx.redirect('/');
            }
            else if(!user) {    
                message = '用户名不存在！'
            } 
            else {  
                message = '密码输入错误！'
            }
            await await ctx.render('login',{
                message: message,
                user: ctx.session?.username
            });
        } catch(error) {
            throw new Error(`Login error：${error} `)
        }
    })

export default loginRouter;
```

----

#### 注销路由

登入路由做的事情很简单，就是将session清空即可

```ts
// src/routes/logout.ts
import { Context}  from 'koa';
let logoutRouter =  require('koa-router')();
import {Session}  from 'koa-session'

logoutRouter.get('/logout', async (ctx: Context) => { 
    let userSession = ctx.session as Session;    
    userSession.username = '';
    ctx.redirect('/');
})

export default logoutRouter;
```



---

#### 优化页面

优化页面的元素在于，在登入前后`aside.ejs`部分渲染不同的内容，如登入后就没必要显示注册和登入按钮，而应该显示注销按钮

先修改`aside.ejs`

```ejs
<!-- /src/views/public/aside.ejs -->
<aside>
    <section class="main-aside-avatar">
    </section>
    <ul>
        <li><a href="/">博客首页</a></li>
        <li><a href="/edit">撰写博客</a></li>
        <% if( user ){%>
        	<li><a href="/user">个人博客</a></li>
            <li><a href="/logout">注销</a></li>
        <% } else { %>
            <li><a href="/login">登入</a></li>
            <li><a href="/sign">注册</a></li>
        <% } %>
    </ul>
</aside>
```

因为此时需要用到`user`，因此应该在之前的有渲染该页面的路由中增加传参`user`

```ts
wait ctx.render('xxx',{
    ...
	user: ctx.session?.username 
})
```



---

### 文章

#### 编写文章

```ejs
<!-- /src/views/edit.ejs -->
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <%- include("./public/head.ejs") %>
  </head>
  <body>
    <%- include("./public/header.ejs") %>
    <section class="main floatfix">
      <%- include("./public/aside.ejs") %>
      <section class="main-articles">
        <form action="/edit" method="POST" class="edit">
          <div class="form-group">
            <label for="">标题</label>
            <input type="text" name="title">
          </div>
          <div class="text-group">
            <label for="">内容</label>
            <textarea name="content" ></textarea>
          </div>
          <p><input type="submit" value="保存"></p>
        </form>  
      </section>    
    </section>
  </body>
</html>
```

编写文章路由做的主要事情：

- 验证登入信息，如果未登入则不能编写
- 获取表单信息写进数据库的文章集合

```ts
import { Context } from 'koa';
let editRouter =  require('koa-router')();

import Article from '../db/model/article';
import Author from '../db/model/author';


editRouter
    .get('/edit', async (ctx: Context) => {
        // 如果沒有登入，则跳转到登入页
        if(!ctx.session?.username) {
            ctx.redirect('/login');
            return;
        }
        await ctx.render('edit', {
            user: ctx.session?.username
        })
    })

    .post('/edit', async (ctx: Context) => {
        const {title, content} = ctx.request.body;
        const author = ctx.session?.username;
        let article = new Article({
            articleTitle: title,
            articleAuthor: author,
            articleContent: content,
        })
        try {
            let data =  await article.save();
            let author = await Author.findOne({authorName: ctx.session?.username});
            author?.articles.push(data._id);    // 用文章数据的唯一标志_id和文章作者联结起来
            await author?.save();               // 更新信息
            ctx.redirect('/');
        } catch(error) {
            throw new Error(`add article error:${error}`);
        }
    })

export default editRouter;
```

---

#### 查看文章

```ejs
<!-- /src/views/article.ejs -->
<!DOCTYPE html>
<html lang="zh-cn">
  <%- include("./public/head.ejs") %>
<body>
  <%- include("./public/header.ejs") %>
  <section class="main floatfix">
    <%- include("./public/aside.ejs") %>
    <section class="main-articles articles">
      <section class="main-articles-title">
        <h2><%= article.articleTitle %></h2>
        <p>
          <span>作者：<%= article.articleAuthor %></span>
          <span>发布时间：<%= article.articleTime %></span>
          <span>浏览量：<%= article.articleClick%></span>
        </p>        
      </section>
      <section class="main-articles-content">
        <p><%= article.articleContent %></p>
      </section>
    </section>
  </section>
</body>
</html>
```

查看文章路由做的主要事情：

- 通过url上的参数获取对应的文章号
- 更新阅读量

```ts
// /src/routes/articles.ts
import { Context } from 'koa';
let articlesRouter =  require('koa-router')();

import Article from '../db/model/article';


articlesRouter
    .get('/articles/:articleID', async (ctx: Context) => {
        let articleID =  ctx.params.articleID;
        try {
            let article = await Article.findById(articleID);    // 通过链接上的id拿到对应的文章
            // 更新阅读量,因为ts的类型判断需要额外操作一下
            let _articleClick = article?.articleClick;
            if(_articleClick || _articleClick === 0) {
                await Article.update({_id: articleID},{$set:{articleClick: _articleClick + 1}})  
            }
            await ctx.render('article', {
                article: article,
                user: ctx.session?.username
            })
            
        } catch(error) {
            throw new Error(`An error occurred while viewing the article:${error}`);
        }
    }) 

export default articlesRouter;
```

----

#### 编辑文章

```ejs
<!-- /src/views/modify.ejs -->
<!DOCTYPE html>
<html lang="zh-cn">
  <head>
    <%- include("./public/head.ejs") %>
  </head>
  <body>
    <%- include("./public/header.ejs") %>
    <section class="main floatfix">
      <%- include("./public/aside.ejs") %>
      <section class="main-articles">
        <form action="" method="POST" class="edit">
          <div class="form-group">
            <label for="">标题</label>
            <input type="text" name="title" value="<%= article.articleTitle %>"> 
          </div>
          <div class="text-group">
            <label for="">内容</label>
            <textarea name="content" ><%= article.articleContent %></textarea>
          </div>
          <p><input type="submit" value="保存"></p>
        </form>  
      </section>    
    </section>
  </body>
</html>
```

编辑文章路由做的主要事情：

- 通过url上的参数获取对应的文章号
- 获取表单提供的数据更新相应数据

```ts
// /src/routes/modify.ts
import { Context } from 'koa';
let modifyRouter =  require('koa-router')();

import Article from '../db/model/article';

modifyRouter.
    get('/modify/:articleID', async (ctx: Context) => {
        let articleID =  ctx.params.articleID;
        try {
            let article = await Article.findById(articleID);
            await ctx.render('modify', {
                article: article,
                user: ctx.session?.username
            })
        } catch(error) {
            throw new Error(`An error occurred while modifying the article:${error}`);
        }
    })

    .post('/modify/:articleID', async (ctx: Context) => {
        let articleID =  ctx.params.articleID;
        const {title, content} = ctx.request.body;
        try {
            await Article.updateOne({_id: articleID}, {$set:{articleTitle: title, articleContent: content}});
            ctx.redirect('/');
            
        } catch(error) {
            throw new Error(`An error occurred while modifying the article:${error}`);
        }
    })

export default modifyRouter;
```

----

#### 删除文章

```ts
// /src/routes/delete.ts
import { Context } from 'koa';
let deleteRouter =  require('koa-router')();

import Article from '../db/model/article';



deleteRouter.get('/delete/:articleID', async (ctx: Context) => {
    let articleID =  ctx.params.articleID;
    try {
        await Article.deleteOne({_id: articleID});
        ctx.redirect('/');
    } catch(error) {
        throw new Error(`An error occurred while deleting the article:${error}`);
    }
})

export default deleteRouter;
```

---

修改首页部分代码，即只能操作作者自己的文章

```ejs
<!-- /src/views/index.ejs -->
<p>
    <span>作者：<%= articles[i].articleAuthor %></span>
	<span>发布时间：<%= articles[i].articleTime %></span>
	<span>浏览量：<%= articles[i].articleClick %></span> 
	<% if(articles[i].articleAuthor === user) {%>
    	<span class="modify"><a href="/modify/<%= articles[i]._id %>">编辑</a></span>
        <span class="delete"><a href="/delete/<%= articles[i]._id %>">删除</a></span>
    <% } %>    
</p>
```



---

### 个人博客

用于只展示个人博客的页面，页面与`index.ejs`完全一样，只是展示内容不同



```ts
import { Context } from 'koa';
let userRouter =  require('koa-router')();
import Article from '../db/model/article';
import Author from '../db/model/author';


async function getArticles(ctx: Context) {
    let page = ctx.query.page || 1;     // 根据url上的传参来判断显示第几页数据
    let articlesMaxNum = 8;    // 设置每页显示最大数据条数
    let start = (page -1) * articlesMaxNum;
    try {
        // 通过联结获取当前用户的个人博客
        let author = await Author.findOne({authorName: ctx.session?.username}).populate({
            path: 'articles',
            options: {
                skip: start,
                limit: articlesMaxNum,
                sort: {articleTime: 1}
            }}); 
        let articles = author?.articles
        let articlesCount = await Article.find().count();                                                  // 拿到Artic表内总数据条数
        let pageNum = Math.ceil(articlesCount / articlesMaxNum);                                           // 根据文章总数除于每页最大数得到当前总共文章页数
        return {articles: articles, articlesCount: articlesCount, page: page, pageNum: pageNum};
    } catch(error) {
        throw new Error(`搜索文章失败:${error}`);
    }
}

userRouter.get('/user', async (ctx: Context) => {
    try {
        const {articles, articlesCount, page, pageNum} =  await getArticles(ctx);
        if(articles) {
            await ctx.render('index',{
                articles: articles,
                articlesCount: articlesCount,
                page: page,
                pageNum: pageNum,
                user: ctx.session?.username 
            })
        } 
    } catch(error) {
        throw new Error(`首页路由加载失败:${error}`);
    }
})

export default userRouter;
```



---

### 后续优化

可以选择优化的点：

- 密码是明文加进数据库的，可以进行加密（如md5）

- 可以设置404路由及页面
- 加入博客头像功能
- 加入搜索博客功能