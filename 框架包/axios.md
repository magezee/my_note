## axios

### 简介

```
可在rap2建立自己的后端数据去模拟请求
http://rap2.taobao.org/
```

发送Ajax请求：原生XHR → jquery ajax  → fetch → axios

**原生XHR**

原生XHR是最传统的Ajax

```js
var xhr = new XMLHttpRequest();
xhr.open('GET', url);	// 连接服务器（打开和服务器的连接）
xhr.onreadystatechange = function() {
    if (xhr.readyState === 4 && xhr.status === 200) {
        console.log(xhr.responseText)   // 从服务器获取数据
    }
}
xhr.send()	// 发送请求
```

**jquery ajax**

是对原生ajax的封装

```js
$.ajax({
    type: 'POST',
    url: url,
    data: data,
    dataType: dataType,
    success: function() {},
    error: function() {}
})
```

**fetch**

```
yarn add isomorphic-fetch
或者
yarn add node-fetch
```

```js
const fetch = require('isomorphic-fetch')
// or
const fetch = require('node-fetch')
```

```js
fetch('http://localhost:10340/content/render',{
  method: 'POST',
  mode: 'cors',
  body: JSON.stringify({
    tempId: 'Payment',
    data: {}
  })
}).then(fileData => {
  console.log(fileData)
})
```

用于替代 jquery ajax

fetch不是ajax的进一步封装，而是原生js，Fetch函数就是原生js，没有使用XMLHttpRequest对象

参数：第一个参数是请求地址，第二个参数是可选参数，用于控制不同配置的 init 对象

```js
fetch(url).then(response => response.json())
  .then(data => console.log(data))
  .catch(err => console.log("Oops, error", err))
```

```js
// 通过Request构造器函数创建一个新的请求对象
var req = new Request(URL, {method: 'GET', cache: 'reload'})

fetch(req).then(res => {
    if(res.ok) {
        return res.json();
    }
})
    .then(data => {
    	...
	})
    .catch(err => {
    	...
	})

// async函数写法
async function demo() {
    try{
        let res = await fetch(req)
        if(res.ok) {
            let data = await res.json() 
            ...
        }
    } catch {
        ...
    }   
}
```

与 ajax 的区别：

- 当接收到一个代表错误的HTTP状态码时，从`fetch()`返回的Promise不会被标记为reject，即使该HTTP状态码是404或500，仅当网络故障或请求被阻止时才会标记为reject
- 在默认情况下 fetch不会从服务端发送或接收任何cookie，如果网页依赖于用户session，则会导致未经认证的请求（可以通过设置credentials配置发送cookie）

由于fetch是原生的js，因此很多时候并不能直接使用，需要对它进行再次封装

```js
// jquery ajax
$.post(url, {name: 'test'})

// 同一个功能用fetch进行封装
// 由于fetch是比较底层的API，所以需要手动将参数拼接成'name=test'的格式
fetch(url, {
    method: 'POST',
    body: Object.keys({name: 'test'}).map((key) => {
        return encodeURIComponent(key) + '=' + encodeURIComponent(params[key]);
    }).join('&')
})
```

因此实际上fetch并不好用，且目前还不支持超时控制等功能，因此出现了axios

**axios**

Axios是一个异步请求技术，用于代替ajax（因为ajax无法完成前后端分离的系统架构），是一个基于Promise 用于浏览器和 nodejs 的 HTTP 客户端，本质上也是对原生XHR的封装，只不过它是Promise的实现版本，符合最新的ES规范

```
基于XHMLHttpRequest对象发起的请求的都是异步请求
异步请求特点：请求之后页面不刷新，响应回来更新的是页面的局部，多个请求之间互不影响，并行执行
```

具有功能：

- 从浏览器中创建 XMLHttpRequest
- 支持 Promise API
- 客户端支持防止CSRF

- 提供了一些并发请求的接口（重要，方便了很多的操作）
- 从 node.js 创建 http 请求
- 拦截请求和响应
- 转换请求和响应数据
- 取消请求
- 自动转换JSON数据

```
防止CSRF:就是让每个请求都带一个从cookie中拿到的key, 根据浏览器同源策略，假冒的网站是拿不到用户cookie中得key的，这样，后台就可以轻松辨别出这个请求是否是用户在假冒网站上的误导输入，从而采取正确的策略
```



------

### 发送请求

#### get请求

```jsx
import axios from 'axios';

axios.get('http://www.baidu.com/user?ID=12345')		// get请求网址( 若配置了baseUrl 则可只用写附加网址，如baseUrl为http://www,baidu.com，则get('/user?ID=12345‘) )
    .then(function (response) {
    	console.log(response);
  	})
  	.catch(function (error) {
    	console.log(error);
  	})


// 上面的请求也可以这样写
axios.get('/user', {
	params: {
		ID: 12345
    }
})
	.then(function (response) {
    	console.log(response);
  	})
 	 .catch(function (error) {
    	console.log(error);
  	});
```

通过回调获取的响应信息response是一个json对象，包含如下信息：

```jsx
{	
	data: {},			// `data` 由服务器提供的响应 	
	status: 200,		// `status` 来自服务器响应的 HTTP 状态码 	
	statusText: 'OK',	// `statusText` 来自服务器响应的 HTTP 状态信息	
  	headers: {},		// `headers` 服务器响应的头
	config: {},			// `config` 是为请求提供的配置信息
	request: {}			// 'request' 是生成这个响应的请求，它是node.js(重定向)中的最后一个ClientRequest实例以及浏览器的XMLHttpRequest实例
}
```

使用.then接收指定内容：

```jsx
axios.get('/user/12345')
	.then(function(response) {
        console.log(response.data);
    	console.log(response.status);
    	console.log(response.statusText);
    	console.log(response.headers);
    	console.log(response.config);
	});
```

#### post请求

```jsx
axios.post('/user', {
	firstName: 'Fred',
    lastName: 'Flintstone'
  })
	.then(function (response) {
    	console.log(response);
  	})
  	.catch(function (error) {
   	 	console.log(error);
	});
```

#### 执行多个并发请求

```jsx
function getUserAccount() {
	return axios.get('/user/12345');
}

function getUserPermissions() {
	return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
	.then(axios.spread(function (acct, perms) {
    // 两个请求现在都执行完成时才执行then
  }));
```

----

### 创建实例

#### 配置默认值

配置会以一个优先顺序进行合并，后者将优先于前者，冲突将会覆盖

- 在 `lib/defaults.js` 找到的库的默认值
- 实例的 `defaults` 属性
- 请求的 `config` 参数

全局的 axios 默认值

```jsx
axios.defaults.baseURL = 'https://api.example.com';
```

自定义实例默认值

```jsx
const instance = axios.create({
  baseURL: 'https://www.baidu.com'
});
// 等同于 
const instance = axios.create()
instance.baseURL = 'https://www.baidu.com'
```

```jsx
// 使用由库提供的配置的默认值来创建实例
// 此时超时配置的默认值是 `0`
var instance = axios.create();

// 覆写库的超时默认值
// 现在，在超时前，所有请求都会等待 2.5 秒
instance.defaults.timeout = 2500;

// 为已知需要花费很长时间的请求覆写超时设置
instance.get('/longRequest', {
  timeout: 5000
});
```

#### 自定义实例请求配置

**常用：**

```jsx
{ 	
	url: '/user', 	// 用于请求的服务器 URL    
        
 	method: 'get',  // 创建请求时使用的方法
        
  	baseURL: 'https://some-domain.com/api/',	// baseURL将自动加在url前面，除非url是一个绝对URL
  	
   	headers: {'X-Requested-With': 'XMLHttpRequest'},	//发送自定义请求头   
        
	timeout: 1000,    // 定请求超时的毫秒数(0 表示无超时时间)，超时则请求中断
  	
    // 即将与请求一起发送的URL参数，必须是一个无格式对象(plain object)或 URLSearchParams对象
 	params: {	
    	ID: 12345
  	},  
    
    // 作为请求主体被发送的数据，只适用于PUT, POST和PATCH请求,在没有设置transformRequest时，必须是string、plain object、ArrayBuffer、ArrayBufferView、URLSearchParams类型之一
   	data: {	 	
    	firstName: 'Fred'
  	},
        
    // 定义对于给定的HTTP,响应状态码是resolve还是reject，如果返回true，promise将被resolve，否则rejecte  
  	validateStatus: function (status) {    
		return status >= 200 && status < 300; // default
  	},
	
    // 定义代理服务器的主机名称和端口，auth表示 HTTP 基础验证应当用于连接代理，并提供凭据，这将会设置一个 Proxy-Authorization头，覆写掉已有的通过使用header设置的自定义Proxy-Authorization头
    proxy: {
    	host: '127.0.0.1',
    	port: 9000,
   		auth: {
      		username: 'mikeymike',
     		 password: 'rapunz3l'
    	}
    },
               
}
```

**其他：**

```jsx
{
  // `transformRequest` 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data, headers) {
    // 对 data 进行任意转换处理
    return data;
  }],
         
  // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // `paramsSerializer` 是一个负责 `params` 序列化的函数
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },
 
   // `withCredentials` 表示跨域请求时是否需要使用凭证
  withCredentials: false, // default

  // `adapter` 允许自定义处理请求，以使测试更轻松，返回一个 promise 并应用一个有效的响应 
  adapter: function (config) {
    // ... 
  },
    
  // `auth` 表示应该使用 HTTP 基础验证，并提供凭据，这将设置一个 `Authorization` 头，覆写掉现有的任意使用 `headers` 设置的自定义 `Authorization`头
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

   // `responseType` 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // default

  responseEncoding: 'utf8', // default

  xsrfCookieName: 'XSRF-TOKEN', // default

  xsrfHeaderName: 'X-XSRF-TOKEN', // default

   // `onUploadProgress` 允许为上传处理进度事件
  onUploadProgress: function (progressEvent) {
    // ...
  },

  // `onDownloadProgress` 允许为下载处理进度事件
  onDownloadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

   // `maxContentLength` 定义允许的响应内容的最大尺寸
  maxContentLength: 2000,

  maxRedirects: 5, // default

  socketPath: null, // default

  // `httpAgent` 和 `httpsAgent` 分别在 node.js 中用于定义在执行 http 和 https 时使用的自定义代理。允许像这样配置选项：
  // `keepAlive` 默认没有启用
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // `cancelToken` 指定用于取消请求的 cancel token
  cancelToken: new CancelToken(function (cancel) {
  })
}
```

#### 错误处理

```jsx
axios.get('/user/12345')
  .catch(function (error) {
    if (error.response) {
      console.log(error.response.data);
      console.log(error.response.status);
      console.log(error.response.headers);
    } else if (error.request) {
      console.log(error.request);
    } else {
      console.log('Error', error.message);
    }
    console.log(error.config);
  });
```

----

### 拦截器

在请求或响应被 `then` 或 `catch` 处理前拦截它们，先处理拦截器的内容再往下执行

请求拦截器实际上做的操作：拿到请求内容，作出一系列加工操作后，返回请求内容，发送加工后的请求内容，响应拦截器同理

- 请求拦截器`axios.interceptors.request.use()`的参数为一个方法，该方法会返回一个参数（默认声明config接收），这个参数包含了请求的信息（如请求头，报文等）

- 响应拦截器`axios.interceptors.response.use()`的参数为一个方法，该方法会返回一个参数（默认声明response接收），这个参数包含了请求或响应的（如响应头，报文等）

```jsx
// 添加请求拦截器
axios.interceptors.request.use(function (config) {	
    // 在发送请求之前做些什么
    return config;	// 需要将该加工后的请求内容返回
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error)
  })

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  })
```

一个请求的基本使用：

```jsx
import axios from 'axios'
const isDev = process.env.NODE_ENV === 'development'    // webpack自动生成的process.env.NODE_ENV
// 在node中，有全局变量process表示的是当前的node进程，process.env包含着关于系统环境的信息，但是process.env中并不存在NODE_ENV这个东西，NODE_ENV是用户一个自定义的变量，在webpack中它的用途是判断生产环境或开发环境的依据的

const service = axios.create({
    baseURL: isDev ? 'http://rap2.taobao.org:38080/app/mock/252971' : ''
})

// 请求拦截器
service.interceptors.request.use((config) => {
    config.data = Object.assign({},config.data, {
        // 需要添加的请求参数
        authToken: "请求过去的必选参数"          
    })
    return config
})

// 响应拦截器
service.interceptors.response.use((resp) => {
    if (resp.data.code === 200) {
        return resp.data.data		// 若请求成功，返回数据
    } else {
        // 全局处理错误
    }
})


export const getArticles = (offset = 0, limited = 10) => {		// 定义固定的post传参
    return service.post('/api/v1/articleList',{
        offset,
        limited
    })
}
```

移除拦截器

```jsx
const myInterceptor = axios.interceptors.request.use(function () {})
axios.interceptors.request.eject(myInterceptor)
```

为自定义 axios 实例添加拦截器

```jsx
const instance = axios.create();
instance.interceptors.request.use(function () {})
```

**用拦截器发送token**

```jsx
// 从服务器返回的token会存储到localStorage中
axios.interceptors.request.use(config => {	
	let token = localStorage.getItem('token')
    token && (config.headers.Authorization = token)
    return config
}, error => {
    return Promise.reject(error)
})
```





------

### 取消请求

可以使用同一个`cancel token `取消多个请求

使用场景：在发送第二次请求的时候如果第一次请求还未返回，则取消第一次请求，以保证后发送的请求返回的数据不会被先发送的请求覆盖

取消方式一：

```jsx
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/user/12345', {
  cancelToken: source.token    // 必须对请求进行cancelToken设置
})
  .catch(function (thrown) {	// 如果请求被取消则进入该方法判断	
	if (axios.isCancel(thrown)) {
    	console.log('Request canceled', thrown.message)
    } 
    else {
    // 错误处理
  	}
  });

// 取消上面的请求 message为可选项，必须为String
source.cancel('Operation canceled by the user.');
```

取消方式二：

```jsx
const CancelToken = axios.CancelToken;
let cancel;

axios.get('/user/12345', {
  // 在options中直接创建一个cancelToken对象
  cancelToken: new CancelToken(function executor(c) {
    cancel = c;
  })
});

cancel();	// 取消上面的请求
```



---

### CROS跨域请求

在服务器端开启了CROS跨域后，在请求方也要进行一些配置

**跨域请求分为：简单请求和预检请求**

- 简单请求

  请求满足以下规则则属于简单请求

```markdown
请求方式
	仅限GET、POST、HEAD
请求头字段
	Content-Type的值仅限为text/plain、multipart/form-data、application/x-www-form-urlencoded
请求内容
	XMLHttpRequestUpload对象均没有注册任何事件监听器
	【可使用XMLHttpRequest.upload属性访问XMLHttpRequestUpload对象检查】
	请求中没有ReadableStream对象
```

- 复杂请求

  不满足简单请求的皆为复杂请求，在发送复杂请求前，会发送一个预检请求，预检请求的方式为`OPTION`，通过该请求来得知服务器是否允许跨域请求，不允许则不再发送请求

**Axios实现跨域请求**

在跨域请求中，若服务端返回了正确的跨域响应部首：`Access-Control-Allow-Origin`、`Access-Control-Allow-Method`、`Access-Control-Allow-Headers`, 则跨域请求能正常获取数据

而Axios的请求头`Content-Type`的值默认为`application/json;charset=utf-8`，且 `POST` 请求数据为 `json` 格式，故进行 `POST` 请求会先发出预检请求，若服务端对预检请求的响应为不支持，则请求终止

**解决方式**

- 服务器端：

  在服务器端的`Access-Control-Allow-Methods`头中允许`OPTION`类型的请求方式，且对跨域预检请求的请求进行完整的响应匹配，以通过预检请求完成后续请求发送

- axios：

  将请求头的`Content-Type`设为`application/x-www-form-urlencoded`

  对POST请求发送的数据进行处理，即将`json`格式转换为`application/x-www-form-urlencoded`

  - 一般使用第三方包`qs`的`stringify`对格式进行转换，如果请求数据中某个字段的值为引用类型，则需先通过`JSON.stringify` 处理

  - 通过 `URLSearchParams` 生成`application/x-www-form-urlencoded`格式的数据

```js
import axios from 'axios'
import qs from 'qs'

axios.defaults.withCredentials = true // 若跨域请求需要带 cookie 身份识别
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'

// 方法一：使用qs
axios.interceptors.request.use(req => {
    // 对 post 请求数据进行处理
    if (req.method === 'post') {
        Object.keys(req.data).forEach(item => {
            !isPrimeval(req.data[item]) && (req.data[item] = JSON.stringify(req.data[item]))
        })
        req.data = qs.stringify(req.data)
    }
    return req
}, error => {
    return Promise.reject(error)
})



/* 通过 URLSearchParams 生成 POST 请求数据 */


// 可参考博客：https://juejin.im/post/6844903850684465159
```

```js
import axios from 'axios'

axios.defaults.withCredentials = true 
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'

// 方法二：使用URLSearchParams
async function anInterface (url, params = {}) {
    let data = new URLSearchParams()
    for(let key in params) {
        data.append(params[key])
    }
	return data
}

axios.interceptors.request.use(req => {
    // 对 post 请求数据进行处理
    if (req.method === 'post') {
		data = anInterface(req.data)
        req.data = data
    }
    return req
}, error => {
    return Promise.reject(error)
})
```













