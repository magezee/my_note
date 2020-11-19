### 使用选择器动态更改样式

**如属性选择器**

写入匹配不同模式的css，为标签添加指定属性时，则可更改样式

```less
[custom="large" ] {
    ...
}

[custom="middle"]

// 更改类名更改样式同理
[class="lager"]
```

```html
<div custom='lager' />
<div custom='middle' />
```

如实现一个通过属性自由更改栅格长度的功能

```less
// 设置栅格元素宽度
.col-1 {
    grid-column: span 1;
}

.col-2 {
    grid-column: span 2;
}

.col-3 {
    grid-column: span 3;
}
.....

[class^="col-"] {
    // 设置栅格元素同一样式
}

// <div class="col-3" /> 
```





----

### 处理高度

现在主流的高度方法：不直接设置高度，高度由内容撑开，设置margin隔开相邻元素



---

### 父元素随着子元素宽度变化

- 给父元素设置行内块元素显示：`display:inline-block`（只给子元素设置无法实现功能）
- 父元素设置绝对定位：`position:absolute`
- 设置浮动：`float:left`
- 父元素设置flex布局：`display:flex`

---

### 令两个块状元素并排

默认情况下块状元素会换行

- 切换为行内元素或者行内块状元素（注意：A、B若想并排，需要同时设置A、B，B才能和A同行，只设置任意一个都不行）
- 使用flex布局
- 两个都设置为`float:left`

---

### 使用vertical-align设置子元素高度

需满足两个前置条件：

- 父元素为块状元素（`inline-block`或`block`），为`inline-block`时，必须设置行高`line-height`属性
- 子元素为行内元素（`inline-block`或`inline`），vertical-align不可继承，必须对子元素单独设置

----

### 带图标的表单元素

设计思路：将一个div分成两块，一块做表单，一块做图标

使用到`::after`伪类

```jsx
<div >
    <input type="text"/>
    <span></span>
</div>
```

```less
div {
    border: solid 1px #ddd;
    width: 180px;
    
}

div>input[type='text'] {    // 匹配div子元素带有`type='text`属性的input标签
    border:none;
    outline: none;      // 清除获得焦点时产生的外边框
}

div>input[type='text']+span::after {    // 匹配span标签
    content: '\21B7';   // 符号字符编码
    cursor: pointer;    // 鼠标移到图标时将指针改为手型指针
}
```

<img src="https://img-blog.csdnimg.cn/20200521154432933.png" style="margin:0">

---

### 设置行高

一般行高都不会直接设置具体值，因为如果改变字体大小行高固定死就不会合适了，因此一般使用相对大小

```css
line-height: 1.5em
```

---

### 实现居中

#### 实现水平居中

```html
<div className='father'>
    <div className='child' />
</div>
```

**对于行内元素：**

```less
.father {
    display: inline-block;
    text-align: center;		// 一句话便可
    
    .child {
        display: inline;
    }
}
```

**对于确定宽度的块级元素：**

- margin和width实现水平居中（常用，需要设置了width值）

  但是这种方式子元素的宽度实际上是占满父元素的，因为有margin值

```less
.father {  
    .child {
        witth:50px;
        margin-left: auto;
        margin-right: auto;
    }
}
```

**对于未知宽度的块级元素：**

- table标签配合margin左右auto实现水平居中

  直接将块级元素设值为`display:table`，再通过给该标签添加左右margin为auto（上下为auto时不会垂直居中）

```less
.father {
    .child {
        display: table;
        margin: auto;
    }
}
```

- inline-block实现水平居中方法

  display：inline-block;（或display:inline）和text-align:center;实现水平居中

```less
.father {
    width: 200px;
    text-align: center;
    
    .child {
        display:inline-block;  
    }
}
```

#### 实现垂直居中

```jsx
<div >
    <input type="text"/>
    <span></span>
</div>
```

**使用vertical-algin:middle**

将父类设置为`table`，子类设置成`table-cell`，就可以使用表格的 `vertical-align`属性

```less
.father {
    display: table;
    width: 200px;
    height: 400px;
    
    .child {
       display: table-cell;
       vertical-align: middle;
    }
}
```

---

#### 同时实现水平和垂直居中

**对于确定宽高的元素**

- 使用绝对定位

方法一：移动自身

```less
.father {
    width: 200px;
    height: 200px;
    border: solid 1px #ddd;
    
    position: relative;
    
    .child {
        width: 50px;
        height: 50px;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);   // 在屏幕坐标系x、y轴移动自身长度的-50%，即向左和向上移动（屏幕坐标系左上角为原点）
        // 不可以分开transform: translateX(-50%);transform: translateY(-50%); 后一个transform属性会覆盖前一个，因此只有后一个生效，所以要同时写
        border: solid 1px red;
    }
}
```

使用left将自身移动到距父元素宽度一半的位置，因此此时元素最左边在父元素中点，只要再向左移动自身宽度一般则自身中心在父元素中点，实现左右居中，上下同理

<img src="https://img-blog.csdnimg.cn/20200522095947825.png" style="margin:0">

方法二：使用margin撑到中间（这种方式必须要宽高都有值才行）

当设置`left: 0`，`right: 0`时，`margin:auto`就会撑满左右两边以此达到距父元素左右0的情况，达到水平居中，上下同理

自身有宽高度时，使用 `margin:auto` 可以水平居中，但是要垂直居中必须要设置 `top:0` 和 `bottom:0` 才可以

```less
.father {
    width: 200px;
    height: 200px;
    border: solid 1px #ddd;
    
    position: relative;
    
    .child {
        width: 50px;
        height: 50px;
        position: absolute;
        top: 0;
        right: 0;
        bottom: 0;
        left: 0;
        margin: auto;
        border: solid 1px red;
    }
}
```

**对于未知宽高的元素**

- 使用flex布局

方法一：

```less
.father {
    width: 200px;
    height: 100px; 
    display: flex;
    justify-content: center;	// 实现水平居中

    .child {
         align-self: center; 	// 实现垂直居中  或者父元素内定义align-items: center;也可以
    }
}
```

方法二：

```less
.father {
    width: 200px;
    height: 100px; 
    border: solid 1px #ddd;
    display: flex;	
   
    .child {
        margin: auto;      
    }
}
```

----

### 边框使用

有些效果可以直接使用边框整出来

```less
div {
    padding: 0 20px 0;
    border-bottom: 3px solid red;
    border-bottom-left-radius: 50%;
    border-bottom-right-radius: 50%;
    
}
```

<img src="https://img-blog.csdnimg.cn/20200522105400754.png" style="margin:0">

**四周圆角**

将四周圆角长度设置为远大于元素长度

```less
border-radius:99999px; 
```

<img src="https://img-blog.csdnimg.cn/20200531211147134.png" style="margin:0" />



----

### 元素隐藏

```less
{
    display: none;		// 元素文档流消失
    visibility: hidden;	// 元素隐藏，文档流保持不动
    opactity: 0;		// 直接将透明度为0，和visibility功能一样
    transform: scale(0) // 元素缩放至0，和相对定位类似，即使元素放大，其文档流所占大小和位置是不变的  
}	
```

**结构：**

- **display:none：** 会让元素完全从渲染树中消失，渲染的时候不占据任何空间, 不能点击

- **visibility: hidden：**不会让元素从渲染树消失，渲染元素继续占据空间，只是内容不可见，不能点击
- **opacity: 0：** 不会让元素从渲染树消失，渲染元素继续占据空间，只是内容不可见，可以点击

**继承：**

- **display: none、opacity: 0：**非继承属性，子孙节点消失由于元素从渲染树消失造成，通过修改子孙节点属性无法显示

-  **visibility: hidden：**继承属性，子孙节点消失由于继承了hidden，通过设置`visibility: visible;`可以让子孙节点显式

**性能：**

- **displaynone ：** 修改元素会造成文档回流，读屏器不会读取`display: none`元素内容，性能消耗较大
- **visibility:hidden：** 修改元素只会造成本元素的重绘,性能消耗较少读屏器读取`visibility: hidden`元素内容
- **opacity: 0 ：** 修改元素会造成重绘，性能消耗较少

----

### 使用css定义表格

| 值                 | 属性                                                         |
| ------------------ | ------------------------------------------------------------ |
| table              | 此元素会作为块级表格来显示（类似 <table>），表格前后带有换行符。 |
| inline-table       | 此元素会作为内联表格来显示（类似 <table>），表格前后没有换行符。 |
| table-row-group    | 此元素会作为一个或多个行的分组来显示（类似 <tbody>）。       |
| table-header-group | 此元素会作为一个或多个行的分组来显示（类似 <thead>）。       |
| table-footer-group | 此元素会作为一个或多个行的分组来显示（类似 <tfoot>）。       |
| table-row          | 此元素会作为一个表格行显示（类似 <tr>）。                    |
| table-column-group | 此元素会作为一个或多个列的分组来显示（类似 <colgroup>）。    |
| table-column       | 此元素会作为一个单元格列显示（类似 <col>）                   |
| table-cell         | 此元素会作为一个表格单元格显示（类似 <td> 和 <th>）          |
| table-caption      | 此元素会作为一个表格标题显示（类似 <caption>）               |

```html
<div className='talble'> 
    <section>
        <ui>
            <li>编号</li>
            <li>标题</li>
        </ui>    
    </section>     
    <section>
        <ui>
            <li>1</li>
            <li>标题1</li>
        </ui>    
    </section>
    <section>
        <ui>
            <li>2</li>
            <li>标题2</li>
        </ui>    
    </section>             

</div>
```

<img src="https://img-blog.csdnimg.cn/20200523120027573.png" style="margin:0">

```less
.table {
    display: table;
    
    section {
        &:nth-of-type(1) {  // less符号&，相当于section:nth-of-type(1)
            display: table-header-group;
        }

        &:nth-of-type(2) {
            display: table-row-group;
        } 

        &:nth-of-type(3) {
            display: table-footer-group;
        }

        ul {
            display: table-row;

            li {
                display: table-cell;
            }
        }
    }
}
```

<img src="https://img-blog.csdnimg.cn/20200523122316569.png" style="margin:0">

可以加点样式

```less
.table {
    margin: 50px;
    display: table;
    
    section {
        &:nth-of-type(1) {  // less符号&，相当于section:nth-of-type(1)
            display: table-header-group;
            font-weight:bold;
            background-color: rgba(189, 134, 134, 0.2);
        }

        &:nth-of-type(2) {
            display: table-row-group;
        } 

        &:nth-of-type(3) {
            display: table-footer-group;
        }

        ul {
            display: table-row;

            li {
                
                display: table-cell;
                border: 1px #ddd solid;
                padding: 10px;
            }
        }
    }
}
```

<img src="https://img-blog.csdnimg.cn/20200523122915829.png" style="margin:0">

```less
.table {
    display: table;
    
    section {
        &:nth-of-type(1) {  
            display: table-header-group;
            font-weight:bold;
        }

        &:nth-of-type(n+1) {
            display: table-row-group;
        } 

        ul {
            display: table-row;
            border: none;
            

            li {
                border-top: solid 1px #ccc;
                
                display: table-cell;               
                padding: 10px 30px;
            }
        }
    }
}
```

<img src="https://img-blog.csdnimg.cn/20200523142051846.png" style="margin:0">

-----

### 动画实现鼠标悬浮效果

`transition`不要放在伪类选择器中，因为这样实现效果就会有额外条件

```html
<main>
    <div><img src="./edit.jpg" alt="" /></div>
    <div><img src="./home.jpg" alt="" /></div>
    <div><img src="./comment.jpg" alt="" /></div>
</main>
```

```less
// 当鼠标移入时，指向的那个图标元素会放大
main {
    margin: 100px;
    display: flex;

    div {
        width: 200px;
        height: 200px;
        margin: 10px;
        border: solid 5px;
		transition: 0.3s;
        
        &:hover {
               transform: scale(1.1);  
        }

        img {
           width: 100%;
           height: 100%;   
        }
    }
}
```

<img src="https://img-blog.csdnimg.cn/20200525204228707.png"  />

也可以设置成其他效果

```less
// 鼠标移入时，指定元素放大，其他元素全部缩小表示
main {
    margin: 100px;
    display: flex;
	transition: 1s;
    
    &:hover div {	// 鼠标移入最外层，所有元素缩小
        
        transform: scale(.8);
    }

    div {
        width: 200px;
        height: 200px;
        border: solid 5px;
		transition: 0.3s;
        
        &:nth-child(2){
            margin: 0 20px;
        }

        &:hover {	// 鼠标移入指定元素，指定元素放大   
            transform: scale(1.1);  
        }

        img {
           width: 100%;
           height: 100%;   
        }
    }
}
```

<img src="https://img-blog.csdnimg.cn/20200525205047109.png" style="margin:0" />

还可以设置其他效果，如只有内部图像变大，外框不动等等

---

### 动态logo

```html
<main>
    <div>
        <strong>G</strong>egeda
    </div>
</main>
```

```less
main {
    width: 500px;
    height: 500px;
    background-color: #CCCCCC;
    display: flex;
    justify-content: center;
    align-items: center;

    div {
        font-size: 2em;
        color: #666699;

        strong {
            display: inline-block;  // <strong>是行内元素标签
            background-color: #99CCFF;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            border: 1px solid;
            text-align: center;
            transition: 1s;
            

            &:hover {

                transform:  rotate(-30deg );
            }
        }
    }

}
```

<img src="https://img-blog.csdnimg.cn/20200526104557329.png" style="margin:0" />

-----

### 制作翻转卡片

当1元素翻转到背面时，会显示2元素的内容（如果不用`backface-visibility: hidden`则翻转到背面也只显示1内容，2内容无法显示）

```html
<main>
    <div>1</div>
    <div>2</div>
</main>
```

```less
main {
    display: flex;
    width: 200px;
    height: 200px;
    border: solid 1px;
    transition: 1s;
    justify-content: center;
    align-items: center;
    position: relative;
    perspective: 900px ;    // 不要设置transform-style: preserve-3d;否则能看到z轴，会看到两个元素而非重合
    
 
    &:hover div:nth-child(1) {
      transform: rotateY(-180deg);      // 1转的同时设置2同方向转动
    }

    &:hover div:nth-child(2) {
        transform: rotateY(0deg);      // 1转的同时设置2同方向转动
      }

    div {
        position: absolute;     // 设置绝对定位以此达到两个元素重合
        display: flex;
        justify-content: center;
        align-items: center;
        width: 100px;
        height: 100px;
        font-size: 2em;
        transition: 2s;
        backface-visibility: hidden;    // 隐藏元素背面
      
        &:nth-child(1) {
            background-color: rgb(230, 76, 76);   
        }

        &:nth-child(2) {
            background-color: rgb(166, 236, 119);
            transform: rotateY(180deg);     // 翻转到元素背面，达到隐藏效果
        }
    }
}
```

<img src="https://img-blog.csdnimg.cn/20200526220148691.png" style="margin:0" />

---

### 纯css实现点击控制显示影藏

使用label与check表单绑定，点击label元素可以控制check的true/false

通过伪类选择表单check的选中状态以此控制选中和未选中的样式

```html
<main>
    <label className='btn' for="control">按钮</label>
    <input type="checkbox" id="control" />
    <div className='element'>控制显示的元素</div>
</main>
```

```less
main {
    .btn {
        background-color: #ddd;
        text-align: center;
        width: 100px;
        border: solid 1px;
        border-radius: 6%;
    }
    
	input:checked  { 
        & + .element {      // 选中状态下选择紧邻自己的element元素（不这样写选择不了）
            display: block;
        }
    }

    .element {
        display: none;		// 默认不显示
    }
	
    input {
        display: none;		// 不显示check，如果显示就会跟配图一样
    }

}
```

<img src="https://img-blog.csdnimg.cn/20200529153134365.png" style="margin:0" />

----

### 响应式布局

#### 栅格系统

实现一个全局可复用的的响应栅格样式

```html
<main className='row '>
    <div className='col-lg-3'>1</div>
    <div className='col-lg-1'>2</div>
    <div className='col-lg-4'>3</div>
</main>
```



```less
// 将要使用栅格的元素分成24列
.row {
    display: grid;
    grid-template-columns:repeat(24,1fr);
   
   
}

// 循环生成指定栅格列宽的样式属性 如 .col-lg-4{grid-column: span 4;}
.loop(@index) when (@index > 0) {
    .loop(@index - 1);
    .col-lg-@{index} {
        grid-column: span @index;
    }
}

.loop(24);


main div {
    border: 1px solid;
    text-align: center;
}
```

<img src="https://img-blog.csdnimg.cn/20200530114156818.png" style="margin:0" />



通过窗口大小实现不同的响应布局

```less
// large.less
.row {
    display: grid;
    grid-template-columns:repeat(24,1fr);
}
.loop(@index) when (@index > 0) {
    .loop(@index - 1);
    .col-small-@{index} {
        grid-column: span @index;
    }
}
.loop(24);
main div {
    border: 1px solid;
    text-align: center;
}
```

```less
// small.less
.row {
    display: grid;
    grid-template-columns:repeat(24,1fr);
}
.loop(@index) when (@index > 0) {
    .loop(@index - 1);
    .col-small-@{index} {
        grid-column: span @index;
    }
}
.loop(24);
main div {
    border: 1px solid;
    text-align: center;
}
```

通过按条件引入不同的less样式实现响应式布局

```less
// index.less
@import url(large.less) only screen and (min-width:768px);
@import url(small.less) only screen and (max-width:768px);
```

将各条件下的样式统一添加到元素中，指定条件下引入不同的样式less文件从而实现条件更改样式

```jsx
// 宽度更多时可以显示更多元素，因此应该在宽度大的时候把元素所占的栅格变小（无论宽度如何变化总栅格都为24）
import 'index.less'

<main className='row '>
    <div className='col-large-8 col-small-12'>1</div>
    <div className='col-large-8 col-small-12'>2</div>
    <div className='col-large-8 col-small-12'>3</div>
</main>
```

<img src="https://img-blog.csdnimg.cn/20200530150533864.png" style="margin:0" />



-----

### 翻页器

通过增加元素中的类`current`来给定选中样式

可以通过js动态添加类

```jsx
<div className="page">
    <a href="" > &lt; </a> {/* >的转义字符 */}
    <a href=""> 1 </a>
    <a href=""> 2 </a>
    <a href="" className="current"> 3 </a>
    <a href=""> 4 </a>
    <a href=""> 5 </a>
    <a href=""> &gt; </a> {/* <的转义字符 */}
</div>
```

```less
.page {
    display: flex;
    
    a {
        display: block;
        padding: .3rem .8rem;
        border: solid 1px #ddd;
        text-decoration: none;
        margin-left: -1px;    // 移除互相重叠的边框
        color: #555;

        // 制造圆角
        &:first-child {
            border-top-left-radius: .3rem;
            border-bottom-left-radius: .3rem;
        }
        &:last-child {
            border-top-right-radius: .3rem;
            border-bottom-right-radius: .3rem;
        }

        &.current {
            border: solid 1px #16a085;
            background-color: #16a085;
            color: #fff;
        }
    }
}
```

<img src="https://img-blog.csdnimg.cn/2020053016421710.png" style="margin:0" />









