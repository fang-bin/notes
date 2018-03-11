# vue-router

vue-router 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。

### 动态路由匹配

1. 当使用路由参数时，例如从 /user/foo 导航到 /user/bar，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用。

    复用组件时，想对路由参数的变化作出响应的话，你可以简单地 watch（监测变化） $route 对象：

        const User = {
            template: '...',
            watch: {
                '$route' (to, from) {
                // 对路由变化作出响应...
                }
            }
        }

    或者使用 2.2 中引入的 `beforeRouteUpdate` 守卫：

        const User = {
            template: '...',
            beforeRouteUpdate (to, from, next) {
                // react to route changes...
                // don't forget to call next()
            }
        }

2. 匹配优先级

    有时候，同一个路径可以匹配多个路由，此时，匹配的优先级就按照路由的定义顺序：谁先定义的，谁的优先级就最高。

### 嵌套路由

### 编程式导航

    // 字符串
    router.push('home')

    // 对象
    router.push({ path: 'home' })

    // 命名的路由，假设'/user/:userId'的name为user 则下面的跳转为 'user/123'
    router.push({ name: 'user', params: { userId: 123 }})

    // 带查询参数，变成 /register?plan=private
    router.push({ path: 'register', query: { plan: 'private' }})`

    router.push()和router.replace()很像，唯一不同的就是router.replace()不会向history添加新记录，而是替换掉当前的history记录。

    router.go()  类似于  window.history.go()

    router.push()  ---  window.history.pushState()

    router.replace()   ---  window.history.replaceState()

    router.go()  ---  window.history.go()

### 命名视图

有时候想同时（同级）展示多个视图，而不是嵌套展示，例如创建一个布局，有 sidebar（侧导航） 和 main（主内容） 两个视图，这个时候命名视图就派上用场了。你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 router-view 没有设置名字，那么默认为 default。

    <router-view class="view one"></router-view>
    <router-view class="view two" name="a"></router-view>
    <router-view class="view three" name="b"></router-view>

    const router = new VueRouter({
        routes: [
            {
                path: '/',
                components: {
                    default: Foo,
                    a: Bar,
                    b: Baz
                }
            }
        ]
    })


### 别名

/a 的别名是 /b，意味着，当用户访问 /b 时，URL 会保持为 /b，但是路由匹配则为 /a，就像用户访问 /a 一样。

    const router = new VueRouter({
        routes: [
            { path: '/a', component: A, alias: '/b' }
        ]  routes: [
            { path: '/a', component: A, alias: '/b' }
        ]
    })

### 向路由组件传递props

对于包含命名视图的路由，你必须分别为每个命名视图添加 `props` 选项

    {
        path: '/user/:id',
        components: { default: User, sidebar: Sidebar },
        props: { default: true, sidebar: false }
    }

## 导航守卫

参数或查询的改变并不会触发进入/离开的导航守卫。你可以通过观察 $route 对象来应对这些变化，或使用 beforeRouteUpdate 的组件内守卫。

#### 全局守卫

###### 全局前置守卫

    const router = new VueRouter({ ... })

    router.beforeEach((to, from, next) => {
        // ...
    })

next: Function: 一定要调用该方法来 resolve 这个钩子。


###### 全局后置钩子

    router.afterEach((to, from) => {
        // ...
    })

###### 路由独享的守卫

可以在路由配置上直接定义 beforeEnter 守卫：

    const router = new VueRouter({
        routes: [
            {
                path: '/foo',
                component: Foo,
                beforeEnter: (to, from, next) => {
                    // ...
              }
            }
        ]
    })

这些守卫与全局前置守卫的方法参数是一样的。

###### 组件内的守卫

beforeRouteEnter(to, from, next)  在渲染该组件的对应路由被 confirm 前调用，不！能！获取组件实例 `this`，因为当守卫执行前，组件实例还没被创建

beforeRouteUpdate(to, from, next) (2.2 新增)   在当前路由改变，但是该组件被复用时调用，举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。可以访问组件实例 `this`

beforeRouteLeave   导航离开该组件的对应路由时调用，可以访问组件实例 `this`

## 数据获取

1. 导航完成之后获取

使用这种方式，会立即跳转路由和渲染组件，然后在created钩子中获取数据，同时监控路由，当路由发生变化时，要重新请求数据，例：

    watch: {
        // 如果路由有变化，会再次执行该方法
        '$route': 'fetchData'   // fetchDate是请求数据的方法
    },

2. 导航完成之前获取

通过这种方式，我们在跳转路由之前获取数据，可以在组件的 beforeRouteEnter 守卫中获取数据，获取数据成功之后，调用next()方法


##  滚动行为

使用前端路由，当切换到新路由时，想要页面滚到顶部，或者是保持原先的滚动位置，就像重新加载页面那样。 vue-router 能做到，而且更好，它让你可以自定义路由切换时页面如何滚动。注：这个功能只在支持 history.pushState 的浏览器中可用。

    const router = new VueRouter({
        routes: [...],
        scrollBehavior (to, from, savedPosition) {
          // return 期望滚动到哪个的位置
        }
    })

## 路由懒加载

将异步组件定义为返回一个Promise的工厂函数（该函数返回的Promise应该resolve组件本身）

