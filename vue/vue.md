# 面试题

## vue

虽然没有完全遵循 MVVM 模型，但是 Vue 的设计也受到了它的启发。

1. 值得注意的是只有当实例被创建时 data 中存在的属性才是响应式的。也就是说如果你添加一个新的属性,那么对新的属性的改动将不会触发任何视图的更新。

2. 生命周钩子：`beforeCreate`  `created`  `beforeMount`  `mounted`  `beforeUpdate`  `updated`  `beforeDestroy`  `destroyed`

3. 计算属性是基于它们的依赖进行缓存的。计算属性只有在它的相关依赖发生改变时才会重新求值。这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。

    这也同样意味着下面的计算属性将不再更新，因为 Date.now() 不是响应式依赖

4. 虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 watch 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。

5. Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。这么做除了使 Vue 变得非常快之外，还有其它一些好处。例如，如果你允许用户在不同的登录方式之间切换。

    所以 Vue 为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”。只需添加一个具有唯一值的 key 属性即可。

6. 一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。

    当 v-if 与 v-for 一起使用时，v-for 具有比 v-if 更高的优先级。

7. v-for在遍历对象时，是按 Object.keys() 的结果遍历，但是不能保证它的结果在不同的 JavaScript 引擎下是一致的。

8. 当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用“就地复用”策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。

    这个默认的模式是高效的，但是只适用于不依赖子组件状态或临时 DOM 状态 (例如：表单输入值) 的列表渲染输出。

    为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。理想的 key 值是每项都有的且唯一的 id。

9. Vue 包含一套观察数组变异的方法，所以它们也将触发 Vue 视图更新。例：push()  pop()  shift()   unshift()   splice()   sort()   reverse(),这些方法都会改变原数组。

    相比之下也有非变异方法，这些方法不会改变原数组，而是会返回一个新数组，例：`filter()`  `concat()`   `slice()`，可以用新数组去替换旧数组。

10. 由于 JavaScript 的限制，Vue 不能检测以下变动的数组

    当你利用索引直接设置一个项时，例如：`vm.items[indexOfItem] = newValue`

    当你修改数组的长度时，例如：`vm.items.length = newLength`

    解决第一种情况，可以使用： 

    `Vue.set(example1.items, indexOfItem, newValue)`
    或
    `example1.items.splice(indexOfItem, 1, newValue)`

    解决第二种情况，可以使用：

    `example1.items.splice(newLength)`

11. 由于 JavaScript 的限制，Vue 不能检测对象属性的添加或删除，可以使用 Vue.set(object, key, value) 方法向嵌套对象添加响应式属性。

12. 有时你可能需要为已有对象赋予多个新属性，比如使用 Object.assign() 或 _.extend()。在这种情况下，你应该用两个对象的属性创建一个新的对象。所以，如果你想添加新的响应式属性，不要像这样：

        Object.assign(vm.userProfile, {
            age: 27,
            favoriteColor: 'Vue Green'
        })

    你应该这样做：

        vm.userProfile = Object.assign({}, vm.userProfile, {
            age: 27,
            favoriteColor: 'Vue Green'
        })

13. 事件修饰符 `stop`  `prevent`  `capture`(添加时间监听器时，使用事件捕获模式，即元素自身触发的事件优先在此处理，然后再交由内部元素处理)  `self`  `once`

    `.passive`  <div v-on:scroll.passive="onScroll">...</div>  滚动事件的默认行为 (即滚动行为) 将会立即触发，而不会等待 `onScroll` 完成，这其中包含 `event.preventDefault()` 的情况，这个 .passive 修饰符尤其能够提升移动端的性能。

    不要把 .passive 和 .prevent 一起使用，因为 .prevent 将会被忽略，同时浏览器可能会向你展示一个警告。请记住，.passive 会告诉浏览器你不想阻止事件的默认行为。

14. `v-cloak`   这个指令保持在元素上直到关联实例结束编译。和 CSS 规则如 [v-cloak] { display: none } 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。



### vue 和 MVVM

MVVM 是Model-View-ViewModel 的缩写，它是一种基于前端开发的架构模式，其核心是提供对View 和 ViewModel 的双向数据绑定，这使得ViewModel 的状态改变可以自动传递给 View，即所谓的数据双向绑定。

## 组件

组件系统是 Vue 的另一个重要概念，因为它是一种抽象，允许我们使用小型、独立和通常可复用的组件构建大型应用。
在 Vue 里，一个组件本质上是一个拥有预定义选项的一个 Vue 实例。
子单元通过 prop 接口与父单元进行了良好的解耦。
在一个大型应用中，有必要将整个应用程序划分为组件，以使开发更易管理。

1. 注意在 JavaScript 中对象和数组是引用类型，指向同一个内存空间，如果 prop 是一个对象或数组，在子组件内部改变它会影响父组件的状态。

2. 默认情况下，一个组件的 v-model 会使用 value prop 和 input 事件。但是诸如单选框、复选框之类的输入类型可能把 value 用作了别的目的。model 选项可以避免这样的冲突。

3. 自定义组件的 v-model

    默认情况下，一个组件的 v-model 会使用 value prop 和 input 事件。但是诸如单选框、复选框之类的输入类型可能把 value 用作了别的目的。model 选项可以避免这样的冲突：

        Vue.component('my-checkbox', {
            model: {
                prop: 'checked',
                event: 'change'
            },
            props: {
                checked: Boolean,
                // 这样就允许拿 `value` 这个 prop 做其它事了
                value: String
            },
            // ...
        })
        <my-checkbox v-model="foo" value="some value"></my-checkbox>

    你只需要在组件中声明一个name为value的props，并且通过触发input事件传入一个值，就能修改这个value。

4. 父组件模板的内容在父组件作用域内编译；子组件模板的内容在子组件作用域内编译。

5. 动态组件： 通过使用保留的 <component> 元素，并对其 `is` 特性进行动态绑定，你可以在同一个挂载点动态切换多个组件

6. keep-alive  如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。

7. Vue 组件的 API 来自三部分——prop、事件和插槽：

    Prop 允许外部环境传递数据给组件；

    事件允许从组件内触发外部环境的副作用；

    插槽允许外部环境将额外的内容组合在组件中。

8. 子组件引用

    尽管有 prop 和事件，但是有时仍然需要在 JavaScript 中直接访问子组件。为此可以使用 `ref` 为子组件指定一个引用 ID。

    当 `ref` 和 `v-for` 一起使用时，获取到的引用会是一个数组，包含和循环数据源对应的子组件。

    `$refs` 只在组件渲染完成后才填充，（beforeCreate created beforeMount生命周期内，是取不到$ref的引用的）并且它是非响应式的。它仅仅是一个直接操作子组件的应急方案——应当避免在模板或计算属性中使用 $refs。

9. 异步组件

    将一个大项目拆分成数个组件，然后按需从服务器下载，为了进一步简化，Vue.js允许将组件定义为一个工厂函数，异步的解析组件的定义。Vue.js只要在组件需要渲染时触发工厂函数，并把结果缓存起来，用于后面的再次渲染

    我一般在路由文件做如此处理，写一个工厂函数，用来解析组件的定义，例如文件中：

        component: resolve => require(['../page/index.vue'], resolve)

10. 对低开销的静态组件使用 v-once

    尽管在 Vue 中渲染 HTML 很快，不过当组件中包含大量静态内容时，可以考虑使用 v-once 将渲染结果缓存起来。

11. vm.$nextTick

    官方解释：将回调延迟到下次 DOM 更新循环之后执行。在修改数据之后立即使用它，然后等待 DOM 更新。它跟全局方法 Vue.nextTick 一样，不同的是回调的 this 自动绑定到调用它的实例上。

    深层原因： Vue 实现响应式并不是数据发生变化之后 DOM 立即变化，而是按一定的策略进行 DOM 的更新。Vue的数据驱动视图更新，是异步的，即修改数据的当下，视图不会立刻更新，而是等同一事件循环中的所有数据变化完成之后，再统一进行视图更新。

    Vue是异步执行dom更新的，一旦观察到数据变化，Vue就会开启一个队列，然后把在同一个事件循环 (event loop) 当中观察到数据变化的 watcher 推送进这个队列。如果这个watcher被触发多次，只会被推送到队列一次。这种缓冲行为可以有效的去掉重复数据造成的不必要的计算和DOm操作。而在下一个事件循环时，Vue会清空队列，并进行必要的DOM更新。[事件循环](http://www.cnblogs.com/hity-tt/p/6733062.html)

    当你修改过数据，DOM 并不会马上更新，而是在异步队列被清除，也就是下一个事件循环开始时执行更新时才会进行必要的DOM更新。如果此时你想要根据更新的 DOM 状态去做某些事情，就会出现问题。。为了在数据变化之后等待 Vue 完成更新 DOM ，可以在数据变化之后立即使用 Vue.nextTick(callback) 。这样回调函数在 DOM 更新完成后就会调用。

## 混入

    混入 (mixins) 是一种分发 Vue 组件中可复用功能的非常灵活的方式。混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被混入该组件本身的选项。

### 全局混入是每个引用都会调用一遍么？？？  我在main.js中全局混入一个created(){console.log('a')}   发现打印了好多遍   怎么回事？

## 过滤器


# vue-router



