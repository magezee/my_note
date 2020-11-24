## 偏概念

### 选择器

**CSS3选择器：**

- 基本选择器
- 层次选择器
- 伪类选择器
  - 动态伪类选择器
  - 目标伪类选择器
  - 语言伪类选择器
  - UI元素状态伪类选择器
  - 结构伪类选择器
  - 否定伪类选择器
- 伪元素
- 属性选择器



#### 基本选择器

| 选择器              | 类型       | 功能                                     |
| ------------------- | ---------- | ---------------------------------------- |
| *                   | 统配选择器 | 选择所有元素                             |
| E                   | 元素选择器 | 选择指定元素                             |
| #id                 | ID选择器   | 选择指定ID元素                           |
| .class              | 类选择器   | 选择指定类名元素                         |
| selector1,selectorN | 群组选择器 | 使用逗号分隔，将各选择器选择的元素集合并 |

```
内联样式权值：1000
ID选择器权值：100
类选择器权值：10
元素选择器权值：1
通用选择器（*）和其他选择器（如子选择器 > ）权值都为0
```

**当多个选择器不用空格而直接连着写时，表示且的意思，同时满足所有条件生效**

```jsx
// 要选用一个元素中含有的一个类时 用element.class 表示满足xxx元素且类名为xxx
<a className='choose'></a>

// a.choose{ ... }
// 不要空格，否则就是选择a的子类 a .choose
```

```jsx
// 当存在多个类名时
<a className='A B C D'></a>
// .A.C{ ... }	表示选择含有类A且含有类C的元素
```

#### 层次选择器

| 选择器 | 类型                  | 功能                                             |
| ------ | --------------------- | ------------------------------------------------ |
| E    F | 后代选择器/包含选择器 | 选择E元素的子元素F（包括F满足条件的所有F子元素） |
| E > F  | 子选择器              | 选择E元素的子元素F元素（不包括F子元素）          |
| E + F  | 相邻兄弟选择器        | 选择紧位于E元素后的F元素（不包括F子元素）        |
| E ~ F  | 通用选择器            | 选择位于E元素后的所有F元素（不包括F子元素）      |

后代选择器和子选择器的区别：

```html
<ul>
    <li>
    	<a herf='#'>一级菜单</a>
        <a>一级菜单</a>
        <div>
            <a>二级菜单</a>
            <a>二级菜单</a>
        </div>
        
    </li>
</ul>
```

```less
// li内的所有a标签都变为红色了
ul li a{
    color:red;
}

// 只有一级菜单的a标签变为蓝色，二级的不受影响
ul li > a{
    color:blue;
}
```

#### 伪类选择器

伪类选择器都以`：`开头

- **动态伪类选择器**

  并不存在于HTML中，只有用户和网站交互的时候才能体现出来

| 选择器     | 类型               | 功能                                                  |
| ---------- | ------------------ | ----------------------------------------------------- |
| E：link    | 链接伪类选择器     | 选择被定义了超链接且未被访问过的E元素（常用链接锚点） |
| E：visited | 链接伪类选择器     | 选择被定义了超链接且已被访问过的E元素（常用链接锚点） |
| E：active  | 用户行为伪类选择器 | 选择被激活的E元素（常用于链接和锚点）                 |
| E：hover   | 用户行为伪类选择器 | 选择被用户鼠标停留的E元素                             |
| E：focus   | 用户行为伪类选择器 | 选择获得焦点的E元素                                   |

- **目标伪类选择器**

  URL中包含锚点（#），如 `#contact`，则该选择器则匹配id为contact的元素

| 选择器    | 类型           | 功能                     |
| --------- | -------------- | ------------------------ |
| E：target | 目标伪类选择器 | 选择被URL锚点指向的E元素 |

```
应用场景：
高亮显示区块
从相互层叠的盒容器或图片中突出显示其中一项
tabs效果
幻灯片效果
灯箱效果
相册效果
```

```html
<body>
    <div class="accordionMenu"> 
        <div class="menuSection" id="brand">
            <h2><a href="#brand">Brand</a></h2>
            <p>Lorem ipsum dolor</p>
        </div>
        <div class="menuSection" id="promotion">
            <h2><a href="#promotion">Promotion</a></h2>
            <p>Lorem ipsum dolor sit amet...</p>
        </div>
        <div class="menuSection" id="event"> 
            <h2><a href="#event">Event</a></h2>
            <p>Lorem ipsum dolor sit amet...</p>
        </div>
    </div>
    
</body>
```

```css
.accordionMenu {
    background-color:#fff;
    color: #424242;
    font: 12px Arial, Verdana, sans-serif;
    margin: 0 auto;
    padding: 10px;
    width: 500px;
}

.accordionMenu h2 {
    padding: 0;
    position: relative;
}

/* 制作向下三角效果 */
.accordionMenu h2:before {
    border: 5px solid #fff;
    border-color: #fff transparent transparent;
    content: "";
    height: 0;
    position: absolute;
    right: 10px;
    top: 15px;
    width: 0;
}

/* 制作手风琴标题栏效果 */
.accordionMenu h2 a {
    background: #8f8f8f;
    background: linear-gradient(top, #cecece, #8f8f8f);
    border-radius: 5px;
    color: #424242;
    display: block;
    font-size: 13px;
    font-weight: normal;
    margin: 0;
    padding: 10px 10px;
    text-shadow: 2px 2px 2px #aeaeae;
    text-decoration: none;
}

/* 目标标题效果 */
.accordionMenu :target h2  a,
.accordionMenu h2 a:focus,
.accordionMenu h2 a:hover,
.accordionMenu h2 a:active {
    background: #2288dd;
    background: linear-gradient(top, #6bb2ff, #2288dd);
    color: #fff;
}

/* 标题栏对应的内容 */
.accordionMenu p {
    margin: 0;
    height: 0;	/* 默认栏目内容高度为0，达到影藏效果 */
    overflow: hidden;	
    padding: 0 10px;
    transition: height 0.5s ease-in;
}

/* 展开对应目标内容 */
.accordionMenu :target p { 
    height: 100px;
    overflow: auto;
}

/* 展开时标题三角效果 */
.accordionMenu :target h2::before {
    border-color: transparent transparent transparent #fff;
}
```

效果：

```
https://magezee.github.io/cssTest/1/index.html
```

- **语言伪类选择器**

  根据不同语言版本设置页面的不同字体风格

  根据元素的语言编码匹配元素，这种语言信息必须包含在文档中，或者与文档关联，不能从css指定

指定文档语言的两种方式：

```html
<!-- HTML5直接设置文档语言，当网站转译时，lang属性会动态更改为对应语言 -->
<!DOCTYPE HTML>
<html lang="en-US">
```

```html
<!-- 在文档中指定lang属性 -->
<body lang="fr">
```

| 选择器  | 类型           | 功能                                      |
| ------- | -------------- | ----------------------------------------- |
| E：lang | 语言伪类选择器 | 选择指定了lang属性且其值为language的元素E |

```css
:lang(en-US) {
    /* 设置对应css */
}

:lang(fr) {
    /* 设置对应css */
}
```

- **UI元素状态伪类选择器**

  主要用于form表单元素

  UI的状态一般包括：启用、禁用、选中、未选中、获得焦点、失去焦点、锁定和待机

| 选择器    | 示例           | 说明                        |
| --------- | -------------- | --------------------------- |
| :enabled  | input:enabled  | 选择每个启用的 input 元素   |
| :disabled | input:disabled | 选择每个禁用的 input 元素   |
| :checked  | input:checked  | 选择每个被选中的 input 元素 |
| :required | input:required | 包含`required`属性的元素    |
| :optional | input:optional | 不包含`required`属性的元素  |
| :valid    | input:valid    | 验证通过的表单元素          |
| :invalid  | input:invalid  | 验证不通过的表单            |

使用UI伪类选择器时，需要在HTML中存在这种状态，如禁用输入框，需要在HTML的对应元素上添加禁用属性

```html
<input id="disabliedInput" type="text" disabled="" >
```

- **结构伪类选择器**

  可以根据元素在文档树种的某些特性（如相对位置）定位到它们，通过文档树结构的相互关系来匹配特定元素，从而减少HTML文档对ID或类名的定义

| 选择器                  | 功能                                                         |
| ----------------------- | ------------------------------------------------------------ |
| E ：first-child         | 匹配父元素的首个子元素E（与 E：nth-child(1)作用一样）        |
| E ：last-child          | 匹配父元素的最后一个元素E（与 E：nth-last-child(1)作用一样） |
| E：root                 | 匹配元素E所在文档的根元素，在HTML文档中，根元素始终是html，此时该选择器与html元素选择器匹配内容相同 |
| E  F：nth-child(n)      | 选择父元素E的第n个子元素F，n可以为整数，关键字（even、odd），公式(2n+1、-n+5)等（n的起始值为1而不是0） |
| E  F：nth-last-child(n) | 选择父元素E的倒数第n个子元素F，与上面计算顺序相反            |
| E ：nth-of-type(n)      | 选择父元素内具有指定类型的第n个E元素                         |
| E ：nth-last-of-type(n) | 选择父元素内具有指定类型的倒数第n个E元素                     |
| E ：first-of-type       | 选择父元素内具有指定类型的第一个E元素                        |
| E ：last-of-type        | 选择父元素内具有指定类型的倒数第一个E元素                    |
| E ：only-child          | 选择E元素，该元素在父元素中是仅有的子元素                    |
| E ：only-of-type        | 选择E元素，该元素在父元素中是仅有的同类型子元素              |
| E：empty                | 选择没有子元素的元素，且该元素不包含任何文本节点             |

```html
<div>
	<ul>						<!-- ul:only-of-type -->
        <li>one</li>			<!-- li:nth:child(2n+1) -->  <!-- li:first-child -->
        <li>two</li>			<!-- li:nth-child(2) -->
        <li>three</li>			<!-- li:nth:child(2n+1) -->  <!-- li:last-child -->
    </ul>
    <div>abc</div>				<!-- div div:first-of-type --> 
    <p>para</p>
    <div>def</div>				<!-- div div:last-of-type -->
    <p>para</p>				    <!-- p:nth-of-type(2) -->
    <b>ghi</b>
</div>
```

```
n：

当n直接被指定为具体的值时，表示只选择具体值的元素（单个）

但当n保留用作与表达式时（如2n），此时会自动对n从1开始进行枚举直至元素耗尽无法选择为止（多个）
如：2n
n=0，选择第二个
n=1，选择第四个
……
因此匹配到的元素会有多个

当参数为关键字
odd：匹配所有奇数
even：匹配所有偶数
```

----

#### 伪元素

伪元素可用于定位文档中包含的文本，但无法再文档树中定位

| 伪元素         | 功能                                      |
| -------------- | ----------------------------------------- |
| ::first-letter | 匹配文本块的第一个字母                    |
| ::first-line   | 匹配元素的第一行文本                      |
| ::before       | 用于在css渲染中向元素逻辑上的头部添加内容 |
| ::after        | 用于在css渲染中向元素逻辑上的尾部添加内容 |
| ::selection    | 匹配突出显示的文本                        |

`::first-letter`

```css
/* 制作首字下沉 */
p:first-child::first-letter {
    float: left;
    color: #903;
    padding: 4px 8px 0 3px;
    font: 75px/60px Georgia;
}
```

`::first-line`

```css
/* 最后一个段落的第一行文字显示为红色斜体 */
p:last-child::first-line {
    font: italic 16px/18px Georgia;
    color: #903;
}
```

`::before和::after`

不要用:before或:after展示有实际意义的内容，尽量使用它们显示修饰性内容，例如图标

```
::before和::after必须配合content属性来使用
content用来定义插入的内容，content必须有值，至少是空，默认情况下，伪类元素的display是默认值inline，可以通过设置display:block来改变其显示
```

content取值：

- 使用引号包含一段字符串，将会向元素内容中添加字符串

```css
/* <p>平凡的世界</p> */
p::before {
    content: “《”;
    color: blue;
}
p::after {
    content: "》";
    color: blue;
}
/* 《平凡的世界》 */
```

- 通过attr( )调用当前元素的属性，比如将图片alt提示文字或者链接的href地址显示出来

```less
/* <a href="http://www.gegeda.online">咯咯哒</a> */
a::after {
    content: "("  attr(href) ")"	// 这里搭配了字符串使用
}
/* 咯咯哒(http://www.gegeda.online) */
```

- 通过url( )或uri( )引用媒体文件

```css
/* <a href="http://www,baidu.com">百度</a> */
a::before {
    content: url("https://www.baidu.com/img/baidu.gif")
}
a::after {
    content: "("attr(href)")"
}
/* [引用的图片]百度(http://ww.baidu.com) */
```

`::selection`

匹配突出显示的文本（浏览器默认情况下，框选网页文本是深蓝背景白色字体）

仅接受两个属性：background、color

```css
::selection {
    background: red;
    color: #fff;
}
```

----

#### 属性选择器

| 选择器        | 功能                                                         |
| ------------- | ------------------------------------------------------------ |
| E[attr]       | 选择匹配具有属性attr的E元素（E可省略，表示匹配任意具有attr属性的元素） |
| E[atrr=val]   | 选择具有属性attr且值为val的E元素（E可省略）必须完全匹配（a[class="links"]匹配不到<a class="links inem"></a>，必须a[class="links item"]） |
| E[attr\|=val] | 选择具有属性attr且值为val或以val-开头的元素E（常用与lang属性，如lang="en-us"，p[lang=en]将匹配定义为英语的任何段落，无论是英式还是美式） |
| E[attr~=val]  | 选择具有属性attr且拥有多个用空格分隔的值，其中一个值为val（如.info[title~=more]将匹配 <a class="info" tilte="click here for more"></a>） |
| E[attr*=val]  | 选择具有属性attr且属性值任意位置有val字符串的元素（如.info[title*=more]将匹配 <a class="info" tilte="abcdmoreddddd"></a>） |
| E[attr^=val]  | 选择具有属性attr且属性值以val字符串开头的元素                |
| E[attr$=val]  | 选择具有属性attr且属性值以val字符串结尾的元素                |

| 通配符 | 功能         | 示例                                                         |
| ------ | ------------ | ------------------------------------------------------------ |
| ^      | 匹配通配符   | span[class^=span]表示选择以类名以"span"开头的所有span元素    |
| $      | 匹配终止符   | a[href$=pdf]表示选择以"pdf"结尾的href属性的所有a元素         |
| *      | 匹配任意字符 | a[title*=site]匹配a元素，而且a元素的title属性值中任意位置有"site"字符的任何字符串 |

```html
<ul>
	<li><a href="http://www.w3cplus.com">w3cplus.com</a></li>
	<li><a href="home">home</a></li>
	<li><a href="a.doc">Word文档</a></li>
</ul>
```

```css
/* 匹配所有外部链接 */
a[href^=http://] {
    
}

/* 匹配首页 */
a[href="home"] {
    
}

/* 匹配.doc文档 */
a[href$="doc"] {
    
}
```

-----

### 基本单位

- **px**
   表示像素，是绝对单位，不会因为其他元素的尺寸变化而变化，页面按照精确像素展示
- **pt**
   即磅 Points，是一种绝对单位，是物理长度单位，等于 1/72 寸
-  **em**
  - em的值并不是固定的
  - em会继承父级元素的字体大小
- **rem**
   相对单位，相对根节点html的字体大小来计算
-  **vw**
   表示viewpoint width,视窗宽度（1vw表示1%的视窗宽度）
- **vh**
   表示viewpoint height 视窗高度
-  **vmin**
   表示vm和vh中较小的那个
- **vmax**
   表示vw和vh中较大的那个
- **in**
   表示英寸
- **ch**
   数字0的宽度, 1ch 就是数字 0 的宽度

------

### 属性继承

**自动继承的属性：**

- 字体系列属性

  - `font`：组合字体

  - `font-family`：规定元素的字体系列

  - `font-weight`：设置字体的粗细

  - `font-size`：设置字体的尺寸

  - `font-style`：定义字体的风格
  - `font-variant`：设置小型大写字母的字体显示文本，这意味着所有的小写字母均会被转换为大写，但是所有使用小型大写字体的字母与其余文本相比，其字体尺寸更小。
  - `font-stretch`：允许你使文字变宽或变窄。所有主流浏览器都不支持。
  - `font-size-adjust`：为某个元素规定一个 aspect 值，字体的小写字母 "x" 的高度与"font-size" 高度之间的比率被称为一个字体的 aspect 值。这样就可以保持首选字体的 x-height。

- 文本系列属性
  - `text-indent`：文本缩进
  - `text-align`：文本水平对齐
  - `line-height`：行高
  - `word-spacing`：增加或减少单词间的空白（即字间隔）
  - `letter-spacing`：增加或减少字符间的空白（字符间距）
  - `text-transform`：控制文本大小写
  - `direction`：规定文本的书写方向
  - `color`：文本颜色
- 元素可见性：
  - `visibility`
- 表格布局属性：
  - `caption-side`
  - `border-collapse`
  - `border-spacing`
  - `empty-cells`
  - `table-layout`

- 列表属性：
  - `list-style-type`
  - `list-style-image`
  - `list-style-position`
  - `list-style`

- 生成内容属性：
  - `quotes`

- 光标属性：
  - `cursor`

- 页面样式属性：

  - `page`

  - `page-break-inside`

  - `windows`

  - `orphans`

- 声音样式属性：

  - `speak`
  - `speak-punctuation`
  - `speak-numeral`

  - `speak-header`

  - `speech-rate`

  - `volume`

  - `voice-family`

  - `pitch`

  - `pitch-range`

  - `stress`

  - `richness`

  - `azimuth`

  - `elevation`

**所有元素可继承的属性：**

- 元素可见性：

  - `visibility`

  - `opacity`

- 光标属性：
  - `cursor`

**内联元素可以继承的属性:**

- 字体系列属性
- 除text-indent、text-align之外的文本系列属性

**块级元素可以继承的属性:**

- 文本属性
  - `text-indent`
  - `text-align`

**无继承的属性：**

- `display`

- 文本属性：`vertical-align`、`text-decoration`、`text-shadow`、`white-space`、`unicode-bidi`

- 盒子模型的属性：宽度、高度、内外边距、边框等
- 背景属性：背景图片、颜色、位置等
- 定位属性：浮动、清除浮动、定位position等
- 生成内容属性：`content`、`counter-reset`、`counter-increment`
- 轮廓样式属性：`outline-style`、`outline-width`、`outline-color`、`outline`
- 页面样式属性：`size`、`page-break-before`、`page-break-after`

**特殊情况**

拥有默认css的标签

- a 标签的字体颜色不能被继承
- h1-h6标签字体的大下也是不能被继承的



---

### 盒子模型

一个div盒子分为四大块范围，由内向外：

- content（内容框）
- padding（内边框、内边距）
- border（边框）
- margin（外边距）

**height和width的范围：**

```
标准盒子模型：height和width不包含margin、border、padding
IE盒子模型：height和width包含padding和border，不包含margin

可以使用css来更改：
    box-sizing: border-box;		// 标准模型
    box-sizing: content-box;	// IE模型
```

**margin的范围：**

```
块元素上下排列时，margin的范围是公用的，取两个magin中最大的那个；左右排列时，margin范围为两者叠加
```

---

### 简写标准

```
四个方向都设置，简写顺序为：上、右、下、左
margin: 1px 2px 3px 4px

四个方向都相等
margin: 1px

上下相等，左右相等，简写顺序为：上下、左右
margin: 1px 2px

上下不等，左右相等，简写顺序为：上、下、左右
margin: 1px 2px 3px

上下相等，左右不等
不可简写
```



---

### 基线

在各种内联相关模型中，凡是涉及到垂直方向的排版或者对齐的，都离不开最最基本的基线(baseline)。其他中线顶线一类的定义也离不开基线

 **作为父元素的块级元素基线（baseline）**

块级元素如果只有一行的内容,那么就以字母x的底部作为基线
当有多行内容时，每行都有一个基线，两个相邻基线间的距离就是行高
<img src="https://img-blog.csdnimg.cn/201811301917074.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTM1MDIyNQ==,size_16,color_FFFFFF,t_70" style="margin:0">

**文本元素基线**







----

### 布局种类

- 静态布局：即传统Web设计，网页上的所有元素的尺寸一律使用px作为单位。不管浏览器尺寸具体是多少，网页布局始终按照最初写代码时的布局来显示

- 流式布局：页面元素的宽度按照屏幕分辨率进行适配调整，但整体布局不变。这就导致如果屏幕太大或者太小都会导致元素无法正常显示

- 自适应布局：分别为不同的屏幕分辨率定义布局，即创建多个静态布局，每个静态布局对应一个屏幕分辨率范围

- 响应式布局：分别为不同的屏幕分辨率定义布局，同时，在每个布局中，应用流式布局的理念，即页面元素宽度随着窗口调整而自动适配

----

### 行内元素和块状元素

- **inline（行内元素）:**
  - 使元素变成行内元素，拥有行内元素的特性，即可以与其他行内元素共享一行，不会独占一行

  - 不能更改元素的height，width的值，大小由内容撑开 

  - 可以使用padding，上下左右都有效，而margin只有left和right产生边距效果，top和bottom不行

- **block（块级元素）:**
  - 使元素变成块级元素，独占一行，在不设置自己的宽度的情况下，块级元素会默认填满父级元素的宽度.

  - 能够改变元素的height，width的值

  - 可以设置padding，margin的各个属性值，top，left，bottom，right都能够产生边距效果

- **inline-block（行内块元素）:**
- 如果没有设置宽高，则由自身内容撑开
  
- 两个兄弟块元素如果想设置在同一行，则应该两个元素都应该设置`display:inline-block`
  - padding和margin都可以生效

```
块状元素的定位是以左上角为准的 
块状元素居中是左上角的在中间 
所以此时要响应地去减掉自度宽 高度 度实现整体在中间
```



----

### 兼容性

为了让不同浏览器都能进行渲染需要加上前缀，如`-moz-border-top-colors`

| 浏览器分类      | 浏览器            | 私有属性前缀 |
| --------------- | ----------------- | ------------ |
| Gecko引擎内核   | Mozilla           | -moz-        |
| Presto引擎内核  | Opera             | -o-          |
| KHTML引擎内核   | Konqueror         | -khtml-      |
| Trident引擎内核 | Internet Explorer | -ms-         |



---

## 偏编程

### 边框

```
还要一种框为轮廓线框：outline
但是和border不同，outline是不会占用文档流位置的
```

**基本属性**

| 语法         | 功能             |
| ------------ | ---------------- |
| border-width | 设置元素边框粗细 |
| border-color | 设置元素边框颜色 |
| border-style | 设置元素边框类型 |

可合并

```css
div {border: 3px red soild }
```

使用合并和具体定义方法结合可以令css代码更加灵活

```css
li {
    border: 1px soild red;
    border-width: 1px 0;	/* 将上面定义的四面均有边框覆盖 变成只有上下有边框 */
}
```

**border-style**

| 属性值  | 功能                                                         |
| ------- | ------------------------------------------------------------ |
| none    | 无边框                                                       |
| hidden  | 与none相同，但应用于表时除外，对于表，hidden用于解决边框冲突 |
| dotted  | 点状边框                                                     |
| dashed  | 虚线边框                                                     |
| soild   | 实线边框                                                     |
| double  | 双线，宽度等于border-width                                   |
| groove  | 3D凹槽边框，效果取决于border-color                           |
| ridge   | 3D垄状边框，效果取决于border-color                           |
| inset   | 3Dinsert边框，效果取决于border-color                         |
| outset  | 3Dousser边框，效果取决于border-color                         |
| inherit | 从父元素继承边框属性                                         |

**border-radius**

设置边框圆角半径，左上角的圆角由左边和上边的绘制圆弧一同决定

```less
length
%	从自身某边长度%多少开始绘制圆弧（如果上下左右都是50%则表示一个圆）

border-top-right-radius:none;           /* 右上角圆角 */
border-bottom-right-radius: none;       /* 右下角圆角 */
border-bottom-left-radius: none;        /* 左下角圆角 */
border-top-left-radius: 40%;           /* 左上角圆角 */

/* 
border-radius 可包含两个参数值 第一个表示圆角水平半径 第二个表示圆角垂直半径 两个参数值用斜线分割 如果仅包含一个参数值 则默认第二个与第一个相同,如:border-radius:50%/40%
	border-radius:12px      		只包含一个数值时，表示1/4个圆角
	border-radisu：0/12px           如果参数中包含0，则为矩形，不会显示为圆角   
*/
```



-----

### 文本

```
px：像素，根据屏幕像素点尺寸变化而变化，屏幕分辨率越大，相同像素的字体就越小
em：根据父辈字体的大小来定义字体大小。如父元素字体大小为12px，设置子元素字体大小2em，则实际显示24px
ex：相对于父辈字体的x高度来定义字体大小，ex单位大小既取决于字体的大小也取决于字体的类型
%： 与em效果相同，相对于父辈字体的大小来定义字体大小
```

**font**

```
简写包含属性：
font-style
font-variant
font-weight
font-size/line-height
font-family
```

- font-family

  定义字体类型

```css
.div { font-family: 'Courier New', Courier, monospace; }
```

- font-size

  定义字体大小

```css 
/* 对字体尺寸，不会受外界因素影响，根据对象字体进行调整 */
xx-small
x-small
small
medium
large
x-large
xx-large        
        
/* 相对字体尺寸，受到外界因素影响，根据父对象字体尺寸进行增大或减小 */
larger
smaller;         
```

- font-weight

  定义字体粗细

```css
normal      /* 默认 相当于font-weight:400 */
bold        /* 粗体 相当于font-weight:700 */

bolder
lighter;     /* 相对于normal而言的粗细变化 */
```

- font-style

  定义字体倾斜效果

```css
normal      
italic      /* 斜体 */
oblique;    /* 倾斜的字体 */
```

- font-variant

  定义字体大小效果

```css
normal
small-caps;      /* 小型的大写字母字体 中文无效 */
```

- text-decoration

  定义字体下划线

```css
none        
underline       	/* 下划线 */
blink           	/* 闪烁 */
overline       	 	/* 上划线 */
line-through;   	/* 贯穿线 */
```

- text-align

  定义文本水平对齐方式

```css
left        /* 左对齐 */
right       /* 右对齐 */
center      /* 居中 */
justify;    /* 两端对齐 */
```

```
text-align 属性只能设计文本的水平对齐问题，而对于块状元素的的水平对齐还需要使用CSS的 margin 属性，但块状元素左右边界都被设置为自动时，则居中显示
margin-left:auto;
margin-right-auto;
```

- vertical-align

  定义文本垂直对齐（设置行内元素垂直对其方式），如果一个父元素设置了vertical-align且子元素仅为文本时，则文本居中，如果子元素不是文本，则是另一种情况

```css
auto
baseline
sub
super
top
text-top
middle
bottom
text-bottom
/* 不支持块元素对齐，只有当包含框显示为单元格时才有效 —— display:table-cell */
```

- letter-spacing

  定义字距

```css
letter-spacing:1em;
```

- line-height

  定义行高（行距）

```css
normal      /* 默认 一般为1.2em */
length;     /* 百分百数字 */
```

- text-indent

  定义首行缩进

```less
text-indent: 2em;	// 表示缩进两个字节，使用em无论字号多大都会缩进两个字节
```

- white-space

  规定文本空格处理（浏览器默认会将html中的多个空格合并成一个）

| 值       | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| normal   | 默认。空白会被浏览器忽略。                                   |
| pre      | 空白会被浏览器保留。其行为方式类似 HTML 中的 <pre> 标签。    |
| nowrap   | 文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止。 |
| pre-wrap | 保留空白符序列，但是正常地进行换行。                         |
| pre-line | 合并空白符序列，但是保留换行符。                             |
| inherit  | 规定应该从父元素继承 white-space 属性的值                    |





----

### 图像

- opacity

  设置图像不透明度

```css
0       /* 完全透明 */
0.5     /* 半透明 */
1;      /* 不透明 */
```

- border-style

  定义图像边框

```css
none        /* 清除图像边框 */
solid       /* 实线 */
double      /* 双框线 */
groove      /* 立体凹槽 */
ridge       /* 立体凸槽 */
inset       /* 立体凹边 */
outset      /* 立体凸边 */
dotted      /* 点 虚线框 */
dashed;      /* 虚线 虚线框 */
```

- border-color

  定义边框颜色

```css
{ border-color:red,blue,yellow,white; }           /* 定义边框颜色 */
/* 取值分别为 上 右 下 左 */
/* 也可以使用 border-top-color  border-right-color  border-bottom-color  border-left-color 单独设置 */
```

- border-width

  边框宽度

```css
border-width: 1em,2px,100%,40px;
/* 取值分别为 上 右 下 左 */
/* 也可以使用border-top-width …… */
```

- border-radius

  定义圆角样式





----

### 背景

**background** 

```
简写包含属性：
background-color		
background-position
background-size
background-repeat
background-origin
background-clip
background-attachment
background-image
```

**background-position**

设置背景图像的起始位置

| 值                                                           | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| top left center bottom right两两搭配，对边不可，如（top right） | 如果仅规定了一个关键词，那么第二个值将是"center"。默认值：0% 0%。 |
| x% y%                                                        | 第一个值是水平位置，第二个值是垂直位置。左上角是 0% 0%。右下角是 100% 100%。如果仅规定了一个值，另一个值将是 50%。 |
| xpos ypos                                                    | 第一个值是水平位置，第二个值是垂直位置。左上角是 0 0。单位是像素 (0px 0px) 或任何其他的 CSS 单位。如果仅规定了一个值，另一个值将是50%。您可以混合使用 % 和 position 值。 |

**background-size**

规定背景图像的尺寸

| 值           | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| *length*     | 设置背景图像的高度和宽度。第一个值设置宽度，第二个值设置高度。如果只设置一个值，则第二个值会被设置为 "auto"。 |
| *percentage* | 以父元素的百分比来设置背景图像的宽度和高度。第一个值设置宽度，第二个值设置高度。如果只设置一个值，则第二个值会被设置为 "auto"。 |
| cover        | 把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。背景图像的某些部分也许无法显示在背景定位区域中。 |
| contain      | 把图像图像扩展至最大尺寸，以使其宽度和高度完全适应内容区域。 |

**background-repeat**

规定是否重复及如何重复背景图像

| 值        | 描述                                                |
| :-------- | :-------------------------------------------------- |
| repeat    | 默认。背景图像将在垂直方向和水平方向重复。          |
| repeat-x  | 背景图像将在水平方向重复。                          |
| repeat-y  | 背景图像将在垂直方向重复。                          |
| no-repeat | 背景图像将仅显示一次。                              |
| inherit   | 规定应该从父元素继承 background-repeat 属性的设置。 |

**background-clip**

规定背景的绘制区域，默认绘制到边框

| 值          | 描述                   |
| :---------- | :--------------------- |
| border-box  | 背景被裁剪到边框盒。   |
| padding-box | 背景被裁剪到内边距框。 |
| content-box | 背景被裁剪到内容框。   |

**background-attachment**

设置背景图像是否固定或者随着页面的其余部分滚动

| 值      | 描述                                                    |
| :------ | :------------------------------------------------------ |
| scroll  | 默认值。背景图像会随着页面其余部分的滚动而移动。        |
| fixed   | 当页面的其余部分滚动时，背景图像不会移动。              |
| inherit | 规定应该从父元素继承 background-attachment 属性的设置。 |

**background-image**

规定要使用的背景图像，设置的背景图不会改变盒子宽高

可以定义多个背景，如`background-image: url(urlA), url(urlB)`，多个背景会重叠才一起（起始位置相同），需要定位将其分开

多个背景图时，背景图的所有属性也可以设置多个，用逗号分开，表示依次设定背景图，如`background-repe: no-repeat, repeat`表示第一个背景不平铺，第二个平铺

| 值           | 描述                                               |
| :----------- | :------------------------------------------------- |
| url('*URL*') | 指向图像的路径。                                   |
| none         | 默认值。不显示背景图像。                           |
| inherit      | 规定应该从父元素继承 background-image 属性的设置。 |



----

### 定位

 **Position：**

-  absolute：绝对定位，是相对于最近的且不是static定位的父元素来定位（脱离文档流）
-  fixed：绝对定位，是相对于浏览器窗口来定位的，是固定的，不会跟屏幕一起滚动（脱离文档流）
-  relative：相对定位，是相对于其原本的位置来定位的
-  sticky：粘性定位，同时拥有relative和fixed的特性
- static：默认值，没有定位
- inherit：继承父元素的position值

```
元素默认position为static，在文档流中是从上往下，从左到右的紧密布局，不可以通过top、left等属性直接改变它的偏移
```

**absolute和relative的区别：**

正常情况的div排序：

```html
<body>
    <style>
        #A {
            background-color:mediumpurple;
            height: 100px;
        }
        #B_Father {
            background-color:#a1afc9;
            height: 100px;
        }
        #B {
            background-color:#9ed900;
            height: 50px;
        }
    </style>
    
    <div id='A'>A号div</div>
    <div id='B_Father'>
        <div id='B'>B号div</div>
    </div>    
</body>
```

<img src="https://img-blog.csdnimg.cn/2020050611284817.png" style="float:left">

---

absolute：

使用`absolute`定位时，会脱离文档流，若不具体声明宽高，默认宽高为子元素宽高（如这里的文本）

特殊技巧：

默认情况下（不声明宽高时），div的高由填充的子元素确定，宽填满全屏幕

要让一个div的宽随着子元素的宽度变化，可令该父div声明`absolute`定位，这样该div的宽度就能随着子元素的文档流宽度变化

```css
#B {
	background-color:#9ed900;
	position: absolute;
	top:40px;
	left: 40px;
}
```

由于最近的非static为`<html>`，因此`top`和`left`都是相对于html来决定的

<img src="https://img-blog.csdnimg.cn/20200506113010432.png" style="float:left">

---

若B的父元素声明了非static定位，则会根据B_Father来定位

```css
#B_Father {
	background-color:#a1afc9;
	height: 100px;
	position: relative;
}
#B {
	background-color:#9ed900;
	position: absolute;
	top:40px;
	left: 40px;
}
```

<img src="https://img-blog.csdnimg.cn/20200506113150184.png" style="float:left">

----

relative：

使用`relative`定位时，会根据自身原来的定位进行相对定位（偏移），同时进行偏移时文档流依然保留在原来的地方，不会影响其他元素的位置，可以理解为只是显示的位置变了而已

```css
<body>
    <style>
        #A {
            background-color:mediumpurple;
            height: 100px;
        }
        #B_Father {
            background-color:#a1afc9;
            height: 100px;
            
        }
        #B {
            background-color:#9ed900;
            height: 50px;
            width: 80px;
            position: relative;
            top:60px;
            left: 60px;  
        }
    </style>
    <div id='A'>A号div</div>
    <div id='B_Father'>
        <div id='B'>B号div</div>
        B父元素存在的text
    </div> 
<body>
```

原来的位置：由于B号div的存在，因此父元素内的文本被挤到下方显示

<img src="https://img-blog.csdnimg.cn/20200506115015707.png" style="float:left">

```css
#B_Father {
	background-color:#a1afc9;
	height: 100px;
}
#B {
	background-color:#9ed900;
	height: 50px;
    width: 80px;
    left: 60px; 
    top:60px;
}
```

相对与之前的位置偏移了距离左和上60px，且B原来占的文档流并没有消失，只是显示的地方更改而已

<img src="https://img-blog.csdnimg.cn/20200506115137576.png" style="float:left">

-----

**sticky**

当元素在屏幕内，表现为relative，就要滚出显示器屏幕的时候，表现为fixed

前提：

- 设置`position:sticky`
- 必须指定`top、bottom、left、right`4个值之一，否则只会处于相对定位（一般用top:0）

- 父元素不能`overflow:hidden`或者`overflow:auto`属性且不能低于sticky元素的高度（一般设置`overflow:scroll`）

- sticky元素仅在其父元素内生效

```html
<article>
    <section>
        <h4>标题一</h4>
        <p>
            一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一一
        </p>
    </section>
    <section>
        <h4>标题二</h4>
        <p>
            二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二二
        </p>
    </section>
    <section>
        <h4>标题三</h4>
        <p>
            三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三三
        </p>
    </section>
</article>
```

```less
article {
    margin: 100px;
    height: 250px;
    width: 250px;
    border: 1px #ddd solid;
    overflow: scroll;
    section {
        margin: 0;
        padding-bottom: 40px;
        h4 {
            margin: 0;
            background-color: #ddd;
            position: sticky;
            top: 0px;
        }

        p {
            margin: 0;
        }
    }
}

```

特性：当滚动条滑到下一个sticky定位的元素前，会以fixed定位显示，当滑到下一个sticky定位元素时，会更改fixed显示的元素

<img src="https://img-blog.csdnimg.cn/20200524145243443.png" style="float:left">



---

**z-index**

z-index 属性设置元素的堆叠顺序，拥有更高堆叠顺序的元素总是会处于堆叠顺序较低的元素的前面，仅可用于定位元素（非static表示）

`z-index`会影响一些伪类功能的元素起作用，如`:hover`，如果匹配的元素的上层有物体，则判定不到该元素（即使不被挡住能看到）

| 值       | 描述                                    |
| :------- | :-------------------------------------- |
| auto     | 默认。堆叠顺序与父元素相等。            |
| *number* | 设置元素的堆叠顺序。                    |
| inherit  | 规定应该从父元素继承 z-index 属性的值。 |

```
注意：这里的层级是相对而非绝对
如AB为兄弟元素，A的z-index设置为1，B设置为2，A的子元素的设置为99，最终显示在最上层的还是B，因为B已经比A层级高了，因此A的子元素无论层级设置的多高都不会超过B
```



----

### 浮动

**【float布局、flex布局、grid布局都是一种思想，且使用了flex布局之后就不会使用到浮动设置，且浮动设置目前以及被flex布局所取代】**

定义float的元素会变成行内块元素

前者元素如果设置了浮动属性，会对后面元素产生影响

<img src="https://img-blog.csdnimg.cn/20200523182450448.png" style="float:left">

**由于是时时代眼泪的布局思想，因此不再研究**



----

### 盒模型

#### 内容溢出

**overflow：** 规定当内容溢出元素框时发生的事情

| 值      | 描述                                                         |
| :------ | :----------------------------------------------------------- |
| visible | 默认值。内容不会被修剪，会呈现在元素框之外。                 |
| hidden  | 内容会被修剪，并且其余内容是不可见的。                       |
| scroll  | 内容会被修剪，但是浏览器会固定显示滚动条以便查看其余的内容（无论有没有超出）。 |
| auto    | 如果内容被修剪，则浏览器会显示滚动条以便查看其余的内容。     |
| inherit | 规定应该从父元素继承 overflow 属性的值。                     |

```less
// 如：
{ overflow: scroll; }
```

<img src="https://img-blog.csdnimg.cn/20200522140201798.png" style="float:left">

---

**text-overflow：**规定当文本溢出包含元素时发生的事情

| 值       | 描述                                 |
| :------- | :----------------------------------- |
| clip     | 修剪文本。                           |
| ellipsis | 显示省略符号来代表被修剪的文本。     |
| *string* | 使用给定的字符串来代表被修剪的文本。 |

```less
{
    white-space: nowrap;		// 不换行
	overflow: hidden;			// 影藏超出容器的内容
	text-overflow: ellipsis;	// 显示省略符号来代表被修剪的文本
}
```

<img src="https://img-blog.csdnimg.cn/20200521172434800.png" style="margin:0">

----

#### 美化处理

**阴影：box-shadow**

| 值         | 描述                                     |
| :--------- | :--------------------------------------- |
| *h-shadow* | 必需。水平阴影的位置。允许负值。         |
| *v-shadow* | 必需。垂直阴影的位置。允许负值。         |
| *blur*     | 可选。模糊距离。                         |
| *spread*   | 可选。阴影的尺寸。                       |
| *color*    | 可选。阴影的颜色。请参阅 CSS 颜色值。    |
| inset      | 可选。将外部阴影 (outset) 改为内部阴影。 |

```less
{
    width: 100px;
    height: 100px;
    box-shadow: 0 0 10px #ddd;
}
```

<img src="https://img-blog.csdnimg.cn/20200522170938748.png" style="float:left">





---

### 自适应

【ie不兼容，webkit内核的浏览器要加上-webki前缀】

- **fill-available：**自动撑满空闲空间

`fill-available`关键字值的价值在于，可以让元素的100%自动填充特性不仅仅在`block`水平元素上（inline-block）

```less
{
    width:  -webkit-fill-available;
    height: -webkit-fill-available;
}
```

- **fit-content：**将元素宽度收缩为内容宽度（只适用于block，inline-block不可）

- **max-content：**采用内部元素宽度值最大的那个元素的宽度作为最终容器的宽度（文字不换行）

- **min-content：**采用内部元素最小宽度值最大的那个元素的宽度作为最终容器的宽度

  文本：如果是中文，则最小宽度为一个中文字符，如果为单词，为最长一个单词长度，最小宽度度为最长的那个单词长度，如果为数字，则为最长的那个数字

  图片：最小宽度为图片长度

```html
<div className='father' >
    <div>this is word</div>
    <div>这是一串字符</div>
</div>
```

```less
.father {
    width: min-content;
    background-color: #ddd;
}
```

最小宽度中，英语单词中word最长，汉字固定为一个汉字宽度，word比一个汉字的宽度更宽，因此最大的宽度为word的宽度，父元素就以word的宽度作为自己的宽度

<img src="https://img-blog.csdnimg.cn/2020052215235733.png" style="float:left">



---

### flex布局

[详细](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)

弹性布局，任何一个容器都可以指定为flex布局

设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效

```less
// 块状元素
.box{
  display: flex;
}

// 行内元素
.box{
  display: inline-flex;
}
```

----

采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"

**容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）**

主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；

交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`

项目默认沿主轴排列，单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`

<img src="http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071004.png" style="float:left">

----

**容器属性**

- **flex-direction**：定义主轴的方向（即项目的排列方向）

  ```css
  row | row-reverse | column | column-reverse
  ```

- **flex-wrap**：定义项目换行方式

  ```css
  nowrap | wrap | wrap-reverse
  ```

- **flex-flow**：`flex-direction` 和 `flex-wrap` 的简写

  ```css
  <flex-direction> || <flex-wrap>
  ```

- **justify-content**：定义项目在主轴上的对齐方式

  ```css
  flex-start | flex-end | center | space-between | space-around
  ```

- **align-items**：定义项目在交叉轴上如何对齐

  ```css
  flex-start | flex-end | center | baseline | stretch
  ```

- **align-content**：定义了存在换行时，各行项目的对其方式（不换行，即只有一条主轴时不起作用）

  ```css
  flex-start | flex-end | center | space-between | space-around | stretch
  ```

**项目属性**

- **order**：定义项目的排列顺序。数值越小，排列越靠前

- **flex-grow**：定义项目的放大比例（总项目宽或高＜容器宽或高时适用）

  ```less
  // flex-grow的比例越高代表该元素最终宽高越大
  // 如三个宽度100px的块ABC在宽度为400px的容器中，由于总宽度为300px＜400px，因此会启用flex-grow属性
  .A { flex-grow: 0; }
  .B { flex-grow: 1; }
  .C { flex-grow: 2; }
  // 最终宽度计算公式：容器空余宽度值/felx-grow总比例*对应项目felx-grow比例+对应项目原宽
  // 如C最终宽度为(400-300)/(0+1+2)*2+100 = 166px
  // 如果ABC都没有具体的宽度，则相当于ABC设置宽度为0，那么最终C的宽度为400/(0+1+2)*2+0 = 266px
  ```

- **flex-shrink**：定义了项目的缩小比例（总项目宽或高＞容器宽或高时适用，一般只会设置为0或1)

  ```less
  // flex-grow的比例越高代表该元素最终宽高越小
  // 如三个宽度100px的块ABC在宽度为200px的容器中，由于总宽为300px＞200px，因此会启用flex-shrink属性
  .A { felx-shrink: 0; }
  .B { felx-shrink: 1; }
  .C { felx-shrink: 2; }
  // 最终宽度计算公式：对应项目原宽-超出容器宽度值/felx-grow总比例*对应项目felx-grow比例
  // 如C最终宽度为100-(300-200)/(0+1+2)*2 = 33px
  // 同理：A不会进行压缩，B最终宽度为67px
  ```

- **flex-basis**：分配多余空间之前，项目占据的主轴空间

  ```
  flex-basis：与直接定义宽高width或height功能一样（定义的是宽还是高取决于主轴方向）
  优先级：max-width(min-width) > flex-basis > width 【高同理】
  ```

- **flex**：是 `flex-grow`、`flex-shrink` 和 `flex-basis` 的缩写

- **align-self**：允许单个项目有与其他项目不一样的对齐方式（只作用于交叉轴），可覆盖 `align-items` 属性

  ```css
  auto | flex-start | flex-end | center | baseline | stretch
  ```



---

### 栅格

```
设为网格布局以后，容器子元素（项目）的
float
display: inline-block
display: table-cell
vertical-align
column-*
等设置都将失效
```

<img src="https://img-blog.csdnimg.cn/20200524175748607.png" style="float:left;width:40%;height:40%">

```less
// 块状元素
.box{
  display: grid;
}

// 行内元素
.box{
  display: inline-grid;
}
```

| 属性                      | 功能             |
| ------------------------- | ---------------- |
| **grid-template-columns** | 定义每一列的列宽 |
| **grid-template-rows**    | 定义每一行的行高 |

```less
// 指定了一个三行三列的网格，列宽和行高都是100px
.container {
    dispaly: grid;
    grid-temlate-colunms: 100px 100px 100px
    grid-temlate-rows: 100px 100px 100px
}

// 使用百分比
.container {
  display: grid;
  grid-template-columns: 33.33% 33.33% 33.33%;
  grid-template-rows: 33.33% 33.33% 33.33%;
}

// 使用repeat() 第一个参数是重复的次数，第二个参数是所要重复的值
.container {
  display: grid;
  grid-template-columns: repeat(3, 33.33%);
  grid-template-rows: repeat(3, 33.33%);
}

// 重复生成两次3个指定宽度的列，即会生成6个列
.container {
  display: grid;
  grid-template-columns: repeat(2, 100px 20px 80px);
}
```

**关键字**

- **auto-fill** 

  有时，单元格的大小是固定的，但是容器的大小不确定。如果希望每一行（或每一列）容纳尽可能多的单元格，这时可以使用`auto-fill`关键字表示自动填充

```less
// 每列宽度100px，然后自动填充，直到容器不能放置更多的列
.container {
  display: grid;
  grid-template-columns: repeat(auto-fill, 100px);
}
```

- **fr**

  方便表示比例关系

```less
// 第二列为第一列的两倍宽
.container {
  display: grid;
  grid-template-columns: 1fr 2fr;
}
```

- **minmax()**

  `minmax()`函数产生一个长度范围，表示长度就在这个范围之中，在项目总高宽大于或小于容器高宽时会起作用

```LESS
// minmax(100px, 1fr)表示列宽不小于100px，不大于1fr
.container {
	grid-template-columns: 1fr 1fr minmax(100px, 1fr);
}
```

- **auto**

  表示由浏览器自己决定长度

```less
// 第二列的宽度，基本上等于该列单元格的最大宽度，除非单元格内容设置了min-width，且这个值大于最大宽度
.container {
	grid-template-columns: 100px auto 100px;
}
```

**网格线命名**

使用方括号，指定每一根网格线的名字，方便以后的引用

网格布局允许同一根线有多个名字，比如`[fifth-line row-5]`

```less
// 指定网格布局为3行 x 3列，因此有4根垂直网格线和4根水平网格线。方括号里面依次是这八根线的名字
.container {
  display: grid;
  grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4];
  grid-template-rows: [r1] 100px [r2] 100px [r3] auto [r4];
}
```

**其他容器属性**

- **grid-row-gap，grid-column-gap（已废弃）**

  `grid-row-gap`属性设置行与行的间隔（行间距），`grid-column-gap`属性设置列与列的间隔（列间距）

```less
.container {
  grid-row-gap: 20px;
  grid-column-gap: 20px;
}
```

----

- **grid-gap（已废弃）**

  `grid-row-gap`和`grid-column-gap`的合并简写，如果`grid-gap`省略了第二个值，浏览器认为第二个值等于第一个值

```less
.container {
	grid-gap: 20px 20px;
}
```

----

- **grid-template-areas**

  网格布局允许指定"区域"（area），一个区域由单个或多个单元格组成。`grid-template-areas`属性用于定义区域

```less
// 划分出9个单元格，然后将其定名为a到i的九个区域，分别对应这九个单元格
.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-template-areas: 'a b c'
                       'd e f'
                       'g h i';
}
```

​		多个单元格合并成一个区域

```less
// 将9个单元格分成a、b、c三个区域
{
    grid-template-areas: 'a a a'
                     	 'b b b'
                     	 'c c c';
}

// 顶部是页眉区域header，底部是页脚区域footer，中间部分则为main和sidebar
{
	grid-template-areas: "header header header"
                         "main main sidebar"
                         "footer footer footer";
}

// 如果某些区域不需要利用，则使用.来表示，表示没有用到该单元格，或者该单元格不属于任何区域
{
	grid-template-areas: 'a . c'
                         'd . f'
                         'g . i';
}
```

```markdown
注意，区域的命名会影响到网格线。每个区域的起始网格线，会自动命名为`区域名-start`，终止网格线自动命名为`区域名-end`
	比如，区域名为header，则起始位置的水平网格线和垂直网格线叫做header-start，终止位置的水平网格线和垂直网格线叫做header-end
```

---

- **grid-auto-flow**

  划分网格以后，容器的子元素会按照顺序，自动放置在每一个网格。默认的放置顺序是"先行后列"，即先填满第一行，再开始放入第二行

  这个顺序由`grid-auto-flow`属性决定，默认值是`row`，即"先行后列"。也可以将它设成`column`，变成"先列后行"

```less
{
    grid-auto-flow: column;
}
```

​		除了设置成`row`和`column`，还可以设成`row dense`和`column dense`。这两个值主要用于，某些项目指定位置以后，剩下的项目怎么自动放置

​		**1号项目和2号项目各占据两个单元格情况下：**

```less
// 1号项目后面的位置是空的，这是因为3号项目默认跟着2号项目，所以会排在2号项目后面
{ grid-auto-flow: row }
```

<img src="https://www.wangbase.com/blogimg/asset/201903/bg2019032513.png" style="float:left;width:20%;height:20%">

```less
// "先行后列"，并且尽可能紧密填满，尽量不出现空格
{ grid-auto-flow: row dense; }
```

<img src="https://www.wangbase.com/blogimg/asset/201903/bg2019032514.png" style="float:left;width:20%;height:20%">

```less
// "先列后行"，并且尽量填满空格
{ grid-auto-flow: column dense; }
```

<img src="https://www.wangbase.com/blogimg/asset/201903/bg2019032515.png" style="float:left;width:20%;height:20%">

```
如果设定行先，则只会挤压产生新的行而容器总列宽不会发生变化
如果设定列先，则只会挤压产生新的列而容器总行高不会发生变化
```

---

- **justify-items，align-items，place-items**

  `justify-items`属性设置单元格内容的水平位置（左中右），`align-items`属性设置单元格内容的垂直位置（上中下）

  默认`stretch`，占满单元格的整个高宽

  `place-items`为合并简写，如果省略第二个值，则浏览器认为与第一个值相等

```less
.container {
  justify-items: start | end | center | stretch;
  align-items: start | end | center | stretch;
}
```

​		如内容物左对齐

<img src="https://www.wangbase.com/blogimg/asset/201903/bg2019032516.png" style="float:left;width:40%;height:40%">

---

- **justify-content，align-content，place-content**

  这几个属性需要栅格内容区小于容器区才体现作用，如 `400x400`的容器栅格总宽高只有 `300x300`

  ```less
.container {
    height: 400px;
    width: 400px;
    grid-template-columns: repeat(6, 50px);
    grid-template-rows: repeat(6, 50px);
}
  ```
​	  `justify-content`属性是整个内容区域在容器里面的水平位置（左中右），`align-content`属性是整个内容区域的垂直位置（上中下）

 	 `place-content`是合并简写，如果省略第二个值，浏览器就会假定第二个值等于第一个值

```less
.container {
    justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
    align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
}
```

```markdown
start 			- 对齐容器的起始边框
end 			- 对齐容器的结束边框
center 			- 容器内部居中
stretch 		- 项目大小没有指定时，拉伸占据整个网格容器
space-around 	- 项目与项目的间隔相等 (项目之间的间隔比项目与容器边框的间隔大一倍)
space-between 	- 项目与项目的间隔相等 (项目与容器边框之间没有间隔)
space-evenly 	- 项目与项目的间隔相等 (项目与容器边框之间也是同样长度的间隔)
```

​		`justify-content`各情况，`align-content`同理

<img src="https://img-blog.csdnimg.cn/20200524201318280.png" style="float:left;">

----

- **grid-auto-columns， grid-auto-rows**

  一些项目的指定位置，在现有网格的外部。比如网格只有3列，但是某一个项目指定在第5行。这时，浏览器会自动生成多余的网格，以便放置项目

  `grid-auto-columns`属性和`grid-auto-rows`属性用来设置，浏览器自动创建的多余网格的列宽和行高

  它们的写法与`grid-template-columns`和`grid-template-rows`完全相同

  如果不指定这两个属性，浏览器完全根据单元格内容的大小，决定新增网格的列宽和行高

```less
// 指定新增的行高统一为50px（原始的行高为100px）
.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-auto-rows: 50px; 
}
```

<img src="https://www.wangbase.com/blogimg/asset/201903/bg2019032525.png" style="float:left;width:20%;height:20%">

----

- **grid-template，grid**

  `grid-template`属性是`grid-template-columns`、`grid-template-rows`和`grid-template-areas`这三个属性的合并简写形式

  `grid`属性是`grid-template-rows`、`grid-template-columns`、`grid-template-areas`、 `grid-auto-rows`、`grid-auto-columns`、`grid-auto-flow`这六个属性的合并简写形式

  建议不要这样直接合并写

----

**项目属性**

- **grid-column-start， grid-column-end，grid-row-start，grid-row-end**

  项目的位置是可以指定的，具体方法就是指定项目的四个边框，分别定位在哪根网格线

  使用这四个属性，如果产生了项目的重叠，则使用`z-index`属性指定项目的重叠顺序

  除了指定为第几个网格线，还可以指定为网格线的名字
  
  序列从1开始而非0

```less
// 1号项目的左边框是第二根垂直网格线，右边框是第四根垂直网格线
// 只指定了1号项目的左右边框，没有指定上下边框，所以会采用默认位置，即上边框是第一根水平网格线，下边框是第二根水平网格线
.item-1 {
  grid-column-start: 2;
  grid-column-end: 4;
}
```

<img src="https://www.wangbase.com/blogimg/asset/201903/bg2019032526.png" style="float:left;width:20%;height:20%">

​		这四个属性的值还可以使用`span`关键字，表示"跨越"，即左右边框（上下边框）之间跨越多少个网格

```less
.item-1 {
  grid-column-start: span 2;
}
// 和这个效果完全一样，只用一个就可以
.item-1 {
  grid-column-end: span 2;
}
// 搭配起始线使用
.item-1 {
  grid-row-start: 1;
  grid-row-end: span 1;
  grid-column-start: 1;
  grid-column-end: span 2;
}
```

<img src="https://www.wangbase.com/blogimg/asset/201903/bg2019032528.png" style="float:left;width:20%;height:20%">

---

-  **grid-column，grid-row**

  `grid-column`属性是`grid-column-start`和`grid-column-end`的合并简写形式，`grid-row`属性是`grid-row-start`属性和`grid-row-end`的合并简写形式

  斜杠以及后面的部分可以省略，默认跨越一个网格

```less
.item-1 {
  grid-column: 1 / 3;	// 用/进行隔开而不是空格
  grid-row: 1 / 2;
}
// 等同于 
.item-1 {
  grid-column-start: 1;
  grid-column-end: 3;
  grid-row-start: 1;
  grid-row-end: 2;
}
```

```less
.item-1 {
  background: #b03532;
  grid-column: 1 / 3;
  grid-row: 1 / 3;
}
/* 等同于 */
.item-1 {
  background: #b03532;
  grid-column: 1 / span 2;
  grid-row: 1 / span 2;
}
```

---

- **grid-area**

  指定项目放在哪一个区域

```less
.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-template-areas: 'a b c'
                       'd e f'
                       'g h i';
}

// 中间的格子命名为e，放在
.item-1 {
  grid-area: e;
}
```

<img src="https://www.wangbase.com/blogimg/asset/201903/bg2019032530.png" style="float:left;width:20%;height:20%">

​		`grid-area`属性还可用作`grid-row-start`、`grid-column-start`、`grid-row-end`、`grid-column-end`的合并简写形式，直接指定项目的位置

```less
.item {
  grid-area: <row-start> / <column-start> / <row-end> / <column-end>;
}
```

```less
// 如
.item-1 {
  grid-area: 1 / 1 / 3 / 3;
}
```

---

- **justify-self ， align-self ， place-self**

  `justify-self`属性设置单元格内容的水平位置（左中右），跟`justify-items`属性的用法完全一致，但只作用于单个项目。

  `align-self`属性设置单元格内容的垂直位置（上中下），跟`align-items`属性的用法完全一致，也是只作用于单个项目。

  `place-self`属性是`align-self`属性和`justify-self`属性的合并简写形式，如果省略第二个值，`place-self`属性会认为这两个值相等

```less
.item {
  justify-self: start | end | center | stretch;
  align-self: start | end | center | stretch;
}
```

-----































