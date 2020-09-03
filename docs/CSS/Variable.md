?> 本文介绍`sass`、`less`、`css`中常见的变量声明和函数使用

### 变量申明

sass 声明变量用的是`$`符号
~~~ sass
    html{
        $font-size: 12px; // 定义变量
        font-size: $line-height; // 使用变量 
    }
~~~

css 变量声明用的是`--`， 使用时需要用`val（变量名）`
~~~ css
     html{
        --font-size: 12px; // 定义变量
        font-size: val(--line-height); // 使用变量 
    }
~~~





### 控制指令 (Control Directives) if

> if

先介绍sass的if使用方法
` @if`
当 @if 的表达式返回值不是` false `或者 `null` 时，条件成立，输出` {} `内的代码：

    p {
        @if 1 + 1 == 2 { border: 1px solid; }
        @if 5 < 3 { border: 2px dotted; }
        @if null  { border: 3px double; }
    }

编译为

    p {
        border: 1px solid; 
    }

@if 声明后面可以跟多个` @else if `声明，或者一个 `@else` 声明。如果 @if 声明失败，Sass 将逐条执行 @else if 声明，如果全部失败，最后执行 @else 声明，例如：

~~~ sass
    $type: monster;
    p {
        @if $type == ocean {
            color: blue;
        } @else if $type == matador {
            color: red;
        } @else if $type == monster {
            color: green;
        } @else {
            color: black;
        }
    }
~~~ 

编译成：

    p { color: green; }


> for

介绍sass的for使用方法

`@for` 指令可以在限制的范围内重复输出格式，每次按要求（变量的值）对输出结果做出变动。这个指令包含两种格式：`@for $var from <start> through <end>`，或者 `@for $var from <start> to <end>`，区别在于 through 与 to 的含义：当使用 through 时，条件范围`包含 <start>`  `与<end>` 的值，而使用 to 时条件范围只`包含 <start> `的值不`包含 <end> `的值。另外，$var 可以是任何变量，比如` $i；<start> `和 `<end>` 必须是整数值。

    @for $i from 1 through 3 {
    .item-#{$i} { width: 2em * $i; }
    }
    编译结果
    .item-1 { width: 2em; }
    .item-2 { width: 4em; }
    .item-3 { width: 6em; }

> each

`@each `指令的格式是 `$var in <list>`, $var 可以是任何变量名，比如 $length 或者 $name，而 `<list>` 是一连串的值，也就是值列表。

`@each` 将变量` $var` 作用于值列表中的每一个项目，然后输出结果，例如：
~~~ sass
    @each $animal in puma, sea-slug, egret, salamander {
        .#{$animal}-icon {
            background-image: url('/images/#{$animal}.png');
        }
    }
    编译为

    .puma-icon { background-image: url('/images/puma.png'); }
    .sea-slug-icon { background-image: url('/images/sea-slug.png'); }
    .egret-icon { background-image: url('/images/egret.png'); }
    .salamander-icon { background-image: url('/images/salamander.png'); }
~~~

Multiple Assignment 多个赋值

~~~ sass
@each $animal, $color, $cursor in (puma, black, default), (sea-slug, blue, pointer),  (egret, white, move) {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
    border: 2px solid $color;
    cursor: $cursor;
  }
}
另一种：
@each $header, $size in (h1: 2em, h2: 1.5em, h3: 1.2em) {
  #{$header} {
    font-size: $size;
  }
}
~~~

> while

`@while `指令重复输出格式直到表达式返回结果为 `false`。这样可以实现比` @for` 更复杂的循环，只是很少会用到。例如：
~~~ 
    $i: 6;
    @while $i > 0 {
        .item-#{$i} { width: 2em * $i; }
        $i: $i - 2;
    }
~~~


### 混合指令 (Mixin Directives)

`混合指令（Mixin）`用于定义可重复使用的样式，避免了使用无语意的` class`，比如 .float-left。混合指令可以包含所有的 CSS 规则，绝大部分 Sass 规则，甚至通过参数功能引入变量，输出多样化的样式。

> 定义混合指令 @mixin (Defining a Mixin: @mixin)

混合指令的用法是在` @mixin 后`添加`名称`与`样式`，比如名为 large-text 的混合通过下面的代码定义：
~~~
@mixin large-text {
  font: {
    family: Arial;
    size: 20px;
    weight: bold;
  }
  color: #ff0000;
}
~~~

> 引用混合样式 `@include` (Including a Mixin: @include)

    .page-title {
        @include large-text;
        padding: 4px;
        margin-top: 10px;
    }


> 参数 (Arguments)

`参数`用于给混合指令中的样式`设定变量`，并且赋值使用。在定义混合指令的时候，按照变量的格式，通过逗号分隔，将参数写进圆括号里。引用指令时，按照参数的顺序，再将所赋的值对应写进括号：

~~~ sass
@mixin sexy-border($color, $width) {
  border: {
    color: $color;
    width: $width;
    style: dashed;
  }
}
p { @include sexy-border(blue, 1in); }
~~~

>  向混合样式中导入内容 (Passing Content Blocks to a Mixin)

在`引用混合样式`的时候，可以先将一段代码导入到混合指令中，然后再输出混合样式，额外导入的部分将出现在` @content `标志的地方：

~~~ sass
@mixin apply-to-ie6-only {
  * html {
    @content;
  }
}
@include apply-to-ie6-only {
  #logo {
    background-image: url(/logo.gif);
  }
}

编译后:
* html #logo {
  background-image: url(/logo.gif);
}
~~~
为便于书写，`@mixin `可以用 `= `表示，而 `@include `可以用` + `表示，所以上面的例子可以写成：

~~~
=apply-to-ie6-only
  * html
    @content

+apply-to-ie6-only
  #logo
    background-image: url(/logo.gif)
~~~

###  函数指令 (Function Directives)

Sass 支持自定义函数，并能在任何属性值或 Sass script 中使用：

~~~
$grid-width: 40px;
$gutter-width: 10px;

@function grid-width($n) {
  @return $n * $grid-width + ($n - 1) * $gutter-width;
}

#sidebar { width: grid-width(5); }

编译结果： #sidebar {  width: 240px; }
~~~

数组本身没有太多功能，但 `Sass list function`s 赋予了数组更多新功能：`nth 函数`可以直接访问数组中的某一项；`join 函数`可以将多个数组连接在一起；`append 函数`可以在数组中添加新值；而 `@each 指令`能够遍历数组中的每一项。






