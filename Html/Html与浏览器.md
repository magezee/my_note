# Html

## 偏概念

### Http

#### Http和Https

**HTTPS和HTTP的区别**

```
1.https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用
    2.http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议
    3.http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443
    4.http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全
```

**HTTP和HTTPS的基本概念**

```
HTTP：
	是互联网上应用最为广泛的一种网络协议，是一个客户端和服务器端请求和应答的标准（TCP），用于从WWW服务器传输超文本到本地浏览器的传输协议,它可以使浏览器更加高效，使网络传输减少。

HTTPS：
	是以安全为目标的HTTP通道，简单讲是HTTP的安全版，即HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL

HTTPS协议的主要作用可以分为两种：
一种是建立一个信息安全通道，来保证数据传输的安全；另一种就是确认网站的真实性
```

**HTTP和HTTPS的主要特点**

http：

```
1.支持客户/服务器模式。（C/S模式）

2.简单快速：客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、HEAD、POST。每种方法规定了客户与服务器联系的类型不同，由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快

3.灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记
    
4.无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间

5.无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力，缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快
```

```
http是一个无状态的面向连接的协议。

http无状态：无状态协议是指http协议本身对于事务处理没有记忆功能，服务器不知道浏览器的状态。
    通俗的即使你登录了，去访问同一个网站的不同网页，服务器都不会知道你是谁，如果需要记录登录用户的信息，用户操作，用户行为等数据需要使用cookie或session来存储。

keep-alive：从HTTP/1.1起，浏览器默认都开启了Keep-Alive，保持连接特性，客户端和服务器都能选择随时关闭连接，则请求头中为connection:close。
    简单地说，当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的TCP连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的TCP连接。
    但是Keep-Alive不会永久保持连接，它有一个保持时间，可以在不同的服务器软件（如Apache）中设定这个时间。

误解：无状态不代表HTTP不能保持TCP连接，更不能代表HTTP使用的是UDP协议（无连接）。
    即使http在无状态下，只要客户端和服务器的头部信息connection:keep-alive，则在有效期内他们使用同一条TCP连接
```

https：

```
HTTPS是HTTP协议的修改，它加密数据并确保其机密性。其配置可保护用户在与网站交互时免于窃取个人信息和计费数据

优点:
相比于http，https可以提供更加优质保密的信息，保证了用户数据的安全性，此外https同时也一定程度上保护了服务端，使用恶意攻击和伪装数据的成本大大提高

缺点
	1.https的技术门槛较高，多数个人或者私人网站难以支撑，CA机构颁发的证书都是需要年费的，此外对接Https协议也需要额外的技术支持
	2.目前来说大多数网站并不关心数据的安全性和保密性，其https最大的优点对它来说并不适用
	3.https加重了服务端的负担，相比于http其需要更多的资源来支撑，同时也降低了用户的访问速度
	4.目前来说Http网站仍然大规模使用，在浏览器侧也没有特别大的差别，很多用户不关心的话根本不感知
```

**HTTP和HTTPS的工作流程**

http：

```
第一步：建立TCP/IP连接，客户端与服务器通过Socket三次握手进行连接

第二步：客户端向服务端发起HTTP请求（例如：POST/login.html http/1.1）

第三步：客户端发送请求头信息，请求内容，最后会发送一空白行，标示客户端请求完毕

第四步：服务器做出应答，表示对于客户端请求的应答，例如：HTTP/1.1 200 OK

第五步：服务器向客户端发送应答头信息

第六步：服务器向客户端发送请求头信息后，也会发送一空白行，标示应答头信息发送完毕，接着就以Content-type要求的数据格式发送数据给客户端

第七步：服务端关闭TCP连接，如果服务器或者客户端增Connection:keep-alive就表示客户端与服务器端继续保存连接，在下次请求时可以继续使用这次的连接

```

https：

```
第一步：客户使用https的URL访问Web服务器，要求与Web服务器建立SSL连接。

第二步：Web服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。

第三步：客户端的浏览器与Web服务器开始协商SSL连接的安全等级，也就是信息加密的等级。

第四步：客户端的浏览器根据双方同意的安全等级，建立会话密钥，然后利用网站的公钥将会话密钥加密，并传送给网站。

第五步：Web服务器利用自己的私钥解密出会话密钥。

第六步：Web服务器利用会话密钥加密与客户端之间的通信
```



---

#### Url的组成

一个完整的URL包括7部分

```
例：http://www.aspxfans.com:8080/news/index.asp?boardID=5&ID=24618&page=1#name
```

- 协议部分

  在Internet中可以使用多种协议，如HTTP，FTP等，“ // ”为分隔符

```
http：
```

- 域名部分

  一个URL中，也可以使用IP地址作为域名使用

```
www.aspxfans.com
```

- 端口部分

  跟在域名后面的是端口，域名和端口之间使用“ : ”作为分隔符

  （不是一个URL必须的部分，如果省略，将采用默认端口80）

```
8080
```

- 虚拟目录部分

  从域名后的第一个“ / ”开始到最后一个“ / ”为止，是虚拟目录部分

  （不是一个URL必须的部分）

```
/news/
```

- 文件名部分

  从域名后的最后一个“ / ”开始到“ ？”为止，是文件名部分

  如果没有“ ? ”，则是从域名后的最后一个“ / ”开始到“#”为止，是文件部分

  如果没有 “ ? ”和“ # ”，那么从域名后的最后一个“ / ”开始到结束，都是文件名部分

  （不是一个URL必须的部分，如果省略，则使用默认的文件名）

```
index.asp
```

- 参数部分

  从“ ?  ”开始到“ # ”为止之间的部分为参数部分，又称搜索部分、查询部分

  参数可以允许有多个参数，参数与参数之间用“ & ”作为分隔符

  （不是一个URL必须的部分）

```
boardID=5&ID=24618&page=1
```

- 锚部分

  从“#”开始到最后，都是锚部分

  （不是一个URL必须的部分）

```
name
```



----

#### 请求报文

请求报文主要由三部分构成 —— 请求行、请求头、请求正文

**请求行**

由请求方法、请求网址、HTTP版本（如 GET 127.0.0.1/index HTTP/1.1）组成

```
请求方法（HTTP 1.1协议中）
GET
POST
HEAD
OPTIONS
PUT
DELETE
TRACE
```

根据请求目的，设置对应 HTTP Method

-  GET 对应读取资源（Read）
-  PUT 对应更新资源（Update）
-  POST 对应创建资源（Created）
-  DELETE 代表删除资源（Delete）

**请求头（头域）**

由（域名：域值）组成	—— 域名大小写无关，域值前可以添加任意数量的空格符

常用头域：

- Host

```
Host: www.testroad.org
Host ———— 指定请求资源的Internet主机和端口号，必须表示请求url的原始服务器或网关的位置（HTTP/1.1请求必须包含主机头域 否则返回400状态码）
```

- User-Agent

```
User-Agent: Mozila/5.0(WindowNT 6.1;WOW64; rv:35.0) Gecko/20100101 Firefox/35.0
User-Agent ———— 标识请求者的信息（如浏览器类型和版本、操作系统）
如本例指：Firefox 35.0版本浏览器，基于Window7 64位操作系统
这里的信息通常用来做数据收集，可以分析用户常用什么浏览器访问网页的服务
```

- Accept

```
Accept: image/png, image/*; q=0.8, */*; q=0.5
Accept ———— 用于指定客户端接收哪些类型的响应内容（能够在客户浏览器中直接打开的格式）
```

- Accept-Language

```
Accept-Language: zh-CN,zh;q=0.9
Accept-Language ———— 注明客户端的操作系统语言
```

- Accept-Encoding

```
Accept-Encoding: gzip, deflate
Accept-Encoding ———— 指客户端所能接受的编码规则或格式规范
```

- Referer

```
Referer: http://localhost:4311/edit
Referer ———— 当前地址的上一个依赖地址（即进入此页面前的上一个页面地址）
```

- Connection

```
Connection: keep-alive
Connection ———— 表示当clinet和server通信时对于长链接如何进行处理（HTTP1.1中 clinet和server默认对方支持长链接）

如果不希望使用长链接
clinet ———— header中声明 Connection 的值为close 
server ———— header 和 response 中声明 Connection 的值为close
任意一个声明了close 都表明正在使用的TCP链接在当前请求处理完毕后会被断掉 以后client再进行新的请求时就必须创建新的TCP链接
```



---

#### 响应报文

响应报文主要由3个部分构成 —— 响应行、响应头、响应正文

**响应行**

HTTP版本 状态码（如 HTTP/1.1 200 OK）

**响应头**

与请求头类似

需要注意的头域：

- Content-Encoding

```
Content-Encoding: gzip
表示返回的内容经过了gzip压缩技术
```

- Content-Length

```
Content-Length: 17716
表示返回的正文长度 确保页面传输的内容正确
```

访问一个web页面会产生多个HTTP请求，如果存在长久链接 则只依靠一个TCP连接



---

#### 状态码

浏览器会根据响应返回的状态码来判断请求的状态如何，比如浏览器遇到4xx开头的状态码都会默认为错误响应，并且报出错误信息（即使该请求是能成功返回到数据但是却人为设置的404状态码）

```markdown
1xx
	请求正常
2xx
	请求成功
3xx
	重定向
4xx
	客户端错误
5xx
	服务端错误
```

**常见状态码**

| 状态码 | 功能和含义                                                   |
| :----- | ------------------------------------------------------------ |
| 200    | 表示从客户端发送给服务器的请求被**正常处理并返回**           |
| 204    | 表示客户端发送给客户端的请求得到了成功处理，但在返回的响应报文中不含实体的主体部分（**没有资源可以返回**） |
| 206    | 表示客户端进行了**范围请求**，并且服务器成功执行了这部分的GET请求，响应报文中包含由Content-Range指定范围的实体内容 |
| 301    | **永久性重定向**，表示请求的资源被分配了新的URL，之后应使用更改的URL |
| 302    | **临时性重定向**，表示请求的资源被分配了新的URL，希望本次访问使用新的URL |
| 303    | 表示请求的资源被分配了新的URL，应使用**GET方法**定向获取请求的资源 |
| 304    | 表示客户端发送**附带条件**（是指采用GET方法的请求报文中包含if-Match、If-Modified-Since、If-None-Match、If-Range、If-Unmodified-Since中任一首部）的请求时，服务器端允许访问资源，但是请求为满足条件的情况下返回改状态码 |
| 307    | **临时重定向**，与303有着相同的含义，307会遵照浏览器标准不会从POST变成GET（不同浏览器可能会出现不同的情况） |
| 400    | 表示请求报文中存在**语法错误**                               |
| 401    | **未经许可**，需要通过HTTP认证                               |
| 403    | 服务器**拒绝访问**（访问权限出现问题）                       |
| 404    | 表示服务器上**无法找到请求的资源**，除此之外，也可以在服务器拒绝请求但不想给拒绝原因时使用 |
| 500    | 表示服务器在执行请求时**发生错误**，也有可能是web应用存在的bug或某些临时的错误时 |
| 503    | 表示服务器暂时处于超负载或正在进行停机维护，**无法处理请求** |



------

#### 请求方式

```
除了GET、HEAD、POST，其他均为HTTP1.0后新增
```

| 方法    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| GET     | 请求指定的页面信息，并返回实体主体                           |
| HEAD    | 类似于GET请求，但响应中无具体内容，只用于获取请求头          |
| POST    | 向指定资源提交数据进行处理请求（如提交表单或上传文件），数据被包含在请求体重，POST请求可能会导致新的资源建立或已有资源的修改 |
| PUT     | 从客户端向服务器传送的数据取代指定的文档内容                 |
| DELETE  | 请求服务器删除指定数据                                       |
| CONNECT | 预留给能够将连接改为管道方式的代理服务器                     |
| OPTIONS | 允许客户端查看服务器的性能                                   |
| TRACE   | 回显服务器收到的请求，主要用于测试或诊断                     |

**GET和POST的区别**

理论而言，GET和POST底层都是TCP/IP，即它们都是TCP链接，它们之间能做的事情是一样的，给GET加上request body和POST带上url参数也是可以的

它们之间的不同是由于HTTP协议对它们做出了准则以及浏览器/服务器的限制，如在GET中带上request body，有些服务器可能可以读取数据，有些则直接忽略，所有为了让所有的请求能达到预期目的，应该准寻它们的行为标准

GET和POST的一个重大区别：

- GET产生一个TCP数据包，浏览器会把http、header和data一并发送出去，服务器响应200返回数据
- POST产生两个TCP数据包，浏览器先发送header，服务器响应100 continue，浏览器再发送data，浏览器响应200返回数据（并不是所有浏览器都这样，Firefox只发送一次）

```
这个区别导致了POST的请求速度只有GET的约2/3，即GET请求更快，但是在宏观下可以忽略
```

其他规范性的区别：

- GET参数通过URL传递，POST放在Request body
- GET在浏览器回退时是无害的，而POST会再次提交请求
- GET产生的URL地址可以被Bookmark，而POST不可以
- GET请求会被浏览器主动cache，而POST不会，除非手动设置
- GET请求只能进行url编码，而POST支持多种编码方式
- GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留
- GET请求在URL中传送的参数是有长度限制的，而POST没有
- 对参数的数据类型，GET只接受ASCII字符，而POST没有限制
- GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息

```
http协议并未规定get和post的长度限制，get的最大长度限制是因为浏览器和web服务器限制了URL的长度，不同的浏览器对地址长度的限制也是不同的。要强调的是，这个长度的限制不是对于参数长度的限制，而是对于整个URL的长度
```

**POST和PUT的区别**

幂等性：同一操作执行一次后，再执行多次的对系统的影响一样

在常用的GET、POST、PUT、DELETE请求方式中，只有POST不是幂等性的

如向数据库中操作一条数据，第一次执行，如果没有这条数据，则创建，第二次执行，如果要实现将旧数据完全覆盖的效果，则应该使用PUT，如果每一次执行都会产生一条完全新的数据，则应该用 POST

即PUT执行一次过后，同样的请求对系统的影响是完全一样的，至始至终都只有一条数据，而POST则是每一次请求都会增加一条数据

POST请求在服务器上是未知的，即增删改都可以用POST请求来实现，因此出现了规范语义化的PUT和DELECT请求



-----

#### 实体字符

- `&copy;`：`©` 
- `&nbsp;`：` 小空格` 
- `&emsp;` ：`大空格`

```
浏览器总是会截短 HTML 页面中的空格 要使用多个空格时 需要使用实体字符
```



----

#### 外部资源引入

**引入css文件**

```jsx
<link rel="stylesheet" type="text/css" href="./index.css"/>	 // rel属性不能少
```

**引入JS文件**

```html
<script type="text/javascript" src="js/index.js" ></script>
```

**引入外部API**

```html
<script src="https://cdn.bootcss.com/redux/4.0.1/redux.min.js"></script>
```

**href和src属性区别**

```
href用在link和a等元素上，是在当前元素和引用资源之间建立关系，href表示超文本引用

src用在img、script、iframe上，表示用引用的资源替换当前元素，src表示来源地址
```



---

#### 标签自定义属性

在标签内新添自定义属性方便后续操作读取时，一般命名规范为`data-xxxx`

如：

```html
<head>
    <style>
        h1::before {
            content: attr(title)
        }
    </style>
</head>
<body>
    <h1 data-title='自定义标题'/>
</body>
```



---

### 页面加载

#### 浏览器线程

**GUI渲染线程**

- 负责渲染浏览器界面，解析HTML，CSS，构建DOM树和RenderObject树，布局和绘制等。
- 当界面需要重绘（Repaint）或由于某种操作引发回流(reflow)时，该线程就会执行
- 注意，GUI渲染线程与JS引擎线程是互斥的，当JS引擎执行时GUI线程会被挂起（相当于被冻结了），GUI更新会被保存在一个队列中等到JS引擎空闲时立即被执行。

```
由于js是可操纵DOM的，如果在修改这些元素属性同时渲染界面（即JS线程和UI线程同时运行），那么渲染线程前后获得的元素数据就可能不一致了。因此为了防止渲染出现不可预期的结果，浏览器控制JS线程和UI线程以列队的形式同步执行
```

**JS引擎线程**

- 也称为JS内核，负责处理Javascript脚本程序。（例如V8引擎）
- JS引擎线程负责解析Javascript脚本，运行代码。
- JS引擎一直等待着任务队列中任务的到来，然后加以处理，一个Tab页（renderer进程）中无论什么时候都只有一个JS线程在运行JS程序
- 同样注意，GUI渲染线程与JS引擎线程是互斥的，所以如果JS执行的时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞。

**事件触发线程**

- 归属于浏览器而不是JS引擎，用来控制事件循环（可以理解，JS引擎自己都忙不过来，需要浏览器另开线程协助）
- 当JS引擎执行代码块如setTimeOut时（也可来自浏览器内核的其他线程,如鼠标点击、AJAX异步请求等），会将对应任务添加到事件线程中
- 当对应的事件符合触发条件被触发时，该线程会把事件添加到待处理队列的队尾，等待JS引擎的处理
- 注意，由于JS的单线程关系，所以这些待处理队列中的事件都得排队等待JS引擎处理（当JS引擎空闲时才会去执行）

**异步http请求线程**

- 在XMLHttpRequest在连接后是通过浏览器新开一个线程请求
- 将检测到状态变更时，如果设置有回调函数，异步线程就产生状态变更事件，将这个回调再放入事件队列中。再由JavaScript引擎执行

```
因此，异步JS并不是真的另开了一个线程，只是暂时先把异步的JS放在最后才执行而已 —— JS的单线程性
```

**定时触发器线程**

- 传说中的`setInterval`与`setTimeout`所在线程
- 浏览器定时计数器并不是由JavaScript引擎计数的,（因为JavaScript引擎是单线程的, 如果处于阻塞线程状态就会影响记计时的准确）
- 因此通过单独线程来计时并触发定时（计时完毕后，添加到事件队列中，等待JS引擎空闲后执行）
- 注意，W3C在HTML标准中规定，规定要求setTimeout中低于4ms的时间间隔算为4ms。

```markdown
注意：为了定时的准确，计时功能是由一个单独的线程执行的，但是执行计时函数里的方法时，还是会和异步JS一样，放在event loop中，等待JS引擎空闲再去执行
计时函数
	如：setTimeout、setInterval，这些都是宏异步任务，定时线程决定了它们一定是在JS的最后才执行
	执行setInterval的时候，仅仅是把setInterval放到一个队列中，当它发现目前的代码都执行完成后，它再去队列里面取setInterval，看这个setInterval是否到达时间了
	如果它没有到达，再把它重新放进队列中；如果它到达了时间则执行它，并再重新放到队列中(setTimeOut则不需要重新放入队列)
```

**总结**

实际上可以理解成浏览器有一个UI线程和一个JS线程，且它们会互相堵塞就行，其他的线程只是为了把代码放进一个队列，最终还是需要用JS线程去执行



-----

#### 请求过程

- 输入资源地址，发起请求
- 浏览器启动缓存机制
- 浏览器想DNS服务器发起域名解析请求
- 建立与服务器的TCP/IP连接（三次握手）
- 向服务器发送送http/https请求，创建端口
- 服务器在端口监听客户端请求（接受请求，经过后端处理后返回状态和内容）
- 浏览器得到内容
  - 拿到HTML页面代码，开始解析页面
  - 碰到js、css图片等静态资源，发起请求（发起的请求会同样经过上面步奏）
  - 根据拿到的资源对页面进行渲染

---

##### 解析

```markdown
解析和渲染并不是先后顺序关系，而是互相穿插的
	渲染引擎将会尽可能早的将内容呈现到屏幕上，并不会等到所有的html都解析完成之后再去构建和布局render树
	它是解析完一部分内容就显示一部分内容，同时，可能还在通过网络下载其余内容
```

浏览器会对转化后的数据结构自上而下进行分析：首先开启下载线程，对所有的资源进行优先级排序下载（这里仅仅是下载），同时主线程会对文档进行解析：

- 遇到 script 标签时，首先阻塞后续内容的解析，同时检查该script是否已经下载下来，如果已下载，便执行代码
- 遇到 link 标签时，不会阻塞后续内容的解析（比如 DOM 构建），检查 link 资源是否已下载，如果已下载，则构建 cssom
- 遇到 DOM 标签时，执行 DOM 构建，将该 DOM 元素添加到文档树中

```
浏览器逐行解析，遇到</html>表示解析完成
文档解析完成时触发DOMContentLoaded事件
DOMContentLoaded事件在 html文档加载完毕（不包括样式表，图片，flash），并且 html 所引用的内联 js、以及外链 js 的同步代码都执行完毕后触发
```



---

##### 渲染

- 根据HTML结构生成DOM Tree
- 根据CSS生成CSSOM
- 将DOM和CSSOM整合形成RenderTree
- 根据RenderTree开始渲染和展示
- 遇到`<script>`时，会执行并堵塞渲染【因为JS有权改变DOM Tree，如果不堵塞，可能造成解析dom后又因为JS引发的修改而重新解析，大大影响性能】

```
当所有的资源都加载完后触发window.load事件
window.load是在页面加载完成后才发生，这包括DOM元素和其他页面元素（例如图片）的加载
JS的异步脚本一定会在load事件前执行，但是可能可能会在DOMContentLoaded事件的之前或之后执行
```

----

#### JS及CSS堵塞

**JS**

- 浏览器下载JS或CSS文件都是并行的，因此下载CSS和JS不会互相阻塞
- 浏览器下载第一个JS文件后，不会立即下载第二个，而是等待第一个JS文件执行完毕后才开始（没有使用async和defer的情况下）
- 下载或者执行JS时会阻塞对标签的解析，也就是阻塞了dom树的形成，只有等到JS执行完毕，浏览器才会继续解析标签，没有dom树，浏览器就无法渲染，所以当加载很大的JS文件时，可以看到页面很长时间是一片空白，而下载CSS或其他静态资源则不会（浏览器需要一个稳定的dom结构）

**CSS**

- CSS的下载不会阻塞后面JS的下载，但是JS下载完成后，被阻塞执行
- CSS加载不会阻塞解析，但是会阻塞渲染



```
目前绝大多数JS代码均推迟到DOMContentLoaded和window.onload之后，确保HTML初始结构渲染完成
且由于这个特性，因此一般会将<script>标签放置于<body>末尾，这是前段性能优化的一项普遍准则（不用defer和async情况下）
```

<img src="https://img-blog.csdnimg.cn/20200901164620266.png#pic_center" style="margin:0"/>

---

##### defer/async

JS下载不会堵塞HTML解析，用于解决JS的加载堵塞问题

同步情况下，解析到`<srcipt src="xxx.js"></srcipt>`标签时，浏览器会立即加载并执行该JS文件，对后续操作造成堵塞

async的优先级高于defer，即若有人同时使用这两个，则以async为准，这两者的区别体现在JS文件的执行时机上

**defer：**

```jsx
<srcipt defer src="xxx.js"></srcipt>
```

defer并不是推迟JS文件的下载，而是将其下载的JS执行时机推迟到HTML文档解析完成之后、DOMComtentLoaded之前

<img src="https://img-blog.csdnimg.cn/20200901170503264.png" style="margin:0"/>

**async：**

async声明的标签对应的JS文件一旦被下载便会立刻执行，并且暂停HTML解析，与defer不同的是，async与DOMContentLoaded时间之间没有任何关联性，不会堵塞其时间的触发时机，即该事件不会等待async的JS文件被执行完成

```jsx
<srcipt async src="xxx.js"></srcipt>
```

<img src="https://img-blog.csdnimg.cn/20200901172213590.png#pic_center" style="margin:0"/>

**顺序区别：**

```jsx
<script defer src='xxxx.a,js'></script>
<script defer src='xxxx.b,js'></script>
```

**defer**的下载是无序的，执行是有序的：a比b文件声明在前，浏览器支持并行下载，因此存在b比a更快下载完毕的现象，但是执行时必须等待a执行完毕才开始执行b（后声明的文件需要等之前的文件下载并执行完才能开始运行）

<img src="https://img-blog.csdnimg.cn/20200901173045967.png" style="margin:0"/>

**async**的下载和执行都是无序的，谁先下载好谁先执行

<img src="https://img-blog.csdnimg.cn/20200901172808123.png" style="margin:0"/>

**针对场景**

对于需要与HMTL文档同步加载并且对执行顺序敏感的JS文件而言，在<head>中声明<sript>并使用defer设置，能充分发挥浏览器并行请求的优势，且不会阻塞HTML解析

async适用于按需加载、逻辑独立的JS模块，不会因为调用顺序而产生不好的影响

```
对于动态创建的<script>标签，默认async为true
```



---

#### 资源优先级

 在浏览器中，不同的资源在浏览器渲染的不同阶段进行加载的优先级不同

```
Highest 	最高
Hight 		高
Medium 		中等
Low 		低
Lowest 		最低
```

其中主资源HTML和CSS的优先级最高，其他资源根据情况的不同优先级不一

JS脚本根据它们在文件中的位置是否异步、延迟或阻塞获得不同的优先级：

- 网络在第一个图片资源之前阻塞的脚本在网络优先级中是中级

- 网络在第一个图片资源之后阻塞的脚本在网络优先级中是低级

- async／defer表示的脚本（无论在什么位置）在网络优先级中是很低级

图片（视口可见）将会获得相对于视口不可见图片（低级）的更高的优先级（中级），所以某些程度上浏览器将会尽量懒加载这些图片。低优先级的图片在布局完成被视口发现时，将会获得优先级提升

---

##### preload

```html
<link rel=“preload”> 
```

preload 提供了一种声明式的命令，让浏览器提前加载指定资源(加载后并不执行)，需要执行时再执行

这样做的好处在于：

- 将加载和执行分离开，不阻塞渲染和document的onload事件

- 提前加载指定资源，不再出现依赖的font字体隔了一段时间才刷出的情况

使用 link 标签静态标记需要预加载的资源

```jsx
// preload 使用as属性加载的资源将会获得与资源type属性所拥有的相同的优先级
// 如preload as="style" 将会获得比 as=“script” 更高的优先级
// 不带as属性的 preload 的优先级将会等同于异步请求
<link rel="preload" href="/path/to/style.css" as="style">
```

使用 preload 后，不管资源是否使用都将提前加载。若不确定资源是必定会加载的，则不要错误使用 preload，以免本末导致，给页面带来更沉重的负担

Preload 有 `as` 属性，浏览器可以设置正确的资源加载优先级，这种方式可以确保资源根据其重要性依次加载， 所以，Preload既不会影响重要资源的加载，又不会让次要资源影响自身的加载；浏览器能根据 as 的值发送适当的 Accept 头部信息；浏览器通过 as 值能得知资源类型，因此当获取的资源相同时，浏览器能够判断前面获取的资源是否能重用

```
如果忽略as属性，或者错误的as属性会使preload等同于XHR请求，浏览器不知道加载的是什么，因此会赋予此类资源非常低的加载优先级
如果对所preload的资源不使用明确的as属性，将会导致二次获取
```

　Preload 的与众不同还体现在 `onload` 事件，可以定义资源加载完毕后的回调函数

```html
<link rel="preload" href="..." as="..." onload="preloadFinished()">

<!-- 可以令preload的样式表立即生效 -->
<link rel="preload" href="style.css" onload="this.rel=stylesheet">
```

对跨域的文件进行preload时，必须加上 crossorigin 属性

```html
<link rel="preload" as="font" crossorigin href="https://at.alicdn.com/t/font_zck90zmlh7hf47vi.woff">
```

---

##### prefetch

```html
<link rel=“prefetch”>
```

它的作用是告诉浏览器加载下一页面可能会用到的资源，注意，是下一页面，而不是当前页面。因此该方法的加载优先级非常低，也就是说该方式的作用是加速下一个页面的加载速度

```
preload：告诉浏览器页面必定需要的资源，浏览器一定会加载这些资源
refetch：告诉浏览器页面可能需要的资源，浏览器不一定会加载这些资源
对于当前页面很有必要的资源使用 preload，对于可能在将来的页面中使用的资源使用 prefetch
```

用法和preload类似

```html
<link rel="preload"   href="https://at.alicdn.com/t/font_zck90zmlh7hf47vi.woff" as="font">
```



---

## 偏编程

### 操作浏览器和文档

```
Document        //文档
Element         //元素节点 如<html> <head> <body> <p> <h1> 等
Text            //文本节点 <html> <head> <body> <ul> 等结构元素不会直接包含任何文本节点
Attribute		// 属性节点 如<img src="images/1.gif" title="个人相册" /> title就是属性节点
```

**获取节点**

- getElementsByID（）

  根据id获取节点，还要tap，class，name等，不列举

- document.querySelector（）

  根据选择器获取节点

```javascript
document.querySelector('#id')
```

- childNodes（）

  获取指定元素的所有子节点，返回值为一个数组，里面装的是节点对象（即使某个元素只有一个子节点 childNode属性也会返回一个节点数组

```javascript
var tag = document.getElementsByTagName("ul");      //获取网页文档中"name = ul"的所有节点对象
var a = tag[0].childNodes;      //获取第一个 "ul" 对象
alert(a[0].nodeName);       
```

- haschildNodes（）

  判断某个元素是否包含子节点

- firstChild（）

```javascript
node.childNodes[0] == node.firstChild
```

- lastChild（）

```javascript
node.childNode[node.childNodes.length-1] == node.lastChild
```

- parentNode（）

  返回指定节点父节点

  永远返回的是元素节点，因为只有元素节点才能包含子节点 ，document没有父节点 将返回null 

- nextSibling（）

  返回一个指定节点的下一个相邻节点

- previousSibling（）

  返回一个指定节点的上一个相邻节点

- nodeName

  返回节点名称

```
如果是元素节点 则返回值为标签名称 标签名称永远是大写
如果是属性节点 则返回值为属性的名称
如果是文本节点 则返回值永远是#text标识符
如果是文档节点 则返回值永远是#document标识符
```

- nodeType

  返回节点类型

```
1——表示元素
2——表示属性
3——表示文本
8——表示注释
9——表示文档
```

```javascript
if(document.getElementsByTagName("ul"[0].nodeType == 1 )) 
	alert("元素"); 
```

**操作节点**

- createElement（）

  根据参数指定的名称创建一个新的元素节点并返回新建元素节点对象

```javascript
 var ele = document.createElement("element");  
// 使用createElement()创建的新节点元素不会自动添加到文档结构中，此时节点还没有NodeParent属性，只是存储在内存中的DocumentFragment对象，仅在JS上下文中有效
// 如果需要把这个DocumentFragment对象添加到文档中 则需要使用appendChild()、insertBefore()或replaceChild()方法实现
document.body.appendChild(ele);     //增加节点到body元素下
```

- creatTextNode（）

  为新建或已存在的的元素插入文本内容，该方法返回一个指向新建文本节点的引用（同不会自动添加到文本结构中）

```javascript
var p = document.createElement("p");        
var h1 = document.createElement("h1");      
var txt = document.creatTextNode("Hello World");        // 不能包含任何html标签
p.appendChild(txt);     // 把文本节点添加到段落节点中
h1.appendChild(p);      // 把段落节点添加到标题节点中
document.body.appendChild(h1);      //把标题节点添加到body节点中
// <body> <h1> <p> Hello World </p> </h1> </body>
```

- childNode（）

  复制一个节点，该方法能给节点创建一个副本，被复制的节点和元节点具有完全一样的nodeTpye和nodeName属性值

```javascript
var ele = node.childNode(deep);		// ele 表示返回一个复制的节点  node表示一个已经存在的节点，也就是被复制的节点
// deep 是一个布尔值
// 为true时 复制的节点将包含所有子节点内容
// 为flase时 复制的节点仅包含指定对象本身，不包含任何子节点，如果被复制的节点是一个元素节点，其包含的所有文本将不会被复制，因为文本节点时子节点，但是属性节点将会被复制
// 由于复制后会完整地复制原节点的所有属性，但是id不能重复，所以应在复制后修改某个节点的id值
```

- appendChild（）

  把新创建的节点插入到某个指定元素下，新增节点可以被添加到文档的任何一个元素下面，默认将位于元素所包含的全部子节点的末尾

```javascript
var new_e = ele.appendChild(new_e);		// 把参数节点对象new_e增加到ele元素的下面并返回新增节点的引用
```

- insertBefore（）

  把一个指定的节点插入到给定元素中，可让所插入的节点位于指定元素的指定子节点的前面，而不是放在最后面

```javascript
var new_e = ele.insertBefore(new_e,traget_node);	// 把new_e节点对象添加到到ele的元素的子节点traget_node前面，并返回这个新增节点引用
// 如果不给traget_node 则自动放在最后位 作用与appendChild()一样
```

可以用appendChild和inserBefore来移动指定元素位置 移动时会先删掉原来的再插入到新的位置

- removeChild（）

```javascript
var noede = ele.removeChild(node);		// 删除ele元素节点中的node(node所有子节点会一并删除) 返回被删除的节点的对象引用
```

- replaceChild（）

```javascript
var oldNode = ele.replaceChild(newNode,oldNode);	// 用newNode节点替换ele的子节点oldNode   返回被替换的oldNode对象的引用
```

**操作属性**

- getAttribute（）

  获取元素的指定属性值

```javascript
// <div id = "red"></div>
var redbox = document.getElementById("red");
var strings = redbox.getAttribute("id");       //返回"red"
```

- setAttribute（）

  设置节点属性值

```javascript
ele.setAttribute(name,value);     //为ele元素修改(新添)name属性的值为value
```

- removeAttribute（）

  删除指定的属性

```javascript
ele.removeAttribute(name);      //删除ele元素的 name 属性
```



---

### 事件

| 内置方法    | 触发掉件               | 支持元素                                     |
| ----------- | ---------------------- | -------------------------------------------- |
| onabort     | 图像加载时被中断       | img、object                                  |
| onblur      | 元素失去焦点           | button、input、label、select、textarea、body |
| onchange    | 用户改变域的内容       | input、select、textarea                      |
| onclick     | 鼠标点击某个对象       | 大部分                                       |
| ondblclick  | 鼠标双击某个对象       | 大部分                                       |
| onerror     | 加载图像时发生某个错误 | image、object                                |
| onfocus     | 元素获得焦点           | button、input、label、select、textarea、body |
| onkeydown   | 某键盘键被按下         | 所有表单元素、body                           |
| onkeypress  | 某键盘键按下后释放     | 所有表单元素、body                           |
| onkeyup     | 某键盘键被松开         | 所有表单元素、body                           |
| onload      | 文档或图像加载完毕     | body、frameset、iframe、img、object          |
| onmousedown | 某个鼠标按键被按下     | 大部分                                       |
| onmousemove | 鼠标被移动             | 大部分                                       |
| onmouseout  | 鼠标从某元素移开       | 大部分                                       |
| onmouseover | 鼠标被移动到某元素上   | 大部分                                       |
| onkeyup     | 某个鼠标按键被松开     | 大部分                                       |
| onreset     | 表单被重置             | form                                         |
| onresize    | 窗口或框架被调整尺寸   | body、frameset、iframe                       |
| onselect    | 文本被选定             | input、textarea                              |
| onsubmit    | 表单被提交             | form                                         |
| onunload    | 卸载文档或框架集       | body、frameset、iframe                       |

**绑定事件**

静态绑定事件（把脚本直接作为属性值直接赋给事件属性）

```html
<button onclick="alert("你单击了一次");">按钮</button>
```

动态绑定事件（直接为页面元素附加事件 不破坏HTML结构 灵活）

```html
<body>
	<button id="btn">按钮</button>
    
    <script>
	var button = docunment.getElementById("btn");
	button.onclick = function(){
	alert("你单击了一次");
	}
	</script>
</body>    
```

**注册事件**

 addEventListener()

可以为多个对象注册相同的事件处理函数，也可以为同一个对象注册多个事件处理函数（要求事件类型不一样）

```
addEventListener(String type, Function listener, boolean useCapture);

type:注册事件的类型名 与事件属性不同，事件类型名没有事件属性名on的前缀 如事件属性onclick的事件类型为click

listenner:监听函数 事件处理函数  在指定类型的事件发生时调用该函数 调用该函数时 默认传递给它的唯一参数是Event对象

useCapture:如果为ture 则指定的事件处理函数将在事件传播的捕获阶段触发 如果为false 则事件处理函数将在冒泡阶段触发
```

**注销事件**

removeEventListener()

只能注销addEventListener()注册的事件 直接写在元素属性上的事件无法删除

```
removeEventListener(String type, Function listener, boolean useCapture);
```



------

### 富文本编辑框

使用div元素添加`contenteditable`属性，配合`document.execCommand()`更改选中的字体格式

```jsx
<div contenteditable style='border:1px solid #dedede; width:500px; min-height:300px'>	{/* 自带自适应，内容过多时会自动扩充高度 */}
	<img ...>	{/* 可以插入图像等元素 */}
</div>
```





----

## 标签

### 元素

#### \<textarea\>

文本框

无法自动实现的功能：

- 文本框随着文本输入内容自适应拉长
- 传入图片时显示的是html标签，无法正常显示

---

#### \<a>

如果不想被点击则设置

```jsx
<a href="javascript:;"/>		// 表示点击时什么也不干
```

