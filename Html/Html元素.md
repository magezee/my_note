## 功能标签

### 非页面元素

#### meta

```
参考博客：https://segmentfault.com/a/1190000004279791
```

meta常用于定义元数据，这些元数据包括定义页面说明，关键字，最后修改日期以及其他，元数据是用于描述数据的数据，它不会显示在页面上，但是机器却可以识别，服务于浏览器（如何布局或重载页面），搜索引擎和其他网络等

meta标签只有两个属性：`http-equiv` 和 `name`

**name**

主要用于描述网页，比如网页的关键字，叙述等，与之对应的属性值为 `content`，content 中的内容时对 name 填入类型的具体描述，便于搜索引擎抓取

```jsx
<meta name="参数" content="具体的描述" />
```

参数如下：

- keyword：用于告诉搜索引擎网页关键字

```jsx
<meta name="keywords" content="明日方舟,明日方舟官网,明日方舟手游,二次元,明日方舟Arknights,魔物娘,战棋,策略,塔防,塔防RPG,Arknights,人外,Monster">
```

- description：用于告诉搜索引擎网站主要内容

```jsx
<meta name="description" content="《明日方舟》是一款魔物主题的策略手游。在游戏中，玩家将管理一艘满载“ 魔物干员”的方舟，为调查来源神秘的矿石灾难而踏上旅途。在这个宽广而危机四伏的世界中，你或许会看到废土中的城市废墟，或许会看到仿若幻境的亚人国度，或许会遭遇无法解读的神秘，或许参与无比残酷的战争。在有关幻想与异种生命的世界中，体验史诗与想象，情感与牵绊！">
```

- viewport：常用于设计移动端网页

```jsx
<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1" />

// width			可视区域的宽度，值可为数字或关键词device-width
// height			和width类似
// intial-scale		页面首次被显示是可视区域的缩放级别，取值1.0则页面按实际尺寸显示，无任何缩放
// maximum-scale	可视区域的缩放级别
// minimum-scale	可视区域的缩放级别
// maximum-scale	用户可将页面放大的程序，1.0将禁止用户放大到实际尺寸之上
// user-scalable	是否可对页面进行缩放，no 禁止缩放
```

```
在默认情况下，一般来讲，移动设备上的viewport都是要大于浏览器可视区域的，这是因为考虑到移动设备的分辨率相对于桌面电脑来说都比较小，所以为了能在移动设备上正常显示那些传统的为桌面浏览器设计的网站，移动设备上的浏览器都会把自己默认的viewport设为980px或1024px（也可能是其它值，这个是由设备自己决定的），但带来的后果就是浏览器会出现横向滚动条，因为浏览器可视区域的宽度是比这个默认的viewport的宽度要小的
更多相关信息参考：https://www.cnblogs.com/yelongsan/p/7975580.html
```

- robots：告诉搜索引擎爬虫哪些页面需要索引，哪些不需要，默认all

```jsx
<meta name="robots" content="none" />

// none		搜索引擎将忽略此网页，等价于noindex,nofollow
// noindex  搜索引擎不索引此网页
// nofollow 搜索引擎不继续通过此页面的链接索引其他的网页
// all		搜索引擎将索引此网页与继续通过此网页的链接索引，等价于index,foolow
// index	搜索引擎索引此网页
// follow 	搜索引擎继续通过此网页的链接索引搜索其他的网页
```

- author：用于标注网页作者

```jsx
<meta name="author" content="xq,7565....@qq.com" />
```

- generator：用于表明网页用了哪些技术

```jsx
<meta name="generator" content="React" />
```

- copyright：用于标注版权信息

```jsx
<meta name="copyright" content="xxx" />
```

- revist-after：如果页面不是经常刷新，为了减轻搜索引擎爬虫对服务器的压力，可以设置爬虫重坊时间，如果重坊时间过短，则按定义默认时间访问

```jsx
<meta name="revist-after" content="7 days" />
```

- renderer：用于指定双核浏览器默认以何种方式渲染页面

```jsx
<meta name="renderer" content="webkit" />

// webkit		默认webkit内核
// ie-comp		默认IE兼容模式
// ie-stand		默认IE标准模式
```

----

**http-equiv**

相当于http的作用，如定义http参数

```jsx
<meta http-equiv="参数" content="具体的描述" />
```

参数如下：

- content-Type：用于设定网页字符集，便于浏览器解析与渲染页面

```jsx
<meta http-equiv="content-Type" content="text/html;charset=utf-8" />	// 旧的写法
<meta charset="utf-8" />	// html5的写法
```

- X-UA-Compatible：用于告知浏览器以何种版本来渲染页面（一般都设置为最新模式）

```jsx
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrom=1" />		// 指定IE和Chrome使用最新版本渲染当前页面
```

- cache-control：

  用法一：定义浏览器如何缓存某个响应以及缓存多长时间

```jsx
<meta http-equiv="cache-control" content="no-cache" />

// no-cache		先发送请求，与服务器确认该资源是否被修改，如果未被更改，则使用缓存
// no-store		不允许缓存，每次都要去服务器上，下载完整的响应（安全措施）
// public		缓存所有响应，但并非必须，max-age也可以做到相同效果
// private		只为单个用户缓存，因此不允许任何中继进行缓存（如CDN不允许缓存private的响应）
// maxage		当前请求开始，该响应在多久内能被缓存和重用，而不去服务器重新请求，单位为秒
```

​		用法二：避免百度自动转码

```jsx
<meta http-equiv="cache-control" content="no-siteapp" />
```

- expires：设定网页到期时间，过期后网页必须到服务器上重新上传

```jsx
<meta http-equiv="expires" content="Sunday 26 October 2020 01:00 GMT" />
```

- refresh：网页将在设定的时间内，自动刷新并调向设定的网址，单位为秒

```jsx
<meta http-equiv="refresh" content="2; URL=http://www.baidu.com" />
```

- Set-Cookie：如果网页过期，则这个网页存在本地的cookies也会被自动删除

```jsx
<meta http-equiv="Set-Cookie" content="User=XQ; path=/; expires=Sunday 26 October 2020 01:00 GMT" />
```



---

### 媒体元素

#### a

- 行内元素

- `download `属性指示浏览器下载 URL 资源而不是导航到它，如果该属性设置了值，那么这个值就是下载的文件名

```html
<a href="javascript:;"/>		// 表示点击时什么也不干
<a href="http://www.xxx.com/xxx.exe" download='res.exe'>百度一下</a>
```

---

#### img

- 行内元素
- 可以放在 `<a>` 标签内部，使得图片变成一个可以点击的链接

---



### 表单元素

#### form

- 块状元素
- 用于把浏览者输入的数据传送到服务器端，这样服务器端程序就可以处理表单传过来的数据

```html
<form method="post" action="http://www.baidu.com">
    <input type="text/password" name="data" value="默认值" />
    <button type="submit">提交</button>
</form>
```

---

#### input

- 行内元素‘
- 通过 `type` 属性来控制渲染的表单类型

```markdown
button
	定义可点击按钮
checkbox
	定义复选框，一组复选框name属性必须相同
file
	定义输入字段和 “浏览” 按钮，供文件上传
hidden
	定义隐藏的输入字段
image
	定义图像形式的提交按钮
passwor
	d定义密码字段，该字段中的字符被掩码
radio
	定义单选按钮，一组单选按钮的name属性值必须相同
reset
	定义重置按钮，清除表单中的所有数据
submit
	定义提交按钮，提交到action属性指定的地址
text
	定义单行的输入字段，用户可在其中输入文本，默认宽度为20字符
```

```html
<!-- 文本框 -->
<input type="text" >
<hr>

<!-- 设置文本框颜色 -->
<input type="color">
<hr>

<!-- 密码框，输入密码会以...显示-->
<input type="password">
<hr>

<!-- 单选框 实现多选一必须要设置共同的name -->
<input type="radio" name="gender"> 男
<input type="radio" name="gender"> 女
<hr>

<!-- 复选框 要有共同的name -->
<input type="checkbox" name="hobby"> 唱
<input type="checkbox" name="hobby"> 跳
<input type="checkbox" name="hobby"> Rap
<hr>

<!-- 上传文件，multiple表示可以上传多个文件 -->
<input type="file" multiple>
<hr>
看不见的input: <input type="hidden">
<hr>

<!-- textarea多行文本输入 style属性可以固定文本框大小 -->
<textarea></textarea>
<hr>

<!-- select下拉选框 -->
<select>
    <option value="">-请选择-</option>
    <option value="1">星期一</option>
    <option value="2">星期二</option>
</select> 
```

<img src="https://img-blog.csdnimg.cn/20201217140415165.png" style="margin:0;width:250px">

---

#### select

- 行内元素
- 下拉表单元素，用于定义一个下拉列表

- 标签 `option` 为下拉列表中的元素，定义 `selected="selected"` 属性时，当前项为默认选中项

```html
<select name="year">
    <option selected="selected">--请选择年--</option>
    <option>1990</option>
    <option>2000</option>
    <option>2010</option>
</select>
```

---

#### textarea

文本框

无法自动实现的功能：

- 文本框随着文本输入内容自适应拉长
- 传入图片时显示的是html标签，无法正常显示

----

#### label

`<label>` 标签为 input 元素定义标注，label 元素不会向用户呈现任何特殊效果。不过，它为鼠标用户改进了可用性，如果在 label 元素内点击文本，就会触发此控件。就是说，当用户选择该标签时，浏览器就会自动将焦点转到和标签相关的表单控件上

`<label> ` 标签的 for 属性应当与相关元素的 id 属性相同

可以是用该标签配合 css 实现巧妙的效果，如仅用 CSS 实现切换相关功能

- 使用label与check表单绑定，点击label元素可以控制check的 `true/false`

- 通过伪类选择表单check的选中状态以此控制选中和未选中的样式，隐藏不需要显示的表单

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

## 语义化标签

```
语义类标签对开发者更为友好，使用语义类标签增强了可读性，即便是在没有 CSS 的时候，开发者也能够清晰地看出网页的结构，也更为便于团队的开发和维护
除了对开发者之外，语义类标签也十分适宜机器阅读，它的文字表现力丰富，更适合搜索引擎检索（SEO），也可以让搜索引擎爬虫更好地获取到更多有效信息，有效提升网页的搜索量，并且语义类还可以支持读屏软件，根据文章可以自动生成目录等等
```

### 渲染性

#### i

- 行内元素

- 用于渲染小图标
- 用于区分文本，i 标签内的文本会自动渲染为斜体

```html
<div>
    <i class="dot"/>
    已完成
</div>
```

```less
.dot {
  width: 1.5rem;
  height: 1.5rem;
  border-radius: 100%;
  margin-right: 1rem;
  background-color: #f25f66;
}
```

<img src="https://img-blog.csdnimg.cn/20201026145100883.png#pic_center" style="margin:0">

---

#### em

- 行内元素
- 用于强调某句话，标签内文本会被渲染为斜体，和 `<i>`  标签用法不同的是，i 标签主要用于渲染一整段文本，而 em 是一段文本中的一部分

```html
<i>我今天不舒服</i>
<p>我<em>今天</em>不舒服</p>
```

---

#### u

- 行内元素
- 标签内文本会下划线渲染

---

#### b

- 行内元素
- 用于强调，标签内文本会加粗渲染

---

#### strong

- 行内元素
- 用于强调，标签内文本会加粗渲染，用在比 `<b>` 更强调的文本上

---

#### s

- 行内元素
- 标签内文本会使用删除线样式渲染

---

#### hr

- 块状元素

- 渲染为一条分隔线

---

#### template

- html中的`template`标签中的内容在页面中不会显示。但是在后台查看页面DOM结构存在`template`标签。这是因为template标签天生不可见，它设置了`display:none;`属性

```html
<!--当前页面只显示"我是自定义表现abc"这个内容，不显示"我是template",这是因为template标签天生不可见-->
<template><div>我是template</div></template>
<abc>我是自定义表现abc</abc>
```



---

### 区域性

#### aside

- 块状元素
- 表示一个和其余页面内容几乎无关的部分，被认为是独立于该内容的一部分并且可以被单独的拆分出来而不会使整体受影响，其通常表现为侧边栏或者标注框

---

#### section

- 块状元素
- 表示一个包含在HTML文档中的独立部分，一般来说会有包含一个标题，而且在该标签中的标题元素 `H1~H6` 会下降一级 `H1变成H2`

```html
<section>
  <h1>Heading</h1>
  <p>Bunch of awesome content</p>
</section>
```

---

#### article

- 块状元素

- 用于定义独立于文档且有意义的来自外部的内容，比如：一些投稿文章、新闻记者的文章，或者是摘自其它博客、论坛的信息等

- 表示具有一定独立性质的文章，article 和 body 具有相似的结构，一个 HTML 页面中，可能有多个 article 存在，除了内容主题以外，一个 article 元素通常会有自己的标题及脚注

```html
<article>
    <header>
        <h3>
            <a href=""></a>
        </h3>
    </header>
    <section>
        <p></p>
    </section>
    <footer>
        <small>
            <time datetime=""></time> 
            <a href=""></a>
        </small>
    </footer>
</article>
```

---

#### header

- 块状元素
- 通常出现在前部，表示导航或者介绍性的内容

---

#### footer

- 块状元素
- 通常出现在尾部，包含一些作者信息、相关链接、版权信息等

----

#### nav

- 块状元素
- 专门用于菜单导航，链接导航的元素

----

#### main

- 块状元素
- 定义页面的主要内容，一个页面只能使用一次，如果是web应用，则包围其主要功能

---

#### span

- 行内元素
- 用于和 div 一样划分区域性质，不过 span 控制的是行内元素区域（不要在span标签内使用块状元素）
- 用于为其中一个范围指定样式

```html
<h1>进行<span style="color:blue">实例</span>测试</h1>
<!-- 实例两个字变成了蓝色 -->
```

---

#### time

- 行内元素
- 用来表示24小时制时间或者公历日期，若表示日期则也可包含时间和时区

```html
<p>The concert starts at <time>20:00</time></p>
<p>This article was created on <time pubdate>2011-01-28</time></p>
<p>The concert took place on <time datetime="2001-05-15 19:00">May 15</time></p>
```

