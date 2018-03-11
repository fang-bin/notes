# 函数的扩展

### 1. 函数参数的默认值

    function log(x, y = 'World') {
        console.log(x, y);
    }

    log('Hello') // Hello World
    log('Hello', 'China') // Hello China
    log('Hello', '') // Hello

参数变量是默认声明的，所以不能在函数内部使用let或const再次声明。

### 2. 与解构复制默认值结合使用

    function foo({x, y = 5}) {
        console.log(x, y);
    }

    foo({}) // undefined 5
    foo({x: 1}) // 1 5
    foo({x: 1, y: 2}) // 1 2
    foo() // TypeError: Cannot read property 'x' of undefined

上面代码只使用了对象的解构赋值默认值，没有使用函数参数的默认值。只有当函数foo的参数是一个对象时，变量x和y才会通过解构赋值生成。如果函数foo调用时没提供参数，变量x和y就不会生成，从而报错。通过提供函数参数的默认值，就可以避免这种情况。

    function foo({x, y = 5} = {}) {
        console.log(x, y);
    }

    foo() // undefined 5

#### 3. 参数默认值的位置

通常，定义了默认值的参数，应该是函数的尾参数。

参数如果传入undefined，将触发该参数等于默认值，null则没有这个效果。（不能通过）

#### 4. 函数的 length 属性

指定了默认值以后，函数的length属性，将返回没有指定默认值的参数个数。也就是说，指定了默认值后，length属性将失真。

    (function (a) {}).length // 1
    (function (a = 5) {}).length // 0
    (function (a, b, c = 5) {}).length // 2

length属性的含义是，该函数预期传入的参数个数。某个参数指定默认值以后，预期传入的参数个数就不包括这个参数了。

如果设置了默认值的参数不是尾参数，那么length属性也不再计入后面的参数了。

    (function (a = 0, b, c) {}).length // 0
    (function (a, b = 1, c) {}).length // 1

#### 5. 作用域

一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域（context）。等到初始化结束，这个作用域就会消失。

    var x = 1;
    function f(x, y = x) {
        console.log(y);
    }
    f(2) // 2
--

    let x = 1;
    function f(y = x) {
        let x = 2;
        console.log(y);
    }
    f() // 1

如果参数的默认值是一个函数： 

    let foo = 'outer';
    function bar(func = () => foo) {
        let foo = 'inner';
        console.log(func());
    }
    bar(); // outer

上面代码中，函数bar的参数func的默认值是一个匿名函数，返回值为变量foo。函数参数形成的单独作用域里面，并没有定义变量foo，所以foo指向外层的全局变量foo，因此输出outer。如下，则错误

    function bar(func = () => foo) {
        let foo = 'inner';
        console.log(func());
    }

    bar() // ReferenceError: foo is not defined

上面代码中，匿名函数里面的foo指向函数外层，但是函数外层并没有声明变量foo，所以就报错了。

### 6. 作用域的应用

利用参数默认值，可以指定某一个参数不得省略，如果省略就抛出一个错误。

    function throwIfMissing() {
        throw new Error('Missing parameter');
    }
    function foo(mustBeProvided = throwIfMissing()) {    //参数的默认值不是在定义时执行，而是在运行时执行。如果参数已经赋值，默认值中的函数就不会运行。
        return mustBeProvided;
    }
    foo()
    // Error: Missing parameter

另外，可以将参数默认值设为undefined，表明这个参数是可以省略的。

    function foo(optional = undefined) { ··· }


## 7. rest参数

ES6 引入 rest 参数（形式为...变量名），用于获取函数的多余参数，这样就不需要使用arguments对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

    function add(...values) {
        let sum = 0;
        for (var val of values) {
            sum += val;
        }
        return sum;
    }
    add(2, 5, 3) // 10

下面是一个 rest 参数代替 `arguments` 变量的例子。

    // arguments变量的写法
    function sortNumbers() {
        return Array.prototype.slice.call(arguments).sort();     //arguments对象不是数组，而是一个类似数组的对象。所以为了使用数组的方法，必须使用Array.prototype.slice.call先将其转为数组。
    }

    // rest参数的写法
    const sortNumbers = (...numbers) => numbers.sort();

    function push(array, ...items) {
        items.forEach(function(item) {
            array.push(item);
            console.log(item);
        });
    }
    var a = [];
    push(a, 1, 2, 3)

注意，rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。

    // 报错
    function f(a, ...b, c) {
        // ...
    }

函数的length属性，不包括 rest 参数。

    (function(a) {}).length  // 1
    (function(...a) {}).length  // 0
    (function(a, ...b) {}).length  // 1

##### 8. 严格模式

从 ES5 开始，函数内部可以设定为严格模式。ES2016 做了一点修改，规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。

# 箭头函数





