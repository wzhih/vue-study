# Vue Router 知识回顾

## Vue Router 路由是什么

`Vue Router`是`Vue`官方的路由管理器。和`vue`核心深度集成，为应用提供`url`映射`组件`的路由管理功能

* 模块化的、基于组件的路由配置
* 可嵌套的路由/视图表
* 路由参数、重新、通配符
* 基于`vue`过滤系统的视图过渡效果
* 细粒度的导航控制
* 带有自动激活的`Css class`的链接
* `H5`历史模式和`hash`模式，低版本浏览器自动降级
* 自定义滚动条行为

主要功能是管理路由的，其他功能是附加的


## 与后端路由对比

* 后端路由是管理请求`url`映射对应需要运行的代码，前端路由是管理`url`映射渲染对应组件

* 大多数后端框架都有专门的路由配置文件，`Vue Roouter`也一样有着专门的路由配置文件

* 后端路由基本上都是由`域名`+`uri`构成，前端路由不止是这样，还多加了入口`<router-link>`、出口`<router-view>`
因为不可能用户想要跳转其他页面的时候，自己去修改`uri`(用户还得知道所有`uri`)，既不合理，也不方便
后台系统一般是循环渲染出`<router-link>`菜单模块，然后在页面主模块使用`<router-view>`渲染`uri`对应的组件


## 基础

### 动态路由匹配

**为什么需要动态路由匹配**

后端路由经常有这样子的需求：同一个`uri`+不同路由参数，运行同一份代码，输入参数不一样而已。
最简单的方法就是`GET`，比较好的方法是`pathinfo`(代码自己从请求`url`中解析出路由参数)

前端也是如此，同一个`uri`+不同路由参数，映射到同一个组件，组件内部监听路由参数(相当于函数输入不同参数)，输出不同数据
所以需要有动态路由匹配

**例子：**

```js
const User = {
  template: '<div>User：{{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```

* `/user/1`和`/user/2`都会匹配到`User`组件，而在组件内部，可以使用`this.$route.params.id`访问`uri`中的`id`

* `uri`中可以有多个这样的路径参数，组件可在`this.$route.paramsd`对象中获取对应值

* 由于使用的是同一个组件，组件可能会被复用，所以生命周期钩子函数可能不会被复用
所以，如果要复用组件，那么需要通过侦听属性`watch`或路由导航守卫，调用方法对路由变化作出响应

* 高级用法就是通过正则表达式去匹配路由了

* 路由优先级是：先定义的先匹配


### 嵌套(视图)路由

实际上，页面应用，经常是通过组件嵌套组合成的，同样的`url`中的各段动态参数也可以映射嵌套的各层组件

通过父组件添加`<router-view>`，`Vue Router`参数中使用`children`配置参数来嵌套子组件

以`/`开头的嵌套路径会被当成根路径(意思是：嵌套组件的父组件的`path`不是必须显示的)，如下例子：

```html
<div id="app">
    <div>
        <router-link to="/root/one">Go to</router-link>
        <router-link to="/two">Go to</router-link>
    </div>
    <router-view></router-view>
</div>
```

```js
const Root = { template: `
        <div>
            <h3>this is root</h3>
            <router-view></router-view>
        </div>
    ` }
const One = { template: '<div>One</div>' }
const Two = { template: '<div>Two</div>' }
    
const router = new VueRouter({
  routes: [
    {
        path: "/root",
        component: Root,
        children: [
            {
                //访问路径：/root/one
                path: "one",
                component: One
            },
            {
                //访问路径： /two
                path: "/two",
                component: Two
            }
        ]
    }
  ]
})

const app = new Vue({
      'el': '#app',
      router
})
```
可以发现，我们通过`/two`即可以访问`Root嵌套Two`的页面组件


### 编程式导航

普通html页面，路由的切换，除了可以通过`<a>`标签变化，还可以通过`js`对`window.location`对象的修改进行切换，这就是编程式导航

`vue`可以使用`<router-link>`创建`<a>`标签定义导航链接，也可以通过对`$router`实例的方法调用，进行导航、切换路由

浏览器会记录访问过的路由历史记录，可以说是一个数组，最尾部的记录就是当前路由，`$router`实例提供方法去操作这个数组

| 方法 | 作用 |
|---|---|
| `push` | 往这个数组尾部添加一项，所以，会跳转到最尾部的路由记录上 |
| `replace` | 与`push`挺像的，但是他是替换掉数组尾部，也就是当前的路由记录 |
| `go` | 参数是整数，代表往前会往后跳转多少个路由记录 |


### 命名路由

其实就是给路由起个唯一的名字，在想要跳转到某个路由的时候可以使用这个名字，好处：

* 路由太长了，路由名字比较短
* 路由可能会被修改，但只要路由名称不改，使用路由名称照样不用修改


### 命名视图

在同一级，可以有多个`<router-view name=''></router-view>`，只要`name`属性不一样
相对应的，多个视图显示，就代表需要映射多个组件，如下例子：

```html
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```

```js
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
```

### 重定向与别名

重定向是通过`routes`的配置来完成的

只需要添加`redirect`属性，值是想要跳转到哪个路由的标识(`path`、`name`、函数)

重定向实质上是路由切换，跳转。别名是一个组件有两个路由匹配，通过这个两个路由都可以访问此组件，且路由不变


### 路由组件传参

**动态路由匹配**的时候说过，可以从路由中获取参数，组件通过`$route`实例获取参数值

因为组件与`$route`高度耦合，这样子做只适合一次性组件，不适合多次复用的基础组件，所以就有了路由组件传参

视图组件只需要像普通组件那样添加`props`属性，然后在`routes`配置中添加`props`属性，且值为`true`，`route.params`将会被设置为组件属性

```js
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },

    // 对于包含命名视图的路由，你必须分别为每个命名视图添加 `props` 选项：
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }
    }
  ]
})
```


## 进阶

### 导航守卫

> "导航"表示路由正在发生变化

> "守卫"表示自定义钩子函数

**完整的导航解析流程**
一般第3步的守卫用的比较多

1. 导航被触发
2. 在失活的组件里调用 `beforeRouteLeave` 守卫
3. 调用全局的 `beforeEach` 守卫
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)
5. 在路由配置里调用 `beforeEnter`
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`
8. 调用全局的 `beforeResolve` 守卫 (2.5+)
9. 导航被确认
10. 调用全局的 `afterEach` 钩子
11. 触发 `DOM` 更新
12. 用创建好的实例调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数

#### 全局前置守卫

使用`router.beforeEach`注册一个全局前置守卫
这个守卫会在解析流程里面的第3步触发


#### 全局解析守卫

你可以用 `router.beforeResolve` 注册一个全局守卫。
这和 `router.beforeEach` 类似，区别是在导航被确认之前，**同时在所有组件内守卫和异步路由组件被解析之后**，解析守卫就被调用
这个守卫会在解析流程里面的第8步触发


#### 全局后置钩子

你也可以注册`router.afterEach`全局后置钩子，然而和守卫不同的是，这些钩子不会接受 `next` 函数也不会改变导航本身
这个守卫会在解析流程里面的第10步触发


#### 路由独享的守卫

可以在路由配置上直接定义 `beforeEnter` 守卫
这个守卫会在解析流程里面的第5步触发


#### 组件内的守卫

* `beforeRouteEnter`
* `beforeRouteUpdate`
* `beforeRouteLeave`


### 路由元信息

`routes`定义路由的时候可以配置`meta`字段，这个字段可以存储开发者的一些自定义字段

用于鉴权、icon等等


### 过度动效

可以在`<router-view></router-view>`外添加`<transition>`设置过度效果


### 数据获取

有两中方式，以A页面组件跳转B页面组件为例

* 导航完成之后获取：跳转到B页面之后，开启loding加载中指示，直到数据获取成功或失败
  * `created`钩子函数
* 导航完成之前获取：跳转到B页面之前还在A页面，获取数据，成功后再渲染出B页面
  * `beforeRouteEnter`、`beforeRouteUpdate`



### 滚动行为、路由懒加载



