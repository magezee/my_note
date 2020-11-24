## Less

### 变量

**声明：**`@parmsName`

```less
@bgColor: aqua;

div {
    background-color: @bgColor;
}
```

变量是延迟加载的，在使用前不一定要预先声明

```less
.box{
    background:@green;
    width:500px;
    height:500px;
}
@green:#801f77;
@baise:white;
```

**使用：**

- **作为选择器和属性名**

  变量以`@{变量名}`的方式使用

```less
@mySelector: width;

.@{mySelector} {
    @{mySelector}: 960px;
    height:500px;
}
```

- **作为 url**

  用""将变量的值括起来，同样将变量以@{变量名}的方式使用

```less
@myUrl:"http://class.imooc.com/static/module/index/img";

body{
    background:#ccc url("@{myUrl}/nav.png") no-repeat;
}
```

----

### 函数

**声明：**`.FunctionName`

```less
.setColor(@color) {
    background-color: @color;
}

div {
    setColor(red)
}
```

----

### 循环

使用函数递归来实现循环

```less
.loop(@index) when (@index > 0) {
    .loop(@index - 1);	// 注意，这里有一个天坑，这里的空格必须有规范，不能写成.loop(@index-1)或.loop(@index -1) 或 .loop(@index- 1)
    .d@{index} {
        width: @index * 1px;
    }
}

.loop(10);

// 最终生成： d1 {width:1px;} d2{width:2px} ...

```

