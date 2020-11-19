## 仿Antd项目

```
http://vikingship.xyz
```

---

### 项目主题色

在根目录新建一个 `styles` 文件夹，用于存放样式文件

选取项目总体颜色：

```scss
$white: #fff !default;
$gray-100: #f8f9fa !default;
$gray-200: #e9ecef !default;
$gray-300: #dee2e6 !default;
$gray-400: #ced4da !default;
$gray-500: #abd5bd !default;
$gray-600: #6c757d !default;
$gray-700: #495057 !default;
$gray-800: #343a40 !default;
$gray-900: #212529 !default;
$balck: #000 !default;

$blue:   #0d6efd !default;
$indigo: #6610f2 !default;
$purple: #6f42c1 !default;
$pink:   #d63384 !default;
$red:    #dc4545 !default;
$orange: #fd7e14 !default;
$yellow: #fadb14 !default;
$green:  #52c41a !default;
$teal:   #20c997 !default;
$cyan:   #17a2b8 !default;

$primary:       $blue !default;
$secondary:     $gray-600 !default;
$success:       $green !default;
$info:          $cyan !default;
$warning:       $yellow !default;
$danger:        $red !default;
$light:         $gray-100 !default;
$dark:          $gray-800 !default;

$font-family-sans-serif: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji" !default; 
$font-family-monospace: SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono" !default;
$font-family-base: $font-family-sans-serif;

// 字体大小
$font-size-base:    1rem !default;
$font-size-lg:      $font-size-base * 1.25 !default;
$font-size-sm:      $font-size-base * .875 !default;

// 字重
$font-weight-lighter:   lighter !default;
$font-weight-light:     300 !default;
$font-weight-normal:    400 !default;
$font-weight-bold:      700 !default;
$font-weight-bolder:    bolder !default;
$font-weight-base:      $font-weight-normal !default;

// 行高
$line-height-base:      1.5 !default;
$line-height-lg:        2 !default;
$line-height-sm:        1.25 !default;

// 标题大小
$h1-font-size:          $font-size-base *2.5 !default;
$h2-font-size:          $font-size-base *2 !default;
$h3-font-size:          $font-size-base *1.75 !default;
$h4-font-size:          $font-size-base *1.5 !default;
$h5-font-size:          $font-size-base *1.25 !default;
$h6-font-size:          $font-size-base !default;

// 链接
$link-color:            $primary !default;
$link-decoration:       none !default;
$link-hover-color:      darken($link-color, 15%) !default;
$link-hover-decoration: underline !default;

// body
$body-bg:               $white !default;
$body-color:            $gray-900 !default;
$body-text-align:       null !default;

// 边框
$border-width:          1px !default;
$border-color:          $gray-300 !default;

$border-radius:         .25rem !default;
$border-radius-lg:      .3rem !default;
$border-radius-sm:      .2rem !default;
```

使用 github 上的 `normalize.css` 来规范样式，它规定了不同浏览器的不同的默认样式属性，让不同的浏览器在渲染网页元素的时候形式更统一

```scss
// /styles/_reboot.scss


html {
  line-height: 1.15; 				// 纠正所有浏览器中的行高
  -webkit-text-size-adjust: 100%;   // 防止在iOS中改变方向后调整字体大小
}


body {
  margin: 0;	// 删除所有浏览器中的页边距
}


main {
  display: block;	// 在IE中一致地呈现“main”元素。
}


/// 在Chrome、Firefox和Safari中，修改“section”和“article”上下文中的“h1”元素的字体大小和页边距
h1 {
  font-size: 2em;
  margin: 0.67em 0;
}


hr {
  box-sizing: content-box;  // 在Firefox中添加正确的框大小。
  height: 0;				// 在Firefox中添加正确的框大小。
  overflow: visible; 		// 在Edge和IE中显示overflow
}


pre {
  font-family: monospace, monospace; 	// 纠正所有浏览器中字体大小的继承和缩放
  font-size: 1em;  						// 纠正所有浏览器中奇怪的“em”字体大小。
}


a {
  background-color: transparent;	// 在ie10中移除活动链接的灰色背景
}


abbr[title] {
  border-bottom: none;  			  // 移除Chrome 57-的底部边框
  text-decoration: underline;   	  // 在Chrome、Edge、IE、Opera和Safari中添加正确的文本装饰
  text-decoration: underline dotted;  // 在Chrome、Edge、IE、Opera和Safari中添加正确的文本装饰
}


/// 在Chrome、Edge和Safari中添加正确的字体粗细
b,
strong {
  font-weight: bolder;
}



code,
kbd,
samp {
  font-family: monospace, monospace; 	// 纠正所有浏览器中字体大小的继承和缩放
  font-size: 1em; 						// 纠正所有浏览器中奇怪的“em”字体大小
}



small {
  font-size: 80%;	// 在所有浏览器中添加正确的字体大小
}



/// 防止“sub”和“sup”元素影响所有浏览器的行高
sub,
sup {
  font-size: 75%;
  line-height: 0;
  position: relative;
  vertical-align: baseline;
}

sub {
  bottom: -0.25em;
}

sup {
  top: -0.5em;
}


img {
  border-style: none;	// 在ie10中删除链接内图片的边框
}



button,
input,
optgroup,
select,
textarea {
  font-family: inherit;		// 改变所有浏览器的字体样式
  font-size: 100%; 			// 改变所有浏览器的字体样式
  line-height: 1.15; 		// 改变所有浏览器的字体样式
  margin: 0; 				// 删除Firefox和Safari中的空白
}

/**
 * Show the overflow in IE.
 * 1. Show the overflow in Edge.
 */

button,
input { /* 1 */
  overflow: visible;
}


/// 在Edge、Firefox和IE中删除text-transform的继承
button,
select { /* 1 */
  text-transform: none;		// 在Firefox中删除text-transform的继承
}


/// 纠正在iOS和Safari中无法设计可点击类型的错误
button,
[type="button"],
[type="reset"],
[type="submit"] {
  -webkit-appearance: button;
}


/// 删除Firefox中的内边框和填充。
button::-moz-focus-inner,
[type="button"]::-moz-focus-inner,
[type="reset"]::-moz-focus-inner,
[type="submit"]::-moz-focus-inner {
  border-style: none;
  padding: 0;
}


/// 恢复前面规则未设置的焦点样式。
button:-moz-focusring,
[type="button"]:-moz-focusring,
[type="reset"]:-moz-focusring,
[type="submit"]:-moz-focusring {
  outline: 1px dotted ButtonText;
}


/// 修正Firefox中的填充
fieldset {
  padding: 0.35em 0.75em 0.625em;
}


legend {
  box-sizing: border-box; 	// 纠正Edge和IE中的text wrapping
  color: inherit; 			// 更正IE中从“fieldset”元素继承的颜色
  display: table; 			// 纠正Edge和IE中的text wrapping
  max-width: 100%; 			// 纠正Edge和IE中的text wrapping
  padding: 0; 				// 删除填充，这样开发者在所有浏览器中零“fieldset”元素时就不会被发现
  white-space: normal; 		// 纠正Edge和IE中的text wrapping
}


progress {
  vertical-align: baseline;		// 在Chrome, Firefox和Opera中添加正确的垂直对齐。
}


textarea {
  overflow: auto;	// 在ie10+删除默认的垂直滚动条
}



[type="checkbox"],
[type="radio"] {
  box-sizing: border-box; 	// 在ie10中添加正确的盒大小
  padding: 0; 				// 删除ie10中的填充
}


// 纠正在chrom中的递增和递减按钮的光标样式
[type="number"]::-webkit-inner-spin-button,
[type="number"]::-webkit-outer-spin-button {
  height: auto;
}


[type="search"] {
  -webkit-appearance: textfield;  // 纠正在Chrome和Safari浏览器中的奇怪外观
  outline-offset: -2px; 		  // 修改Safari中的outline-offset样式
}


// 在macOS系统的Chrome和Safari中删除内部填充
[type="search"]::-webkit-search-decoration {
  -webkit-appearance: none;
}


::-webkit-file-upload-button {
  -webkit-appearance: button; 	// 纠正在iOS和Safari系统中无法设计可点击类型的错误
  font: inherit; 				// 在Safari中更改字体属性为' inherit 
}


/// 在Edge、ie10 +和Firefox中添加正确的显示
details {
  display: block;
}


/// 在所有浏览器中添加正确的显示
summary {
  display: list-item;
}



/// 在IE10+添加正确的显示
template {
  display: none;
}


/// 在IE10+添加正确的显示
[hidden] {
  display: none;
}
```

------

### 组件

#### Button

**需求**

<img src="https://img-blog.csdnimg.cn/20200617103740313.png" style="margin:0;width:700px"/>

----

**初始定义的组件属性**

```tsx
<Button
	size="lg"
    type="primary"
    disabled
    href=""?
    className=""?
    autoFocus=""?
    ...
>
	Viking Button
</Button>
```

---

**定义组件样式**

在 `/styles/_variables.scss` 文件中添加按钮组件的样式

```scss
/// 按钮
// 按钮基本属性
$btn-font-weight:       400;
$btn-padding-y:         .375rem !default;
$btn-padding-x:         .75rem !default;
$btn-font-family:       $font-family-base !default;
$btn-font-size:         $font-size-base !default;
$btn-line-height:       $line-height-base !default;

// 不同大小按钮的 padding 和 font size
$btn-padding-y-sm:      .25rem !default;
$btn-padding-x-sm:      .5rem !default;
$btn-font-size-sm:      $font-size-sm !default;

$btn-padding-y-lg:      .5rem !default;
$btn-padding-x-lg:      1rem !default;
$btn-font-size-lg:      $font-size-lg !default;

// 按钮边框
$btn-border-width:      $border-width !default;

// 按钮其他
$btn-box-shadow:        inset 0 1px 0 rgba($white, .15), 0 1px 1px ;
$btn-disabled-opacity:  .65 !default;

// 链接按钮
$btn-link-color:        $link-color !default;
$btn-link-hover-color:  $link-hover-color !default;
$btn-link-disabled-color: $gray-600 !default;

// 按钮radius
$btn-border-radius:     $border-radius !default;
$btn-border-radius-lg:  $border-radius !default;
$btn-border-radius-sm:  $border-radius !default;

$btn-transition:        color .15s ease-in-out, background-color .15;
```



定义可服用的按钮 mixin

```scss
// /src/styles/_mixin.scss
@mixin button-size($padding-y, $padding-x, $font-size, $border-raduis) {
    padding: $padding-y $padding-x;
    font-size: $font-size;
    border-radius: $border-raduis;
}

@mixin button-style(
    $background,
    $border,
    $color, 
    $hover-background:lighten($background, 7.5%),
    $hover-border: lighten($border, 10%),
    $hover-color: $color
){
    color: $color;
    background: $background;
    border-color: $border;

    &:hover {
        color: $hover-color;
        background: $hover-background;
        border-color: $hover-border;
    }

    &:focus,
    &.focus
    {
        color: $hover-color;
        background: $hover-background;
        border-color: $hover-border;
    }

    &:disabled,
    &.disabled {
        color: $color;
        background: $background;
        border-color: $border;
    }
}
```

定义按钮样式

```scss
// /src/components/Button/_style.scss
.btn {
    position: relative;
    display: inline-block;
    font-weight: $btn-font-weight;
    line-height: $btn-line-height;
    color: $body-color;
    white-space: nowrap;
    text-align: center;
    vertical-align: middle;
    background-image: none;
    @include button-size($btn-padding-y, $btn-padding-x, $btn-font-size, $border-radius);
     /* 
        相当于省写了：
        padding: $btn-padding-y $btn-padding-x;
        font-size: $btn-font-size;
        border-radius: $border-radius; 
    */
    border: $btn-border-width solid transparent;
   
    box-shadow: $btn-box-shadow;
    cursor: pointer;
    transition: $btn-transition;

    &.disabled,&[disabled] {
        cursor: not-allowed;
        opacity: $btn-disabled-opacity;
        box-shadow: none;
        
        > * {
            pointer-events:none;
        }
    }

}

.btn-lg {
    @include button-size($btn-padding-y-lg, $btn-padding-x-lg, $btn-font-size-lg, $border-radius-lg);
}

.btn-sm {
    @include button-size($btn-padding-y-sm, $btn-padding-x-sm, $btn-font-size-sm, $border-radius-sm);
}

.btn-primary {
    @include button-style($primary, $primary, $white)
}

.btn-danger {
    @include button-style($danger, $danger, $white)
}

.btn-default {
    @include button-style($white, $gray-400, $body-color, $white, $primary)
}

.btn-link {
    font-weight: $font-weight-normal;
    color: $btn-link-color;
    text-decoration: $link-decoration;
    box-shadow: none;
    &:hover {
        color: $btn-link-hover-color;
        text-decoration: $link-hover-decoration;
    }
    &:focus,
    &.focus {
      text-decoration: $link-hover-decoration;
      box-shadow: none;  
    }
    &:disabled,
    &.disabled {
        color: $btn-link-disabled-color;
        pointer-events: none;
    }
}
```

综合引入并导出该文件给 App 组件

```scss
// src/styles/index.scss

@import 'variables';

@import 'reboot';

@import 'mixin';

@import '../components/Button/style'
```

---

**按钮组件**

```tsx
import React from 'react'
import classNames from 'classnames'


// 使用枚举是为了在外部使用的时候会有属性提示
export enum ButtonSize {
    Large = 'lg',
    Small = 'sm'
}

export enum ButtonType {
    Primary = 'primary',
    Default = 'default',
    Danger = 'danger',
    Link = 'link'
}

// 使用接口规范对象类型
interface BaseButtonProps {
    className?: string;
    disabled?: boolean;
    size?: ButtonSize;
    btnType?: ButtonType;
    children: React.ReactNode;
    href?: string
}

// 使用react内置提供的按钮属性接口拿到按钮的全部属性
type NativeButtonProps = BaseButtonProps & React.ButtonHTMLAttributes<HTMLElement>
type AnchorButtonProps = BaseButtonProps & React.AnchorHTMLAttributes<HTMLElement>
export type ButtonProps = Partial<NativeButtonProps & AnchorButtonProps> 



const Button: React.FC<ButtonProps > = (props) => {
    const {
        btnType,
        className,  // 为了将用户自定义的class也加上
        disabled,
        size,
        children,
        href,
        ...restProps    // restProps 接收剩余的属性
    } = props
    
    // 根据传入的props定义样式类型
    const classes = classNames('btn', className, {
        [`btn-${btnType}`]: btnType,
        [`btn-${size}`]: size,
        'disabled': (btnType === ButtonType.Link) && disabled 
    })
    console.log(restProps)
    if(btnType === ButtonType.Link && href) {
        return (
            <a 
            className={classes} 
            href={href}
            {...restProps}
            >
                {children}
            </a>
        )
    }
    else {
        return (
            <button
                className={classes}
                disabled={disabled}
                {...restProps}
            >
                {children}
            </button>
        )
    }

}

Button.defaultProps = {
    disabled: false,
    btnType: ButtonType.Default
}

export default Button
```



----

#### Menu

<img src="https://img-blog.csdnimg.cn/20200619165140446.png" style="margin:0;width:700px"/>









