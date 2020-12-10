## Sass

```
SCSS是 Sass3引入新的语法，其语法完全兼容 CSS3，并且继承了Sass的强大功能
Sass和SCSS 其实是同一种东西，平时都称之为 Sass
```

### 安装

```
npm install node-sass -save
yarn add node-sass
或者指定版本下载：npm install node-sass@4.14.0 -s（4.14.0版本可以直接下载成功）
```

有时候不能正常下载时，使用cnpm试试

注意：使用 `sass` 编写样式文件时，文件的后缀名为 `scss`



---

### 变量

使用 `$` 去声明一个变量

```scss
$primary-coloe: #ddd;

div {
    background-color: $primary-color;
}
```

可以定义多个值

```scss
$primary-coloe: #ddd;
$primary-border: 1px solid $primary-coloe;

div {
    border: $primary-border;
}
```

当用变量表示一个类型或属性名时，需要使用 `#{$xxx}` 去引用

```scss
$name: 'info';
$attr: 'border';

.alert-#{$name} {
    #{$attr}-color: #ccc;
}
```

在字符串中使用时也需要上面那种格式

```scss
$name: 'info';
$path: 'xx/x'
.alert-#{$name} {
    background-image: url(../images/#{$path}.png)
}
```

!default

在变量后声明 `!default` 则在全局中使用时，在其他地方对该变量赋其他值时，编译器不会再将赋 `!default` 表示的值

```scss
$white: #fff !default;
```



----

### 嵌套

#### 调用父类

使用 `&` 去调用父类

```scss
// 相当于 div:hover
div {
    &:hover {
        ...
    }
}
```

---

#### 属性嵌套

使用 `:` 加上括号表示属性嵌套

```scss
body {
    font: {
        family: Arial;
        size: 15px;
        weight: noraml;
    }
}
```

相当于

```scss
body {
    font-family: Arial;
	font-size: 15px;
	font-weight: noraml;
}
```

----

### 混合

一个定义好的样式，可以在任何地方去使用这个样式

``` scss
@mixin 名字 (参数1, 参数2...) {
    ...
}
```

使用 `@include` 引入

```scss
@mixin alert {
    color: #8a6d3b;
    background-color: #fcf8e3;
    
    a {
        color: #664c2b;
    }
}

div {
    @include alert;
}

/*	
	最终效果：
    div {
        color: #8a6d3b;
        background-color: #fcf8e3;
    }
	
	div a {
		color: #664c2b;
	}
*/
```

可使用参数，在调用的时候再确定

```scss
// scss 有很多内置方法，如 `darken($color,$amount)`：通过改变颜色的亮度值，让颜色变暗，创建一个新的颜色
@mixin alert($text-color, $background) {
    color: $text-color;
    background-color: $background;

    a {
        color: darken($text-color, 10%);
    }
}

div {
    @include alert(#8a6d3b, #fcf8e3);
}

/*	
	最终效果：
    div {
        color: #8a6d3b;
        background-color: #fcf8e3;
    }
	
	div a {
		color: darken(#8a6d3b, 10%);
	}
*/
```

调用的时候，可以直接声明变量传入的值，这样就可以不用按顺序传递参数了

```scss
div {
    @include alert($background:#d9edf7 , $text-color:#31708f);
}
```

-----

### 继承

使用 `@extend`

```scss
.alert {
    padding: 15px;
}

.alert-info {
    @extend .alert;
    background-color: #d9edf7;
}
```

相当于

```scss
.alert, .alert-info {
    padding: 15px;
}

.alert-info {
    background-color: #d9edf7;
}
```

---

连子类也会被继承

```scss
.alert {
    padding: 15px;
}

.alert a {
    font-weight: bold;
}

.alert-info {
    @extend .alert;
    background-color: #d9edf7;
}
```

相当于

```scss
.alert, .alert-info {
    padding: 15px;
}

.alert a , .alert-info a {
    font-weight: bold;
}

.alert-info {
    background-color: #d9edf7;
}
```

---

### 导入

使用 `@import` 导入 scss 文件，仅用于被导入使用的 scss 被称为 `Partials`，表示不能单独使用

当使用下划线开头命名 scss 文件时，编译器会将其认作为 `Partials`，即认定这些文件是用于被导入使用的，不会单独将他们编译为 css，提高性能

```scss
// _base.scss
body {
    margin: 0;
    padding: 0;
}
```

```scss
// other.scss
@import 'base'	// 不需要标注下划线和后缀名
// 推荐放在同一个文件夹下直接这样导入，如果是添加了路径名 '../base'则可能不会有智能提示
```

这里会直接将` _base.scss` 内的所有样式直接导入进来并直接生效

导入的顺序是有要求的，如果 A 要依赖 B 内声明的内容，则在主文件导入时声明导入 B 需要在 A 前，否则 A 拿不到 B 的数据

```scss
@import 'B';
@import 'A';
```



----

### 运算

一个坑：带单位运算时可能会导致错误

```scss
10px / 2px = 5	
10px / 2 = 5px
```

---

#### 内置函数

scss 提供了各式的函数，如数学公式，处理字符串等，用法和 JS 接近，具体可以去查

#### 颜色函数

- RGBA（红、绿、蓝、透明度）

```scss
background-color: rgba(255, 100, 30, 0.7)
```

- HSLA（色相、饱和度、明度、透明度）

```scss
background-color: hsla(0, 100%, 30%, 0.7)
```

- adjust-hue

  调整指定颜色色相

```scss
background-color: adjust-hue(#ddd, 120deg)
```

- lighten、darken

  在原来基础上增加、降低明度

```scss
background-color:darken(#ddd, 10%);
```

- saturate、desaturate

  在原来基础上增加、降低饱和度

```scss
background-color:saturate(#ddd, 10%);
```

- transparentize、opacity

  在原来基础上增加、降低透明度

```scss
background-color:transparentize(#ddd, 10%);
```

---

#### @if

```scss
$theme: 'dark';
body {
    @if $theme === dark {
        background-color: black;
    } @else if $theme === light {
        background-color: white;
    } @else {
        background-color: grey;
    }
}
```

---

#### @for

```scss
// 自动每一次循环后变量都会自增
// 使用through时，包括结束值，即 <=
@for $var from <开始值> through <结束值> {
    
}
// 使用to时，不包括结束值，即 <
@for $var from <开始值> to <结束值> {
    
}
```

```scss
$columns:4;
@for $i from 1 through $columns {
    .col-#{$i} {
        width: 100% / $columns * $i;
    }
}

/*
    .col-1 {
		width: 25%;
    }
    .col-2 {
        width: 50%;
    }
    .col-3 {
        width: 75%;
    }
    .col-4 {
        width: 100%;
    }
*/
```

---

#### @each

```scss
@each $var in $list {
    
}
```

列表是指多个属性值用空格隔开

```scss
$icons: success error warning;
@each $icon in $icons {
    .icon-#{$icon} {
        background-image: url(../icons/#{$icon}.png);
    }
}
/*
    .icon-success {
        background-image: url(../icons/success.png);
    }
    .icon-error {
        background-image: url(../icons/error.png);
    }
    .icon-warning {
        background-image: url(../icons/warning.png);
    }
*/
```

----

#### @while

```scss
$i: 6;

@while $i > 0 {
    .item-#{$i} {
        width: 5px * $i;
    }
    $i: $i - 2;
}

/*
    .item-6 {
        width: 30px;
    }
    .item-4 {
        width: 20px;
    }
    .item-2 {
        width: 10px;
    }
*/
```

----

#### 自定义函数

```scss
// map-get() 拿到一个key获取到一个列表的值
$colors: (light: #fffff, dark: #000000);
@function color($key) {
    @return map-get($colors, $key);
}
body {
    background-color: color(light);
}

/*
    body {
        background-color: #fffff;
    }
*/
```

警告与错误

```scss
$colors: (light: #fffff, dark: #000000);
@function color($key) {
    
    @if not map-has-key($colors, $key) {
        @warn '在color里没有找到#{$key}'
        @error 'xxxx'
    }
    
    
    @return map-get($colors, $key);
}
body {
    background-color: color(light);
}
```

