# javascript原型与原型链

JavaScript中，万物皆对象！

对象也分为普通对象和函数对象。

> **凡是通过 new Function() 创建的对象都是函数对象，其他的都是普通对象。**

## 构造函数

    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.sayName = function() { alert(this.name) } 
    }
    var person1 = new Person('Zaxlct', 28, 'Software Engineer');
    var person2 = new Person('Mick', 23, 'Doctor');

person1和person2都是Person的实例，这两个实例都有一个constructor(构造函数)属性，该属性（是一个指针）指向Person。即：

    console.log(person1.constructor == Person); //true
    console.log(person2.constructor == Person); //true

> **实例的构造函数属性（constructor）指向构造函数**

## 原型对象

在 JavaScript 中，每当定义一个对象（函数也是对象）时候，对象中都会包含一些预定义的属性。其中每个函数对象都有一个prototype 属性，这个属性指向函数的原型对象。

    function Person() {}
    Person.prototype.name = 'Zaxlct';
    Person.prototype.age  = 28;
    Person.prototype.job  = 'Software Engineer';
    Person.prototype.sayName = function() {
        alert(this.name);
    }
    
    var person1 = new Person();
    person1.sayName(); // 'Zaxlct'

    var person2 = new Person();
    person2.sayName(); // 'Zaxlct'

    console.log(person1.sayName == person2.sayName); //true

> **每个对象都有 __proto__ 属性，但只有函数对象才有 prototype 属性**

    Person.prototype = {
        name:  'Zaxlct',
        age: 28,
        job: 'Software Engineer',
        sayName: function() {
            alert(this.name);
        }
    }

原型对象，顾名思义，它就是一个普通对象（感觉好像函数对象的prototype属性就是一个普通对象了,类似于一个实例）。从现在开始你要牢牢记住原型对象就是 Person.prototype ，如果你还是害怕它，那就把它想想成一个字母 A： `var A = Person.prototype`

在上面我们给 A 添加了 四个属性：name、age、job、sayName。其实它还有一个默认的属性：constructor

> 在默认情况下，所有的原型对象都会自动获得一个 constructor（构造函数）属性，这个属性（是一个指针）指向 prototype 属性所在的函数（Person）

> **Person.prototype.constructor == Person**

> **person1.constructor == Person**
>
> **Person.prototype.constructor == Person**

person1 为什么有 constructor 属性？那是因为 person1 是 Person 的实例。

那 Person.prototype 为什么有 constructor 属性？？同理， Person.prototype （你把它想象成 A） 也是Person 的实例。
也就是在 Person 创建的时候，创建了一个它的实例对象并赋值给它的 prototype，基本过程如下：

    var A = new Person();
    Person.prototype = A;

> **原型对象（Person.prototype）是 构造函数（Person）的一个实例**

原型对象其实就是普通对象（但 Function.prototype 除外，它是函数对象，但它很特殊，他没有prototype属性（前面说道函数对象都有prototype属性）

    function Person(){};
    console.log(Person.prototype) //Person{}
    console.log(typeof Person.prototype) //Object
    console.log(typeof Function.prototype) // Function，这个特殊
    console.log(typeof Object.prototype) // Object
    console.log(typeof Function.prototype.prototype) //undefined

Function.prototype 为什么是函数对象呢？

    var A = new Function ();
    Function.prototype = A;

上文提到凡是通过 new Function( ) 产生的对象都是函数对象。因为 A 是函数对象，所以Function.prototype 是函数对象。

**原型对象主要作用是用于继承**

    var Person = function(name){
        this.name = name; // tip: 当函数执行时这个 this 指的是谁？实例本身！
    };
    Person.prototype.getName = function(){
        return this.name;  // tip: 当函数执行时这个 this 指的是谁？Person.prototype 即 实例
    }
    var person1 = new person('Mick');
    person1.getName(); //Mick


## __proto__

javascript在创建对象(不管是普通对象还是函数对象)的时候,都有一个 `__proto` 的内置属性,用于指向创建他的构造函数的原型对象.

> **Person.prototype.constructor == Person**
> **person1.__proto__ == Person.prototype**
> **person1.constructor == Person**

不过，要明确的真正重要的一点就是，这个连接存在于实例（person1）与构造函数（Person）的原型对象（Person.prototype）之间，而不是存在于实例（person1）与构造函数（Person）之间。

### 小测试

1. `person1.__proto__ == Person.prototype`
2. `Person.__proto__ == Function.prototype`
    函数对象的构造函数都是Function
3. Person.prototype.__proto__ == Person.prototype ???
    错了!!!前面说过Person.prototype相当于构造函数Person的一个实例,由于构造函数的实例都是通过new Person()创建的,而函数对象都是通过new Function()创建的,所以其是一个普通对象,而普通函数的构造函数 === Object ,所以

    **`Person.prototype.__proto__ == Object.prototype`**
4. `Object.__proto__ == Function.prototype`
    同第二项
5. Object.prototyppe.__proto__ == Object.prototype ???
    Object.prototype对象也有 __proto__ 属性,但它比较特殊, 为null。因为null处于原型链的顶端,这个只需要记住.

    **`Object.prototype.__proto__ == null`**

## 函数对象

所有函数对象的proto都指向Function.prototype，它是一个空函数（Empty function）

    Number.__proto__ === Function.prototype  // true
    Number.constructor == Function //true

    Boolean.__proto__ === Function.prototype // true
    Boolean.constructor == Function //true

    String.__proto__ === Function.prototype  // true
    String.constructor == Function //true

    // 所有的构造器都来自于Function.prototype，甚至包括根构造器Object及Function自身
    Object.__proto__ === Function.prototype  // true
    Object.constructor == Function // true

    // 所有的构造器都来自于Function.prototype，甚至包括根构造器Object及Function自身
    Function.__proto__ === Function.prototype // true
    Function.constructor == Function //true

    Array.__proto__ === Function.prototype   // true
    Array.constructor == Function //true

    RegExp.__proto__ === Function.prototype  // true
    RegExp.constructor == Function //true

    Error.__proto__ === Function.prototype   // true
    Error.constructor == Function //true

    Date.__proto__ === Function.prototype    // true
    Date.constructor == Function //true

JavaScript中有内置(build-in)构造器/对象共计12个（ES5中新加了JSON），这里列举了可访问的8个构造器。剩下如Global不能直接访问，Arguments仅在函数调用时由JS引擎创建，Math，JSON是以对象形式存在的，无需new。它们的proto是Object.prototype。如下

    Math.__proto__ === Object.prototype  // true
    Math.construrctor == Object // true

    JSON.__proto__ === Object.prototype  // true
    JSON.construrctor == Object //true

> **所有的构造器都来自于 Function.prototype，甚至包括根构造器Object及Function自身。所有构造器都继承了·Function.prototype·的属性及方法。如length、call、apply、bind**

Function.prototype也是唯一一个typeof XXX.prototype为 function的prototype。其它的构造器的prototype都是一个对象（原因第三节里已经解释过了）。

    console.log(typeof Function.prototype) // function
    console.log(typeof Object.prototype)   // object
    console.log(typeof Number.prototype)   // object
    console.log(typeof Boolean.prototype)  // object
    console.log(typeof String.prototype)   // object
    console.log(typeof Array.prototype)    // object
    console.log(typeof RegExp.prototype)   // object
    console.log(typeof Error.prototype)    // object
    console.log(typeof Date.prototype)     // object
    console.log(typeof Object.prototype)   // object

上面还提到`Function.prototype`是一个空的函数,知道了所有构造器（含内置及自定义）的__proto__都是Function.prototype，那Function.prototype的__proto__是谁呢？

相信都听说过JavaScript中函数也是一等公民，那从哪能体现呢？如下

    console.log(Function.prototype.__proto__ === Object.prototype) // true

这说明所有的构造器也都是一个普通 JS 对象，可以给构造器添加/删除属性等。同时它也继承了Object.prototype上的所有方法：toString、valueOf、hasOwnProperty等。

最后Object.prototype的proto是谁？

> **Object.prototype.__proto__ === null**

## Prototype

> 在 ECMAScript 核心所定义的全部属性中，最耐人寻味的就要数 prototype 属性了。对于 ECMAScript 中的引用类型而言，prototype 是保存着它们所有实例方法的真正所在。换句话所说，诸如 toString()和 valuseOf() 等方法实际上都保存在 prototype 名下，只不过是通过各自对象的实例访问罢了。

