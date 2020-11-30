### DTD

页面具有 DTD，或者说指定了 `DOCTYPE` 时，使用 `document.documentElement`

页面不具有 DTD，或者说没有指定了 `DOCTYPE`时，使用 `document.body`

```
这两个的使用功能一样，都是为了获取渲染区根元素DOM<Body>，但是是否指定DOCTYPE会直接影响他们的使用，如果指定了则用document.body获取不到值，而现在一般都会指定，因此绝大部分情况都是用第一种
```



----

### 元素范围

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

### 判断元素进入视口

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
        // 如果页尾栏不可见，就返回，如果可见就继续请求数据
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

### 事件捕获与冒泡

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

### 默认事件

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



