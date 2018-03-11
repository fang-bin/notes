# scss

1. scss不仅可以嵌套选择器，同时也可以嵌套属性。例：

        nav {
            border: 1px solid #ccc {
                left: 0px;
                right: 0px;
            }
        }

2. 默认变量值。  `$fancybox-width: 400px!default;`    **!default**

    假如你写了一个可被他人通过@import导入的sass库文件，你可能希望导入者可以定制修改sass库文件中的某些值。使用sass的!default标签可以实现这个目的。

3. 局部文件可以在命名时，使用下划线开头作为文件名，而在引用时，可以不写文件的全名，即省略文件名开头的下划线，这样就只把这个文件用作导入。

4. 嵌套导入。

    sass 允许 @import 命令写在 css 规则内，这种导入方式下，生成对应的 css 文件时，局部文件会被直接插入到 css 规则内导入它的地方。被导入的局部文件中定义的所有变量和混合器，也会在这个规则范围内生效。

5.  当你在 sass 中使用 @import 导入一个原生的 css 文件时, sass 会认为你想用 css 原生 @import ,但是这样会造成浏览器解析 css 时的额外下载。由于 sass 完全兼容 css，所以可以把原始的 css 文件改名为 .scss 后缀。



### **混合器**   **@mixin**   **@include**

混合器使用@mixin标识符定义。这个标识符给一大段样式赋予一个名字，这样你就可以轻易地通过引用这个名字重用这段样式。

    @mixin rounded-corners {
        -moz-border-radius: 5px;
        -webkit-border-radius: 5px;
        border-radius: 5px;
    }

然后就可以在你的样式表中通过@include来使用这个混合器，放在你希望的任何地方。@include调用会把混合器中的所有样式提取出来放在@include被调用的地方。

    notice {
        background-color: green;
        border: 2px solid #00aa00;
        @include rounded-corners;
    }


1. 如何使用混合器

    大量的重用可能会导致生成的样式表过大，导致加载缓慢，应当避免滥用。能想出一个合适的混合器姓名说明混合器中的属性放在一起有一定的意义，可以构造成优良的混合器。如若不然，构造成一个混合器可能并不合适。

2. 混合器中的 css 规则

        @mixin no-bullets {
            list-style: none;
            li {
                list-style-image: none;
                list-style-type: none;
                margin-left: 0px;
            }
        }

    当一个包含 css 规则的混合器通过 @include 包含在一个父规则中时，在混合器中的规则最终会生成父规则中的嵌套规则。

        ul.plain {
            color: #444;
            @include no-bullets;
        }

    混合器中的规则甚至可以使用 sass 的父选择器标识符&，使用起来跟不用混合器时一样。

3. 给混合器传参

    混合器并不一定总得生成相同的样式。可以通过在 @include 混合器时给混合器传参，来定制混合器生成的精确样式。当 @include 混合器时，参数其实就是可以赋值给 css 属性值的变量。如果你写过JavaScript，这种方式跟 JavaScript 的 function 很像：

        @mixin link-colors($normal, $hover, $visited) {
            color: $normal;
            &:hover { color: $hover; }
            &:visited { color: $visited; }
        }
    
    当混合器被 @include 时，你可以把它当作一个 css 函数来传参。

        a {
            @include link-colors(blue, red, green);
        }
        //Sass最终生成的是：
        a { color: blue; }
        a:hover { color: red; }
        a:visited { color: green; }

    当你 @include 混合器时，有时候可能会很难区分每个参数是什么意思，参数之间是一个什么样的顺序。为了解决这个问题，sass 允许通过语法`$name: value`的形式指定每个参数的值。这种形式的传参，参数顺序就不必再在乎了，只需要保证没有漏掉参数即可：

        a {
            @include link-colors(
                $normal: blue,
                $visited: green,
                $hover: red
            );
        }

4. 混合器参数的默认参数值

    为了在 @include 混合器时不必传入所有的参数，我们可以给参数指定一个默认值。参数默认值使用`$name: default-value`的声明形式，默认值可以是任何有效的 css 属性值，甚至是其他参数的引用，如下代码：

        @mixin link-colors(
            $normal,
            $hover: $normal,
            $visited: $normal
        )
        {
        color: $normal;
        &:hover { color: $hover; }
        &:visited { color: $visited; }
        }
    
    如果像下边这样调用：`@include link-colors(red)` $hover 和 $visited 也会被自动赋值为 red。

### 使用选择器继承来精简 CSS   **@extend**

选择器继承是说一个选择器可以继承为另一个选择器定义的所有样式。这个通过@extend语法实现，如下代码:

    //通过选择器继承继承样式
    .error {
        border: 1px solid red;
        background-color: #fdd;
    }
    .seriousError {
        @extend .error;
        border-width: 3px;
    }

在上边的代码中，.seriousError将会继承样式表中任何位置处为.error定义的所有样式。以class="seriousError" 修饰的html元素最终的展示效果就好像是class="seriousError error"。

.seriousError不仅会继承.error自身的所有样式，任何跟.error有关的组合选择器样式也会被.seriousError以组合选择器的形式继承，如下代码:

    //.seriousError从.error继承样式
    .error a{  //应用到.seriousError a
        color: red;
        font-weight: 100;
    }
    h1.error { //应用到hl.seriousError
        font-size: 1.2rem;
    }



### 函数指令

Sass 支持自定义函数，并能在任何属性值或 Sass script 中使用。

    $grid-width: 40px;
    $gutter-width: 10px;
    @function grid-width($n) {
        @return $n * $grid-width + ($n - 1) * $gutter-width;
    }
    #sidebar { width: grid-width(5); }