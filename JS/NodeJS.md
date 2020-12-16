## NodeJS通用

### node地位

node可以用作后端服务器来使用，但是在真实的大型项目中，node无法撼动java的地位，而且java已经有spring这种成熟的框架，因此，一般会把node当做中端来处理，即配合redis，在高并发下对高频操作的数据进行处理，处理完后再交由后端java对数据进行验证以及存入数据库等操作。



-----

### npm和yarn

**npm**

一款JavaScript 包管理工具

```
创建项目依赖package.json	存在package.json时执该命令可更新项目依赖
	npm init --yse（不用yes时会需要手动配置）
下载包
    npm nuinstall +Module_name 
    本地安装(将安装包放在 ./node_modules 下)
    
    -S  在下载包时自动写入dependencies依赖
    npm install module_name -S 等同于 npm install module_name --save

    -D	在下载包时自动写入devDependencies依赖
    npm install module_name -D 等同于 npm install module_name --save-dev

    -g	全局安装（方便cmd命令行使用）
    npm install module_name -g
    
指定版本安装
	npm install  +Module_name+@版本号
卸载包
	npm nuinstall +Module_name 
查看npm包
	npm list					--查看当前目录下已安装的node包
	npm info +Module_name		--查看npm包版本

```

**yarn**

一款新的 JavaScript 包管理工具，它的目的是解决这些团队使用 npm 面临的少数问题

- 安装的时候无法保证速度/一致性
- 安全问题，因为 npm 安装时允许运行代码

```
初始化
	yarn init	
添加依赖（下载并且自动添加到依赖）
	yarn add jquery
添加指定版本的包
	yarn add jquery@2.1.4
将包更新到指定版本
	yarn upgrade jquery@3.0.0
将包更新到最新版本
	yarn upgrade --latest jquery
删除包
	yarn remove jquery
```

-----

### 运行

**热更新**

```
npm install -g  nodemon
nodemon +路径		// 注意nodemon应在项目根目录下启动（nodemon ./src/index.js）	
```

**服务端运行**

```
npm install -g serve

serve -s +指定目录/文件	// 启动服务器，将指定文件或目录下所有资源作为姿态资源暴漏出去
```



----

### 导出和引入

```
一般要引入的文件存放在项目根路径下的node_modules文件夹
如果导入的文件在根目录下找不到的话，则会自动在node_modules文件夹下找，即存放在node_modules的导出文件不需要写路径

..../tool.js
引入:require('..../tool.js')

/node_modules/tool.js （这里假设直接放在node_modules目录下，正常情况应该是/node_modules/tool/tool.js）
引入:require(tool.js)
```

```
目录寻找的机制：
指定文件夹名字和指定js文件，但是如果目录下js文件文件名为index时，则不需指明文件，指明目录名即可（项目中任意文件夹都适用，不只是node_modules目录）

node_modules/tool/tool.js
引入：require('tool/tool.js')

node_modules/tool/index.js
引入：require('tool')
```

**Node标准**

- 导出

```
module.exports
	直接对外暴露对象（推荐）
	
exports.xxx
	声明了一个变量指向暴露对象，再将其暴露（这个变量名多用于描述 -- 方便人理解）
```

```javascript
// A.js
var tool ={
    add:function(x,y){
        return x+y;
    },
    
}

exports.add_tool = tool;
module.exports = tool; 
```

- 引入

```
require
	需要指明目录和文件名，可省略后缀
```

```javascript
// B.js
var tool = require('./A.js')

tool.add_tool.add(1,2)		// 使用exports的调用
tool.add(1,2)				// 使用module.exports的调用
```

**ES6标准**

- 导出

```
export 
	按需导出，可重复使用，导出多个对象
	能直接导出变量表达式，而export default不行
	
export default
	导出默认对象，只能使用一次
	
（不推荐同时使用export default与export）
```

```javascript
// A.js
const Say = 'HelloWorld';
const Person = {
	name:Lbc,
	age:27
};


export Say;
export Person;
export var title = '标题'；
```

```javascript
// a.js
const Person = {
	name:Lbc,
	age:27
};

export default Person
```

- 引入

```
import {}
	通过export方式导出，在引入时要使用 import { }，可以只引入一部分 
	使用export导出的成员，必须严格按照导出时候的名称，来使用{ }按需接收，换个变量名称接收，可以使用as来起别名
	export .. as .. 
	
import
```

```javascript
import { Say, Person, title } from './A.js'
console.log(Person.name + Person.age)
console.log(title + Say)
```

```javascript
import Person from './a.js'
console.log(Person.name +  Person.age)
console.log(title + Say)
```



---

### 文件路径处理 

需要引入`path`模块

- **path.join ( )**

  将所有参数连接起来，返回一个路径

```javascript
let outputPath = path.join(_dirname,'node','node.js')	// _dirname指当前文件所在目录的绝对路径
console.log(outputPath)

// C:\frontEnd\node\node,js	（假设该文件在 C:\frontEnd下）
```

- **path.extname ( )**

  返回路径参数的扩展名，无扩展名时返回空字符串

```javascript
let outputPath = path.join(_dirname,'node','node.js')
let ext = path.extname(outputPath)
console.log(ext)

// .js
```

- **path.parse ( )**

  将路径解析为一个路径对象

```
NodeJS中，一个文件对象有root,dir,base,ext,name五个字段
分别对应根目录（一般对应磁盘名）、完整目录、路径最后一部分（可能是文件名或目录名）、扩展名、文件名（不带扩展名）
```

```javascript
const str = 'C:/frontEnd/node/node.js'
let obj = path.parse(str)
console.log(obj)

/*
{
	root:'C:/'
	dir:'C:/frontEnd/'
	base:'node.js'
	ext:'js'
	name:'node'
}
*/
```

- **path.format ( )**

  接收一个路径对象为参数，返回一个完整的路径地址（是上面的反操作）

- **path.resolve ( )**

  路径拼接（可解析相对路径）

```javascript
// 假设当前执行的js文件路径为C:\Users\Marisue\Desktop\test.js
var result_1 = path.resolve(__dirname,"dist")		// __dirname 存储被执行的js文件所在路径（不包括文件名）
var result_2 = path.resolve(__dirname,"../dist")
console.log(result_1) 	// C:\Users\Marisue\Desktop\dist
console.log(result_1)	// C:\Users\Marisue\dist
```

- **path.cwd ( )**

  返回项目根路径

```javascript
// C:\Users\Marisue\Desktop\webpackDemo\src\jc\test.js
console.log(path.cwd())  // C:\Users\Marisue\Desktop\webpackDemo
```



---

### 构建服务器 

需要引入`http`、`url`模块

**获取url**

- nodejs里获取完整的url

  req.headers.referer

```javascript
http.createServer(function(req,res){   
	console.log(req.headers.referer)
}).listen(9997)
```

- req.url

  获取到的是端口号之后的所有信息

```javascript
// url = 'http://localhost:9997/news?aid=123&cid=3

console.log(req.url)
// /news?aid=123&cid=3
// /favicon.ico		
// favicon.ico是浏览器自带的一个加载网页小图标返回信息，一般无用，可过滤
```

- url.parse (  )

  获取特定url参数值

  参数1为url，参数2为bool，为true时会将get传值的数据转换为对象

```javascript
var testUrl =  'http://localhost:8888/select?aa=001&bb=002';
var p = URL.parse(testUrl); 
console.log(p.href); 				// http://localhost:8888/select?aa=001&bb=002
console.log(p.protocol); 			// http: 
console.log( p.hostname);			// locahost
console.log(p.host);				// localhost:8888
console.log(p.port);				// 8888
console.log(p.path);				// select?aa=001&bb=002
console.log(p.pathname);			// select
console.log(p.hash);				// null 
console.log(p.query);				// aa=001&bb=002

var p = URL.parse(testUrl, true);
console.log(p.query);				// { aa: '001', bb: '002' }
console.log(p.query.aa)				// 001
```

<img src="https://img-blog.csdnimg.cn/20200511144012450.png" style="float:left"/>

```javascript
var http = require('http');    
var url = require('url');  

 //声明变量req用于获取请求信息，res用于获取响应信息
http.createServer(function(req,res){       

var query = url.parse(req.url,true);       
      
if(req.url != "/favicon.ico"){      			
	var result = url.parse(req.url,true);       
	console.log(result.query.cid);      	
}
    

//发送Http头部，状态码是200，文件类型是html，字符集是utf-8
res.writeHead(200,{"Content-Type":"text/html;charset = 'utf-8'"});		
res.write("hello world");
res.end();      //结束响应
    
}).listen(9997); //创建端口 即现在的访问url为 localhost:9997	如果是listen(8002,'127.0.0.1')这种写法则访问的url为127.0.0.1:8002


//改代码自动重启web服务 在cmd界面（要对应目录下）输入 supervisor + 文件名，可以在不运行代码的情况下直接修改代码并保存，刷新浏览器页面就能看到效果
```



---

### 操作文件和目录

需要引入`fs`模块

- fs.stat( )

  检测是文件还是文件夹（目录）

```javascript
fs.stat('html',function(err,stats){    
    if(err){
        console.log(err);
        return false;
    }

    console.log('文件:'+stats.isFile());
    console.log('文件夹'+stats.isDirectory());
})
```

- fs.mkdir( )

  创建目录

```javascript
fs.mkdir('css',function(err){       
    if(err){
        console.log(err);
        return false;
    }

    console.log("创建目录成功");
    
})
```

- fs.writeFile( ) 和  fs.writeFileSync( )

  创建并写入文件（覆盖方式）

```javascript
// fs.writeFile( )  异步写入
fs.writeFile('t.txt','写入文件的内容',function(err){        
    if(err){
        console.log(err);
        return false;
    }
    console.log('写入成功');
})

// fs.writeFileSync( )  同步写入
fs.writeFileSync('t.txt','写入文件的内容')
```

- fs.appendFile( ) 和 fs.appendFileSync( )

  创建并写入文件（追加方式）

```javascript
fs.appendFile('t.txt','追加写入的内容\n',function(err){    
    if(err){
        console.log(err);
        return false;
    }
    console.log('写入成功');
})
```

- fs.readFile( )  和  fs.readFileSync( )

  读取文件

```javascript
// fs.readFile( ) 异步读取
fs.readFile('./index.txt',function(err,data){      
    if(err){
        console.log(err);
        return false;
    }
    console.log(data);      			//此时打印的是16进制的字符信息
    console.log(data.toString());       //转换为文本信息
})

// fs.readFileSync( ) 同步读取
var data = fs.readFileSync('./index.txt');
```

- fs.readdir( ) 和  fs.readFileSync( )

  读取目录，只读取指定文件夹下所有子文件/目录，不会再往下读取子目录的内容

```javascript
fs.readdir('./html',function(err,data){  
    if(err){
        console.log(err);
        return false;
    }
    console.log(data);
}) 
```

- fs.rename( )

  重命名

```javascript
fs.rename('./html','./new_html',function(err){ 
    if(err){
        console.log(err);
        return false;
    }
    console.log('改名成功');
})
```

- fs.rmdir( )

  删除指定目录 只能删除目录

```javascript
 fs.rmdir('t',function(err){       
    if(err){
        console.log(err);
        return false;
    }
    console.log('删除目录成功');
})
```

- fs.unlink( )

  删除指定目录 只能删除目录

```javascript
fs.unlink('index.txt',function(err){     
    if(err){
        console.log(err);
        return false;
    }
    console.log('删除文件成功');
})
```

**管道流（处理大量数据时使用，不会卡死）**

- 读取流

```javascript
var fs_read = require('fs');

//指定读取的文件
var readStream = fs_read.createReadStream('./index.txt');        

var str = '';       //用于保存数据
readStream.on('data',function(chunk){       // 读取时执行，'data'是固定名称
    str += chunk;
})

readStream.on('end',function(chunk){        // 读取完成时执行，'end'为固定名称
    console.log(str);
})

readStream.on('error',function(err){        // 读取失败时执行  error为固定名称
    console.log(err);
})
```

- 写入流

  写入的数据必须要为字符串格式

  `writerStream.write()`  操作代码得放在 `writerStream.end()` 前
  
  `createWriteStream()`传入两个参数，第一个为路径，第二个为一个对象，包含了流的各选项控制，写入流默认以覆盖的形式写内容，如果要以追加的方式来写，需要第二个参数声明为 `{ 'flags': 'a' }`

```javascript
var writerStream = fs_write.createWriteStream('t.txt', { 'flags': 'a' });
```

```javascript
var fs_write = require('fs');       

//指定写入文件
var writerStream = fs_write.createWriteStream('t.txt');  
var data = '我要保存起来的数据\n';

for(var i = 0; i<100; i++){
    writerStream.write(data,'utf8');
}

writerStream.end();     //标记写入完成    只能有一个end结束标志 若把此代码放入上面的循环里则报错

writerStream.on('finish',function(){	// 写入完成时执行，固定finish名称
    console.log('写入完成');
})

writerStream.on('error',function(){		// 写入错误时执行，固定error名称
    console.log('写入失败');
})
```

- 管道

  即读取一个文件的内容并把该内容写入到另外一个文件

```javascript
var fs = require('fs');

var readStream = fs.createReadStream('input.txt');
var writeStream = fs.createWriteStream('output.txt');
readStream.pipe(writeStream);
console.log('程序执行完毕');
```

**demo**

```javascript
 var fs = require('fs');


// 1 判断服务器上面有没有upload目录，没有则创建此目录
fs.stat("upload",function(err,stats){
    if(err){        //没有这个目录
        fs.mkdir('upload',function(error){
            if(error){
                console.log(error);
                return false;
            }
            
            console.log('创建成功');
        })       
    }
    else{
        console.log('目录已经存在');
        console.log(stats.isDirectory());
    }
    
}) 



// 2 找出html目录下面的所有的目录 然后打印出来
fs.readdir('./html',function(err,files){      //读取html文件夹下所有文件和目录
    if(err){
        console.log(err);
    }
    else{       //判断是目录还是文件夹
        console.log(files);

        for(var i=0; i<files.length; i++){
            fs.stat(files[i],function(error,stats){     //循环循环检索是文件还是目录 ——fs.stat异步函数 不能那么写 这么写会造成循环到i=4时才开始检索 导致一直在检索files[4]    
                console.log(files[i]);      
            })
        }
    }
})



//用递归写法来写：
var filesArr = [];      //用于存放检索目录结果的数组
fs.readdir('./html',function(err,files){      //读取html文件夹下所有文件和目录
    
    if(err){
        console.log(err);
    }
    
    else{       //判断是目录还是文件夹
        console.log(files);

        (function getFile(i){       //匿名函数的写法 写完就把对应参数写入 一般用于只调用一次的函数 不重复调用

            if(i == files.length){      //循环结束
                console.log(filesArr);
                return false;
            }

            fs.stat(files[i],function(error,stats){
                if(files[i].isDirectory()){     //如果是目录 则放入结果数组
                    filesArr.push(files[i]);
                }

                getFile(i+1);       //递归调用 注意这个是写在异步函数下的 等于每次调用递归实现i+1都是异步操作 即在他们间实现了同步 所以不会出现上面例子的情况
            })
        })(0)       //将i=0代入函数getfile();    
    }       
            
})

```



---

### 事件（广播）

通过引入 `events `模块，并通过实例化 `EventEmitter `类来注册和监听事件（又称广播和接受广播）

```javascript
var events = require('events');
var EventEmitter = new events.EventEmitter();       
```

- EventEmitter.on ( )

  注册事件，声明事件名和事件执行操作，但是此时不会执行

- EventEmitter.emit ( )

  监听事件，代码执行监听事件时，会跳到指定事件执行事件声明的操作，同时可以将参数对象发送给注册事件

```javascript
EventEmitter.on('get_str',function(str){
  console.log('广播接收到的数据:' + str)  
})

EventEmitter.emit('get_str','data')

// 广播接收到的数据:data
```

**事件的执行顺序**

```javascript
var events = require('events');
var EventEmitter = new events.EventEmitter();       

console.log('同步操作 1');

EventEmitter.on('to_mime',function(mime_data){
    console.log(mime_data);
})

EventEmitter.on('to_parent',function(par_data){     
    console.log('接收到了这个广播 %s',par_data);
    EventEmitter.emit('to_mime','mime接收到的数据');       
    console.log('广播结束');
})

console.log('开始广播');
EventEmitter.emit('to_parent','发送的数据');     
                                               		
console.log('同步操作 2');

/* 
    同步操作 1
    开始广播
    接收到了这个广播 发送的数据
    mime接收到的数据
    广播结束
    同步操作 2
*/
```



---

### 传统Node处理异步数据

在Node.js中，流行两种响应逻辑管理方式：回调和事件监听；

异步编程中，一般用回调来处理一次性事件，使用事件监听器处理处理重复性事件

（ES6后使用`promise`和`async function`来处理，不用这么麻烦了）

**传统：**

```javascript
let fn = () => {
  console.log("数据结果的处理");
}

setTimeout(() => {
    console.log("得到数据");
    fn()
}, 2000);
```

**使用回调函数：**

```javascript
let fn = () => {
    console.log("数据结果的处理");
}
  
setTimeout(
    ((callback) => {
        console.log("得到数据");  
        callback();   
    })(fn)
,2000)
```

**使用事件：**

```javascript
const EventEmitter = require("events").EventEmitter
const myEmitter = new EventEmitter

let fn = () => {
    console.log("数据结果的处理");
}

setTimeout(() => {
    console.log("得到数据");
    myEmitter.emit("dataMake")
}, 0);

myEmitter.on("dataMake",fn)
```



---

### 嵌入式模板ejs

**使用**

必须放在名为views的文件夹下

新建一个html文件 将后缀名改成ejs即可

ejs是一个html模板，可用于动态获取js传输过来的数据

**常用标签**

```
<% %>     --流程控制标签
<%= %>    --输出标签（原文输出html标签,如<p></p>不会被解析）       
<%- %>    --输出标签（html会被浏览器解析）
```

js：

```javascript
var http = require('http');
var url = require('url');
var ejs = require('ejs');

http.createServer(function(req,res){
    res.writeHead(200,{"Content-Type":"text/html;charset = 'utf-8'"});
    var pathname = url.parse(req.url).pathname;
    
    if(pathname == '/index'){
        var data = '我是后台数据';
        var list = [1,2,3];
        var h2 = '<h2>h2数据</h2>';
        ejs.renderFile('../views/index.ejs',{
            msg:data,
            list:list,
            h2:h2
        },function(err,data){
            if(err){
                console.log(err);
            }
            
            res.end(data);
        })
    }
}).listen(8009);
```

index.ejs：

```ejs
<body>
    <% include public/header.ejs %>	<!-- 通过include引入外部文件 -->
    <h2><%= msg %></h2>     <!-- 在脚本中定义msg值 然后再ejs里通过%来取值 -->

    <br>
    <hr>
    <ul>
        <% for(var i=0; i<list.length; i++) { %>        <!-- 循环的写法 -->
            <li>
                <%= list[i] %>
            </li>
        <% } %>
    </ul>
    <hr>
    <%= h2 %>   <!-- h2 = '<h2>h2数据</h2>' 会直接输出 <h2>h2数据</h2> -->
    <hr>
    <%- h2 %>   <!-- 会输出h2式样的 h2数据  即此字符串里的<h2></h2>标签被浏览器解析了 -->
</body>
```

```
更多使用方法：https://ejs.bootcss.com/#docs
```



---

## Express

Express 是一个基于 Node.js 平台的快速、开放、极简的 web 开发框架 

特点： 

```
1. Express 是一个基于 Node.js 平台的极简、灵活的 web 应用开发框架，它提供一 系列强大的特性，帮助你创建各种 Web 和移动设备应用
 
2. 丰富的 HTTP 快捷方法和任意排列组合的 Connect 中间件，让你创建健壮、友好的 API 变得既快速又简单 
 
3. Express 不对 Node.js 已有的特性进行二次抽象，只是在它之上扩展了 Web 应用所需的基本功能 
```

简单使用：

```javascript
var express = require('express');

var app = new express();  // 实例化

// get请求 配置路由 默认
app.get('/',function(req,res){
    res.send('aaa')
})

// localhost:3000/news
app.get('/news',function(req,res){
    res.send('news');
})

app.listen(3000,'127.0.0.1');       // localhost:3000
```

### 路由

```javascript
var express = require('express');

var app =  express();       // 实例化 不使用 app = new express()

// 动态路由     即 localhost:3000/newsContent/xxx 去访问
app.get('/newsContent/:aid',function(req,res){   //  xxx的值保存在 req.params中 存储结果为 {aid:'xxx'} aid只是一个变量名字 可任意
    console.log(req.params);
    var aid = req.params.aid;
    res.send('news'+aid);
})

// get传值      即 localhost:3000/product?aid=xxx 去访问
app.get('/product',function(req,res){
    var get_value = req.query;  // 获取get传值 即问号之后的所有属性和值 
    res.render('news',{get_value:get_value})	// 渲染项目文件夹下的views文件里的news.ejs文件，并将值传递过去
})

app.listen(3000,'127.0.0.1'); 
```



---

### 中间件

本质上来说，一个 Express 应用就是在调用各种中间件

中间件（Middleware） 是一个函数，它可以访问请求对象（request object (req)）, 响应对象（response object (res)）, 和 web 应用中处理请求-响应循环流程中的中间件（一般被命名为 next 的变量）

**中间件的功能：**

```
执行任何代码
修改请求和响应对象
终结请求-响应循环
调用堆栈中的下一个中间件
```

 如果get、post 回调函数中，没有 next 参数，那么就匹配上第一个路由，就不会往下匹配了。如果想往下匹配的话，那么需要写 next() 

**Express 应用可使用如下几种中间件：**

```
应用级中间件 
路由级中间件
错误处理中间件
内置中间件
第三方中间件 
```

==总结：中间件就是匹配路由之前和匹配路由之后做的一系列操作==

- 应用级中间件

```javascript
var express = require('express');
var app = express();

var time = new Date();

// 不注明路由或者注明'/'时表示该中间件表示匹配任意路由
app.use(function(req,res,next){
    console.log('/的时间:'+ time);      // 表示在匹配路由之前打印时间
    next();     // 如果这里不写next() 则只会打印时间而不会向下匹配路由 即打印了时间但不会执行news路由的操作
})  

app.use('/news',function(req,res,next){     // 输入 127.0.0.1:3001/news 时 先打印 /的时间 再打印 /news的时间 最后才响应/news路由 (如果此方法里没有写next() 则不会执行匹配get路由并send网页
    console.log('news的时间:'+ time);
    next();
})

app.get('/',function(req,res){
    res.send('主页');
})

app.get('/news',function(req,res){
    res.send('新闻页');
})


app.listen(3001,'127.0.0.1');
```

- 路由中间件

```javascript
var express = require('express');
var app = express();

app.get('/',function(req,res){
    res.send('你好express');
})

app.get('/news',function(req,res,next){     // 都是匹配/news路由 先在第一个路由里设置自己想要的操作 然后next() 去匹配下一个/news路由 并响应 即实现了/news路由在渲染页面前执行一系列操作的功能
    console.log('这是路由中间件');      // 操作
    next();
})

app.get('/news',function(req,res){
    res.send('这是路由中间件news');
})

app.listen(3001,'127.0.0.1');
```

- 错误处理中间件

```javascript
var express=require('express'); 
var app= express();  

app.get('/',function(req,res){
    res.send('你好express');
})

app.get('/news',function(req,res){
    res.send('你好express news');
})


// 匹配所有路由 并且错误处理的路由一定要放在最后声明 
app.use(function(req,res){
    res.status(404).send('这是404 表示路由没有匹配到')    // res.status() 设置http响应状态码

})

app.listen(3000,'127.0.0.1');
```

- 内置中间件

```javascript
var express=require('express'); 
var app = express();  

// 托管静态页面

// 在项目根目录下的子文件夹public中放置静态文件 然后在浏览器里访问localhost:3000/文件名时 会自动在public寻找该文件并响应 如 localhost:3000/css/style.css 会找到public文件下的css文件夹下的style.css
app.use(express.static('public'));

// 使用虚拟路径 指定一个虚拟路径(原文件夹下不存在该路径) 浏览器里访问localhost:3000/文件名 和访问 localhost:3000/static/文件名时 效果一样 起到注明效果
app.use('/static',express.static('public'));

app.listen(3000,'127.0.0.1');
```

**app.use 和 app.get（app.post ） 执行原理**

```
当存在app.use('/',~)和app.use('/news',~)时  输入 ~/news 时 会执行 / 和 /news 的方法(执行顺序看定义的顺序 如果先定义app.use('/news',~) 则先执行/news)
```

app.use() 匹配的原理：

```
从根路径开始向子路径匹配，如果存在满足的父路径，也可匹配 
如 /news/app 和 /news 两个路由，当输入地址为 /news/app 时 /news/app 满足匹配条件, /news 也满足匹配条件

因为 express里自带一个存放函数的数组 每声明一次 app.use(function(){}) 便相当于在这个数组里执行 push 操作
所以，当先声明一个 app.use(function A(){}) 再声明一个 app.use(function B (){}) 并满足匹配条件时，会先执行 A() 且 A() 里有next() 操作，才会执行数组里存放的下一个函数 B() 以此类推
```

app.get() 匹配的原理：

```
当存在app.get('/news',~)和app.get('/news/app',~)时  输入 ~/news/app 时 只会执行 /news/app 的方法（精确匹配）
app.get() 里也可使用 next()方法，和app.use() 类似
即：先声明 app.get('/news',function A(){}) 再声明 app.get('/news',function B(){}) 当地址输入/news 时 会先执行 A() 且在A()里存在 next() 操作才执行 B()
```

**next ( )**

- next() 操作的有无决定了是否继续往下匹配路由并执行对应操作

- app.get() 和 app.ues() 操作都是在往==同一个==express框架中内置存放函数的数组里做push操作

  当请求地址满足路由条件时会依次从这个内置数组里，寻找可以满足匹配条件的方法并执行，直到没有next() 或者再也找不到满足匹配条件的函数

```javascript
// 如(假定ABD函数里都有next() C 没有 )：

app.get('/news',function A(){})

app.use('/news/app',function B(){})  

app.get('/news/app',function C(){})

app.use('/',function D(){})

// 请求为 /news/app 时 执行顺序为 B() → C()  虽然 D 也可被匹配到 但是因为 C 中没有next(), D 在 C 后声明 所以不会执行 D
```

----

## Koa

```
npm install koa
```

### 请求

```
写的好的博客：https://www.jianshu.com/p/e6aec8bcdcf4
```

#### Context

Koa Context 将节点的请求和响应对象封装到单个对象中，该对象为编写Web应用程序和API提供了许多有用的方法

每个请求都会创建一个`Context`，并在中间件中作为接收者或ctx标识符引用

```javascript
app.use(async ctx => {
  ctx; 				// is the Context
  ctx.request		// is a Koa Request
  ctx.response 		// is a Koa Response
})
```

为方便起见，许多上下文的访问器和方法与委托给它们的`ctx.request`或`ctx.response`是相同的

如：`ctx.status` 等同于 `ctx.response.status` 

```js
// ctx意思为context（上下文），拥有请求和响应的信息，大概内容如下
{
  request: {
    method: 'GET',
    url: '/',
    header: {
      host: 'localhost:3000',
      connection: 'keep-alive',
      'cache-control': 'max-age=0',
      'upgrade-insecure-requests': '1',
      'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36',
      'sec-fetch-dest': 'document',
      accept: 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9',
      'sec-fetch-site': 'none',
      'sec-fetch-mode': 'navigate',
      'sec-fetch-user': '?1',
      'accept-encoding': 'gzip, deflate, br',
      'accept-language': 'zh-CN,zh;q=0.9'
    }
  },
  response: {
    status: 200,
    message: 'OK',
    header: [Object: null prototype] {
      'content-type': 'text/plain; charset=utf-8',
      'content-length': '6'
    }
  },
  app: { subdomainOffset: 2, proxy: false, env: 'development' },
  originalUrl: '/',
  req: '<original node req>',
  res: '<original node res>',
  socket: '<original node socket>'
}

```

`ctx.request` 和 `ctx.req` 的区别（res同理）：

- `ctx.request`是koa2中context经过封装的请求对象，它用起来更直观和简单

- `ctx.req`是context提供的node.js原生HTTP请求对象，虽然不那么直观，但是可以得到更多的内容，更适合深度编程



---

#### API

-  **ctx.req**

  Node的请求对象

- **ctx.res**

  Node的响应对象

- **ctx.request**

  Koa的请求对象

- **ctx.response**

  Koa的响应对象

- **ctx.method**

  获取请求的类型

- **ctx.url**

  获取请求的地址



----

### 路由

koa和express不同的是，koa要使用路由时，需要额外引入包

```
npm install koa-router
```

```jsx
var Koa = require('koa')
var app = new Koa()
// 引入时直接创建实例 等同于var Router = require('koa-router') ; var router = new Router()
var router = require('koa-router')()	

// 配置路由
router.get('/',async (ctx) => {
    // 返回数据，相当于res.writeHead() 和 res.end()
    ctx.body = '首页'
    console.log(ctx)
}).get('/news', async (ctx) => {
    ctx.body = '新闻页'
})
// 链式调用写法，也可以再 router.get()，下面的app.use同理


// 启动路由（让路由生效）
app.use(router.routes())    // 启动路由 必须加上才能正常使用路由
    // 可以配置也可以不配置，建议配置，作用：用在router.routes()之后，所以在当所有路由中间件最后调用，此时根据ctx.status设置response响应头
    .use(router.allowedMethods())      

app.listen(3000)
```

**其它使用技巧**

```javascript
import { Context } from 'koa'
import Router from 'koa-router'

const router = new Router({
  prefix: '/api',		// prefix 表示默认在所有路由前加的前缀
})

// 如声明一个router.get('/login') 实际声明的是'/api/login'，用prefix可以省略
```



----

#### 获取请求内容

##### get

在ctx里获取

- query：返回将字符串格式化好的json对象
- querystring：返回字符串

```js
// 请求url: /admin?ad=123&ac=321
app.use(async(ctx)=>{
    let url =ctx.url
    
    //从request中获取GET请求
    let request =ctx.request
    let req_query = request.query
    let req_querystring = request.querystring
    
    //从上下文中直接获取
    let ctx_query = ctx.query				// { ad: '123', ac: '321' }
    let ctx_querystring = ctx.querystring	// ad=123&ac=321

})
// 使用 ctx.xxx 和 ctx.request.xxx 功能完全一样
```

如果是使用动态路由的写法，则使用`ctx.params`

```js
// 请求地址：XXX/123
router.get('XXX/:id', async (ctx) => {
	console.log(ctx.params)		// { id:123 }
})
```

##### post

对于POST请求的处理，koa2没有封装方便的获取参数的方法，需要通过解析上下文context中的原生node.js请求对象req来获取

```
获取POST请求的步骤：
 1、解析上下文ctx中的原生nodex.js对象req。
 2、将POST表单数据解析成query string-字符串(如:name=zyb&age23)。
 3、将字符串转换成JSON格式
```

[一般用中间件来实现获取post请求数据（ctrl点击跳转）](#postData)



---

#### 动态路由

在声明匹配路由路径时用`:`声明跟在这之后的url为指定变量，并且可以被匹配到

通过`ctx.params`获取到动态路由的传值

```jsx
router.get('/admin/:aid',async (ctx) => {
	console.log(ctx.params)
}
           
// 请求/admin/test1，打印{ aid: 'test1' }
// 请求/admin/test2，打印{ aid: 'test2' }
```



---

#### 设置返回内容

```js
// 设定访问/home时返回给前端的内容
router.get('/home', async(ctx) => {
   	ctx.status = 200 								// ctx.status 直接设置响应状态码
	ctx.body = 'abc' 								// ctx.body 直接设置响应body
	ctx.set('Content-Type', 'application/zip')		// ctx.set 直接设置响应头
})
```



-----

### 中间件

koa的中间件和express不同，express的中间件执行的顺序是只看声明的顺序 

koa中间件的执行顺序：匹配到没有`next()`返回去重新执行一遍之前匹配过的中间件，同路径的路由或中间件看声明顺序执行

匹配中间件→匹配路由→重新回去匹配中间件或路由执行`next()`后的代码

声明路径为 `/`（省略路径默认 `/`）时，表示匹配所有中间件

```jsx
const Koa = require("koa");
const app = new Koa;

app.use(async (ctx, next) => {
    console.log('1'); 
    await next(); // 调用下一个middleware
    console.log('5')
});
app.use(async (ctx, next) => {
    console.log('2');
    await next(); 
    console.log('4');
});
app.use(async (ctx, next) => {
    console.log('3');
});

app.listen(3000)
console.log("listening on port 3000")

// 执行顺序：1 2 3 4 5
```

```jsx
const Koa = require("koa");
const app = new Koa; 
var router = require('koa-router')()

router.get('/app', async (ctx,next) => {
    ctx.body = 'app'
    console.log('3')
    await next()
    console.log('5')
})

app.use(async (ctx, next) => {
    console.log('1'); 
    await next(); 
    console.log('7')
});

app.use(async (ctx, next) => {
    console.log('2'); 
    await next(); 
    console.log('6')
});

router.get('/app', async (ctx) => {
    console.log('4');  
});

router.get('/app', async (ctx) => {
    console.log('因为没有next因此无法匹配到这'); 
});


app.use(router.routes()) 
app.listen(3000)

// 访问 /app时，执行顺序 1 2 3 4 5 6 7
// 可以看到，是先匹配完了能匹配的中间件才开始去匹配路由的
```

错误处理中间件：

```jsx
app.use(async (ctx,next) => {
    next()	// 也可以写在if判断后
    if(ctx.status == 404) {
        ctx.body('404页面')
    }
    else {
        ..... 
    }
})
```

-----

#### ejs模板

```
npm istall koa-views
npm install esj
```

在koa中使用ejs并不需要安装了还要引用，只需下载就行，配置koa使用的是`koa-views`

同时配置该中间件时，必须保证在所有路由前配置，即`app.use`

配置方法一：网页文件的后缀名为`.ejs`

```js
const views = require('koa-views');
//views 代表引入的koa-views模块;template代表我们保存模板文件的目录
app.use(views('views', {    
    extension: 'ejs'                   //指定我们使用的模板为ejs
  })
);
```

配置方法二：网页文件的后缀为`.html`

```js
app.use(views('views', {
  map: {
    html: 'ejs'
  }
}))
```

```jsx
const Koa = require("koa");
const app = new Koa; 
var router = require('koa-router')()
const views = require('koa-views')

app.use(views('views', {
    extension: 'ejs'
}))


router.get('/',async (ctx) => {  
    // index.ejs 需要放在项目/views文件夹下才能找到, awit必须加上
    await ctx.render('index', {
        title: "标题"
    })	
})

app.use(router.routes()) 
app.listen(3000)
```

```jsx
// index.ejs 
<body>
    <%= title %>
</body>
```

**坑**

使用TS编写时，ctx的返回类型为`Context`，但是此时如果使用`import`方式来导入`koa-router`，将ctx声明为`Context`时会因为有点问题报错，使用`require`的方式来导入就不会出现此问题，待找原因

即`koa-views`和`koa-router`同时用时，使用`require`方式引入路由



-----

#### 获取请求数据

**原生NodeJs获取提交数据方式**

【连接上面ejs代码】由于这里是异步渲染ejs，因此获取页面数据时也应该异步获取（用promise）

这种方式很麻烦

```jsx
const Koa = require("koa");
const app = new Koa; 
var router = require('koa-router')()
const views = require('koa-views')

const getPostData = function(ctx) {
    return new Promise(function(resolve,reject){
        try { 
            let str = ''
            ctx.req.on('data', function(chunk) {
                str += chunk
            })
            ctx.req.on('end', function(chunk) {
                resolve(str)
            })
        }
        catch(err) {
            reject(err)
        }
    })       
}

// 在/doAdd路由里获取传送过来的post数据{name:...}
router.post('/doAdd', async(ctx) => {
    // 原生nodejs在koa中获取提交的数据
    var data = await getPostData(ctx)
    ctx.body = data
})

app.use(views('views', {
    extension: 'ejs'
}))

router.get('/',async (ctx) => {  
    await ctx.render('index')
})

app.use(router.routes()) 
app.listen(3000)

```

**使用koa中间件获取提交数据方式**

```j
npm install koa-bodyparser
```

 <a name="postData"> </a>

```jsx
var bodyParser = require('koa-bodyparser')

app.use(bodyParser())	// 配置中间件

ctx.request.body	// 获取表单提交数据
```

```jsx
const Koa = require("koa");
const app = new Koa; 
var router = require('koa-router')()
const views = require('koa-views')
var bodyParser = require('koa-bodyparser')

router.post('/doAdd', async(ctx) => {
    ctx.body = ctx.request.body
})

app.use(views('views', {
    extension: 'ejs'
}))

router.get('/',async (ctx) => {  

    await ctx.render('index')
})

app.use(bodyParser())		// 注意要放在路由中间件前，否则提交表单不会自动跳转到提交页面
app.use(router.routes()) 
app.listen(3000)

```

----

#### 静态资源

去指定目录寻找静态资源文件，如果能找到返回对应文件，否则`next()`

```
npm install koa-static
```

```jsx
const static = require('koa-static')

//app.use(static(dir_name))
app.use(static('static'))	// 匹配/static目录下的静态资源
app.use(static('public'))	// 可以配置多个，去多个目录下寻找

// 这里是绝对路径，且省略第一个/
// 即如果要匹配/src/style下的内容时，应该是下面这个，写成/src/style不行
app.use(static('src/style'))
```

```jsx
// 这里有个坑，如：html需要成功导入一个/static/css/index.css的静态资源文件，由于中间件已经默认去/static文件夹下寻找了，因此路径需要省略static，应该用第二种写法
<link rel="stylesheet" href="static/css/index.css">	
<link rel="stylesheet" href="css/index.css">	
```



----

### 认证机制

#### Cookie

**cookie介绍**

- 保存在浏览器客户端

- 用同一个浏览器访问同一个域名的时候共享数据

浏览器与WEB服务器之间是使用HTTP协议进行通信的，当某个用户发出页面请求时，WEB服务器只是简单的进行响应，然后就关闭与该用户的连接，因此当一个请求发送到WEB服务器时，无论其是否是第一次来访，服务器都会把它当作第一次来对待，每一次的访问是没有建立联系的（http的无状态）

再再一次请求某个网站的链接时，将本地的cookie一并发送过去，这样浏览器就能识别到还是同一个客户在操作（实现多个页面之间数据的传递）

```jsx
ctx.cookies.set(name, value, [options])
```

options属性：

- maxAge：一个数组表示从`Date.now()`得到的毫秒数，表示相对时间
- expires：cookie过期的Date，表示绝对时间
- path：cookie路径，默认`'/'`都可访问，也可设置具体路由表示只在访问特定路由中传递cookie
- domain：cookie域名，默认当前域下面的所有页面都可以访问
- secure：安全cookie，默认false，为true时表示仅https可访问
- httpOnly：是否只是服务器可访问cookie，默认true
- overwrite：是否覆盖以前设置的同名cookie，默认flase

```jsx
// 正常的基本配置
const Koa = require("koa");
const app = new Koa(); 
const router = require('koa-router')()

router.get('/',async (ctx) => {  
    ctx.cookies.set('userinfo','mary',{
        maxAge:60*1000*60  // 多少毫秒后过期
    })
})

router.get('/news',async (ctx) => {  
    var userinfo = ctx.cookies.get('userinfo')
    console.log(userinfo)   // 访问首页再访问/news路由，打印mary
})

app.use(router.routes()) 
app.listen(3000)
```

koa中没法直接设置中文的cookie值

```jsx
console.log(new Buffer('汉字').toSting('base64'))	 //拿到打印的中文base64字符
console.log(new Buffer('拿到的base64字符', 'base64').toString())	// 还原中文字符
```

因此在设置的时候使用base64字符，想要取cookie并正常显示的时候转换为汉字即可

----

#### Session

session也用来记录客户状态，不同的是cookie保存在客户端浏览器中，而session保存在服务器上

**工作流程**

session需要依赖cookie：

当浏览器访问服务器并发送第一次请求时，服务器端会创建一个session对象，生成一个类似于key，value的键值对

然后将key（cookie）返回到浏览器（客户）端，浏览器下次再访问时，携带key（cookie）找到对应的session（value），客户的信息都保存在session中

<img src="https://img-blog.csdnimg.cn/20200512195339574.png" style="float:left">

```
npm install koa-session
```

```jsx
// 配置
const session = require('koa-session')

app.keys = ['some secret hurr']     // cookie的签名

const CONFIG = {
    // 主要设置maxAge和renew
    key: 'koa:sess',    // 默认 
    maxAge: 600000,     // 最大过期时间（相对毫秒）
    overwrite:true,     // 默认 true
    httpOnly:true,      // 默认true
    signed:true,        // 签名，默认true
    rolling:false,      // 每次访问的时候重新注册session，默认false
    renew:true         	// 当用户一直停留在一个页面时，有操作情况下session快过期的时候重新设置，默认flase（以防长时间操作过程中session失效）
}

app.use(session(CONFIG, app))
```

当设置了`signed:true`时，则必须要配置`app.key`属性，里面的字符串属性可以随意填，但是一般默认`'some secret hurr'`，作用是将cookie的内容通过秘钥进行加密处理

```jsx
const Koa = require("koa");
const app = new Koa; 
const router = require('koa-router')()
const session = require('koa-session')

router.get('/',async (ctx) => {  
    ctx.session.userinfo = '张三'  // 可以直接设置中文
})

router.get('/news',async (ctx) => {  
    var userinfo = ctx.session.userinfo
    console.log(userinfo)   
})


app.keys = ['some secret hurr']    
const CONFIG = {
    key: 'koa:sess',    
    maxAge: 600000,    
    overwrite:true,    
    httpOnly:true,      
    signed:true,       
    rolling:false,    
    renew:true        
}

app.use(session(CONFIG, app))
app.use(router.routes()) 
app.listen(3000)
```

-----

### 跨域处理

#### JSONP

html中，有不受同源策略限制的标签

```jsx
<script src="...">`		//加载js脚本
<img src="..."> 		//图片
<link href="...">		//css
<iframe src="...">		//任意资源
```

JSONP就是利用了`<script/>`标签的这个性质，利用脚本读取到跨域的数据并加载到本服务器，缺点是只能在GET请求中使用且不安全

**方法和目的：**在远程服务器上设法把数据装进js格式的文件里，供客户端调用和进一步处理

```js
// 发起请求服务器
<script type="text/javascript">
    var localHandler = function(data){
        alert(data.result, data.returnCode);
    };
</script>
<script type="text/javascript" src="b"></script>

// 跨域服务器 执行该方法并返回一个对象
function localData() {
    return {
        result:'result',
        returCode:'200'
    }
}
```



---

#### CROS

浏览器发出CORS请求，需要对请求头增加一些信息，服务器会根据这些信息来是否决定同意这次请求

CROS需要请求方和服务器同时支持（IE不能低于10版本），几乎所有浏览器都支持CROS，因此只需要在服务器上实现CROS即可

**服务器控制CROS的主要头信息字段：**

- **Access-Control-Allow-Origin**

  必须字段，浏览器通过设置该字段表示开启CROS，它指定允许进入来源的域名、ip+端口号 。 如果值是 `*`，表示接受任意的域名请求，这个方式不推荐，主要是因为其不安全

-  **Access-Control-Allow-Credentials**

  可选字段，它设置是否可以允许发送cookie，true表示cookie包含在请求中，false则相反，默认为false。如果项目需要cookie就得设置该字段了，CORS请求默认不发送Cookie和HTTP认证信息的，所以在此基础上同时也需要在前端设置（以axios为例）避免没有发送：` axios.defaults.withCredentials = true`

- **Access-Control-Max-Age**

  可选字段，用于配置CORS缓存时间，即本次请求的有效期，单位为秒

- **Access-Control-Allow-Methods**

  可选字段，设置允许请求的方法

- **Access-Control-Allow-Headers**

  可选字段，设置允许的请求头信息

  

---

#### koa处理跨域

koa解决跨域的方式有很多种，最好的方案应该是在服务器端设置支持跨域

- **原生设置方式：在koa2中设置具体的请求头信息**

  设置允许请求的域名

```javascript
app.use(async (ctx, next) => {
    ctx.set("Access-Control-Allow-Origin", "*")		// 允许所有域名请求
    await next()
})

// 只允许来自 http://localhost:8080 域名的请求
// ctx.set("Access-Control-Allow-Origin", "http://localhost:8080")
```

​		设置允许的跨域请求方式

```js
ctx.set("Access-Control-Allow-Methods", "OPTIONS, GET, PUT, POST, DELETE")
```

​		设置服务器所支持的所有头信息字段（不同值用逗号分隔）

```js
ctx.set("Access-Control-Allow-Headers", "x-requested-with, accept, origin, content-type")
/* 
	服务器收到请求，检查
		Origin
		Access-Control-Request-Method
		Access-Control-Request-Headers
	字段，确认允许跨源请求，即可做出响应。
*/
```

​		设置具体请求中的媒体类型信息

```js
ctx.set("Content-Type", "application/json;charset=utf-8")
```

​		设置是否允许发送cookie【可选设置】

```js
ctx.set("Access-Control-Allow-Credentials", true);
// 默认情况下，Cookie不包括在CORS请求之中,当设置成允许请求携带凭证cookie时，需要保证"Access-Control-Allow-Origin"是服务器有的域名，而不能是"*"
```

​		设置次验证的有效时间，即在该时间段内服务端可以不用再进行验证【可选设置】

```js
ctx.set("Access-Control-Max-Age", 300);		// 单位为s
// 当请求方法是PUT或DELETE等特殊方法或者Content-Type字段的类型是application/json时，服务器会提前发送一次请求进行验证
```

​		返回自定义所需字段

```markdown
CORS请求时，XMLHttpRequest对象的getResponseHeader()方法只能拿到6个基本字段：
    - Cache-Control
    - Content-Language
    - Content-Type
    - Expires
    - Last-Modified
    - Pragma
需要获取其他字段时，使用Access-Control-Expose-Headers
```

```js
ctx.set("Access-Control-Expose-Headers", "myData")
```

完整的一次请求：

```js
app.use(async (ctx, next) => {
    ctx.set("Access-Control-Allow-Origin", "*")
    ctx.set("Access-Control-Allow-Headers", "x-requested-with, accept, origin, content-type")
    ctx.set("Access-Control-Allow-Headers", "x-requested-with, accept, origin, content-type")
    ctx.set("Content-Type", "application/json;charset=utf-8")
    ctx.set("Access-Control-Allow-Credentials", true)
    ctx.set("Access-Control-Max-Age", 300)
    ctx.set("Access-Control-Expose-Headers", "myData")
    await next()
})
```

- **使用中间件方式：koa2-cors**

```
npm i koa2-cors -s
```

注册中间件

```js
const cors = require('koa2-cors')
app.use(cors(...))
```

```js
const Koa = require('koa');
const bodyParser = require('koa-bodyparser'); //post数据处理
const router = require('koa-router')(); //路由模块
const cors = require('koa2-cors'); //跨域处理

const app = new Koa();
app.use(
    cors({
        origin: function(ctx) { //设置允许来自指定域名请求
            if (ctx.url === '/test') {
                return '*'; // 允许来自所有域名请求
            }
            return 'http://localhost:8080'; //只允许http://localhost:8080这个域名的请求
        },
        maxAge: 5, //指定本次预检请求的有效期，单位为秒。
        credentials: true, //是否允许发送Cookie
        allowMethods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'], //设置所允许的HTTP请求方法
        allowHeaders: ['Content-Type', 'Authorization', 'Accept'], //设置服务器支持的所有头信息字段
        exposeHeaders: ['WWW-Authenticate', 'Server-Authorization'] //设置获取其他自定义字段
    })
);
router.post('/', async function (ctx) {
    ctx.body = '请求成功了'
});


app.use(router.routes())
    .use(router.allowedMethods());

app.listen(3000);
```

一般无特殊用法的话直接全部配置默认就可（即什么都不需要填）

```js
app
  .use(cors())
  .use(bodyParser())
  .use(router.routes())
```





