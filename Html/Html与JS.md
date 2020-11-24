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

有很多种实现方式，目前最简单的是一个已经封装好的 API —— `IntersectionObserver`，一个浏览器原生提供的构造函数

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

`IntersectionObserverEntry`对象提供目标元素的信息，一共有六个属性

- time：可见性发生变化的时间，是一个高精度时间戳，单位为毫秒
- target：被观察的目标元素，是一个 DOM 节点对象
- rootBounds：根元素的矩形区域的信息，`getBoundingClientRect()` 方法的返回值，如果没有根元素（即直接相对于视口滚动），则返回 `null`
- boundingClientRect：目标元素的矩形区域的信息
- intersectionRect：目标元素与视口（或根元素）的交叉区域的信息
- intersectionRatio：目标元素的可见比例，即 `intersectionRect`占 `boundingClientRect` 的比例，完全可见时为 `1`，完全不可见时小于等于 `0`

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

