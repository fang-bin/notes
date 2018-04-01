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


