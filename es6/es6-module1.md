# 字符串的扩展

##### 1. 字符的 Unicode 表示法

##### 2. codePointAt()

JavaScript 内部，字符以 UTF-16 的格式存储，每个字符固定位 2 个字节。ES6 提供了 `codePointAt` 方法，能够正常处理 4 个字节存储的字符，返回一个字符的码点。

##### 3. String.fromCodePoint()

ES6 提供了String.fromCodePoint方法，可以识别大于0xFFFF的字符，弥补了String.fromCharCode方法的不足。

### 4. 字符串的遍历器接口

ES6 为字符串添加了遍历器接口（详见《Iterator》一章），使得字符串可以被for...of循环遍历。

    for (let codePoint of 'foo') {
    console.log(codePoint)
    }
    // "f"
    // "o"
    // "o"

### 5. at()

类似于 ES5 中的 `chartAt()` 方法，但是可以识别码点大于 0xFFFF 的字符，返回字符串给定位置的字符。

    'abc'.charAt(0) // "a"
    '𠮷'.charAt(0) // "\uD842"   错误的

    'abc'.at(0) // "a"
    '𠮷'.at(0) // "𠮷"

##### 6. normalize()

### 7. includes()、 startsWith()、 endsWith()

传统上， JavaScript 只有 `indexOf` 方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6 又提供了三种新方法。

includes()：返回布尔值，表示是否找到了参数字符串。

startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。

endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

    let s = 'Hello world!';
    s.startsWith('Hello') // true
    s.endsWith('!') // true
    s.includes('o') // true

这三个方法都支持第二个参数，表示开始搜索的位置。

    let s = 'Hello world!';
    s.startsWith('world', 6) // true
    s.endsWith('Hello', 5) // true   ----> endsWith的行为与其他两个方法有所不同。它针对前n个字符
    s.includes('Hello', 6) // false

### 8. repeat()

repeat方法返回一个新字符串，表示将原字符串重复n次。

    'x'.repeat(3) // "xxx"
    'hello'.repeat(2) // "hellohello"
    'na'.repeat(0) // ""
    'na'.repeat(2.9) // "nana"   -->   如果是小数，则会向下取整

而如果参数是负数或则Infinity，就会报错。参数NaN等同于0，如果参数是字符串，则会先转换成数字。

## 9. padStart()、 padEnd()

如果字符串长度不够指定长度，则会补全。padStart()用于头部补全，padEnd()用于尾部补全。

    'x'.padStart(5, 'ab') // 'ababx'
    'x'.padStart(4, 'ab') // 'abax'

    'x'.padEnd(5, 'ab') // 'xabab'
    'x'.padEnd(4, 'ab') // 'xaba'

1. 如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。
2. 如果用来补全的字符串与原字符串，两者的长度之和超过了指定的最小长度，则会截去超出位数的补全字符串。
3. 如果省略第二个参数，默认使用空格补全长度。

例： 

    '12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
    '09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"

### 10. matchAll()

matchAll方法返回一个正则表达式在当前字符串的所有匹配

原先，字符串对象有 4 个方法可以使用正则表达式: `match()`  `replace()`  `search()`   `split()`


# 模板字符串

模板字符串是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

    // 普通字符串
    `In JavaScript '\n' is a line-feed.`

    // 多行字符串
    `In JavaScript this is
    not legal.`

    console.log(`string text line 1
    string text line 2`);

    // 字符串中嵌入变量
    let name = "Bob", time = "today";
    `Hello ${name}, how are you ${time}?`

注意： 
1. 如果要在模板字符串中使用反引号，则要在前面使用反斜杠转义。
2. 使用模板字符串定义多行字符串时，所有的空格和缩进都会被保留在输出之中。
3. 模板字符串中嵌入变量，需要将变量名写在${}之中。大括号内部可以放入任意的 JavaScript 表达式，可以进行运算，以及引用对象属性。
4. 模板字符串之中还能调用函数。 `foo ${fn()} bar`
5. 如果大括号中的值不是字符串，将按照一般的规则转为字符串。比如，大括号中是一个对象，将默认调用对象的toString方法。
6. 如果模板字符串中的变量没有声明，将报错。
7. 由于模板字符串的大括号内部，就是执行 JavaScript 代码，因此如果大括号内部是一个字符串，将会原样输出。
8. 模板字符串甚至还能嵌套。

        const tmpl = addrs => `
            <table>
            ${addrs.map(addr => `
                <tr><td>${addr.first}</td></tr>
                <tr><td>${addr.last}</td></tr>
            `).join('')}
            </table>
        `;

    即是

        const data = [
            { first: '<Jane>', last: 'Bond' },
            { first: 'Lars', last: '<Croft>' },
        ];

        console.log(tmpl(data));
        // <table>
        //
        //   <tr><td><Jane></td></tr>
        //   <tr><td>Bond</td></tr>
        //
        //   <tr><td>Lars</td></tr>
        //   <tr><td><Croft></td></tr>
        //
        // </table>

9. 如果需要引用模板字符串本身，在需要时执行，可以像下面这样写。

        // 写法一
        let str = 'return ' + '`Hello ${name}!`';
        let func = new Function('name', str);
        func('Jack') // "Hello Jack!"

        // 写法二
        let str = '(name) => `Hello ${name}!`';
        let func = eval.call(null, str);
        func('Jack') // "Hello Jack!"

### 模板编译

    let template = `
    <ul>
        <% for(let i=0; i < data.supplies.length; i++) { %>
            <li><%= data.supplies[i] %></li>
        <% } %>
    </ul>
    `;

上面代码在模板字符串之中，放置了一个常规模板。该模板使用<%...%>放置 JavaScript 代码，使用<%= ... %>输出 JavaScript 表达式。

### 标签模板

模板字符串可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串。这被称为“标签模板”功能。

    alert`123`
    // 等同于
    alert(123)

标签模板其实不是模板，而是函数调用的一种特殊形式。“标签”指的就是函数，紧跟在后面的模板字符串就是它的参数。

**比较复杂，待续。。。。。。**

# 正则表达式

# 数值的扩展

### 1. Number.isFinite(), Number.isNaN()

`Number.isFinite()` 用来检查一个数值是否为有限的（finite）即不是 Infinity 。注意，如果参数类型不是数值，Number.isFinite一律返回false。

Number.isNaN()用来检查一个值是否为NaN。注意，如果参数类型不是数值，Number.isNaN一律返回false。

它们与传统的全局方法isFinite()和isNaN()的区别在于，传统方法先调用Number()将非数值的值转为数值，再进行判断，而这两个新方法只对数值有效，Number.isFinite()对于非数值一律返回false, Number.isNaN()只有对于NaN才返回true，非NaN一律返回false。

    isFinite(25) // true
    isFinite("25") // true
    Number.isFinite(25) // true
    Number.isFinite("25") // false

    isNaN(NaN) // true
    isNaN("NaN") // true
    Number.isNaN(NaN) // true
    Number.isNaN("NaN") // false
    Number.isNaN(1) // false

### 2. Number.parseInt(), Number.parseFloat()

ES6 将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。

    // ES5的写法
    parseInt('12.34') // 12
    parseFloat('123.45#') // 123.45

    // ES6的写法
    Number.parseInt('12.34') // 12
    Number.parseFloat('123.45#') // 123.45

这样做的目的，是逐步减少全局性方法，使得语言逐步模块化。

### 3. Number.isInteger()

Number.isInteger()用来判断一个数值是否为整数。JavaScript 内部，整数和浮点数采用的是同样的储存方法，所以 25 和 25.0 被视为同一个值。

    Number.isInteger(25) // true
    Number.isInteger(25.1) // false
    Number.isInteger(25.0) // true

如果参数不是数值，Number.isInteger返回false。

    Number.isInteger() // false
    Number.isInteger(null) // false
    Number.isInteger('15') // false
    Number.isInteger(true) // false

注意： 如果对数据精度的要求较高，不建议使用Number.isInteger()判断一个数值是否为整数。（这是因为JavaScript的数值存储为64位双精度格式，数值精度最多可以达到53个二进制为（1个隐藏位与52个有效位），如果数值的精度超过这个限度，第54位及后面的位就会被丢弃。）例：`Number.isInteger(3.0000000000000002) // true`

## Math 对象的扩展

1. **Math.trunc()**    --->  Math.trunc方法用于去除一个数的小数部分，返回整数部分。对于非数值，Math.trunc内部使用Number方法将其先转为数值。对于空值和无法截取整数的值，返回NaN。

        Math.trunc(4.1)       // 4
        Math.trunc(-4.1)      // -4
        Math.trunc(-0.1234)     // -0
        Math.trunc('123.456')     // 123
        Math.trunc(true)      //1
        Math.trunc(false)      // 0
        Math.trunc(null)      // 0
        Math.trunc(NaN);      // NaN
        Math.trunc('foo');    // NaN
        Math.trunc();         // NaN
        Math.trunc(undefined) // NaN

2. **Math.sign()**   --->   Math.sign方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。

    * 参数为正数，返回+1；
    * 参数为负数，返回-1；
    * 参数为 0，返回0；
    * 参数为-0，返回-0;
    * 其他值，返回NaN

**等等，后面很少使用，不做深究。。。**



