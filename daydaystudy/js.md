# 记录平常一些javascript中容易遗忘的点

1. 数组去重的方法(es6)

    let arr = [1,2,2,3];
    let newArr = [...new Set(arr)];  // [1,2,3]

2. JavaScript 是一种弱类型或者说动态语言。这意味着你不用提前声明变量的类型，在程序运行过程中，类型会被自动确定。这也意味着你可以使用同一个变量保存不同类型的数据

3. Vue 中写定时器时，一定要使用箭头函数，因为普通函数中的this指向是可变的，但在箭头函数中是固定的，箭头函数函数体内的 this 对象指向其定义时所在的对象，而不是使用时所在的对象。假如 setTimeout 的参数是一个箭头函数，这个箭头函数的定义生效是在函数生成时，而它的真正执行要等到 一段时间 后。如果是普通函数，执行时this应该指向全局对象window。

    注意： 在Vue实例中data,methods,computed,watch等属性中，不应该使用箭头函数来定义相应的函数，因为箭头函数绑定了父级作用域的上下文，所以 this 不会按照期望指向 Vue 实例。取到的实例属性为 undefined 。

    this指向的固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。正是因为它没有this，所以也就不能用作构造函数。

    除了this，以下三个变量在箭头函数之中也是不存在的，指向外层函数的对应变量：arguments、super、new.target。

4. 扩展运算符（spread）是三个点（...）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。

5. ES6 中对象的属性和方法可以简写

6. 在`sessionStorage`、`localStorage`中存储对象和数组的时候。

        const obj = {abc: '123'};
        sessionStorage.setItem('a',JSON.stringfy(obj));
        const obj0 = JSON.parse(sessionStorage.getItem('a'));

7. 使用tree命令生成目录结构树

    在命令行工具中使用: `Tree [drive:][path] [/F] [/A]`，例如：

        Tree C: /F >C:\TXT\Filelist.txt    //可以将C盘上所有文件夹中的文件列表保存到“C:\TXT\Filelist.txt”文件中

8. hasOwnProperty：是用来判断一个对象是否有你给出名称的属性或对象。不过需要注意的是，此方法无法检查该对象的原型链中是否具有该属性，该属性必须是对象本身的一个成员。

    * for in可以获取object的所有属性,包括自定义属性以及原型链属性。
    * hasOwnProperty()只能获取自定义属性，无法获取原型链属性。
    * Object.keys，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。

9. 函数声明的优先级高于变量声明的优先级，不过不会覆盖变量赋值。

        (function fuc (a){
            console.log(a);
            var a = 1;
            function a (){}
        })(100);
    
    上例中主要考察:

    * 就近原则（变量查找按照就近原则，当就近没查找到就去外层查找）
    * 变量声明提前（如果用es6中的let、const进行声明，再之前调用变量或常量就回报错，因为暂时性死区）（JS的变量和函数声明都会被存储到执行上下文的变量对象或活动对象中,即声明提前）
    * 函数声明的优先级高于变量声明的优先级，不过不会覆盖变量赋值。

    [相关链接](https://blog.csdn.net/wy818/article/details/49247675)





