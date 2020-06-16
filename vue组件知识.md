# vue 组件

## 比较好的做法

* `data`是个函数
* 子组件通过触发事件修改父组件绑定的值
  * 通过使用`.sync`标识符，子组件只需要触发`update:propName`事件，并且抛出修改后的`propName`的值即可
* 单文件组件
* 需要时再引入组件(即使是基础组件)
* 组件`props`增加验证，为每一个传入属性定义`类型`、`默认值`、`是否必须`


## QA

### 组件是什么

组件含义理解：组件是可复用的Vue实例，具有与普通`new Vue`实例**基本**一样的`生命周期`、`属性`、`方法`。
面向对象说法：`Vue`是一个`类`，组件就是继承的`子类`。

组件特征：
* 没有`el`根元素(或者说根元素就是组件本身)
* 可复用(所以`data`属性必须是一个函数)
* 组件可在组件中嵌套使用(就像在`div`中使用`table`，`table`中使用`th`.将组件理解为自定义html标签可能更好理解)
* 主要包含
  * data
  * props
  * methods
  * template(Html(单个根元素) & Css(局部样式))
  * 生命周期部分与`new Vue`的生命周期一样，钩子方法也一样


### data为什么必须是函数

`data`属性是引用类型

如果`data`是对象的形式，那么当复用对象的时候，多个实例化对象的`data`都指向同一引用地址的同一个数据，修改其中一个组件对象的`data`，其他组件对象的`data`都会随着变化

如果`data`是函数的形式，那么当复用对象的时候，多个实例化对象的`data`都是独立的数据，修改其中一个对象，其他组件对象并不会变化

*如果想要同步多个组件内的数据，应该使用`Vuex`等全局状态管理*

### 组件如何传值

按组件层级可分为：

* 父子组件传值
  * 父传子：使用`prop`自定义属性，子组件通过`props`属性，绑定父组件在子组件上的自定义属性
  * 子传父：使用`event`自定义事件，子组件通过`$emit`触发事件，父组件通过`v-on:event-name`捕获冒泡事件及参数
* 同级组件传值
  * 通过相同父组件传值(子传父，父再传其他子)
  * 使用`Vuex`全局状态管理
* 非同级非父子
  * 使用`Vuex`全局状态管理

**注意:不应该直接修改这些传入的自定义属性的值，应该通过`子传父`的方式去修改**
**vue2.3.0新增了`.sync`修饰符简化父子组件传值**

### 子组件是否可以通过直接修改父组件传入的自定义属性，来进行父子传值

父组件在子组件上面的自定义属性`prop`是一个`单向下行绑定`(就是父组件对自定义属性的修改，会更新到子组件中)

这个`单向下行绑定`其实是引用类型的应用，所以，从原理来说，子组件可以通过直接修改父组件传入的自定义属性，来进行父子传值

但是不建议这样做，官方文档：**防止从子组件意外变更父级组件的状态，从而导致你的应用的数据流向难以理解**


### 非props绑定的自定义属性会在组件上怎么样

默认情况下：
* `class`属性会与子组件`class`自动合并
* `style`属性会与子组件`style`自动合并
* 其他属性将会自动添加/替换子组件的属性

可以设置组件`inheritAttrs`属性为`false`禁用此继承(不影响`class`、`style`)


### 不带参数的v-bind是什么意思

可以将一个对象里面的所有属性，传入组件中(不是直接传入这个对象)，有点类似于`ES6`的函数参数的解构写法


### v-model在组件上的应用

`v-model`本质上是`v-bind`与`v-on`的语法糖，在组件上的应用如下例子

```html
<!-- 下面两个输入框是一样的 -->
<!-- v-model -->
<component-name v-model="name"></component-name>
<!-- v-bind v-on -->
<component-name v-bind:value="name" v-on:input="name = $event.target.value"></component-name>
```

组件上的`v-model`默认会利用名为`value`的`prop`和名为`input`的事件(复选框、下拉框是`change`事件)。组件有个`model`属性可以修改此默认

```js
Vue.component('componentName', {
        model: {
            prop: "name", //自定义属性
            event: "hahaha" //自定义事件
        },
        props: {
            name: {
                type: String,
                required: true
            }
        },
        methods: {
            inputName(val) {
                this.$emit("hahaha", val)
            }
        },
        template: `
        <input type='text' v-bind:value="name" v-on:input="inputName($event.target.value)" />
        `
    })
```




























