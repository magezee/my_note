### 画布

#### 基础概念

```
参考博客：https://juejin.cn/post/6876411520391970829
```

```
canvas是HTML5新增的元素，通过javascript脚本来完成图形的绘制。它可以用于动画、游戏画面、数据可视化、图片编辑以及实时视频处理等方面
```

每一次绘制过程可以总结为以下步骤

```js
const canvas=document.querySelector('#canvas');   // 获取画布
const ctx=canvas.getContext('2d');				  // 定义画笔
ctx.fillStyle='red';							  // 设置画笔的颜色
ctx.beginPath();								  // 开启一次绘制路径
ctx.moveTo(x,y);								  // 设置起始坐标
ctx.arc(x,y,r,开始弧度,结束弧度,方向); 			  // 定义绘制图形的形状，如定义一个圆弧
ctx.fill()									      // 渲染路径
```

----

**划分区域**

canvas 画布是二维的，屏幕坐标系，以左上角为坐标原点，像素为最小单元，大小默认 `300 * 150`

```js
const  canvas=document.querySelector('#canvas');
canvas.width=300;
canvas.height=150;
```

不要使用 CSS 去定义画布样式，会造成图形失真

```jsx
<canvas id="canvas" width="700" height="800"></canvas>
```

----

**定义画笔**

```js
const  ctx=canvas.getContext('2d');		// 2d画笔
const  ctx=canvas.getContext('webgl');	// 3d画笔
```

----

**绘制图形**

绘制过程中所有的坐标都是针对当前画布的相对屏幕坐标，如果需要绘制下一个无相关的图形，则使用 `ctx.moveTo` 重置点的连接

```jsx
const canvas = document.querySelector('#canvas')
const ctx = canvas.getContext('2d')

ctx.beginPath();	// 新建一条路径集合，生成之后，图形绘制命令被指向到路径集合上生成路径
ctx.moveTo(50,50);	// 声明画笔起始点
ctx.closePath();	// 用直线连接起点和终点形成闭合曲线
ctx.stroke();		// 用指定样式进行渲染
ctx.fill();			// 通过填充路径的内容区域生成实心的图形（调用该方法会自动闭合，因此不需要closePath）
```

------

**路径详情**

路径：

- 路径是子路径的集合
- 一个上下文对象同时只有一个路径，想要绘制新的路径，就要把当前路径置空。
- `beginPath()` 方法可以将当前路径置空，也就是将路径恢复到默认状态，让之后绘制的路径不受以前路径的影响。

子路径

- 子路径是一条只有一个起点的、连续不断开的线
- `moveTo(x,y)`  是设置路径起点的方法，也是创建一条新的子路径的方法
- 路径里的第一条子路径可以无需设置起点，它的起点默认是子路径中的第一个点



<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7cd8c4aa0b18476293d8110aaaf6dade~tplv-k3u1fbpfcp-zoom-1.image" style="margin:0;width:600px">

`ctx.stroke()` 渲染的是当前路径的内容

因此下面代码如果注释掉 `ctx.beginPath()` ，则渲染两个圆，而如果开启，则只渲染子路径2，要渲染每个路径内容需要在每个路径都使用 `ctx.stroke()`

```js
//beginPath开启一条路径
ctx.beginPath();
//子路径1
ctx.moveTo(190,100);
ctx.arc(100,100,90,0,Math.PI*2);
// 控制是否开启另一新路径
// ctx.beginPath();	

//子路径2
ctx.moveTo(400,300);
ctx.arc(300,300,100,0,Math.PI*2);
ctx.stroke();
```

----

**描边**

使用 `ctx.stokenStyle`描述 2D 画笔颜色或者样式的属性，默认颜色黑色

```js
ctx.strokeStyle = "blue";
```

画渐变颜色

```js
for (var i=0;i<6;i++){
  for (var j=0;j<6;j++){
    ctx.strokeStyle = 'rgb(0,' + Math.floor(255-42.5*i) + ',' + Math.floor(255-42.5*j) +')';
    ctx.beginPath();
    ctx.arc(12.5+j*25,12.5+i*25,10,0,Math.PI*2,true);
    ctx.stroke();
  }
}
```

<img src="https://img-blog.csdnimg.cn/20201130160707270.png" style="margin:0;width:200px">



---

#### 绘制图形

**直线**

直线主要是点与点之间连直线

```jsx
lineTo(x,y);
```

```js
// 一个三角形
ctx.beginPath();
ctx.moveTo(50,50);
ctx.lineTo(400,50);
ctx.lineTo(400,300);
ctx.closePath();
ctx.stroke();
```

<img src="https://img-blog.csdnimg.cn/20201130145027416.png" style="margin:0;width:200px">

**圆弧**

```js
arc(圆点x,y,半径，开始弧度，结束弧度，方向)
// 方向true表示顺时针，false表示逆时针
```

```
1°= π / 180°
1rad= 180° / π
360°= 2π
```

```js
ctx.beginPath();
ctx.arc(150,150,100,0,Math.PI*2,true);
ctx.stroke();

ctx.moveTo(400,150);    // 注意这里是移到500px才正常,否则会造成下面的情况
ctx.arc(400,150,100,0,Math.PI*3/2,false);
ctx.stroke();
```

<img src="https://img-blog.csdnimg.cn/2020113015060690.png" style="margin:0;width:350px">

**切线圆弧**

```js
arcTo(x1,y1,x2,y2,半径)
// x2，y2只用于确定方向，其与x1，y1的距离不影响最终结果（距离不为0就行）
```

```js
ctx.beginPath();
ctx.moveTo(50,50);
ctx.arcTo(400,50,400,300,100);
ctx.stroke();
```

<img src="https://img-blog.csdnimg.cn/20201130150832171.png" style="width:250px;float: left;">

<img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bb15294d51be45b98baf07c8c25373f0~tplv-k3u1fbpfcp-zoom-1.image" style="width:250px;float: left;">

**贝塞尔曲线**

[贝塞尔曲线原理](https://blog.csdn.net/tianhai110/article/details/2203572)

```js
quadraCurverTo(cpx1,cpy1,x,y)				// 2阶
// cpx1/cpy1 表示控制点，x/y表示结束点

// 绘图过程如下（p1控制点，p2结束点）：
// 由 P0 至 P1 的连续点 Q0，描述一条线段
// 由 P1 至 P2 的连续点 Q1，描述一条线段
// 由 Q0 至 Q1 的连续点 B(t)，描述一条二次贝塞尔曲线
```

```js
bezierCurverTo(cpx1,cpy1,cpx2,cpy2,x,y)		// 三阶
```





·