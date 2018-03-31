# 深克隆和浅克隆

浅克隆：对象只会呗克隆最外层的一层，至于最深层的对象，则依然是通过引用指向同一块堆内存

## 浅克隆

* 方法一：函数方式

        function shallowClone (o){
            const obj = {};
            for (let i in o){
                obj[i] = o[i];
            }
            return obj;
        }
        const oldObj = {
            a: 1,
            b: ['e','f','g']
            c: {h: {i : 2}}
        };
        const newObj = shallowClone(oldObj);
        console.log(newObj.c.h, oldObj.c.h);  //{i:2}  {i:2}
        console.log(oldObj.c.h === newObj.c.h);  //true

    由此可以看出，虽然oldObj.c.h被克隆了，但是它还与oldObj.c.h相当，这表明他们依然指向同一段堆内存，只要改变了newObj.c.h.i的值，oldObj.c.h.i也被改变了，这就是浅克隆的问题所在。

* 方法二：Object.assign()

    Object.assign方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。

    Object.assign方法的第一个参数是目标对象，后面的参数都是源对象。

    注意，如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。

    如果只有一个参数，Object.assign会直接返回该参数。如果该参数不是对象，则会先转成对象，然后返回。

    由于undefined和null无法转成对象，所以如果它们作为参数，就会报错。如果非对象参数出现在源对象的位置（即非首参数），那么处理规则有所不同。首先，这些参数都会转成对象，如果无法转成对象，就会跳过。这意味着，如果undefined和null不在首参数，就不会报错。

    Object.assign方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。

## 深克隆

* JSON.parse方法

        const newObj = JSON.parse(JSON.stringify(oldObj));

    JSON对象的parse方法可以将JSON字符串反序列化成JS对象，stringify方法可以将JS对象序列化成JSON字符串，这两个方法结合起来就能产生一个便捷的深克隆。

    但是这个方法虽然可以解决绝大多数的场景，但是却有很多坑。

    1. 它无法实现对函数、RegExp等特殊对象的克隆
    2. 会抛弃对象的constructor,所有的构造函数会指向Object
    3. 对象有循环引用，会报错

* 深克隆函数

    通常的深克隆方法，也会出现上面的问题。例如：

        function isArray(arr){
            return Object.prototype.toString.call(arr) === '[object Array]';
        }
        function deepClone (obj){
            if (typeof obj !== "object" && typeof obj !== "function"){
                return obj;   //原始类型直接返回
            }
            var o = isArray(obj) ? [] : {};
            for (let i in obj){
                if(obj.hasOwnProperty(i)){
                    o[i] = typeof obj[i] === "object" ? deepClone(obj[i]) : obj[i];
                }
            }
            return o;
        }

    （此方法也会出现上面的问题）

    构造一个深克隆函数

        /**
        * deep clone
        * @param  {[type]} parent object 需要进行克隆的对象
        * @return {[type]}        深克隆后的对象
        */
        const clone = parent => {
        // 维护两个储存循环引用的数组
        const parents = [];
        const children = [];

        const _clone = parent => {
            if (parent === null) return null;
            if (typeof parent !== 'object') return parent;

            let child, proto;

            if (isType(parent, 'Array')) {
            // 对数组做特殊处理
            child = [];
            } else if (isType(parent, 'RegExp')) {
            // 对正则对象做特殊处理
            child = new RegExp(parent.source, getRegExp(parent));
            if (parent.lastIndex) child.lastIndex = parent.lastIndex;
            } else if (isType(parent, 'Date')) {
            // 对Date对象做特殊处理
            child = new Date(parent.getTime());
            } else {
            // 处理对象原型
            proto = Object.getPrototypeOf(parent);
            // 利用Object.create切断原型链
            child = Object.create(proto);
            }

            // 处理循环引用
            const index = parents.indexOf(parent);

            if (index != -1) {
            // 如果父数组存在本对象,说明之前已经被引用过,直接返回此对象
            return children[index];
            }
            parents.push(parent);
            children.push(child);

            for (let i in parent) {
            // 递归
            child[i] = _clone(parent[i]);
            }

            return child;
        };
        return _clone(parent);
        };


    
