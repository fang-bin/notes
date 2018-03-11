此页主要是 **let 和 const** 、**变量的解构赋值**

# let 和 const

考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。

const 声明的变量并不是值不可改动，而是这个值指向内存的指针不可改动，因此声明一个对象，可以改动内存上的这个对象，却不可以替换，不可变动的只是这个地址，对象本身是可以改变的。

如果真的想要将这个对象冻结，应该使用 Object.freeze 方法。例：const abc = Object.freeze({});

除了将对象本身冻结，对象的属性也应该冻结，例如：

    var constantize = (obj) => {
        Object.freeze(obj);
        Object.keys(obj).forEach( (key, i) => {
            if ( typeof obj[key] === 'object' ) {
                constantize( obj[key] );
            }
        });
    };

声明变量的六种方法： **var** 、 **function** 、 **let** 、 **const** 、 **import** 、 **class**

顶层对象，在浏览器中指 window 对象，在 Node 中指 global 对象。ES5 中，顶层对象的属性与全局变量是等价的。ES6 改变了这一点，一方面使用 var 命令和 function 命令声明的全局变量，依旧是顶层对象的属性，另一方面规定 let 命令、const 命令、 class 命令声明的全局变量，不属于顶层对象的属性。

-------------------------------------------------

# 变量的结构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构。

## 数组的解构赋值

1. 基本方法

        let a = 1;
        let b = 2;
        let c = 3;

    ↓↓↓

        let [a, b, c] = [1, 2, 3];

    本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。例：

        let [foo, [[bar], baz]] = [1, [[2], 3]];
        foo // 1
        bar // 2
        baz // 3

        let [ , , third] = ["foo", "bar", "baz"];
        third // "baz"

        let [x, , y] = [1, 2, 3];
        x // 1
        y // 3

        let [head, ...tail] = [1, 2, 3, 4];
        head // 1
        tail // [2, 3, 4]

        let [x, y, ...z] = ['a'];
        x // "a"
        y // undefined
        z // []

2. 如果解构不成功，变量的值就等于undefined。(左边的多于右边的，少值)

    `let [bar, foo] = [1];let [foo] = [];`

3. 另一种情况是不完全解构，即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。（右边的少于左边的，多值）

        let [x, y] = [1, 2, 3];
        x // 1
        y // 2

        let [a, [b], d] = [1, [2, 3], 4];
        a // 1
        b // 2
        d // 4

4. 对于 Set 结构，也可以使用数组的结构赋值。

    `let [x, y, z] = new Set(['a', 'b', 'c']);`

5. 结构赋值允许指定默认值。

        let [foo = true] = [];
        foo // true

        let [x, y = 'b'] = ['a']; // x='a', y='b'
        let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'

    注意，ES6 内部使用严格相等运算符（===），判断一个位置是否有值。所以，只有当一个数组成员严格等于undefined，默认值才会生效。

6. 如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。

        function f() {
            console.log('aaa');
        }
        let [x = f()] = [1];  //因为x能取到值，所以函数f根本不会执行

7. 默认值可以引用解构赋值的其他变量，但该变量必须已经声明。

        let [x = 1, y = x] = [];     // x=1; y=1
        let [x = 1, y = x] = [2];    // x=2; y=2
        let [x = 1, y = x] = [1, 2]; // x=1; y=2
        let [x = y, y = 1] = [];     // ReferenceError: y is not defined

    注意： 相同作用于下，不能重复使用 let 、const 声明变量。


## 对象的结构赋值

    let { foo, bar } = { foo: "aaa", bar: "bbb" };
    foo // "aaa"
    bar // "bbb"

对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。

1. 如果变量并未在右边找到对应的同名属性，则会取不到值，最后等于 undefined。`let { baz } = { foo: "aaa", bar: "bbb" };`

2. 如果变量名与属性名不一致，必须写成下面这样:

        let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
        baz // "aaa"

        let obj = { first: 'hello', last: 'world' };
        let { first: f, last: l } = obj;
        f // 'hello'
        l // 'world'

    这实际上说明，对象的解构赋值是 `let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };` 这种形式的简写。

    与数组一样，解构也可以用于嵌套结构的对象。

3. 对象的解构也可以指定默认值。

        var {x = 3} = {};
        x // 3

        var {x, y = 5} = {x: 1};
        x // 1
        y // 5

        var {x: y = 3} = {};
        y // 3

        var {x: y = 3} = {x: 5};
        y // 5

        var { message: msg = 'Something went wrong' } = {};
        msg // "Something went wrong"

    默认值生效的条件是，对象的属性值严格等于undefined。如果解构失败，变量的值等于undefined。

4. 如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错。`let {foo: {bar}} = {baz: 'baz'};`

5. 如果要将一个已经声明的变量用于解构赋值，必须非常小心。

        // 错误的写法
        let x;
        {x} = {x: 1};
        // SyntaxError: syntax error

    上面代码的写法会报错，因为 JavaScript 引擎会将{x}理解成一个代码块，从而发生语法错误。只有不将大括号写在行首，避免 JavaScript 将其解释为代码块，才能解决这个问题。

        // 正确的写法
        let x;
        ({x} = {x: 1});

6. 解构赋值允许等号左边的模式之中，不放置任何变量名。虽然这些赋值表达式毫无意义，但语法是合法的，可以执行。

7. 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。`let {0 : first, 3 : last} = [1, 2, 3];`


## 字符串的解构赋值

字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。

    const [a, b, c, d, e] = 'hello';
    a // "h"
    b // "e"
    c // "l"
    d // "l"
    e // "o"

    let {length : len} = 'hello';
    len // 5


## 数值和布尔值的解构赋值

解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。

    let {toString: s} = 123;
    s === Number.prototype.toString // true

    let {toString: s} = true;
    s === Boolean.prototype.toString // true

上面代码中，数值和布尔值的包装对象都有toString属性，因此变量s都能取到值。

解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。

    let { prop: x } = undefined; // TypeError
    let { prop: y } = null; // TypeError


## 函数参数的结构赋值

    function add([x, y]){
        return x + y;
    }

    add([1, 2]); // 3

    [[1, 2], [3, 4]].map(([a, b]) => a + b);   //[3,7]

1. 函数参数的解构也可以使用默认值。

        function move({x = 0, y = 0} = {}) {
            return [x, y];
        }

        move({x: 3, y: 8}); // [3, 8]
        move({x: 3}); // [3, 0]
        move({}); // [0, 0]
        move(); // [0, 0]


        function move({x, y} = { x: 0, y: 0 }) {
            return [x, y];
        }

        move({x: 3, y: 8}); // [3, 8]
        move({x: 3}); // [3, undefined]
        move({}); // [undefined, undefined]
        move(); // [0, 0]

    上面代码是为函数move的参数指定默认值，而不是为变量x和y指定默认值，所以会得到与前一种写法不同的结果。

    undefined就会触发函数参数的默认值。`[1, undefined, 3].map((x = 'yes') => x);   // [ 1, 'yes', 3 ]`

### 圆括号的问题，其中不能使用圆括号的情况。

1. 变量声明语句

        // 全部报错
        let [(a)] = [1];

        let {x: (c)} = {};
        let ({x: c}) = {};
        let {(x: c)} = {};
        let {(x): c} = {};

        let { o: ({ p: p }) } = { o: { p: 2 } };

2. 函数参数

        // 报错
        function f([(z)]) { return z; }
        // 报错
        function f([z,(x)]) { return x; }

    函数参数也属于变量声明。

3. 赋值语句的模式

        // 全部报错
        ({ p: a }) = { p: 42 };
        ([a]) = [5];

## 变量的结构赋值用处

1. 交换变量的值

        let x = 1;
        let y = 2;
        [x, y] = [y, x];

2. 从函数返回多个值

        // 返回一个数组
        function example() {
        return [1, 2, 3];
        }
        let [a, b, c] = example();

        // 返回一个对象
        function example() {
        return {
            foo: 1,
            bar: 2
        };
        }
        let { foo, bar } = example();

3. 函数参数的定义

        // 参数是一组有次序的值
        function f([x, y, z]) { ... }
        f([1, 2, 3]);

        // 参数是一组无次序的值
        function f({x, y, z}) { ... }
        f({z: 3, y: 2, x: 1});

    结构函数可以方便的将一组参数与变量名对应起来。

4. 提取 JSON 数据

        let jsonData = {
            id: 42,
            status: "OK",
            data: [867, 5309]
        };
        let { id, status, data: number } = jsonData;
        console.log(id, status, number);
        // 42, "OK", [867, 5309]

5. 函数参数的默认值

        jQuery.ajax = function (url, {
            async = true,
            beforeSend = function () {},
            cache = true,
            complete = function () {},
            crossDomain = false,
            global = true,
            // ... more config
        }) {
            // ... do stuff
        };

6. 遍历 Map 结构

        const map = new Map();
        map.set('first', 'hello');
        map.set('second', 'world');

        for (let [key, value] of map) {
            console.log(key + " is " + value);
        }
        // first is hello
        // second is world

        // 获取键名
        for (let [key] of map) {
            // ...
        }

        // 获取键值
        for (let [,value] of map) {
            // ...
        }

7. 输入模块的指定方法。

    其中，我们常用的从一些vue的UI库中引用组件的方法就是这种，例：

    `import { Swiper, SwiperItem } from 'vux'`   其中 import 是 es6 中六种声明方法的一种

    `const { SourceMapConsumer, SourceNode } = require("source-map");`

