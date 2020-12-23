### 概念

#### DTD

页面具有 DTD，或者说指定了 `DOCTYPE` 时，使用 `document.documentElement`

页面不具有 DTD，或者说没有指定了 `DOCTYPE`时，使用 `document.body`

```
这两个的使用功能一样，都是为了获取渲染区根元素DOM<Body>，但是是否指定DOCTYPE会直接影响他们的使用，如果指定了则用document.body获取不到值，而现在一般都会指定，因此绝大部分情况都是用第一种
```

---

#### 富文本

使用div元素添加`contenteditable`属性，配合`document.execCommand()`更改选中的字体格式

```jsx
<div contenteditable style='border:1px solid #dedede; width:500px; min-height:300px'>	{/* 自带自适应，内容过多时会自动扩充高度 */}
	<img ...>	{/* 可以插入图像等元素 */}
</div>
```



----

### 操作DOM

```
Document        //文档
Element         //元素节点 如<html> <head> <body> <p> <h1> 等
Text            //文本节点 <html> <head> <body> <ul> 等结构元素不会直接包含任何文本节点
Attribute		// 属性节点 如<img src="images/1.gif" title="个人相册" /> title就是属性节点
```

#### 获取节点

- **getElementsByID()**

  根据id获取节点，还要tap，class，name等，不列举

- **document.querySelector()**

  根据选择器获取节点

```javascript
document.querySelector('#id')
```

- **childNodes()**

  获取指定元素的所有子节点，返回值为一个数组，里面装的是节点对象（即使某个元素只有一个子节点 childNode属性也会返回一个节点数组

```javascript
var tag = document.getElementsByTagName("ul");      //获取网页文档中"name = ul"的所有节点对象
var a = tag[0].childNodes;      //获取第一个 "ul" 对象
alert(a[0].nodeName);       
```

- **haschildNodes()**

  判断某个元素是否包含子节点

- **firstChild()**

```javascript
node.childNodes[0] == node.firstChild
```

- **lastChild()**

```javascript
node.childNode[node.childNodes.length-1] == node.lastChild
```

- **parentNode()**

  返回指定节点父节点

  永远返回的是元素节点，因为只有元素节点才能包含子节点 ，document没有父节点 将返回null 

- **nextSibling()**

  返回一个指定节点的下一个相邻节点

- **previousSibling()**

  返回一个指定节点的上一个相邻节点

- **nodeName**

  返回节点名称

```
如果是元素节点 则返回值为标签名称 标签名称永远是大写
如果是属性节点 则返回值为属性的名称
如果是文本节点 则返回值永远是#text标识符
如果是文档节点 则返回值永远是#document标识符
```

- **nodeType**

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

---

#### 操作节点

- **createElement()**

  根据参数指定的名称创建一个新的元素节点并返回新建元素节点对象

```javascript
 var ele = document.createElement("element");  
// 使用createElement()创建的新节点元素不会自动添加到文档结构中，此时节点还没有NodeParent属性，只是存储在内存中的DocumentFragment对象，仅在JS上下文中有效
// 如果需要把这个DocumentFragment对象添加到文档中 则需要使用appendChild()、insertBefore()或replaceChild()方法实现
document.body.appendChild(ele);     //增加节点到body元素下
```

- **creatTextNode()**

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

- **childNode()**

  复制一个节点，该方法能给节点创建一个副本，被复制的节点和元节点具有完全一样的nodeTpye和nodeName属性值

```javascript
var ele = node.childNode(deep);		// ele 表示返回一个复制的节点  node表示一个已经存在的节点，也就是被复制的节点
// deep 是一个布尔值
// 为true时 复制的节点将包含所有子节点内容
// 为flase时 复制的节点仅包含指定对象本身，不包含任何子节点，如果被复制的节点是一个元素节点，其包含的所有文本将不会被复制，因为文本节点时子节点，但是属性节点将会被复制
// 由于复制后会完整地复制原节点的所有属性，但是id不能重复，所以应在复制后修改某个节点的id值
```

- **appendChild()**

  把新创建的节点插入到某个指定元素下，新增节点可以被添加到文档的任何一个元素下面，默认将位于元素所包含的全部子节点的末尾

```javascript
var new_e = ele.appendChild(new_e);		// 把参数节点对象new_e增加到ele元素的下面并返回新增节点的引用
```

- **insertBefore()**

  把一个指定的节点插入到给定元素中，可让所插入的节点位于指定元素的指定子节点的前面，而不是放在最后面

```javascript
var new_e = ele.insertBefore(new_e,traget_node);	// 把new_e节点对象添加到到ele的元素的子节点traget_node前面，并返回这个新增节点引用
// 如果不给traget_node 则自动放在最后位 作用与appendChild()一样
```

可以用appendChild和inserBefore来移动指定元素位置 移动时会先删掉原来的再插入到新的位置

- **removeChild()**

```javascript
var noede = ele.removeChild(node);		// 删除ele元素节点中的node(node所有子节点会一并删除) 返回被删除的节点的对象引用
```

- **replaceChild()**

```javascript
var oldNode = ele.replaceChild(newNode,oldNode);	// 用newNode节点替换ele的子节点oldNode   返回被替换的oldNode对象的引用
```

----

#### 操作属性

- **getAttribute()**

  获取元素的指定属性值

```javascript
// <div id = "red"></div>
var redbox = document.getElementById("red");
var strings = redbox.getAttribute("id");       //返回"red"
```

- **setAttribute()**

  设置节点属性值

```javascript
ele.setAttribute(name,value);     //为ele元素修改(新添)name属性的值为value
```

- **removeAttribute()**

  删除指定的属性

```javascript
ele.removeAttribute(name);      //删除ele元素的 name 属性
```



----

### 事件

#### 内置事件

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

**注册事件（优先使用）**

`addEventListener()`

可以为多个对象注册相同的事件处理函数，也可以为同一个对象注册多个事件处理函数（要求事件类型不一样）

```markdown
addEventListener(String type, Function listener, boolean useCapture);
	type:注册事件的类型名 与事件属性不同，事件类型名没有事件属性名on的前缀 如事件属性onclick的事件类型为click
	listenner:监听函数 事件处理函数  在指定类型的事件发生时调用该函数 调用该函数时 默认传递给它的唯一参数是Event对象
	useCapture:如果为ture 则指定的事件处理函数将在事件传播的捕获阶段触发 如果为false 则事件处理函数将在冒泡阶段触发
```

**注销事件**

`removeEventListener()`

只能注销 addEventListener() 注册的事件 直接写在元素属性上的事件无法删除

```
removeEventListener(String type, Function listener, boolean useCapture);
```

----

#### 事件捕获与冒泡

DOM 事件先捕获，后冒泡，默认执行顺序是从子元素开始往上触发

```html
<div id='div'>
    <div id='div1'>
        <div id='div2'>
            <div id='div3'></div>
        </div>
    </div>
</div>
```

```js
div1.οnclick=function(){ alert("div1") }
div2.οnclick=function(){ alert("div2") }
div3.οnclick=function(){ alert("div3") }
```

默认冒泡事件，点击 div3 弹出顺序为 `div3 → div2 → div1` 

如果使用绑定事件监听函数 `addEventListener()` 来添加事件，则可以更改事件处理机制

```js
// 指定事件处理机制，默认false，事件冒泡，true为事件捕获
// 此时点击 div3，弹除顺序为 div1 → div2 → div3
div1.addEventListener('click',function(event){
	alert("div1")
},true);
div2.addEventListener('click',function(event){
	alert("div2")
},true);
div3.addEventListener('click',function(event){
	alert("div3")
},true);
```

**stopPropagation**

在事件方法中直接使用 `stopPropagation` 来取消冒泡

```js
div3.addEventListener('click',function(event){
    event.stopPropagation()
    alert("div3")
});
```

**事件委托**

```js
window.onload = function() {
    document.getElementById("div").addEventListener("click", function() {
        let eTarget = event.target;
        switch(eTarget.id) {
            case "div1":
                console.log("点击的div1")
                break;

            case "div2":
                console.log("点击的div2")
                break;

            case "div3":
                console.log("点击的div3")
                break;
        }
        event.stopPropagation()
    })
}
```

---

#### 默认事件

在HTML中,基本上所有的事件都有一个默认行为，如

- Submit按钮: 在form表单中的，提交form表单中的数据到服务器
- Button: 在PC中不做任何事情， 在手机浏览器中，若是在form中,则是submit
- a标签: 默认将当前页面跳转为a标签中href的地址

**阻止默认事件**

在事件方法中执行 `preventDefault()`

```js
function eventHandle(event) {
	event.preventDefault()
}
```



---

### 元素范围

#### 滚轮与视口

每个HTML元素都具有 `clientHeight` 、`offsetHeight` 、`scrollHeight` 、`offsetTop` 、`scrollTop` 这5个和元素高度、滚动、位置相关的属性（以高度为例，宽度如clientWidth同理）

**clientHeight、offsetHeight**

<span style="color:#FF4040">这两个属性和元素的滚动、属性没有关系，它代表的是元素的高度</span>

- clientHeight：

  包括padding但不包括border、水平滚动条、margin的元素的高度

  对于inline的元素这个属性一直是0，单位px，只读元素

<img src="https://img.mukewang.com/58f5b2fa00012ea604110247.jpg" style="margin:0"/>

- offsetHeight：

  包括padding、border、水平滚动条，但不包括margin的元素的高度

  对于inline的元素这个属性一直是0，单位px，只读元素

<img src="https://img.mukewang.com/58f5b32e0001a2fa04110247.jpg" style="margin:0"/>

-----

**scrollHeight 、offsetTop、scrollTop**

<span style="color:#FF4040">和滚动情况有关，当本元素的子元素比本元素高且 `overflow=scroll` 时，本元素会出现滚动条</span>

- scrollHeight：

  代表包括当前不可见部分的元素的高度，单位px，只读元素

  可见部分的高度其实就是 `clientHeight`，也就是 `scrollHeight>=clientHeight` 恒成立

  在有滚动条时讨论 `scrollHeight` 才有意义，在没有滚动条时 `scrollHeight==clientHeight` 恒成立

<img src="https://img.mukewang.com/58f5b3700001627d05110413.jpg" style="margin:0"/>

- scrollTop：

  代表在有滚动条时，滚动条向下滚动的距离也就是元素顶部被遮住部分的高度，单位px，可读可设置

  在没有滚动条时 `scrollTop==0` 恒成立

<img src="https://img.mukewang.com/58f5b3e80001873105110413.jpg" style="margin:0"/>

- offsetTop: 

  当前元素顶部距离最近父元素顶部的距离,和有没有滚动条没有关系，单位px，只读元素

<img src="https://img.mukewang.com/58f5b4050001e99204480302.jpg" style="margin:0;width:300px"/>

---

**如果要获取页面范围**

- 网页可见区域高：`document.documentElement.clientHeight`

- 网页正文全文高：`document.documentElement.scrollHeight`
- 网页可见区域高（包括边线的高）：`document.documentElement.offsetHeight`
- 网页被卷去的高：`document.documentElement.scrollTop`
- 屏幕分辨率高：`window.screen.height`



---

#### 判断元素进入视口

```
http://www.ruanyifeng.com/blog/2016/11/intersectionobserver_api.html
```

```
常见使用场景：图片懒加载，在未滑动到图片范围的时候不加载图片，判断图片进入视口范围后开始加载
```

有很多种实现方式，目前最简单的是一个已经封装好的 API —— `IntersectionObserver`（交叉观察），一个浏览器原生提供的构造函数

```js
// callback是可见性变化时的回调函数，option是配置对象（该参数可选）
var io = new IntersectionObserver(callback, option);
```

构造函数的返回值是一个观察器实例。实例的 `observe` 方法可以指定观察哪个 DOM 节点

```js
// 开始观察
io.observe(document.getElementById('example'));

// 停止观察
io.unobserve(element);

// 关闭观察器
io.disconnect();
```

如果要观察多个节点，就要多次调用这个方法

```js
io.observe(elementA);
io.observe(elementB);
```

目标元素的可见性变化时，就会调用观察器的回调函数`callback`，`callback`一般会触发两次。一次是目标元素刚刚进入视口（开始可见），另一次是完全离开视口（开始不可见）

```js
// entries 是一个数组，每个成员都是一个IntersectionObserverEntry对象
// 如果同时有两个被观察的对象的可见性发生变化，entries 数组就会有两个成员
var io = new IntersectionObserver(
    entries => {
        console.log(entries);
    }
);
```

`IntersectionObserverEntry`对象提供目标元素的信息，一共以下属性

- time：可见性发生变化的时间，是一个高精度时间戳，单位为毫秒
- target：被观察的目标元素，是一个 DOM 节点对象
- rootBounds：根元素的矩形区域的信息，`getBoundingClientRect()` 方法的返回值，如果没有根元素（即直接相对于视口滚动），则返回 `null`
- boundingClientRect：目标元素的矩形区域的信息
- intersectionRect：目标元素与视口（或根元素）的交叉区域的信息
- intersectionRatio：目标元素的可见比例，即 `intersectionRect`占 `boundingClientRect` 的比例，完全可见时为 `1`，完全不可见时小于等于 `0`
- isIntersecting：目标是否可见
- isVisible：目标是否隐藏

<img src="https://img-blog.csdnimg.cn/20201125042753709.png" style="margin:0"/>



<img src="https://upload-images.jianshu.io/upload_images/4060631-dfe6e9f2e933ae23.png" style="margin:0;width:900px"/>

```html
<body>
  <div id="a"></div>
  <div id="b"></div>
  <div id="c"></div>
</body>
```

```js
var io = new IntersectionObserver(
    entries => {
      entries.forEach(i => {
        console.log(i.time);
        console.log(i.target);
        console.log(i.intersectionRatio);
        console.log(i.rootBounds);
        console.log(i.boundingClientRect);
        console.log(i.intersectionRect);
      });
    },
    {
        /* Using default options. Details below */
    }
);

// 观察的对象有两个，因此上面entries数组的长度为2
io.observe(document.querySelector('#a'));
io.observe(document.querySelector('#b'));
```

`IntersectionObserver`构造函数的第二个参数是一个配置对象。它可以设置以下属性

- threshold：决定了什么时候触发回调函数。它是一个数组，每个成员都是一个门槛值，默认为[0]，即交叉比例（`intersectionRatio`）达到0时触发回调函数

  ```js
  // 表示当目标元素 0%、25%、50%、75%、100% 可见时，会触发回调函数
  new IntersectionObserver(
    entries => {/* ... */}, 
    {
      threshold: [0, 0.25, 0.5, 0.75, 1]
    }
  );
  ```

- root：指定目标元素所在的容器节点（即根元素），容器元素必须是目标元素的祖先节点

- rootMargin：定义根元素的`margin`，用来扩展或缩小 `rootBounds`这个矩形的大小，从而影响 `intersectionRect` 交叉区域的大小

  ```js
  // 很多时候，目标元素不仅会随着窗口滚动，还会在容器里面滚动，容器内滚动也会影响目标元素的可见性，因此有必要进行额外设置
  var opts = { 
    root: document.querySelector('.container'),
    rootMargin: "500px 0px" 
  };
  
  var observer = new IntersectionObserver(
    callback,
    opts
  );
  ```

**实现图片懒加载**

html标签处理，将图片的真实url设置为自定义属性 `data-src` ，而`src`属性为占位图（下面代码中是一个文件很小的loading的动画gif），当元素可见时候用 data-src  的内容替换 src，从而达到懒加载

```js
<div class="img"><img data-src="https://gtd.alicdn.com/sns_logo/i1/TB17LgBNFXXXXaSXVXXSutbFXXX.jpg_240x240xz.jpg" src="https://img.alicdn.com/tps/i3/T1QYOyXqRaXXaY1rfd-32-32.gif">
```

```js
// 实例化 默认基于当前视窗
const io = new IntersectionObserver(callback)  

let ings = document.querySelectorAll('[data-src]')

function callback(entries){  
    entries.forEach((item) => { 
        if(item.isIntersecting){ // 当前元素可见
            item.target.src = item.target.dataset.src  // 替换src
            io.unobserve(item.target)  // 停止观察当前元素 避免不可见时候再次调用callback函数
        }   
    })
}

// io.observe接受一个DOM元素，添加多个监听 使用forEach
imgs.forEach((item)=>{  
    io.observe(item)
})
```

**实现无限滚动**

无限滚动时，最好在页面底部有一个页尾栏一旦页尾栏可见，就表示用户到达了页面底部，从而加载新的条目放在页尾栏前面，这样做的好处是，不需要再一次调用`observe()`方法，现有的`IntersectionObserver` 可以保持使用

```js
var intersectionObserver = new IntersectionObserver(
    function (entries) {
        // entries[0]代表页尾栏元素，如果不可见，就返回，如果可见就继续请求数据
        if (!entries[0].isIntersecting) return;
        loadItems(10);	// 这里指的是重新去请求10条数据的方法
        console.log('Loaded new items');
    });

// 开始观察
intersectionObserver.observe(
    document.querySelector('.scrollerFooter')
);
```



-----



