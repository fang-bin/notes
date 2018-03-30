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

    
