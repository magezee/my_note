## CSS3

### 变形

**变形操作 transform**

- 行内元素不产生变形效果，将其转为 `inline-block` 或 `block` 以及弹性元素时都可以产生变化效果

- 同一个元素设置多次变形无法叠加，后面的设置会覆盖前面的

- 设置%单位时，%的依据是自身高宽而非父类的

- transform不仅可以变更形状，还要其他性质，如颜色等，且如果设置了时间效果，会自动填补变形过程动画，使之平滑改变

```less
// 只会向下移动50px而非向右移动500px向下移动50px
div {
    transform: translateX(500px);
    transform: translateY(50px);
}
// 可以组合起来写
div {
    transform: translateX(500px) translateY(50px);
}
// 一般用translate合并
div {
    transform: translate(500px, 50px);
}
```

| 选项                              | 说明                                  |
| --------------------------------- | ------------------------------------- |
| **none**                          | 定义不进行转换。                      |
| **translate(*x*,*y*)**            | 定义 2D 转换。                        |
| **translate3d(*x*,*y*,*z*)**      | 定义 3D 转换。                        |
| **translateX(*x*)**               | 定义转换，只是用 X 轴的值。           |
| **translateY(*y*)**               | 定义转换，只是用 Y 轴的值。           |
| **translateZ(*z*)**               | 定义 3D 转换，只是用 Z 轴的值。       |
| **scale(*x*,*y*)**                | 定义 2D 缩放转换（为0时会消失）。     |
| **scale3d(*x*,*y*,*z*)**          | 定义 3D 缩放转换。                    |
| **scaleX(*x*)**                   | 通过设置 X 轴的值来定义缩放转换。     |
| **scaleY(*y*)**                   | 通过设置 Y 轴的值来定义缩放转换。     |
| **scaleZ(*z*)**                   | 通过设置 Z 轴的值来定义 3D 缩放转换。 |
| **rotate(*angle*)**               | 定义 2D 旋转，在参数中规定角度。      |
| **rotate3d(*x*,*y*,*z*,*angle*)** | 定义 3D 旋转。                        |
| **rotateX(*angle*)**              | 定义沿着 X 轴的 3D 旋转。             |
| **rotateY(*angle*)**              | 定义沿着 Y 轴的 3D 旋转。             |
| **rotateZ(*angle*)**              | 定义沿着 Z 轴的 3D 旋转。             |
| **skew(*x-angle*,*y-angle*)**     | 定义沿着 X 和 Y 轴的 2D 倾斜转换。    |
| **skewX(*angle*)**                | 定义沿着 X 轴的 2D 倾斜转换。         |
| **skewY(*angle*)**                | 定义沿着 Y 轴的 2D 倾斜转换。         |
| **perspective(*n*)**              | 为 3D 转换元素定义透视视图。          |

**注意：transform属性声明多次时会进行覆盖：**

```less
// div在未执行伪类时，已经在初始元素上旋转了180度，触发伪类时，等于初始元素旋转360度而非初始元素旋转180＋360度
// 且由于一开始渲染时已经旋转了180度，因此触发伪类后，视觉上只会再进行旋转180度，但是实际上相当于初始元素旋转了360度
div {
    transform: rotateY(180deg);
    &:hover{
        transform: rotateY(360deg);    
    } 
}
```

----

```less
//旋转的单位为deg
{ transform: rotateX(45deg); }
```

先后顺序不同导致最后结果会不同

```less
{ transform: rotateX(30deg) rotateY(60deg); }
// 效果不等同
{ transform: rotateY(60deg) rotateX(30deg); }
```

**perspective**

要查看全部子元素组成的一个立体图形时，应该给他们加上一个公共的父元素并且在该父元素身上加上透视，如果只是单独想看某个子元素，则单独给子元素加上透视

```less
perspective:900px;		// 父元素上整体观察的声明，是不用transform的，这种声明方法只影响子元素，自己本身不会发生透视

transform: perspective(900px);		// 单个子元素的声明
```

**transform-style**

指定子元素2d显示还是3d显示（flat|preserve-3d）

```less
涉及到z轴相关操作时，必须设置 transform-style:preserve-3d，不然很多功能只能操作xy轴
```

要体验z轴的感觉时，需要使用`perspective`

属性值的大小决定了透视的具体力度，z轴单位不能填百分数

```html
<div className='father'>
	<div className='A'></div>
</div>
```

```less
.father {
    width: 200px;
    height: 200px;
    border: 3px solid ;

    .A {
        width: 200px;
        height: 200px;
        background-color: red;
        transform:  rotateY(45deg);
    }
}
```

<img src="https://img-blog.csdnimg.cn/20200525155514752.png" style="float:left;width:20%;height:20%">

```less
{	
    ....
    transform: perspective(900px) rotateY(45deg);
}
```

<img src="https://img-blog.csdnimg.cn/20200525155706726.png" style="float:left;width:20%;height:20%">

```less
{	
    ....
    transform: perspective(900px) rotateY(45deg) translateZ(-100px);
}
```

<img src="https://img-blog.csdnimg.cn/20200525155856773.png" style="float:left;width:20%;height:20%">

---

**transform-origin**

设置旋转元素的基点位置，默认在图形中心位置（50%,50%）

设置元素变化时的轴点（如从哪个点开始伸缩）

```
transform-origin: x-axis y-axis z-axis;
```

| 值     | 描述                                                         |
| :----- | :----------------------------------------------------------- |
| x-axis | 定义视图被置于 X 轴的何处。可能的值：left center right length % |
| y-axis | 定义视图被置于 Y 轴的何处。可能的值：left center right length % |
| z-axis | 定义视图被置于 Z 轴的何处。可能的值：*length*                |

-----

**rotate3d(*x*,*y*,*z*,*angle*)**

这里的xyz为向量的意思，最终的旋转轴为合向量的单位向量

```less
rotate3d(1,1,0,90deg)
```

---

**skew**

```less
{ 
    transform: skewX(45deg)  	// 相当于图形的X轴不动，Y轴旋转45度
	transform: skewY(45deg)		// 相当于图形的Y轴不动，X轴旋转45度
}	
```

如：`transform: skewY(45deg)`

<img src="https://img-blog.csdnimg.cn/20200526113434267.png" style="float:left;width:50%;height:50%">

---

**backface-visibility**

元素背面显示处理

| 值      | 描述             |
| :------ | :--------------- |
| visible | 背面是可见的。   |
| hidden  | 背面是不可见的。 |

---



### 过渡

**transition：**简写属性，用于设置四个过渡属性：

- **transition-property**
- **transition-duration**
- **transition-timing-function**
- **transition-delay**

```less
{
    transition: background 2s 2s,
        width linear 2s 4s,
        height linear 2s 4s;
}
```

----

**transition-duration**

创建补间动画过渡时间（只要是前后关机键任意不同，如坐标，形状，颜色等，使用该属性时会自动创建补间动画）

```less
// 多个补间动画：如鼠标覆盖时产生一系列变化，离开时会自动复原，2s指的是复原回原样的部件时间，0.2s指鼠标移上发生变化的补间时间
div {
    transition:2s;
    
    &:hover {
        transition: 0.2s;
    }
}
```

时间不会叠加，如果有多个补间时间，则以最大的那个为整个补间动画时间

```less
// 总的动画时间为10s
{
    transition: background 2s ,
        width linear 10s ,
        height linear 5s ;
}
```

---

**transition-delay**

规定在过渡效果开始之前需要等待的时间，一般使用延迟来实现一个变化结束的同时开始另一个变化

当存在多个值时，并非叠加关系

当时间数量少于属性时，会重头开始往后继续设定

```less
// 表示开始后立即执行一次，开始后两秒执行一次，开始后四秒执行一次，而不是开始后2秒执行一次，后2+4秒执行一次（注，并非等待补间动画完成才会开始延时）
{ transition-delay: 0, 2s, 4s; }
```

```less
// 配合transition-property使用
{
	transition: 2s;		// 每一个动画都执行2秒，因此依次往后延时2秒开始执行即可实现A变化结束的同时B开始变化
    transition-property: background, width, border-radius;  
    transition-delay: 0, 2s, 4s;	// 立即变化背景，延时两秒后开始变化宽度，再延时两秒后开始变化边框
}
```

---

**transition-property**

规定应用过渡效果的 CSS 属性的名称。（当指定的 CSS 属性改变时，过渡效果将开始）

| 值         | 描述                                                  |
| :--------- | :---------------------------------------------------- |
| none       | 没有属性会获得过渡效果。                              |
| all        | 所有属性都将获得过渡效果。                            |
| *property* | 定义应用过渡效果的 CSS 属性名称列表，列表以逗号分隔。 |

```less
{
    transition: 2s;
    transition-property: background;  // 只有背景的变化会创建补间动画，其他都是瞬间变化
}
```

```less
// 可以单独设置变化时间
{

    transition-property: background, width, border-radius;  
    transition: 2s,1s;	// 背景变化2秒，宽度变化1秒，圆框变化2秒（当时间数量少于属性时，会重头开始往后继续设定）
}
```

---

**transition-timing-function** 

规定过渡效果的速度曲线

| 值                            | 描述                                                         |
| :---------------------------- | :----------------------------------------------------------- |
| linear                        | 规定以相同速度开始至结束的过渡效果（等于 cubic-bezier(0,0,1,1)）。 |
| ease                          | 规定慢速开始，然后变快，然后慢速结束的过渡效果（cubic-bezier(0.25,0.1,0.25,1)）。 |
| ease-in                       | 规定以慢速开始的过渡效果（等于 cubic-bezier(0.42,0,1,1)）。  |
| ease-out                      | 规定以慢速结束的过渡效果（等于 cubic-bezier(0,0,0.58,1)）。  |
| ease-in-out                   | 规定以慢速开始和结束的过渡效果（等于 cubic-bezier(0.42,0,0.58,1)）。 |
| cubic-bezier(*n*,*n*,*n*,*n*) | 在 cubic-bezier 函数中定义自己的值。可能的值是 0 至 1 之间的数值。 |
| steps(n,start\|end)           | 规定以帧的形式补全动画（非平滑）                             |

<img src="https://img-blog.csdnimg.cn/20200527155608942.png" style="float:left;">

```less
{	
    transition: 2s;
    transition-timing-function: steps(4, start)		// 表示变形的过程分为四个步奏，2s内显示四个步奏		start表示立即执行，如果是end第一个帧显示的是初始状态，第二帧才开始执行变化
}

{
	transition-timing-function: steps(1, end)		// 设置end且只有一帧时，即使有动画也不会有执行效果，因为end的第一帧默认是初始状态
}
```



---

### 帧动画

**animation：简写属性，用于设置六个动画属性（设置的时间是先播放时间后延时时间）**

- **animation-name：**规定需要绑定到选择器的 keyframe 名称
- **animation-duration：**规定完成动画所花费的时间，以秒或毫秒计
- **animation-timing-function：**规定动画的速度曲线
- **animation-delay：**规定在动画开始之前的延迟
- **animation-iteration-count：**规定动画应该播放的次数
- **animation-direction：**规定是否应该轮流反向播放动画
- **animation-fill-mode**：规定动画在播放之前或之后，其动画效果是否可见

`animation-duration`、`animation-timing-function`、`animation-delay`使用方法和transform的一样

并非所有属性都能产生变化，只有存在中间值才会建立平滑变形的动画，否则是瞬间就变化的，如`soild边框`和`dotted边框`之间无无变化中间值，无法形成变形动画，只能瞬间变化

----

过程：

- 声明一组帧动画
- 在指定元素中使用该帧动画

若不声明初始状态或终点状态（0%，100%），则默认使用调用元素的默认状态

关键字`form`表示0%，`to`表示100%，是另一种声明名字而已

【如果设置了100或0%状态（取决于动画播放方向），动画播放完毕后，会立即瞬间回到初始状态（不是0%或者100%，是元素使用动画之前的自身属性），如果不声明，则会创建逐渐回到初始状态的动画】

```less
div {
    animation-name: aa;	// 使用帧动画aa
    animation-duration: 2s;		// 注意不是transition-duration！！！
}

@keyframes aa {		// 声明一个帧动画aa
    // 在animation-duration * x% 时执行什么变化操作
    0% {
        background: white;	// 也可以写成 form { background: white; }
    }

    25% {
        background: red;
    }

    100% {
        background: yellow;
    }
}
```

当变化内容一样时，可以组合起来写

```less
@keyframes aa {		
    25%,75% {
        background: red;
    }
    
    50% {
        background: yellow;
    }
}
```

**多个帧动画**

规则：

- 声明所有动画是同时执行的，不是执行完A才开始执行B
- 如果同时使用A、B动画，那AB里的属性如果出现重复，则以后声明使用的动画为准，无论声明的时间是否会冲突（如AB里都令background发生变化，B比A后声明，那只有B会起作用，如果A动画的执行时间比B要长，则B动画执行完后，冲突的属性部分会继续由A动画来完成）

- 使用动画名称和动画执行时间帧动画才能起作用，若声明的时间少于动画个数时，则会重头开始给时间

【因此一般用不同的动画控制不同的变化，如A控制坐标，B控制背景颜色,C控制形状等】

```less
{
    animation-name: anim1, anim2,anim3;
    animation-duration: 6s,3s;		// 执行动画的时间分别为 6s 3s 6s
}

```

```less
div {    
    width: 100px;
    height: 100px;
    border: 1px solid;
    animation-duration: 10s,5s;
    animation-name: change1, change2;
    background-color: red;
}

@keyframes change1{
    0% {
        transform: translateY(100px);	// 由于transform属性和change2动画冲突，因此不会执行这个变化，但是下面的颜色变化会执行，等到2动画5s执行完后，则1动画的transform属性变化可以生效div 
        background-color: yellow;
    }

    95% {
        background-color: blue;
    }

}

@keyframes change2{
    95% {
        transform: translateX(100px);		
    }
}
```

---

**animation-fill-mode**

规定动画在播放之前或之后，其动画效果是否可见

【使用帧动画时，播完当前动画后，会自动返回初始状态（不是设置100%的状态）】

| 值        | 描述                                                         |
| :-------- | :----------------------------------------------------------- |
| none      | 不改变默认行为。                                             |
| forwards  | 当动画完成后，保持动画中100%关键帧中定义的属性               |
| backwards | 在动画开始之前，运用的属性非初始属性而是用动画0%设置的属性（可配合延时开始动画看出效果） |
| both      | 向前和向后填充模式都被应用。                                 |

forwards可设置动画播放后保持变化不会再次返回初始状态

```less
main {    
    width: 100px;
    height: 100px;
    border: 1px solid;
    animation-duration: 5s;
    animation-name: change ;
    background-color: red;
    animation-fill-mode: forwards;  
}

@keyframes change{
    75% {
        transform: scale(2);
    }

    100% {
        background-color: blue;
        transform: scale(2);	// 注意，forwards保持的是最后一个关键帧的状态，如果不设置100%的话则默认使用初始状态作为100%状态，因此必须要设置100%的状态
        // 此时如果不在100%再设置一遍transform属性的话，则只有背景颜色会保持，比例会回到初始状态
    }
}
```

backwards可设置一段动画开始前以什么状态显示

```less
// 在动画change1播放结束前元素div2隐藏
div1 {
	animation-duration: 3s;
    animation-name: change1 ;
}

div2 {
    animation-duration: 3s;
    animation-name: change2 ;
    animation-fill-mode: forwards;
    animation-delay: 3s;
}

@keyframes change2{
    0% {
        transform: scale(0)       
    }
} 
```





---

**animation-iteration-count**

| 值       | 描述                     |
| :------- | :----------------------- |
| *n*      | 定义动画播放次数的数值。 |
| infinite | 规定动画应该无限次播放。 |

同理可以设置多个值去匹配多个动画，匹配方式和其他一样

```less
{
    animation-name: anim1, anim2,anim3;
    animation-duration: 6s,3s;		// 执行动画的时间分别为 6s 3s 6s
    animation-iteration-count: 3, 2	// 动画分别执行 3 2 3 次
}
```

---

**animation-direction**

【如果设置了100或0%状态（取决于动画播放方向），动画播放完毕后，会立即瞬间回到初始状态（不是0%或者100%，是元素使用动画之前的自身属性），如果不声明，则会创建逐渐回到初始状态的动画】

想声明起点或末尾状态的情况下创建平滑动画时，可以使用`animation-direction: alternate`或`animation-direction: alternate-reverse`，同时要声明动画播放次数

| 值                | 描述                                                         |
| :---------------- | :----------------------------------------------------------- |
| normal            | 默认值。动画按正常播放（0% →100%）。                         |
| reverse           | 动画反向播放（100% → 0%）。                                  |
| alternate         | 动画在奇数次正向播放，在偶数次反向播放（0% → 100% →0% →100%）。 |
| alternate-reverse | 动画在奇数次反向播放，在偶数次正向播放（100% →0% →100% → 0%）。 |

---

**animation-delay**

只会在第一次播放动画时生效，如果动画播放不止一次，也只有第一次开始前会产生延时效果

可以为负值-x，表示的效果是立即开始动画，且从动画的第x秒开始执行，x秒前的内容不会播放

```less
// 一个动画播放时长为10秒，播放0-10秒的内容，延时-2秒表示从动画只会播放2-10秒的内容
{
    animation-duration: 10s;
    animation-delay: -2s;
}
```

----

**animation-play-state**

规定动画正在运行还是暂停

| 值      | 描述               |
| :------ | :----------------- |
| paused  | 规定动画已暂停。   |
| running | 规定动画正在播放。 |

如鼠标移入播放动画

```less
div:hover {
   animation-play-state: running;
}
```

----

### @media

针对不同的媒体类型定义不同的样式，可用于设计响应式的页面

**媒体类型**

| 值         | 描述                               |
| :--------- | :--------------------------------- |
| **all**    | 用于所有设备                       |
| **print**  | 用于打印机和打印预览               |
| **screen** | 用于电脑屏幕，平板电脑，智能手机等 |
| **speech** | 应用于屏幕阅读器等发声设备         |

**媒体功能**

| 值                          | 描述                                                         |
| :-------------------------- | :----------------------------------------------------------- |
| **aspect-ratio**            | 输出设备中的页面可见区域宽度与高度的比率                     |
| **color**                   | 输出设备每一组彩色原件的个数。如果不是彩色设备，则值等于0    |
| **color-index**             | 在输出设备的彩色查询表中的条目数。如果没有使用彩色查询表，则值等于0 |
| **device-aspect-ratio**     | 输出设备的屏幕可见宽度与高度的比率                           |
| **device-height**           | 输出设备的屏幕可见高度                                       |
| **device-width**            | 输出设备的屏幕可见宽度                                       |
| **grid**                    | 用来查询输出设备是否使用栅格或点阵                           |
| **height**                  | 输出设备中的页面可见区域高度                                 |
| **max-aspect-ratio**        | 输出设备的屏幕可见宽度与高度的最大比率                       |
| **max-color**               | 输出设备每一组彩色原件的最大个数                             |
| **max-color-index**         | 在输出设备的彩色查询表中的最大条目数                         |
| **max-device-aspect-ratio** | 输出设备的屏幕可见宽度与高度的最大比率                       |
| **max-device-height**       | 输出设备的屏幕可见的最大高度                                 |
| **max-device-width**        | 输出设备的屏幕最大可见宽度                                   |
| **max-height**              | 输出设备中的页面最大可见区域高度                             |
| **max-monochrome**          | 在一个单色框架缓冲区中每像素包含的最大单色原件个数           |
| **max-resolution**          | 设备的最大分辨率                                             |
| **max-width**               | 输出设备中的页面最大可见区域宽度                             |
| **min-aspect-ratio**        | 输出设备中的页面可见区域宽度与高度的最小比率                 |
| **min-color**               | 输出设备每一组彩色原件的最小个数                             |
| **min-color-index**         | 在输出设备的彩色查询表中的最小条目数                         |
| **min-device-aspect-ratio** | 输出设备的屏幕可见宽度与高度的最小比率                       |
| **min-device-width**        | 输出设备的屏幕最小可见宽度                                   |
| **min-device-height**       | 输出设备的屏幕的最小可见高度                                 |
| **min-height**              | 输出设备中的页面最小可见区域高度                             |
| **min-monochrome**          | 在一个单色框架缓冲区中每像素包含的最小单色原件个数           |
| **min-resolution**          | 设备的最小分辨率                                             |
| **min-width**               | 输出设备中的页面最小可见区域宽度                             |
| **monochrome**              | 在一个单色框架缓冲区中每像素包含的单色原件个数。如果不是单色设备，则值等于0 |
| **orientation**             | 输出设备中的页面可见区域高度是否大于或等于宽度（portrait \| landscape）portrait匹配高度大于或等于宽度 |
| **resolution**              | 设备的分辨率。如：96dpi, 300dpi, 118dpcm                     |
| **scan**                    | 电视类设备的扫描工序                                         |
| **width**                   | 输出设备中的页面可见区域宽度                                 |

**逻辑关系**

- 与：`and`
- 或：`,`（不是用or）

- 非：`not`
- 仅：`only`（和not一样放在总表达式前，自动只能排除低端浏览器）

```less
// not一般放在表达式的前面
// 宽度不是200px~400px时启用该样式 相当于not( screen and (max-width: 400px) and (min-width:200px) )
@media not screen and (max-width: 400px) and (min-width:200px)  {
    main {
        height: 100px;
        width: 100px;
        border: 1px solid;
        background-color: blue;
    }
}

```

----

```less
// 如果文档宽度小于 300 像素则启用该样式
@media screen and (max-width: 300px) {
    body {
        background-color:lightblue;
    }
}
```

这里应用的样式并不是完全覆盖而是添加的关系，相当于在原来的基础上声明了新的属性，如果同名属性则覆盖

```less
// 当窗口宽度不超过400px时添加背景颜色，注意启用样式后仍有宽高和边框样式，因为只是相当于新添了背景属性
@media screen and (max-width: 400px) {
    main {
        background-color: blue;
    }
}
main {    
    width: 100px;
    height: 100px;
    border: 1px solid;
 
}
```

如果需要根据条件完全更改样式，则全部的样式应该声明在条件内部

```less
// 因为200宽度以下的样式没有设置，因此宽度200px以下无main的样式
@media screen and (max-width: 400px) and (min-width:200px)  {
    main {
        margin: 100px;
        width: 100px;
        border: 1px solid;
        background-color: blue;
    }
}

@media screen and (min-width: 400px)  {
    main {
        height: 100px;
        width: 100px;
        border: 1px solid;
        background-color: yellow;
    }
}
```

由于后声明的优先级更高，如果一个样式的匹配范围A比B大，则A应声明在前

不过一般指定具体范围值，就不需要费心考虑顺序了

```less
// >400比>600的范围要广，>600的时候一定也>400，因此如果后声明>400，则永远不会匹配到>600的内容
@media screen and (min-width: 400px)   {
    main {
        margin: 100px;
        width: 100px;
        border: 1px solid;
        background-color: blue;
    }
}

@media screen and (min-width: 600px)  {
    main {
        height: 100px;
        width: 100px;
        border: 1px solid;
        background-color: yellow;
    }
}
```


